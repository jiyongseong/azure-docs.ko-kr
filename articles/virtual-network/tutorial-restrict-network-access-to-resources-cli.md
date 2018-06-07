---
title: PaaS 리소스에 대한 네트워크 액세스 제한 - Azure CLI | Microsoft Docs
description: 이 문서에서는 Azure CLI를 사용하여 가상 네트워크 서비스 엔드포인트를 통해 Azure Storage 및 Azure SQL Database와 같은 Azure 리소스에 대한 네트워크 액세스를 제한하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f357861a7a44b249e06f091a8693b7f2d8dd5178
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Azure CLI를 사용하여 가상 네트워크 서비스 끝점으로 PaaS 리소스에 대한 네트워크 액세스 제한

가상 네트워크 서비스 끝점을 사용하면 일부 Azure 서비스 리소스에 대한 네트워크 액세스를 가상 네트워크 서브넷으로 제한할 수 있습니다. 리소스에 대한 인터넷 액세스를 제거할 수도 있습니다. 서비스 끝점은 가상 네트워크에서 지원되는 Azure 서비스로의 직접 연결을 제공하므로 가상 네트워크의 개인 주소 공간을 사용하여 Azure 서비스에 액세스할 수 있습니다. 서비스 끝점을 통해 Azure 리소스에 도달하는 트래픽은 항상 Microsoft Azure 백본 네트워크에 유지됩니다. 이 문서에서는 다음 방법을 설명합니다.

* 하나의 서브넷이 있는 가상 네트워크 만들기
* 서브넷을 추가하고 서비스 끝점을 사용하도록 설정
* Azure 리소스를 만들고 서브넷에서만 네트워크 액세스 허용
* 각 서브넷에 VM(가상 머신) 배포
* 서브넷에서 리소스에 대한 액세스 확인
* 서브넷 및 인터넷에서 리소스에 대한 액세스가 거부되는지 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 아티클에서 만든 다른 모든 리소스를 만들어야 합니다. [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create)를 사용하여 하나의 서브넷이 있는 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>서비스 끝점 사용 

서비스 끝점을 지원하는 서비스에 대해서만 서비스 끝점을 사용하도록 설정할 수 있습니다. [az network vnet list-endpoint-services](/cli/azure/network/vnet#az_network_vnet_list_endpoint_services)를 사용하여 Azure 위치에서 사용할 수 있는 서비스 끝점 지원 서비스를 확인합니다. 다음 예제에서는 *eastus* 지역에서 사용할 수 있는 서비스 끝점 지원 서비스 목록을 반환합니다. 반환되는 서비스 목록은 시간이 지나면서 서비스 끝점을 사용할 수 있는 Azure 서비스가 증가함에 따라 확장됩니다.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)를 사용하여 가상 네트워크에서 추가 서브넷을 만듭니다. 이 예제에서는 서브넷에 대해 *Microsoft.Storage*용 서비스 끝점이 생성됩니다. 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>서브넷에 대한 네트워크 액세스 제한

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create)를 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsgPrivate*라는 네트워크 보안 그룹을 만듭니다.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

[az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update)를 사용하여 네트워크 보안 그룹을 *Private* 서브넷에 연결합니다. 다음 예제에서는 *myNsgPrivate* 네트워크 보안 그룹을 *Private* 서브넷에 연결합니다.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create)를 사용하여 보안 규칙을 만듭니다. 다음에 나오는 규칙은 Azure Storage 서비스에 지정된 공용 IP 주소에 대한 아웃바운드 액세스를 허용합니다. 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"

