---
title: "Azure PowerShell 스크립트 샘플 - Windows에서 AzCopy를 사용하여 저장소 계정 간에 Blob 마이그레이션 | Microsoft Docs"
description: "AzCopy를 사용하여 한 Azure Storage 계정의 Blob 내용을 다른 저장소 계정에 복사합니다."
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 970315c5d597d691454f9dea0a76f2c0dc4a40ec
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Windows에서 AzCopy를 사용하여 저장소 계정 간에 Blob 마이그레이션

이 샘플에서는 모든 Blob 개체를 사용자가 제공한 원본 저장소 계정에서 사용자가 제공한 대상 저장소 계정으로 복사합니다. 

이 작업은 저장소 계정의 모든 컨테이너를 나열하는 `Get-AzureStorageContainer` 명령을 사용하여 수행됩니다. 그런 다음 샘플에서 AzCopy 명령을 실행하여 각 컨테이너를 원본 저장소 계정에서 대상 저장소 계정으로 복사합니다. 오류가 발생하면 샘플에서 $retryTimes를 다시 시도 합니다(기본값은 3이며, `-RetryTimes` 매개 변수로 수정할 수 있음). 다시 시도할 때마다 실패하는 경우 사용자는 `-LastSuccessContainerName` 매개 변수를 사용하여 마지막으로 성공적으로 복사된 컨테이너를 샘플에 제공함으로써 스크립트를 다시 실행할 수 있습니다. 그런 다음 샘플은 해당 지점에서 컨테이너를 계속 복사합니다.

이 샘플에는 Azure PowerShell 저장소 모듈 버전 **4.0.2** 이상이 필요합니다. `Get-Module -ListAvailable Azure.storage`를 사용하여 설치된 버전을 확인할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

또한 이 샘플에는 [Windows에서 최신 버전의 AzCopy](http://aka.ms/downloadazcopy)가 필요합니다. 기본 설치 디렉터리는 `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`입니다.

이 샘플에서는 원본 저장소 계정 이름과 키, 대상 저장소 계정 이름과 키 및 AzCopy.exe의 전체 파일 경로(기본 디렉터리에 설치되지 않은 경우)를 사용합니다.

이 샘플에 대한 입력 예제는 다음과 같습니다.

AzCopy가 기본 디렉터리에 설치된 경우:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

AzCopy가 기본 디렉터리에 설치되지 않은 경우:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

오류가 발생하고 특정 컨테이너에서 샘플을 다시 실행해야 하는 경우: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 한 저장소 계정에서 다른 저장소 계정으로 데이터를 복사합니다. 표에 있는 각 항목은 명령 관련 설명서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | 이 Storage 계정에 연결된 컨테이너를 반환합니다. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Azure Storage 컨텍스트를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 저장소 PowerShell 스크립트 샘플은 [Azure Blob Storage에 대한 PowerShell 샘플](../blobs/storage-samples-blobs-powershell.md)에서 찾을 수 있습니다.
