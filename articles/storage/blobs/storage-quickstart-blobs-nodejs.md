---
title: Azure 빠른 시작 - Node.js를 사용하여 개체 저장소에 Blob 만들기 | Microsoft Docs
description: 이 빠른 시작에서는 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, Node.js용 저장소 클라이언트 라이브러리를 사용하여 Blob을 Azure Storage에 업로드하고, Blob을 다운로드하고, Blob을 컨테이너에 나열합니다.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 04/09/2018
ms.author: cshoe
ms.openlocfilehash: 07ac5195d9653affdbb0f305ac1697cecd379319
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>빠른 시작: Node.js를 사용하여 Blob 업로드, 다운로드 및 나열

이 빠른 시작에서는 Node.js를 사용하여 Azure Blob 저장소를 통해 컨테이너에 블록 Blob을 업로드, 다운로드 및 나열하는 방법에 대해 알아봅니다.

이 빠른 시작을 완료하려면 [Azure 구독](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)이 필요합니다.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>샘플 응용 프로그램 다운로드

이 빠른 시작의 [샘플 응용 프로그램](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)은 간단한 Node.js 콘솔 응용 프로그램입니다. 시작하려면 다음 명령을 사용하여 리포지토리를 컴퓨터에 복제합니다.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

응용 프로그램을 열려면 *storage-blobs-node-quickstart* 폴더를 찾아 즐겨찾는 코드 편집 환경에서 엽니다.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성

응용 프로그램을 실행하기 전에 저장소 계정에 대한 연결 문자열을 제공해야 합니다. 샘플 리포지토리에는 *.env.example*이라는 파일이 포함되어 있습니다. *.example* 확장명을 제거하여 *.env* 파일이 되도록 이 파일의 이름을 바꿀 수 있습니다. *.env* 파일 내에서 *AZURE_STORAGE_CONNECTION_STRING* 키 뒤에 연결 문자열 값을 추가합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

응용 프로그램 디렉터리에서 *npm install*을 실행하여 응용 프로그램에 필요한 패키지를 설치합니다.

```bash
npm install
```

## <a name="run-the-sample"></a>샘플 실행
종속성이 설치되었으므로 명령을 스크립트에 전달하여 샘플을 실행할 수 있습니다. 예를 들어 Blob 컨테이너를 만들려면 다음 명령을 실행합니다.

```bash
node index.js --command createContainer
```

사용 가능한 명령은 다음과 같습니다.

| 명령 | 설명 |
|---------|---------|
|*createContainer* | *test-container*라는 컨테이너를 만듭니다(컨테이너가 이미 있는 경우에도 성공함). |
|*upload*          | *example.txt* 파일을 *test-container* 컨테이너에 업로드합니다. |
|*download*        | *example* Blob의 내용을 *example.downloaded.txt*로 다운로드합니다. |
|*delete*          | *example* Blob을 삭제합니다. |
|*list*            | *test-container* 컨테이너의 내용을 콘솔에 나열합니다. |


## <a name="understanding-the-sample-code"></a>샘플 코드 이해
이 코드 샘플에서는 몇 가지 모듈을 사용하여 파일 시스템 및 명령줄과 인터페이스로 연결합니다. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

모듈의 용도는 다음과 같습니다. 