Each network security group contains several [default security rules](security-overview.md#default-security-rules). The rule that follows overrides a default security rule that allows outbound access to all public IP addresses. The `destination-address-prefix "Internet"` option denies outbound access to all public IP addresses. The previous rule overrides this rule, due to its higher priority, which allows access to the public IP addresses of Azure Storage.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"

The following rule allows SSH traffic inbound to the subnet from anywhere. The rule overrides a default security rule that denies all inbound traffic from the internet. SSH is allowed to the subnet so that connectivity can be tested in a later step.

az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>리소스에 대한 네트워크 액세스 제한

서비스 끝점에 사용할 수 있는 Azure 서비스를 통해 만든 리소스에 대한 네트워크 액세스를 제한하는 데 필요한 단계는 서비스에 따라 다릅니다. 각 서비스에 대한 특정 단계는 개별 서비스의 설명서를 참조하세요. 이 문서의 나머지 부분에는 Azure Storage 계정에 대한 네트워크 액세스를 제한하는 단계가 예제로 포함되어 있습니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기

[az storage account create](/cli/azure/storage/account#az_storage_account_create)를 사용하여 Azure Storage 계정을 만듭니다. `<replace-with-your-unique-storage-account-name>`을 모든 Azure 위치에서 고유한 이름으로 바꿉니다. 이름은 3~24자 사이여야 하고 숫자와 소문자만 사용할 수 있습니다.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

저장소 계정이 생성된 후 [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string)을 사용하여 저장소 계정에 대한 연결 문자열을 변수로 검색합니다. 이 연결 문자열은 이후 단계에서 파일 공유를 만드는 데 사용됩니다.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>변수의 내용을 확인하고 이후 단계에서 사용되므로 출력에 반환된 **AccountKey** 값을 적어둡니다.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>저장소 계정에 파일 공유 만들기

[az storage share create](/cli/azure/storage/share#az_storage_share_create)를 사용하여 저장소 계정에서 파일 공유를 만듭니다. 이후 단계에서 네트워크 액세스를 확인하기 위해 이 파일 공유가 탑재됩니다.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>저장소 계정에 대한 모든 네트워크 액세스 거부

기본적으로 저장소 계정은 네트워크에 있는 클라이언트의 네트워크 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 [az storage account update](/cli/azure/storage/account#az_storage_account_update)를 사용하여 기본 작업을 *거부*로 변경합니다. 네트워크 액세스가 거부되면 네트워크에서 저장소 계정에 액세스할 수 없습니다.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>서브넷에서 네트워크 액세스 사용

[az storage account network-rule add](/cli/azure/storage/account/network-rule#az_storage_account_network_rule_add)를 사용하여 *Private* 서브넷에서 저장소 계정에 대한 네트워크 액세스를 허용합니다.

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>가상 머신 만들기

저장소 계정에 대한 네트워크 액세스를 테스트하려면 각 서브넷에 VM을 배포합니다.

### <a name="create-the-first-virtual-machine"></a>첫 번째 가상 머신 만들기

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 *공용* 서브넷에 VM을 만듭니다. 또한 기본 키 위치에 SSH 키가 없는 경우 해당 명령이 이 키를 만듭니다. 특정 키 집합을 사용하려면 `--ssh-key-value` 옵션을 사용합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. VM을 만든 후 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

반환된 출력에서 **publicIpAddress**를 적어둡니다. 이 주소는 이후 단계에서 인터넷을 통해 VM에 액세스하는 데 사용됩니다.

### <a name="create-the-second-virtual-machine"></a>두 번째 가상 머신 만들기

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

VM을 만드는 데 몇 분이 걸립니다. 만든 후에는 반환된 출력에서 **publicIpAddress**를 적어둡니다. 이 주소는 이후 단계에서 인터넷을 통해 VM에 액세스하는 데 사용됩니다.

## <a name="confirm-access-to-storage-account"></a>저장소 계정에 대한 액세스 확인

*myVmPrivate* VM으로 SSH를 수행합니다. *<publicIpAddress>*를 *myVmPrivate* VM의 공용 IP 주소로 바꿉니다.

```bash 
ssh <publicIpAddress>
```

탑재 지점에 대한 폴더를 만듭니다.

```bash
sudo mkdir /mnt/MyAzureFileShare
```

만든 디렉터리에 Azure 파일 공유를 탑재합니다. 다음 명령을 실행하기 전에 `<storage-account-name>`을 계정 이름으로, `<storage-account-key>`를 [저장소 계정 만들기](#create-a-storage-account)에서 검색한 키로 바꿉니다.

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

`user@myVmPrivate:~$` 프롬프트가 수신됩니다. Azure 파일 공유가 */mnt/MyAzureFileShare*에 탑재되었습니다.

VM에 다른 공용 IP 주소에 대한 아웃바운드 연결이 없는지 확인합니다.

```bash
ping bing.com -c 4
```

*Private* 서브넷과 연결된 네트워크 보안 그룹이 Azure Storage 서비스에 지정된 주소 이외의 공용 IP 주소에 대한 아웃바운드 액세스를 허용하지 않으므로 응답이 수신되지 않습니다.

*myVmPrivate* VM에 대한 SSH 세션을 종료합니다.

## <a name="confirm-access-is-denied-to-storage-account"></a>저장소 계정에 대한 액세스가 거부되는지 확인

다음 명령을 사용하여 *myVmPublic* VM으로 SSH 세션을 만듭니다. `<publicIpAddress>`를 *myVmPublic* VM의 공용 IP 주소로 바꿉니다. 

```bash 
ssh <publicIpAddress>
```

탑재 지점에 대한 디렉터리를 만듭니다.

```bash
sudo mkdir /mnt/MyAzureFileShare
```

만든 디렉터리에 Azure 파일 공유를 탑재합니다. 이 문서에서는 최신 버전의 Ubuntu를 배포했다고 가정합니다. 이전 버전의 Ubuntu를 사용하는 경우 [Linux에 탑재](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에서 파일 공유 탑재에 대한 추가 지침을 참조하세요. 다음 명령을 실행하기 전에 `<storage-account-name>`을 계정 이름으로, `<storage-account-key>`를 [저장소 계정 만들기](#create-a-storage-account)에서 검색한 키로 바꿉니다.

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

*myVmPublic* VM이 *Public* 서브넷에 배포되었으므로 액세스가 거부되고 `mount error(13): Permission denied` 오류가 수신됩니다. *Public* 서브넷에는 Azure Storage에 사용할 수 있는 서비스 끝점이 없으며 저장소 계정이 *Public* 서브넷이 아닌 *Private* 서브넷의 네트워크 액세스만 허용합니다.

*myVmPublic* VM에 대한 SSH 세션을 종료합니다.

컴퓨터에서 [az storage share list](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list)를 사용하여 저장소 계정의 공유를 봅니다. `<account-name>` 및 `<account-key>`를 [저장소 계정 만들기](#create-a-storage-account)의 저장소 계정 이름과 키로 바꿉니다.

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

액세스가 거부되고 *이 요청은 이 작업을 수행할 권한이 없습니다.* 오류가 수신됩니다. 사용자 컴퓨터가 *MyVirtualNetwork* 가상 네트워크의 *Private* 서브넷에 없기 때문입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure#az_group_delete)를 사용하여 리소스 그룹 및 그룹에 포함된 모든 리소스를 제거합니다.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크 서브넷에 대해 서비스 엔드포인트를 사용하도록 설정했습니다. 여러 Azure 서비스로 배포된 리소스에 대해 서비스 끝점을 사용하도록 설정할 수 있음을 배웠습니다. Azure Storage 계정을 만들고 저장소 계정에 대한 네트워크 액세스를 가상 네트워크 서브넷 내의 리소스로만 제한했습니다. 서비스 엔드포인트에 대한 자세한 내용은 [서비스 엔드포인트 개요](virtual-network-service-endpoints-overview.md) 및 [서브넷 관리](virtual-network-manage-subnet.md)를 참조하세요.

계정에 여러 개의 가상 네트워크가 있는 경우 각 가상 네트워크 내의 리소스가 서로 통신할 수 있도록 두 개의 가상 네트워크를 함께 연결하는 것이 좋습니다. 방법을 알아보려면 [가상 네트워크 연결](tutorial-connect-virtual-networks-cli.md)을 참조하세요.