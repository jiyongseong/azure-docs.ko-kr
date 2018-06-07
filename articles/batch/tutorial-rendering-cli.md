---
title: 클라우드에서 장면 렌더링 - Azure Batch
description: 자습서 - Batch Rendering Service 및 Azure 명령줄 인터페이스를 사용하여 Arnold에서 Autodesk 3ds Max 장면을 렌더링하는 방법을 알아봅니다.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: tutorial
ms.date: 04/19/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 5cd4ce6b04f9257de13aad6e59eb772fbe2fa558
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-render-a-scene-with-azure-batch"></a>자습서: Azure Batch를 사용하여 장면 렌더링 

Azure Batch Rendering Service는 클라우드 수준 렌더링 기능을 사용량 기준 과금으로 제공합니다. Batch Rendering Service는 Autodesk Maya, 3ds Max, Arnold, V-Ray 등의 렌더링 앱을 지원합니다. 이 자습서에서는 Azure 명령줄 인터페이스를 사용하여 Batch를 통해 작은 장면을 렌더링하는 단계를 보여 줍니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure 저장소에 장면 업로드
> * 렌더링을 위한 Batch 풀 만들기
> * 단일 프레임 장면 렌더링
> * 풀 크기 조정 및 다중 프레임 장면 렌더링
> * 렌더링된 출력 다운로드

