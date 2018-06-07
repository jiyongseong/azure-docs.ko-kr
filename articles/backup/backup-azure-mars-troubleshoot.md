---
title: Azure Backup 에이전트 문제 해결 | Microsoft Docs
description: Azure Backup 에이전트의 설치 및 등록 문제 해결
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shreeshd
editor: ''
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: f7f4ac328c4e35f52bcc9708faf96d06189dd9ac
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="troubleshoot-azure-backup-agent-configuration-and-registration-issues"></a>Azure Backup 에이전트 구성 및 등록 문제 해결
## <a name="recommended-steps"></a>권장되는 단계
Azure Backup 에이전트의 구성 또는 등록 중 발생할 수 있는 오류를 해결하려면 다음 표에서 권장 작업을 참조하세요.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>잘못된 자격 증명 모음이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다.
| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| **오류** </br> *잘못된 보관 자격 증명이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)* | <ul><li> 자격 증명 모음 자격 증명이 유효하지 않습니다. (즉 등록되기 전 다운로드된지 48시간 이상이 지났습니다.)<li>Azure Backup 에이전트는 임시 파일을 Windows Temp 폴더에 다운로드할 수 없습니다. <li>자격 증명 모음 자격 증명이 네트워크 위치에 있습니다. <li>TLS 1.0을 사용하지 않도록 설정되어 있습니다.<li> 구성된 프록시 서버가 연결을 차단하고 있습니다. <br> |  <ul><li>새 자격 증명 모음 자격 증명을 다운로드합니다.<li>**인터넷 옵션** > **보안** > **인터넷**으로 이동합니다. 다음으로 **사용자 지정 수준**을 선택하고 파일 다운로드 섹션이 표시될 때까지 스크롤합니다. 그런 다음 **사용**을 선택합니다.<li>또한 사이트를 [신뢰할 수 있는 사이트](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)에 추가해야 합니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다. <li> 컴퓨터와 날짜 및 시간을 일치시킵니다.<li>C:/Windows/Temp로 이동하여 확장명이 .tmp인 파일이 60,000 또는 65,000개 넘게 있는지 확인합니다. 있는 경우 해당 파일을 삭제합니다.<li>서버에서 SDP 패키지를 실행하여 테스트할 수 있습니다. 파일 다운로드가 허용되지 않는다는 오류가 발생하면 C:/Windows/Temp 디렉터리에 많은 수의 파일이 있을 가능성이 높습니다.<li>.NET Framework 4.6.2가 설치되어 있는지 확인합니다. <li>PCI 준수로 인해 TLS 1.0을 사용하지 않도록 설정한 경우 이 [문제 해결 페이지](https://support.microsoft.com/help/4022913)를 참조하세요. <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외합니다. <ul><li>CBengine.exe<li>.NET Framework와 관련되어 있는 CSC.exe 서버에 설치된 모든 .NET 버전에 대한 CSC.exe 파일이 있습니다. 영향을 받는 서버의 모든 .NET Framework 버전에 연결된 CSC.exe 파일을 제외시킵니다. <li>폴더를 스크래치하거나 위치를 캐시합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| **오류** </br><ol><li>*Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인하고 인터넷에 연결할 수 있는지 확인하세요.*<li>*(407) 프록시 인증 필요* |프록시가 연결을 차단 중 |  <ul><li>**인터넷 옵션** > **보안** > **인터넷**으로 이동합니다. 그런 다음 **사용자 지정 수준**을 선택하고 파일 다운로드 섹션이 표시될 때까지 스크롤합니다. **사용**을 선택합니다.<li>또한 사이트를 [신뢰할 수 있는 사이트](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)에 추가해야 합니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다. <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치된 모든 .NET 버전에 대한 CSC.exe 파일이 있습니다. 영향을 받는 서버의 모든 .NET Framework 버전에 연결된 CSC.exe 파일을 제외시킵니다. <li>폴더를 스크래치하거나 위치를 캐시합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |      
| **오류** </br>*안전한 백업을 위한 암호화 키를 설정하지 못했습니다. 내부 서비스 오류 '잘못된 입력 오류'로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요*. |서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.| 자격 증명 모음에서 서버의 등록을 취소하고 다시 등록합니다.

## <a name="the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다.

| 오류 세부 정보 | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |          
| **오류** </br><ol><li>*정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.* <li>*오류 34506. 이 컴퓨터에 저장된 암호화의 암호가 제대로 구성되어 있지 않습니다*. | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 다른 위치로 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다. | <li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 달하는 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [Azure Backup 에이전트에 대한 질문](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)의 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 위치 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.

## <a name="need-help-contact-support"></a>도움이 필요하세요? 지원에 문의
다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Backup 에이전트로 Windows Server를 백업하는 방법](tutorial-backup-windows-server-to-azure.md)에서 자세한 내용을 참조하세요.
* 백업을 복원해야 하는 경우 이 문서를 참조하여 [Windows 컴퓨터에 파일을 복원](backup-azure-restore-windows-server.md)할 수 있습니다.
