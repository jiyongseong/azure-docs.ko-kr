---
title: Azure 빠른 시작 - Azure CLI를 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 Azure CLI를 사용합니다. 그런 다음, CLI를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: rogarana
ms.openlocfilehash: b55fe97adc56f4b8f1baeb2d8bb5f905fcd424bd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 BLOB 업로드, 다운로드 및 나열

Azure CLI는 Azure 리소스를 관리하는 Azure의 명령줄 환경입니다. 브라우저에서 Azure Cloud Shell과 함께 사용할 수 있습니다. macOS, Linux 또는 Windows에 설치하여 명령줄에서 실행할 수도 있습니다. 빠른 시작에서는 Azure CLI를 사용하여 Azure Blob Storage에서 데이터를 업로드 및 다운로드하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요.

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드됩니다. 폴더에서 컴퓨터의 파일을 구성하는 방식과 비슷하게 Blob 그룹을 구성할 수 있습니다.

[az storage container create](/cli/azure/storage/container#az_storage_container_create) 명령으로 Blob을 저장하기 위한 컨테이너를 만듭니다.

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. Blob Storage에 저장된 대부분의 파일은 블록 Blob으로 저장됩니다. 추가 Blob은 로깅과 같이 기존 내용을 수정하지 않고 기존 Blob에 데이터를 추가해야 할 때 사용됩니다. 페이지 Blob은 IaaS 가상 머신의 VHD 파일을 백업합니다.

먼저 Blob에 업로드할 파일을 만듭니다.
Azure Cloud Shell을 사용하는 경우 `vi helloworld` 파일을 만들려면 해당 파일이 열릴 때 **삽입**을 누르고 "Hello world"를 입력한 다음, **Esc** 키를 누르고 `:x`를 입력하고 **Enter** 키를 누릅니다.

이 예제에서는 [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) 명령을 사용하여 마지막 단계에서 만든 컨테이너에 Blob을 업로드합니다.

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Azure Cloud Shell에서 앞에서 설명한 방법으로 파일을 만든 경우 다음 CLI 명령을 대신 사용할 수 있습니다(일반적으로는 경로를 지정해야 하지만, 파일이 기본 디렉터리에 만들어져 있으므로 경로를 별도로 지정할 필요가 없음).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld
    --file helloworld
```

이 작업은 Blob이 없는 경우 만들고, Blob이 있는 경우 덮어씁니다. 원하는 만큼 파일을 업로드한 후 계속합니다.

동시에 여러 파일을 업로드하려면 [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) 명령을 사용할 수 있습니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[az storage blob list](/cli/azure/storage/blob#az_storage_blob_list) 명령으로 컨테이너에 있는 Blob을 나열합니다.

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Blob 다운로드

[az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) 명령을 사용하여 이전에 업로드한 Blob을 다운로드합니다.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>AzCopy를 사용한 데이터 전송

[AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 유틸리티는 Azure Storage를 위한 또 다른 스크립팅 가능한 고성능 데이터 전송 옵션입니다. AzCopy를 사용하여 Blob, File 및 Table Storage 간에 데이터를 전송할 수 있습니다.

간단한 예제로, 다음은 *myfile.txt* 파일을 *mystoragecontainer* 컨테이너에 업로드하기 위한 AzCopy 명령입니다.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 저장소 계정을 비롯하여 리소스 그룹의 어떠한 리소스도 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete) 명령으로 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬 디스크와 Azure Blob Storage의 컨테이너 간에 파일을 전송하는 방법을 알아보았습니다. Azure Storage에서 Blob을 사용하는 방법을 자세히 알아보려면 계속해서 Azure Blob Storage 사용에 대한 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [방법: Azure CLI를 사용한 Blob Storage 작업](storage-how-to-use-blobs-cli.md)
