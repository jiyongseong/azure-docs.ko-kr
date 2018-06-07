---
title: "Azure Backup Server v2 자동 설치 | Microsoft Docs"
description: "PowerShell 스크립트를 사용하여 Azure Backup Server v2를 자동으로 설치할 수 있습니다. 이런 종류의 설치를 무인 설치라고도 합니다."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Azure Backup Server v2의 무인 설치 실행

Azure Backup Server v2의 무인 설치를 실행하는 방법을 알아봅니다. 

Azure Backup Server v1을 설치할 경우에는 이러한 단계가 적용되지 않습니다.

## <a name="install-backup-server-v2"></a>Backup Server v2 설치

1. Azure Backup Server v2를 호스트하는 서버에서 텍스트 파일을 만듭니다. 메모장이나 다른 텍스트 편집기에서 파일을 만들 수 있습니다. 파일을 MABSSetup.ini로 저장합니다. 

2. MABSSetup.ini 파일에 다음 코드를 붙여넣습니다. 대괄호(\< \>) 내부의 텍스트를 사용자 환경의 값으로 바꿉니다. 다음 텍스트는 예제입니다.

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. 파일을 저장합니다. 그다음에 설치 서버의 관리자 권한 명령 프롬프트에서 이 명령을 입력합니다.

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

설치에 다음 플래그를 사용할 수 있습니다.</br>
**/f**: .ini 파일 경로</br>
**/l**: 로그 경로</br>
**/i**: 설치 경로</br>
**/x**: 제거 경로</br>

## <a name="next-steps"></a>다음 단계
Backup Server를 설치한 후 서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.

- [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
- [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
- [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)
- [Backup Server에 Modern Backup Storage 추가](backup-mabs-add-storage.md)
