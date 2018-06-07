---
title: AzCopy를 사용하여 Azure Storage로 온-프레미스 데이터 마이그레이션 | Microsoft Docs
description: AzCopy를 사용하여 Blob, 테이블 및 파일 콘텐츠 간 데이터를 마이그레이션하거나 복사합니다. 로컬 저장소에서 Azure Storage로 데이터를 쉽게 마이그레이션합니다.
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: rogarana
ms.openlocfilehash: 1e7292cf4d647b38a6fe8ceb270ba161e548a537
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션

AzCopy는 간단한 명령을 사용하여 Azure Blob 저장소, Azure Files 및 Azure Table 저장소 간 데이터를 복사하기 위한 명령줄 도구입니다. 명령은 최적의 성능을 위해 설계되었습니다. 파일 시스템과 저장소 계정 간 또는 저장소 계정 간에 데이터를 복사할 수 있습니다.  

두 가지 버전의 AzCopy를 다운로드할 수 있습니다.

* [Linux의 AzCopy](storage-use-azcopy.md)는 .NET Core Framework로 작성됩니다. POSIX 스타일 명령줄 옵션을 제공하여 Linux 플랫폼을 대상으로 합니다. 
* [Windows의 AzCopy](../storage-use-azcopy.md)는 .NET Framework로 작성됩니다. Windows 스타일 명령줄 옵션을 제공합니다. 
 