이 자습서에서는 [Arnold](https://www.autodesk.com/products/arnold/overview) 광선 투사 방식 렌더러를 사용하여 Batch를 통해 3ds Max 장면을 렌더링합니다. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서의 3ds Max 장면 샘플은 Bash 스크립트 샘플 및 JSON 구성 파일과 함께 [GitHub](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/batch/render-scene)에 있습니다. 3ds Max 장면은 [Autodesk 3ds Max 샘플 파일](http://download.autodesk.com/us/support/files/3dsmax_sample_files/2017/Autodesk_3ds_Max_2017_English_Win_Samples_Files.exe)에서 제공된 것입니다. (Autodesk 3ds Max 샘플 파일은 Creative 일반 저작자 표시 - 비영리 목적 - 동일 조건 변경 허락 라이선스에 따라 사용할 수 있습니다. Copyright © Autodesk, Inc.)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-batch-account"></a>Batch 계정 만들기

아직 없는 경우 구독에 리소스 그룹, 배치 계정 및 연결된 저장소 계정을 만듭니다. 

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus2* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

[az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 리소스 그룹에 Azure Storage 계정을 만듭니다. 이 자습서에서는 저장소 계정을 사용하여 입력된 3ds Max 장면과 렌더링된 출력을 저장합니다.

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```
[az batch account create](/cli/azure/batch/account#az_batch_account_create) 명령을 사용하여 배치 계정을 만듭니다. 다음 예제에서는 *mybatchaccount*라는 배치 계정을 *myResourceGroup*에 만들고, 만든 저장소 계정을 연결합니다.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

계산 풀 및 작업을 만들고 관리하려면 Batch를 통해 인증해야 합니다. [az batch account login](/cli/azure/batch/account#az_batch_account_login) 명령으로 계정에 로그인합니다. 로그인되면 이 계정 컨텍스트가 `az batch` 명령에 사용됩니다. 다음 예제에서는 배치 계정 이름과 키를 기반으로 하는 공유 키 인증을 사용합니다. 또한 Batch는 [Azure Active Directory](batch-aad-auth.md)를 통한 인증도 지원하여 개별 사용자 또는 무인 응용 프로그램을 인증합니다.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```
## <a name="upload-a-scene-to-storage"></a>저장소에 장면 업로드

저장소에 입력 장면을 업로드하려면, 먼저 저장소 계정에 액세스하고 Blob에 대한 대상 컨테이너를 만들어야 합니다. Azure 저장소 계정에 액세스하려면 `AZURE_STORAGE_KEY` 및 `AZURE_STORAGE_ACCOUNT` 환경 변수를 내보냅니다. 첫 번째 Bash 셸 명령은 [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) 명령을 사용하여 첫 번째 계정 키를 가져옵니다. 이러한 환경 변수가 설정되면 이 계정 컨텍스트가 저장소 명령에 사용됩니다.

```azurecli-interactive
export AZURE_STORAGE_KEY=$(az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup -o tsv --query [0].value)

export AZURE_STORAGE_ACCOUNT=mystorageaccount
```

이제 장면 파일에 대한 저장소 계정에 Blob 컨테이너를 만듭니다. 다음 예제에서는 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 공용 읽기 액세스를 허용하는 *scenefiles*라는 Blob 컨테이너를 만듭니다.

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name scenefiles
```

[GitHub](https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max)의 `MotionBlur-Dragon-Flying.max` 장면을 로컬 작업 디렉터리로 다운로드합니다. 예: 

```azurecli-interactive
wget -O MotionBlur-DragonFlying.max https://github.com/Azure/azure-docs-cli-python-samples/raw/master/batch/render-scene/MotionBlur-DragonFlying.max
```

로컬 작업 디렉터리에서 Blob 컨테이너로 장면 파일을 업로드합니다. 다음 예제에서는 여러 파일을 업로드할 수 있는 [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) 명령을 사용합니다.

```azurecli-interactive
az storage blob upload-batch \
    --destination scenefiles \
    --source ./
```

## <a name="create-a-rendering-pool"></a>렌더링 풀 만들기

[az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) 명령을 사용하여 렌더링할 Batch 풀을 만듭니다. 이 예제에서는 풀 설정을 JSON 파일에 지정합니다. 현재 셸 내에서 *mypool.json* 파일 이름을 만들고 다음 내용을 복사하여 붙여넣습니다. 모든 텍스트가 올바르게 복사되었는지 확인합니다. (이 파일은 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/mypool.json)에서 다운로드할 수 있습니다.)


```json
{
  "id": "myrenderpool",
  "vmSize": "standard_d2_v2",
  "virtualMachineConfiguration": {
    "imageReference": {
      "publisher": "batch",
      "offer": "rendering-windows2016",
      "sku": "rendering",
      "version": "1.2.1"
    },
    "nodeAgentSKUId": "batch.node.windows amd64"
  },
  "targetDedicatedNodes": 0,
  "targetLowPriorityNodes": 1,
  "enableAutoScale": false,
  "applicationLicenses":[
         "3dsmax",
         "arnold"
      ],
  "enableInterNodeCommunication": false 
}
```
Batch는 전용 노드와 [우선 순위가 낮은](batch-low-pri-vms.md) 노드를 지원하며, 풀에서 하나 또는 둘 다 사용할 수 있습니다. 전용 노드는 풀에 예약되어 있습니다. 우선 순위가 낮은 노드는 Azure의 잔여 VM 용량에서 할인된 가격으로 제공됩니다. Azure에 충분한 용량이 없으면 우선 순위가 낮은 노드는 사용할 수 없게 됩니다. 

지정된 풀에는 Batch Rendering Service용 소프트웨어가 있는 Windows Server 이미지를 실행하는 우선 순위가 낮은 단일 노드가 포함됩니다. 이 풀은 3ds Max 및 Arnold를 사용하여 렌더링할 수 있도록 허가되었습니다. 이후의 단계에서 풀이 더 많은 수의 노드로 확장됩니다.

`az batch pool create` 명령에 JSON 파일을 전달하여 풀을 만듭니다.

```azurecli-interactive
az batch pool create \
    --json-file mypool.json
``` 
풀을 프로비전하는 데 몇 분이 걸립니다. 풀의 상태를 보려면 [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) 명령을 실행합니다. 다음 명령은 풀의 할당 상태를 가져옵니다.

```azurecli-interactive
az batch pool show \
    --pool-id myrenderpool \
    --query "allocationState"
```

풀 상태가 변경되는 동안 작업 및 태스크를 만들려면 다음 단계를 계속합니다. 할당 상태가 `steady`이고 노드가 실행되고 있으면 풀이 완전하게 프로비전됩니다.  

## <a name="create-a-blob-container-for-output"></a>출력을 위한 Blob 컨테이너 만들기

이 자습서의 예제에서는 렌더링 작업의 모든 태스크에서 출력 파일을 만듭니다. 작업을 예약하기 전에 저장소 계정에 Blob 컨테이너를 출력 파일에 대한 대상으로 만듭니다. 다음 예제에서는 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령을 사용하여 공용 읽기 액세스 권한이 있는 *job-myrenderjob* 컨테이너를 만듭니다. 

```azurecli-interactive
az storage container create \
    --public-access blob \
    --name job-myrenderjob
```

출력 파일을 컨테이너에 쓰려면 Batch에서 SAS(공유 액세스 서명) 토큰을 사용해야 합니다. [az storage account generate-sas](/cli/azure/storage/account#az_storage_account_generate_sas) 명령을 사용하여 토큰을 만듭니다. 이 예제에서는 계정의 모든 Blob 컨테이너에 쓸 토큰을 만들고, 이 토큰은 2018년 11월 15일에 만료됩니다.

```azurecli-interactive
az storage account generate-sas \
    --permissions w \
    --resource-types co \
    --services b \
    --expiry 2018-11-15
```

명령으로 반환된 토큰은 적어둡니다. 이 토큰은 다음과 비슷하며, 이후 단계에서 사용합니다.

```
se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## <a name="render-a-single-frame-scene"></a>단일 프레임 장면 렌더링

### <a name="create-a-job"></a>작업 만들기

[az batch job create](/cli/azure/batch/job#az_batch_job_create) 명령을 사용하여 풀에서 실행할 렌더링 작업을 만듭니다. 처음에는 작업에 태스크가 없습니다.

```azurecli-interactive
az batch job create \
    --id myrenderjob \
    --pool-id myrenderpool
```

### <a name="create-a-task"></a>작업을 만듭니다.

[az batch task create](/cli/azure/batch/task#az_batch_task_create) 명령을 사용하여 작업의 렌더링 태스크를 만듭니다. 이 예제에서는 태스크 설정을 JSON 파일에 지정합니다. 현재 셸 내에서 *myrendertask.json* 파일 이름을 만들고 다음 내용을 복사하여 붙여넣습니다. 모든 텍스트가 올바르게 복사되었는지 확인합니다. (이 파일은 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask.json)에서 다운로드할 수 있습니다.)

이 태스크는 *MotionBlur-DragonFlying.max* 장면의 단일 프레임을 렌더링하는 3ds Max 명령을 지정합니다.

저장소 계정 이름과 SAS 토큰이 포함되도록 JSON 파일의 `blobSource` 및 `containerURL` 요소를 수정합니다. 

> [!TIP]
> `containerURL`은 SAS 토큰으로 끝나며 다음과 비슷합니다.
> 
> ```
> https://mystorageaccount.blob.core.windows.net/job-myrenderjob/$TaskOutput?se=2018-11-15&sp=rw&sv=2017-04-17&ss=b&srt=co&sig=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
> ```

```json
{
  "id": "myrendertask",
  "commandLine": "cmd /c \"%3DSMAX_2018%3dsmaxcmdio.exe -secure off -v:5 -rfw:0 -start:1 -end:1 -outputName:\"dragon.jpg\" -w 400 -h 300 MotionBlur-DragonFlying.max\"",
  "resourceFiles": [
    {
        "blobSource": "https://mystorageaccount.blob.core.windows.net/scenefiles/MotionBlur-DragonFlying.max",
        "filePath": "MotionBlur-DragonFlying.max"
    }
  ],
    "outputFiles": [
        {
            "filePattern": "dragon*.jpg",
            "destination": {
                "container": {
                    "containerUrl": "https://mystorageaccount.blob.core.windows.net/job-myrenderjob/myrendertask/$TaskOutput?Add_Your_SAS_Token_Here"
                }
            },
            "uploadOptions": {
                "uploadCondition": "TaskSuccess"
            }
        }
    ],
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonAdmin"
    }
  }
}
```

다음 명령을 사용하여 태스크를 작업에 추가합니다.

```azurecli-interactive
az batch task create \
    --job-id myrenderjob \
    --json-file myrendertask.json
```

Batch는 태스크를 예약하고, 풀의 노드를 사용할 수 있는 즉시 이 태스크를 실행합니다.


### <a name="view-task-output"></a>태스크 출력 보기

태스크를 실행하는 데 몇 분이 걸립니다. [az batch task show](/cli/azure/batch/task#az_batch_task_show) 명령을 사용하여 태스크에 대한 세부 정보를 봅니다.

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id myrendertask
```

태스크는 계산 노드에서 *dragon0001.jpg*를 생성하고, 저장소 계정의 *job-myrenderjob* 컨테이너에 업로드합니다. 출력을 보려면 [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) 명령을 사용하여 파일을 저장소에서 로컬 컴퓨터로 다운로드합니다.

```azurecli-interactive
az storage blob download \
    --container-name job-myrenderjob \
    --file dragon.jpg \
    --name dragon0001.jpg

```

컴퓨터에서 *dragon.jpg*를 엽니다. 렌더링된 이미지는 다음과 비슷합니다.

![렌더링된 용 프레임 1](./media/tutorial-rendering-cli/dragon-frame.png) 


## <a name="scale-the-pool"></a>풀 크기 조정

이제 여러 프레임으로 더 큰 렌더링 작업을 준비하도록 풀을 수정합니다. Batch에서는 태스크 요구 사항이 변경될 때 노드를 추가하거나 제거하는 [자동 크기 조정](batch-automatic-scaling.md)을 포함하여 계산 리소스의 크기를 조정하는 다양한 방법을 제공합니다. 이 기본 예제에서는 [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) 명령을 사용하여 풀에서 우선 순위가 낮은 노드의 수를 *6*으로 늘립니다.

```azurecli-interactive
az batch pool resize --pool-id myrenderpool --target-dedicated-nodes 0 --target-low-priority-nodes 6
```

풀 크기를 조정하는 데 몇 분이 걸립니다. 이 프로세스가 진행되는 동안 기존 렌더링 작업에서 실행될 다음 태스크를 설정합니다.

## <a name="render-a-multiframe-scene"></a>다중 프레임 장면 렌더링

단일 프레임 예제와 같이 [az batch task create](/cli/azure/batch/task#az_batch_task_create) 명령을 사용하여 *myrenderjob*이라는 작업의 렌더링 태스크를 만듭니다. 여기서는 태스크 설정을 *myrendertask_multi.json* JSON 파일에 지정합니다. (이 파일은 [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-cli-python-samples/master/batch/render-scene/json/myrendertask_multi.json)에서 다운로드할 수 있습니다.) 6개 태스크 각각은 *MotionBlur-DragonFlying.max* 3ds Max 장면의 프레임을 하나씩 렌더링하는 Arnold 명령줄을 지정합니다.

현재의 *myrendertask_multi.json*이라는 셸에서 파일을 만들고, 다운로드한 파일의 내용을 복사하여 붙여넣습니다. 저장소 계정 이름과 SAS 토큰이 포함되도록 JSON 파일의 `blobSource` 및 `containerURL` 요소를 수정합니다. 6개 태스크 각각에 대한 설정을 변경해야 합니다. 파일을 저장하고, 다음 명령을 실행하여 태스크를 큐에 넣습니다.

```azurecli-interactive
az batch task create --job-id myrenderjob --json-file myrendertask_multi.json
```

### <a name="view-task-output"></a>태스크 출력 보기

태스크를 실행하는 데 몇 분이 걸립니다. [az batch task list](/cli/azure/batch/task#az_batch_task_list) 명령을 사용하여 태스크의 상태를 봅니다. 예: 

```azurecli-interactive
az batch task list \
    --job-id myrenderjob \
    --output table
```

[az batch task show](/cli/azure/batch/task#az_batch_task_show) 명령을 사용하여 개별 태스크에 대한 세부 정보를 봅니다. 예: 

```azurecli-interactive
az batch task show \
    --job-id myrenderjob \
    --task-id mymultitask1
```
 
태스크는 계산 노드에서 *dragon0002.jpg* - *dragon0007.jpg*라는 출력 파일을 생성하고, 저장소 계정의 *job-myrenderjob* 컨테이너에 업로드합니다. 출력을 보려면 [az storage blob download-batch](/cli/azure/storage/blob#az_storage_blob_download_batch) 명령을 사용하여 파일을 로컬 컴퓨터의 폴더로 다운로드합니다. 예: 

```azurecli-interactive
az storage blob download-batch \
    --source job-myrenderjob \
    --destination .
```

컴퓨터에서 파일 중 하나를 엽니다. 렌더링된 프레임 6은 다음과 비슷합니다.

![렌더링된 용 프레임 6](./media/tutorial-rendering-cli/dragon-frame6.png) 


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용하여 리소스 그룹, 배치 계정, 풀 및 관련된 모든 리소스를 제거할 수 있습니다. 다음과 같이 리소스를 삭제합니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행하는 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure 저장소에 장면 업로드
> * 렌더링을 위한 Batch 풀 만들기
> * Arnold를 사용하여 단일 프레임 장면 렌더링
> * 풀 크기 조정 및 다중 프레임 장면 렌더링
> * 렌더링된 출력 다운로드

클라우드 수준 렌더링에 대한 자세한 내용은 Batch Rendering Service 옵션을 참조하세요. 

> [!div class="nextstepaction"]
> [Batch 렌더링 서비스](batch-rendering-service.md)
