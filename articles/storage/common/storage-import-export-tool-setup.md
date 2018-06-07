---
title: "Azure Import/Export 도구 설정 | Microsoft Docs"
description: "Azure Import/Export 서비스를 위한 드라이브 준비 및 복구 도구 설정 방법을 알아봅니다."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 6b5febd051d0b956c90cb14c260dda1881adac3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="setting-up-the-azure-importexport-tool"></a>Azure Import/Export 도구 설정

Microsoft Azure Import/Export 도구는 Microsoft Azure Import/Export 서비스에서 사용할 수 있는 드라이브 준비 및 복구 도구입니다. 다음 기능을 위해 이 도구를 사용할 수 있습니다.

* 가져오기 작업을 만들기 전에 이 도구를 사용하여 Azure 데이터 센터에 배송하려는 하드 드라이브에 데이터를 복사할 수 있습니다.
* 가져오기 작업이 완료된 후에는 이 도구를 사용하여 손상되거나 누락되거나 다른 Blob과 충돌한 Blob을 복구할 수 있습니다.
* 완료된 내보내기 작업에서 드라이브를 받은 후에는 이 도구를 사용하여 드라이브에서 손상되거나 누락된 파일을 복구할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

가져오기 작업을 위해 **드라이브를 준비**하는 경우 다음과 같은 필수 조건을 충족해야 합니다.

* 활성 Azure 구독이 있어야 합니다.
* 구독에는 가져올 파일을 저장할 수 있는 충분한 공간이 있는 저장소 계정이 있어야 합니다.
* 저장소 계정 액세스 키 중 적어도 하나가 필요합니다.
* Windows 7, Windows Server 2008 R2 또는 최신 Windows 운영 체제가 설치된 컴퓨터("복사 컴퓨터")가 필요합니다.
* .NET Framework 4가 복사 컴퓨터에 설치되어야 합니다.
* 복사 컴퓨터에서 BitLocker를 사용할 수 있어야 합니다.
* 복사 컴퓨터에 연결된 하나 이상의 빈 2.5"/3.5" SATA II/III 또는 SSD 하드 드라이브가 필요합니다.
* 가져오려는 파일은 네트워크 공유 또는 로컬 하드 드라이브에 상관없이 복사 컴퓨터에서 액세스할 수 있어야 합니다.

부분적으로 실패한 **가져오기를 복구**하려는 경우 다음이 필요합니다.

* 복사 로그 파일
* 저장소 계정 키입니다.

부분적으로 실패한 **내보내기를 복구**하려는 경우 다음이 필요합니다.

* 복사 로그 파일
* 매니페스트 파일(선택 사항)
* 저장소 계정 키입니다.

## <a name="installing-the-azure-importexport-tool"></a>Azure Import/Export 도구 설치

