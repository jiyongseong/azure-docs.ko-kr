---
title: "Azure DC/OS 클러스터용 파일 공유"
description: "Azure Container Service에서 DC/OS 클러스터에 파일 공유 만들기 및 탑재"
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 06/07/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: c1c318f4204efd24a2d9d3d83bb1cb71f5775bdb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2017
---
# <a name="create-and-mount-a-file-share-to-a-dcos-cluster"></a>DC/OS 클러스터에 파일 공유 만들기 및 탑재

이 문서에서는 Azure에서 파일 공유를 만들어서 DC/OS 클러스터의 각 에이전트 및 마스터에 탑재하는 방법을 설명합니다. 파일 공유를 설정하면 구성, 액세스, 로그 등과 같은 클러스터 전반에서 파일을 간편하게 공유할 수 있습니다. 이 자습서에서는 다음 태스크를 완료합니다.

> [!div class="checklist"]
> * Azure 저장소 계정 만들기
> * 파일 공유 만들기
> * DC/OS 클러스터에 공유 탑재

이 자습서의 단계를 완료하려면 ACS DC/OS 클러스터가 필요합니다. 필요한 경우 [이 스크립트 샘플](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)을 사용하여 클러스터를 만들 수 있습니다.

이 자습서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-file-share-on-microsoft-azure"></a>Microsoft Azure에서 파일 공유 만들기

ACS DC/OS 클러스터에서 Azure 파일 공유를 사용하기 전에 저장소 계정 및 파일 공유를 만들어야 합니다. 다음 스크립트를 실행하여 저장소 및 파일 공유를 만듭니다. 사용자 환경에서 해당 항목으로 매개 변수를 업데이트합니다.

```azurecli-interactive
# Change these four parameters
DCOS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
DCOS_PERS_RESOURCE_GROUP=myResourceGroup
DCOS_PERS_LOCATION=eastus
DCOS_PERS_SHARE_NAME=dcosshare

# Create the storage account with the parameters
az storage account create -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -l $DCOS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $DCOS_PERS_STORAGE_ACCOUNT_NAME -g $DCOS_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $DCOS_PERS_SHARE_NAME
```

## <a name="mount-the-share-in-your-cluster"></a>클러스터에 공유 탑재

다음으로 파일 공유는 클러스터 내 모든 가상 컴퓨터에 탑재되어야 합니다. cifs 도구/프로토콜을 사용하여 이 작업을 완료합니다. 클러스터의 각 노드에서 수동으로 또는 스크립트를 실행하여 탑재 작업을 완료할 수 있습니다.

이 예제에서는 Azure 파일 공유에 탑재되는 스크립트 및 DC/OS 클러스터의 각 노드에서 이 스크립트를 실행하는 스크립트와 같이 두 개의 스크립트가 실행됩니다.

먼저, Azure Storage 계정 이름과 선택키가 필요합니다. 다음 명령을 실행하여 이 정보를 가져옵니다. 이러한 값을 이후 단계에서 사용하도록 적어둡니다.

Storage 계정 이름:

```azurecli-interactive
STORAGE_ACCT=$(az storage account list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[?contains(name, '$DCOS_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCT
```

Storage 계정 선택키:

```azurecli-interactive
az storage account keys list --resource-group $DCOS_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCT --query "[0].value" -o tsv
```

다음으로 DC/OS 마스터의 FQDN을 가져와서 변수에 저장합니다.

```azurecli-interactive
FQDN=$(az acs list --resource-group $DCOS_PERS_RESOURCE_GROUP --query "[0].masterProfile.fqdn" --output tsv)
```

마스터 노드에 개인 키를 복사합니다. 이 키는 클러스터의 모든 노드와 SSH 연결을 만드는 데 필요합니다. 클러스터를 만들 때 기본값이 아닌 값이 사용된 경우 사용자 이름을 업데이트합니다. 

```azurecli-interactive
scp ~/.ssh/id_rsa azureuser@$FQDN:~/.ssh
```

DC/OS 기반 클러스터의 마스터(또는 첫 번째 마스터)와의 SSH 연결을 만듭니다. 클러스터를 만들 때 기본값이 아닌 값이 사용된 경우 사용자 이름을 업데이트합니다.

```azurecli-interactive
ssh azureuser@$FQDN
```

**cifsMount.sh**라는 파일을 만들어서 여기에 다음과 같은 내용을 복사합니다. 

이 스크립트는 Azure 파일 공유를 탑재하는 데 사용됩니다. `STORAGE_ACCT_NAME` 및 `ACCESS_KEY` 변수를 이전에 수집한 정보로 업데이트합니다.

```azurecli-interactive
#!/bin/bash

# Azure storage account name and access key
SHARE_NAME=dcosshare
STORAGE_ACCT_NAME=mystorageaccount
ACCESS_KEY=mystorageaccountKey

# Install the cifs utils, should be already installed
sudo apt-get update && sudo apt-get -y install cifs-utils

# Create the local folder that will contain our share
if [ ! -d "/mnt/share/$SHARE_NAME" ]; then sudo mkdir -p "/mnt/share/$SHARE_NAME" ; fi

# Mount the share under the previous local folder created
sudo mount -t cifs //$STORAGE_ACCT_NAME.file.core.windows.net/$SHARE_NAME /mnt/share/$SHARE_NAME -o vers=3.0,username=$STORAGE_ACCT_NAME,password=$ACCESS_KEY,dir_mode=0777,file_mode=0777
```
**getNodesRunScript.sh**라는 두 번째 파일을 만들고 파일에 다음 내용을 복사합니다. 

이 스크립트는 모든 클러스터 노드를 검색한 다음 **cifsMount.sh** 스크립트를 실행하여 각 노드에 파일 공유를 탑재합니다.

```azurecli-interactive
#!/bin/bash

# Install jq used for the next command
sudo apt-get install jq -y

# Get the IP address of each node using the mesos API and store it inside a file called nodes
curl http://leader.mesos:1050/system/health/v1/nodes | jq '.nodes[].host_ip' | sed 's/\"//g' | sed '/172/d' > nodes

# From the previous file created, run our script to mount our share on each node
cat nodes | while read line
do
  ssh `whoami`@$line -o StrictHostKeyChecking=no < ./cifsMount.sh
  done
```

스크립트를 실행하여 클러스터의 모든 노드에서 Azure 파일 공유를 탑재합니다.

```azurecli-interactive
sh ./getNodesRunScript.sh
```  

이제 클러스터의 각 노드에 있는 `/mnt/share/dcosshare`에서 파일 공유에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 단계를 사용하여 DC/OS 클러스터에 Azure 파일 공유를 사용할 수 있게 되었습니다.

> [!div class="checklist"]
> * Azure 저장소 계정 만들기
> * 파일 공유 만들기
> * DC/OS 클러스터에 공유 탑재

다음 자습서로 넘어가서 Azure에서 DC/OS와 Azure Container Registry를 통합하는 방법을 알아봅니다.  

> [!div class="nextstepaction"]
> [부하 분산 응용 프로그램](container-service-dcos-acr.md)
