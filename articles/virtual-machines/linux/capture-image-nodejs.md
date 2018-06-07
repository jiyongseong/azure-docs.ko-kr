---
title: Azure Linux VM을 캡처하여 템플릿으로 사용 | Microsoft Docs
description: Azure Resource Manager 배포 모델을 사용하여 만든, Linux 기반 Azure VM(가상 머신)의 이미지를 캡처하고 일반화하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 3b45f46197467dd7b83bd986604338e14daa8107
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Azure에서 실행되는 Linux 가상 머신 캡처하기
Resource Manager 배포 모델에서 Azure Linux 가상 머신(VM)을 일반화하고 캡처하려면 이 문서의 단계를 따릅니다. VM을 일반화하는 경우 개인 계정 정보를 제거하고 VM이 이미지로 사용되도록 준비합니다. 그런 다음 OS용 일반화된 VHD(가상 하드 디스크) 이미지, 연결된 데이터 디스크용 VHD, 새 VM 배포용 [Resource Manager 템플릿](../../azure-resource-manager/resource-group-overview.md)을 캡처합니다. 이 문서에서는 VM에 대해 Azure CLI 1.0으로 관리되지 않는 디스크를 사용하여 VM 이미지를 캡처하는 방법을 자세히 설명합니다. [Azure CLI 2.0으로 Azure Managed Disks를 사용하여 VM을 캡처](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)할 수도 있습니다. 관리되는 디스크는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다. 자세한 내용은 [Azure Managed Disks 개요](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

이미지를 사용하여 VM을 만들려면, 각각의 새 VM에 대해 네트워크 리소스를 설정하고, 템플릿(JSON(JavaScript Object Notation) 파일)을 사용하여 캡처한 VHD 이미지로부터 VM을 배포합니다. 이러한 방식으로 Azure Marketplace에서 이미지를 사용하는 것과 유사한 방식으로 현재 소프트웨어 구성으로 VM을 복제할 수 있습니다.

> [!TIP]
> 백업 또는 디버깅용의 특수화된 상태로 기존 Linux VM의 복사본을 만들려면 [Azure에서 실행되는 Linux 가상 머신의 복사본 만들기](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 온-프레미스 VM으로부터 Linux VHD를 업로드하려면 [사용자 지정 디스크 이미지에서 Linux VM 업로드 및 만들기](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.  

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#before-you-begin) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI

## <a name="before-you-begin"></a>시작하기 전에
다음 필수 조건을 충족하는지 확인합니다.

* **Azure VM이 Resource Manager 배포 모델에 생성됨** - Linux VM을 만들지 않은 경우, [포털](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [Resource Manager 템플릿](create-ssh-secured-vm-from-template.md)을 사용할 수 있습니다. 
  
    필요에 따라 VM을 구성합니다. 예를 들어 [데이터 디스크를 추가하고](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), 업데이트를 적용하고, 응용 프로그램을 설치합니다. 
* **Azure CLI** - 로컬 컴퓨터에 [Azure CLI](../../cli-install-nodejs.md)를 설치합니다.

## <a name="step-1-remove-the-azure-linux-agent"></a>1단계: Azure Linux 에이전트를 제거합니다.
우선 Linux VM에서 **deprovision** 매개 변수와 함께 **waagent** 명령을 실행합니다. 이 명령은 VM이 가상화 준비가 되도록 파일과 데이터를 삭제합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../extensions/agent-windows.md)를 참조하세요.

1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
2. SSH 창에서 다음 명령을 입력합니다.
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 과정이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다.
 
3. 계속하려면 **y** 를 입력합니다. 이 확인 단계를 피하려면 **-force** 매개 변수를 추가합니다.
4. 명령이 완료된 후 **exit**를 입력합니다. 이 단계는 SSH 클라이언트를 닫습니다.

## <a name="step-2-capture-the-vm"></a>2단계: VM 캡처
Azure CLI를 사용하여 VM을 일반화하고 캡처합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 **myResourceGroup**, **myVnet**, **myVM**이 포함됩니다.

1. 로컬 컴퓨터에서 Azure CLI를 열고 [Azure 구독에 로그인](/cli/azure/authenticate-azure-cli)합니다. 
2. Resource Manager 모드에 있는지 확인합니다.
   
    ```azurecli
    azure config mode arm
    ```
3. 다음 명령을 사용하여 프로비전을 해제한 VM을 종료합니다.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. 다음 명령을 사용하여 VM을 일반화합니다.
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. 이제 VM을 캡처하는 **azure vm capture** 명령을 실행합니다. 다음 예의 경우 이름이 **MyVHDNamePrefix**로 시작되는 이미지 VHD가 캡처되며 **-t** 옵션은 **MyTemplate.json** 템플릿에 대한 경로를 지정합니다. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > 이미지 VHD 파일이 원본 VM이 사용된 동일한 저장소 계정에서 기본적으로 생성됩니다. 이미지를 통해 만든 새 VM에 대한 VHD를 저장하려면 *동일한 저장소 계정*을 사용합니다. 

6. 캡처한 이미지의 위치를 찾으려면 텍스트 편집기에서 JSON 템플릿을 엽니다. **storageProfile**에서 **시스템** 컨테이너에 있는 **이미지**의 **uri**를 찾습니다. 예를 들어, OS 디스크 이미지의 URI는 `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`와 유사합니다.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3단계: 캡처한 이미지로부터 새 VM 만들기
템플릿과 이미지를 사용하여 Linux VM을 만듭니다. 이 단계는 Azure CLI 및 캡처한 JSON 파일 템플릿을 사용하여 새 가상 네트워크에 VM을 만드는 방법을 보여줍니다.

### <a name="create-network-resources"></a>네트워크 리소스 만들기
템플릿을 사용하려면 우선 새 VM에 대한 NIC와 가상 네트워크를 생성해야 합니다. VM 이미지가 저장된 위치에 있는 이러한 리소스에 대해 리소스 그룹을 만드는 것이 좋습니다. 다음과 유사한 명령을, 리소스 이름과 Azure 위치(이 명령의 경우 “centralus”)를 적절하게 대체한 후 실행합니다.

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>NIC의 ID 가져오기
캡처를 하는 동안 저장한 JSON을 사용하여 이미지의 VM을 배포하려면 NIC의 ID가 필요합니다. 다음 명령을 실행하여 ID를 확보합니다.

```azurecli
azure network nic show myResourceGroup1 myNIC
```

출력되는 **Id**는 `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`과 유사합니다.

### <a name="create-a-vm"></a>VM 만들기
이제 다음 명령을 실행하여 캡처한 VM 이미지로부터 VM을 만듭니다. **-f** 매개 변수를 사용하여 저장해 놓은 템플릿 JSON 파일에 대한 경로를 지정합니다.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

명령 출력에 새 VM 이름, 관리자 사용자 이름 및 암호, 이전에 만든 NIC의 ID를 제공하라는 프롬프트가 표시됩니다.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

다음 샘플은 배포 완료 시 표시되는 내용을 보여줍니다.

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>배포 확인
생성한 가상 머신에 SSH를 실행하여 배포를 확인하고 새 VM 사용을 시작합니다. SSH를 통해 연결하려면 다음 명령을 실행하여 생성한 VM의 IP 주소를 찾습니다.

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

명령 출력에 공용 IP 주소가 나열됩니다. 기본적으로 SSH 포트 22를 사용하여 Linux VM에 연결합니다.

## <a name="create-additional-vms"></a>추가 VM 만들기
이전 섹션의 단계를 사용하여 캡처한 이미지와 템플릿을 사용하여 추가 VM을 배포합니다. 이미지를 통해 VM을 만드는 다른 옵션에는 빠른 시작 템플릿을 사용하거나 **azure vm create** 명령을 실행하는 옵션이 있습니다.

### <a name="use-the-captured-template"></a>캡처한 템플릿 사용
캡처한 이미지와 템플릿을 사용하려면 다음 단계를 수행합니다(앞의 섹션에 자세히 설명됨).

* VM 이미지가 VM의 VHD를 호스팅하는 계정과 동일한 저장소 계정에 있는지 확인합니다.
* 템플릿 JSON 파일을 복사하고 새 VM VHD의 OS 디스크에 대해 고유한 이름을 지정합니다. 예를 들어, **storageProfile**의 **vhd** 아래 **uri**에 **osDisk** VHD에 대한 고유한 이름을 `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`와 유사하게 지정합니다.
* NIC를 동일한 가상 네트워크 또는 다른 가상 네트워크에 만듭니다.
* 수정된 템플릿 JSON 파일을 사용하여, 가상 네트워크를 설정한 리소스 그룹에 배포를 만듭니다.

### <a name="use-a-quickstart-template"></a>빠른 시작 템플릿 사용
이미지로부터 VM을 만들 때 네트워크에서 자동으로 설정되도록 하려면 템플릿에 해당 리소스를 지정합니다. 예를 들어 GitHub의 [101-vm-from-user-image template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)을 참조하세요. 이 템플릿은 사용자 지정 이미지에서 VM을 만들고 필요한 가상 네트워크, 공용 IP 주소, NIC 리소스를 만듭니다. Azure 포털에서 템플릿 사용에 대한 안내는 [Resource Manager 템플릿을 사용하여 사용자 지정 이미지에서 가상 머신 만드는 방법](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)을 참조하십시오.

### <a name="use-the-azure-vm-create-command"></a>azure vm create 명령 사용
Resource Manager 템플릿을 사용하여 이미지에서 VM을 만드는 것이 일반적으로 가장 쉽습니다. 하지만 **-Q**(**--image-urn**) 매개 변수와 함께 **azure vm create** 명령을 사용하면 *명령적으로* VM을 만들 수 있습니다. 이 방법을 사용하는 경우 새 VM에 대한 OS .vhd 파일 위치를 지정하는 **-d**(**--os-disk-vhd**) 매개 변수도 전달합니다. 이 파일은 이미지 VHD 파일이 저장된 저장소 계정의 VHD 컨테이너에 있어야 합니다. 이 명령은 새 VM에 대한 VHD를 자동으로 **vhds** 컨테이너에 복사합니다.

이미지에 **azure vm create** 을 실행하기 전에 다음 단계를 완료합니다.

1. 리소스 그룹을 만들거나 배포를 위한 기존 리소스 그룹을 확인합니다.
2. 새 VM에 대한 NIC 리소스와 공용 IP 주소 리소스를 만듭니다. CLI를 사용하여 가상 네트워크, 공용 IP 주소, NIC를 만드는 단계는 이 문서의 앞부분을 참조하세요. (**azure vm create** 역시 NIC를 만들 수 있지만 가상 네트워크 및 서브넷에 대해 추가적인 매개 변수를 전달해야 합니다.)

그런 다음 새 OS VHD 파일 및 기존 이미지 양쪽 모두에 URI를 전달하는 명령을 실행합니다. 이 예의 경우 크기가 Standard_A1인 VM이 미국 동부 지역에 만들어집니다.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

추가적인 명령 옵션은 `azure help vm create`을 실행합니다.

## <a name="next-steps"></a>다음 단계
CLI를 사용하여 VM을 관리하려면 [Azure 리소스 관리자 템플릿 및 Azure CLI를 사용하여 가상 머신 배포 및 관리](create-ssh-secured-vm-from-template.md)를 참조하세요.