첫째, [Azure Import/Export 도구를 다운로드](https://www.microsoft.com/download/details.aspx?id=55280)하고 컴퓨터의 디렉터리로 추출합니다 (예: `c:\WAImportExport`).

Azure Import/Export 도구는 다음 파일로 구성됩니다.

* dataset.csv
* driveset.csv
* hddid.dll
* Microsoft.Data.Services.Client.dll
* Microsoft.WindowsAzure.Storage.dll
* Microsoft.WindowsAzure.Storage.pdb
* Microsoft.WindowsAzure.Storage.xml
* WAImportExport.exe
* WAImportExport.exe.config
* WAImportExport.pdb
* WAImportExportCore.dll
* WAImportExportCore.pdb
* WAImportExportRepair.dll
* WAImportExportRepair.pdb

그 다음 **관리자 모드**에서 명령 프롬프트 창을 열고 압축된 파일이 포함된 디렉터리로 변경합니다.

명령에 대한 도움말을 출력하려면 매개 변수 없이 도구(`WAImportExport.exe`)를 실행합니다.

```
WAImportExport, a client tool for Windows Azure Import/Export Service. Microsoft (c) 2013


Copy directories and/or files with a new copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>]
        [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]
        DataSet:<dataset.csv>

Add more drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>

Abort an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession

Resume an interrupted copy session:
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession

List drives:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListDrives

List copy sessions:
    WAImportExport.exe PrepImport /j:<JournalFile> /ListCopySessions

Repair a Drive:
    WAImportExport.exe RepairImport | RepairExport
        /r:<RepairFile> [/logdir:<LogDirectory>]
        [/d:<TargetDirectories>] [/bk:<BitLockerKey>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        [/CopyLogFile:<DriveCopyLogFile>] [/ManifestFile:<DriveManifestFile>]
        [/PathMapFile:<DrivePathMapFile>]

Preview an Export Job:
    WAImportExport.exe PreviewExport
        [/logdir:<LogDirectory>]
        /sn:<StorageAccountName> /sk:<StorageAccountKey>
        /ExportBlobListFile:<ExportBlobListFile> /DriveSize:<DriveSize>

Parameters:

    /j:<JournalFile>
        - Required. Path to the journal file. A journal file tracks a set of drives and
          records the progress in preparing these drives. The journal file must always
          be specified.
    /logdir:<LogDirectory>
        - Optional. The log directory. Verbose log files as well as some temporary
          files will be written to this directory. If not specified, current directory
          will be used as the log directory. The log directory can be specified only
          once for the same journal file.
    /id:<SessionId>
        - Optional. The session Id is used to identify a copy session. It is used to
          ensure accurate recovery of an interrupted copy session.
    /ResumeSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to resume the session.
    /AbortSession
        - Optional. If the last copy session was terminated abnormally, this parameter
          can be specified to abort the session.
    /sn:<StorageAccountName>
        - Required. Only applicable for RepairImport and RepairExport. The name of
          the storage account.
    /sk:<StorageAccountKey>
        - Required. The key of the storage account.
    /InitialDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of drives to prepare.
    /AdditionalDriveSet:<driveset.csv>
        - Required. A .csv file that contains a list of additional drives to be added.
    /r:<RepairFile>
        - Required. Only applicable for RepairImport and RepairExport.
          Path to the file for tracking repair progress. Each drive must have one
          and only one repair file.
    /d:<TargetDirectories>
        - Required. Only applicable for RepairImport and RepairExport.
          For RepairImport, one or more semicolon-separated directories to repair;
          For RepairExport, one directory to repair, e.g. root directory of the drive.
    /CopyLogFile:<DriveCopyLogFile>
        - Required. Only applicable for RepairImport and RepairExport. Path to the
          drive copy log file (verbose or error).
    /ManifestFile:<DriveManifestFile>
        - Required. Only applicable for RepairExport. Path to the drive manifest file.
    /PathMapFile:<DrivePathMapFile>
        - Optional. Only applicable for RepairImport. Path to the file containing
          mappings of file paths relative to the drive root to locations of actual files
          (tab-delimited). When first specified, it will be populated with file paths
          with empty targets, which means either they are not found in TargetDirectories,
          access denied, with invalid name, or they exist in multiple directories. The
          path map file can be manually edited to include the correct target paths and
          specified again for the tool to resolve the file paths correctly.
    /ExportBlobListFile:<ExportBlobListFile>
        - Required. Path to the XML file containing list of blob paths or blob path
          prefixes for the blobs to be exported. The file format is the same as the
          blob list blob format in the Put Job operation of the Import/Export Service
          REST API.
    /DriveSize:<DriveSize>
        - Required. Size of drives to be used for export. For example, 500GB, 1.5TB.
          Note: 1 GB = 1,000,000,000 bytes
                1 TB = 1,000,000,000,000 bytes
    /DataSet:<dataset.csv>
        - Required. A .csv file that contains a list of directories and/or a list files
          to be copied to target drives.

    /silentmode
        - Optional. If not specified, it will remind you the requirement of drives and
          need your confirmation to continue.

Examples:

    Copy a data set to a drive:
    WAImportExport.exe PrepImport
        /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GEL
        xmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /InitialDriveSet:driveset1.csv
        /DataSet:data.csv

    Copy another dataset to the same drive following the above command:
    WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#2 /DataSet:dataset2.csv

    Preview how many 1.5 TB drives are needed for an export job:
    WAImportExport.exe PreviewExport
        /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7K
        ysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\temp\myexportbloblist.xml
        /DriveSize:1.5TB

    Repair an finished import job:
    WAImportExport.exe RepairImport
        /r:9WM35C2V.rep /d:X:\ /bk:442926-020713-108086-436744-137335-435358-242242-2795
        98 /sn:mytestaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94
        f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\temp\9WM35C2V_error.log
```

## <a name="next-steps"></a>다음 단계

* [가져오기 작업을 위한 하드 드라이브 준비](../storage-import-export-tool-preparing-hard-drives-import.md)
* [내보내기 작업에 대한 드라이브 사용량 미리 보기](../storage-import-export-tool-previewing-drive-usage-export-v1.md)
* [복사 로그 파일을 사용하여 작업 상태 검토](../storage-import-export-tool-reviewing-job-status-v1.md)
* [가져오기 작업 복구](../storage-import-export-tool-repairing-an-import-job-v1.md)
* [내보내기 작업 복구](../storage-import-export-tool-repairing-an-export-job-v1.md)
* [Azure Import/Export 도구 문제 해결](storage-import-export-tool-troubleshooting-v1.md)