- *dotenv*는 *.env*라는 파일에 정의된 환경 변수를 현재 실행 컨텍스트에 로드합니다
- *path*는 Blob 저장소에 업로드할 파일의 절대 파일 경로를 결정하는 데 필요합니다.
- *yargs*는 명령줄 인수에 액세스하기 위한 간단한 인터페이스를 공개합니다
- *azure-storage*는 Node.js용 [Azure Storage SDK](https://docs.microsoft.com/javascript/api/azure-storage) 모듈입니다.

다음으로, 일련의 변수가 초기화됩니다.

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

변수는 다음 값으로 설정됩니다.

- *blobService*는 Azure Blob 서비스의 새 인스턴스로 설정됩니다.
- *containerName*은 컨테이너의 이름으로 설정됩니다.
- *sourceFilePath*는 업로드할 파일의 절대 경로로 설정됩니다.
- *blobName*은 파일 이름을 가져와서 파일 확장명을 제거하여 만들어집니다.

다음 구현에서는 [Azure Storage API](/nodejs/api/azure-storage/blobservice)의 콜백 특성을 간소화하기 위해 각 *blobService* 함수가 *Promise*에 래핑되어 JavaScript의 *async* 함수와 *await* 연산자에 액세스할 수 있습니다. 각 함수에 대한 응답이 성공적으로 반환되면, 프라미스에서 작업과 관련된 메시지와 함께 관련 데이터를 사용하여 확인합니다.

### <a name="create-a-blob-container"></a>Blob 컨테이너 만들기

*createContainer* 함수는 [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists)를 호출하고 Blob에 적절한 액세스 수준을 설정합니다.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

**createContainerIfNotExists**에 대한 두 번째 매개 변수(*options*)는 [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists)에 대한 값을 수락합니다. *publicAccessLevel*에 대한 *blob* 값은 특정 Blob 데이터가 공개적으로 노출되도록 지정합니다. 이 설정은 *컨테이너* 수준 액세스와 달리 컨테이너의 내용을 나열하는 기능을 부여합니다.

**createContainerIfNotExists**를 사용하면 컨테이너가 이미 있을 때 응용 프로그램에서 오류를 반환하지 않고 *createContainer* 명령을 여러 번 실행할 수 있습니다. 프로덕션 환경에서는 응용 프로그램 전체에서 동일한 컨테이너가 사용되므로 **createContainerIfNotExists**만 호출하는 경우가 많습니다. 이러한 경우 포털 또는 Azure CLI를 통해 컨테이너를 미리 만들 수 있습니다.

### <a name="upload-a-blob-to-the-container"></a>컨테이너에 Blob 업로드

*upload* 함수는 [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) 함수를 사용하여 파일을 파일 시스템에서 Blob 저장소에 업로드하고 쓰거나 덮어씁니다. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
샘플 응용 프로그램의 컨텍스트에서 *example.txt*라는 파일이 *test-container*라는 컨테이너의 *example*이라는 Blob에 업로드됩니다. 콘텐츠를 Blob에 업로드하는 데 사용할 수 있는 다른 방법은 [text](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) 및 [streams](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream)를 사용하는 것입니다.

파일이 Blob 저장소에 업로드되었는지 확인하려면 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 계정의 데이터를 확인하면 됩니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

*list* 함수는 [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) 메서드를 호출하여 컨테이너의 Blob 메타데이터 목록을 반환합니다. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

*listBlobsSegmented*를 호출하면 Blob 메타데이터가 [BlobResult](/nodejs/api/azure-storage/blobresult) 인스턴스의 배열로 반환됩니다. 결과는 5,000개의 증분 일괄 처리(세그먼트)로 반환됩니다. 컨테이너에 5,000개보다 많은 Blob이 있는 경우 **continuationToken**에 대한 값이 결과에 포함됩니다. Blob 컨테이너에서 후속 세그먼트를 나열하려면 연속 토큰을 두 번째 인수로 **listBlobSegmented**에 다시 전달할 수 있습니다.

### <a name="download-a-blob-from-the-container"></a>컨테이너에서 Blob 다운로드

*download* 함수는 [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile)을 사용하여 Blob의 내용을 지정된 절대 파일 경로로 다운로드합니다.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
여기에 표시된 구현은 원본 파일 경로를 변경하여 파일 이름에 *.downloaded.txt*를 추가합니다. 실제 상황에서는 다운로드 대상을 선택할 때 파일 이름뿐만 아니라 위치도 변경할 수 있습니다.

### <a name="delete-blobs-in-the-container"></a>컨테이너에서 Blob 삭제

*deleteBlock* 함수(*delete* 콘솔 명령으로 별칭이 지정됨)는 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) 함수를 호출합니다. 이름에서 알 수 있듯이 이 함수는 Blob이 이미 삭제된 오류를 반환하지 않습니다.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>업로드 및 나열

프라미스를 사용하는 이점 중 하나는 명령을 함께 연결할 수 있다는 것입니다. **uploadAndList** 함수는 파일을 업로드한 직후 Blob의 콘텐츠를 나열하는 것이 얼마나 쉬운지를 보여줍니다.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>코드 호출

명령줄에 구현된 함수를 노출하기 위해 각 함수는 개체 리터럴에 맵핑됩니다.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

이제 *_module*을 적절한 위치에 사용하여 각 명령을 명령줄에서 사용할 수 있습니다.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

지정된 명령이 없는 경우 *_module*의 속성이 사용자에 대한 도움말 텍스트로 콘솔에 렌더링됩니다. 

*executeCommand* 함수는 *async* 함수이며, *await* 연산자를 사용하여 지정된 명령을 호출하고 데이터에 대한 모든 메시지를 콘솔에 로그합니다.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

마지막으로, executing(실행 중) 코드는 먼저 *commandExists*를 호출하여 알려진 명령이 스크립트에 전달되었는지 확인합니다. 기존 명령을 선택하면 명령이 실행되고 모든 오류가 콘솔에 로그됩니다.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>리소스 정리
이 문서에서 만든 데이터 또는 계정을 사용하지 않으려는 경우 원하지 않는 요금 청구를 방지하기 위해 삭제하는 것이 좋습니다. Blob 및 컨테이너를 삭제하려면 [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) 및 [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_) 메서드를 사용할 수 있습니다. [포털을 통해](../common/storage-create-storage-account.md) 저장소 계정을 삭제할 수도 있습니다.

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Blob을 사용하여 Node.js 응용 프로그램을 개발하기 위한 리소스

Blob 저장소를 사용하여 Node.js 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

### <a name="binaries-and-source-code"></a>이진 파일 및 소스 코드

- GitHub에서 Azure Storage용 [Node.js 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-node)를 검색 및 설치하세요.

### <a name="client-library-reference-and-samples"></a>클라이언트 라이브러리 참조 및 샘플

- Node.js 클라이언트 라이브러리에 대한 자세한 내용은 [Node.js API 참조](https://docs.microsoft.com/javascript/api/overview/azure/storage)를 참조하세요.
- Node.js 클라이언트 라이브러리를 사용하여 작성된 [Blob 저장소 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Node.js를 사용하여 로컬 디스크와 Azure Blob 저장소 간에 파일을 업로드하는 방법을 보여줍니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 계속해서 Blob Storage 방법을 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage 작업 방법](storage-nodejs-how-to-use-blob-storage.md)

Azure Storage에 대한 Node.js 참조는 [azure-storage package](https://docs.microsoft.com/javascript/api/azure-storage)를 참조하세요.