이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 저장소 계정을 만듭니다. 
> * AzCopy를 사용하여 모든 데이터를 업로드합니다.
> * 테스트를 위해 데이터를 수정합니다.
> * 예약된 작업이나 cron 작업을 만들어 업로드할 새 파일을 식별합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) 또는 [Windows](http://aka.ms/downloadazcopy)에 최신 버전의 AzCopy를 다운로드합니다. 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>보조 지역에서 로컬 저장소로 또는 그 반대로 Blob을 다운로드할 수 있기를 원하는 경우 **복제**를 **읽기-액세스-지역 중복 저장소**로 설정합니다. 이 옵션을 선택하면 [지역 중복 저장소](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) 계정이 생성됩니다. 
>
>

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드됩니다. 컴퓨터의 폴더에서 파일을 구성하는 것처럼 컨테이너를 사용하여 Blob 그룹을 구성할 수 있습니다. 

다음 단계에 따라 컨테이너를 만듭니다.

1. 기본 페이지에서 **저장소 계정** 단추를 선택하고 만든 저장소 계정을 선택합니다.
2. **서비스** 아래에서 **Blob**을 선택한 다음 **컨테이너**를 선택합니다. 

   ![컨테이너 만들기](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
컨테이너 이름은 문자 또는 숫자로 시작해야 합니다. 문자, 숫자 및 하이픈(-) 문자만 포함할 수 있습니다. Blob 및 컨테이너의 이름을 지정하는 방법에 대한 자세한 규칙은 [컨테이너, Blob, 메타데이터 이름 명명 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>폴더에서 Blob 저장소로 모든 파일 업로드

AzCopy를 사용하여 폴더의 모든 파일을 [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) 또는 [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download)의 Blob 저장소에 업로드할 수 있습니다. 폴더의 모든 Blob을 업로드하려면 다음 AzCopy 명령을 입력합니다.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

`<key>` 및 `key`를 사용자의 계정 키로 바꿉니다. Azure Portal에서 저장소 계정의 **설정**에서 **액세스 키**를 선택하여 계정 키를 검색할 수 있습니다. 키를 선택하고 AzCopy 명령에 붙여넣습니다. 지정된 대상 컨테이너가 존재하지 않을 경우 AzCopy는 컨테이너를 만든 후 여기에 파일을 업로드합니다. 원본 경로를 데이터 디렉터리로 업데이트하고 대상 URL의 **myaccount**를 사용자의 저장소 계정 이름으로 바꿉니다.

지정된 디렉터리의 콘텐츠를 Blob 저장소로 재귀적으로 업로드하려면 `--recursive`(Linux) 또는 `/S`(Windows) 옵션을 지정합니다. 이러한 옵션 중 하나로 AzCopy를 실행하면 모든 하위 폴더 및 해당 파일도 업로드됩니다.

## <a name="upload-modified-files-to-blob-storage"></a>Blob 저장소에 수정된 파일 업로드
AzCopy를 사용하여 마지막 수정 시간을 기반으로 [파일을 업로드](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features)할 수 있습니다. 이 작업을 시도하려면 테스트를 위해 원본 디렉터리에서 새 파일을 수정하거나 만듭니다. 업데이트된 파일 또는 새 파일만 업로드하려면 `--exclude-older`(Linux) 또는 `/XO`(Windows) 매개 변수를 AzCopy 명령에 추가합니다.

대상에 없는 원본 리소스만 복사하려는 경우 AzCopy 명령에 `--exclude-older` 및 `--exclude-newer`(Linux) 또는 `/XO` 및 `/XN`(Windows) 매개 변수를 지정합니다. AzCopy는 해당 타임스탬프에 따라 업데이트된 데이터만 업로드합니다.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>예약된 작업 또는 cron 작업 만들기 
AzCopy 명령 스크립트를 실행하는 예약된 작업 또는 cron 작업을 만들 수 있습니다. 스크립트는 새 온-프레미스 데이터를 식별하고 특정 시간 간격에 새 온-프레미스 데이터를 클라우드 저장소에 업로드합니다. 

AzCopy 명령을 텍스트 편집기에 복사합니다. AzCopy 명령의 매개 변수 값을 적절한 값으로 업데이트합니다. 파일을 AzCopy에 대해 `script.sh`(Linux) 또는 `script.bat`(Windows)로 저장합니다. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy는 자세한 정보 표시 `--verbose`(Linux) 또는 `/V`(Windows) 옵션으로 실행됩니다. 출력은 로그 파일로 리디렉션됩니다. 

이 자습서에서 [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx)는 Windows에서 예약된 작업을 만드는 데 사용됩니다. [Crontab](http://crontab.org/) 명령은 Linux에서 cron 작업을 만드는 데 사용됩니다. 
 **Schtasks**를 통해 관리자는 로컬 또는 원격 컴퓨터에서 예약된 작업을 만들고, 삭제하고, 쿼리하고, 변경하고, 실행 및 종료할 수 있습니다. **Cron**을 통해 Linux 및 Unix 사용자는 [cron 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)을 사용하여 지정된 날짜와 시간에 명령 또는 스크립트를 실행할 수 있습니다.


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Linux에서 cron 작업을 만들려면 터미널에 다음 명령을 입력합니다. 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

명령에서 cron 식 `*/5 * * * * `을 지정하면 셸 스크립트 `script.sh`에서 5분마다 실행해야 함을 나타냅니다. 스크립트를 매일, 매월 또는 매년 특정 시간에 실행하도록 예약할 수 있습니다. 작업 실행을 위한 날짜 및 시간 설정에 대해 자세히 알아보려면 [cron 식](https://en.wikipedia.org/wiki/Cron#CRON_expression)을 참조하세요. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Windows에서 예약된 작업을 만들려면 명령 프롬프트 또는 PowerShell에서 다음 명령을 입력합니다.

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

명령은 다음을 사용합니다.
- 분 일정을 지정하는 `/SC` 매개 변수
- 5분의 간격을 지정하는 `/MO` 매개 변수
- 작업 이름을 지정하는 `/TN` 매개 변수
- `script.bat` 파일에 대한 경로를 지정하는 `/TR` 매개 변수 

Windows에서 예약된 작업 만들기에 대해 자세히 알아보려면 [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes)를 참조하세요.

---
 
예약된 작업/cron 작업이 제대로 실행되는지 확인하려면 `myfolder` 디렉터리에 새 파일을 만듭니다. 새 파일이 저장소 계정에 업로드되었는지 확인하도록 5분을 기다립니다. 예약된 작업 또는 cron 작업의 출력 로그를 보려면 로그 디렉터리로 이동합니다. 

온-프레미스 데이터를 Azure Storage로 또는 그 반대로 이동하는 방법에 대해 자세히 알아보려면 [Azure Storage 간 데이터 이동](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.  

## <a name="next-steps"></a>다음 단계
Azure Storage 및 AzCopy에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Storage 소개](../storage-introduction.md)
* [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 
* [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md) 
* [저장소 계정을 만드는](../storage-create-storage-account.md)
* [저장소 탐색기를 사용하여 Blob 관리](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

