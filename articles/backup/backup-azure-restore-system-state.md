---
title: 'Azure Backup: Windows Server에 시스템 상태 복원 | Microsoft Docs'
description: Azure의 백업에서 Windows Server 시스템 상태를 복원하기 위한 단계별 설명입니다.
services: backup
documentationcenter: ''
author: saurabhsensharma
manager: shivamg
editor: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/18/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: c673cca6a35cfdc0edaecdc69a797f48772d847c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="restore-system-state-to-windows-server"></a>Windows Server에 시스템 상태 복원

이 문서에서는 Azure Recovery Services 자격 증명 모음에서 Windows Server 시스템 상태 백업을 복원하는 방법에 대해 설명합니다. 시스템 상태를 복원하려면 시스템 상태 백업([시스템 상태 백업](backup-azure-system-state.md#back-up-windows-server-system-state) 지침에 따라 만듦)이 있어야 하고 [최신 버전의 MARS(Microsoft Azure Recovery Services) 에이전트](http://aka.ms/azurebackup_agent)가 설치되어 있는지 확인합니다. Azure Recovery Services 자격 증명 모음에서 Windows Server 시스템 상태 데이터를 복구하는 작업은 두 단계로 이루어집니다.

1. Azure Backup에서 파일로 시스템 상태 복원 Azure Backup에서 파일로 시스템 상태를 복원할 경우 다음을 수행할 수 있습니다.
  * 백업이 수행된 동일한 서버에 시스템 상태 복원 또는
  * 대체 서버에 시스템 상태 파일 복원

2. Windows Server에 복원된 시스템 상태 파일 적용


## <a name="recover-system-state-files-to-the-same-server"></a>동일한 서버에 시스템 상태 파일 복구
다음 단계에서는 Windows Server 구성을 이전 상태로 롤백하는 방법을 설명합니다. 서버 구성을 알려진 안정적인 상태로 다시 롤백하는 작업은 매우 중요할 수 있습니다. 다음 단계는 Recovery Services 자격 증명 모음에서 서버의 시스템 상태를 복원합니다. 

1. **Microsoft Azure Backup** 스냅인을 엽니다. 스냅인이 설치된 위치를 모르는 경우 컴퓨터 또는 서버에서 **Microsoft Azure Backup**을 검색합니다.

    데스크톱 앱이 검색 결과에 나타나야 합니다.

2. 마법사를 시작하려면 **데이터 복구**를 클릭합니다.

    ![데이터 복구](./media/backup-azure-restore-windows-server/recover.png)

3. **시작** 창에서 동일한 서버 또는 컴퓨터로 데이터를 복원하려면 **이 서버(`<server name>`)** 를 선택하고 **다음**을 클릭합니다.

    ![같은 컴퓨터에 데이터를 복원하려면 이 서버 옵션을 선택합니다.](./media/backup-azure-restore-system-state/samemachine.png)

4. **복구 모드 선택** 창에서 **시스템 상태**를 선택하고 **다음**을 선택합니다.

    ![파일 찾아보기](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. **볼륨 및 날짜 선택** 창의 일정에서 복구 지점을 선택합니다. 

    어떤 복구 시점에서라도 복원할 수 있습니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 날짜를 선택하고 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다.

    ![볼륨 및 날짜](./media/backup-azure-restore-system-state/select-date.png)

6. 복원할 복구 지점을 선택했으면 **다음**을 클릭합니다.

    Azure Backup은 로컬 복구 지점을 마운트하고 이를 복구 볼륨으로 사용합니다.

7. 다음 창에서 복구된 시스템 상태 파일에 대상을 지정하고 **찾아보기**를 클릭하여 Windows Explorer를 열고 원하는 파일 및 폴더를 찾습니다. **두 버전 모두 유지하도록 복사본 만들기** 옵션을 통해 전체 시스템 상태 아카이브의 복사본을 만드는 대신 기존의 시스템 상태 파일 아카이브에서 개별 파일의 복사본을 만듭니다.

    ![복구 옵션](./media/backup-azure-restore-system-state/recover-as-files.png)

8. **확인** 창에서 복구의 세부 정보를 확인하고 **복구**를 클릭합니다.

   ![복구를 클릭하여 복구 작업 승인](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. 복구 대상에서 *WindowsImageBackup* 디렉터리를 서버의 중요하지 않은 볼륨에 복사합니다. 일반적으로 Windows OS 볼륨은 중요한 볼륨입니다.

10. 성공적으로 복구되면 [Windows Server에 복원된 시스템 상태 파일 적용](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server) 섹션의 단계에 따라 시스템 상태 복구 프로세스를 완료합니다.

## <a name="recover-system-state-files-to-an-alternate-server"></a>대체 서버에 시스템 상태 파일 복구

Windows Server가 손상되었거나 액세스할 수 없고 Windows Server 시스템 상태를 복구하여 안정적인 상태로 복원하려는 경우 다른 서버에서 손상된 서버의 시스템 상태를 복원할 수 있습니다. 별도 서버에서 시스템 상태를 복원하려면 다음 단계를 사용합니다.  

다음 단계에서 사용되는 용어는 다음과 같습니다.

- *원본 컴퓨터* – 처음에 백업이 수행되었고 현재는 사용할 수 없는 컴퓨터입니다.
- *대상 컴퓨터* – 데이터가 복구되는 컴퓨터입니다.
- *샘플 자격 증명 모음* – *원본 컴퓨터* 및 *대상 컴퓨터*가 등록된 Recovery Services 자격 증명 모음입니다. <br/>

> [!NOTE]
> 이전 버전의 운영 체제를 실행 중인 컴퓨터에는 컴퓨터에서 수행된 백업을 복원할 수 없습니다. 예를 들어 Windows Server 2016 컴퓨터에서 수행된 백업은 Windows Server 2012 R2로 복원될 수 없습니다. 그러나 반대는 가능합니다. Windows Server 2016을 복원하려면 Windows Server 2012 R2에서 백업을 사용할 수 있습니다.
>

1. **대상 컴퓨터**에서 *Microsoft Azure Backup* 스냅인을 엽니다.
2. *대상 컴퓨터* 및 *원본 컴퓨터*가 동일한 Recovery Services 자격 증명 모음에 등록됐는지 확인합니다.
3. **데이터 복구** 를 클릭하여 워크플로를 시작합니다.
4. **다른 서버**

    ![다른 서버](./media/backup-azure-restore-system-state/anotherserver.png)

5. *샘플 자격 증명 모음*에 해당하는 자격 증명 모음 파일을 제공합니다. 자격 증명 모음 파일이 유효하지 않거나 만료된 경우 Azure Portal의 *샘플 자격 증명 모음* 에서 새 자격 증명 모음 파일을 다운로드합니다. 자격 증명 모음 파일이 제공되면 자격 증명 모음 파일과 연결된 Recovery Services 자격 증명 모음이 표시됩니다.

6. Backup 서버 선택 창의 표시된 컴퓨터 목록에서 *원본 컴퓨터*를 선택합니다.
7. 복구 모드 선택 창에서 **시스템 상태**를 선택하고 **다음**을 선택합니다. 

    ![검색](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. **볼륨 및 날짜 선택** 창의 일정에서 복구 지점을 선택합니다. 어떤 복구 시점에서라도 복원할 수 있습니다. **굵게** 표시된 날짜는 하나 이상의 복구 지점을 사용 가능함을 나타냅니다. 날짜를 선택하고 여러 복구 지점을 사용할 수 있는 경우 **시간** 드롭다운 메뉴에서 특정 복구 지점을 선택합니다. 

    ![검색 항목](./media/backup-azure-restore-system-state/select-date.png)

9. 복원할 복구 지점을 선택했으면 **다음**을 클릭합니다.

10. **시스템 상태 복구 모드 선택** 창에서 시스템 상태 파일을 복구하려는 대상을 지정한 후 **다음**을 클릭합니다.

    ![암호화](./media/backup-azure-restore-system-state/recover-as-files.png)

    **두 버전 모두 유지하도록 복사본 만들기** 옵션을 통해 전체 시스템 상태 아카이브의 복사본을 만드는 대신 기존의 시스템 상태 파일 아카이브에서 개별 파일의 복사본을 만듭니다.

11. 확인 창에서 복구의 세부 정보를 확인하고 **복구**를 클릭합니다. 

    ![복구 단추를 클릭하여 복구 프로세스 확인](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. *WindowsImageBackup* 디렉터리를 서버의 중요하지 않은 볼륨에 복사합니다(예: d:\). 일반적으로 Windows OS 볼륨은 중요한 볼륨입니다.

13. 복구 프로세스를 완료하려면 다음 섹션을 사용하여 [Windows Server에서 복원된 시스템 상태 파일을 적용](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server)합니다.




## <a name="apply-restored-system-state-on-a-windows-server"></a>Windows Server에서 복원된 시스템 상태 적용

Azure Recovery Services 에이전트를 사용하여 시스템 상태를 파일로 복구했다면 Windows Server Backup 유틸리티를 사용하여 복구된 시스템 상태를 Windows Server에 적용합니다. Windows Server Backup 유틸리티를 서버에 사용할 수 있습니다. 다음 단계에서는 복구된 시스템 상태를 적용하는 방법을 설명합니다.

1. 다음 명령을 사용하여 *디렉터리 서비스 복구 모드*로 서버를 다시 부팅합니다. 관리자 권한 명령 프롬프트에서 다음을 수행합니다.

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. 다시 부팅한 후에 Windows Server Backup 스냅인을 엽니다. 스냅인이 설치된 위치를 모르는 경우 컴퓨터 또는 서버에서 **Windows Server Backup**을 검색합니다.

    데스크톱 앱이 검색 결과에 나타납니다.

3. 스냅인에서 **로컬 Backup**을 선택합니다.

    ![여기에서 복원할 로컬 Backup 선택](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. 로컬 Backup 콘솔의 **작업 창**에서 **복구**를 클릭하여 복구 마법사를 엽니다.

5. **다른 위치에 저장된 백업** 옵션을 선택하고 **다음**을 클릭합니다.

   ![다른 서버에 복구하도록 선택](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. 시스템 상태 백업을 다른 서버로 복구한 경우 위치 형식에 지정할 때 **원격 공유 폴더**를 선택합니다. 시스템 상태가 로컬로 복구된 경우 **로컬 드라이브**를 선택합니다. 

    ![로컬 서버가 아니면 다른 서버로부터 복구 선택](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. *WindowsImageBackup* 디렉터리에 대한 경로를 입력하거나 Azure Recovery Services 에이전트를 사용하여 시스템 상태 파일 복구의 일부로 복구된 이 디렉터리(예: D:\WindowsImageBackup)를 포함하는 로컬 드라이브를 선택하고 **다음**을 클릭합니다.

    ![공유 파일에 대한 경로](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. 복원하려는 시스템 상태 버전을 선택하고 **다음**을 클릭합니다.

9. 복구 유형 선택 창에서 **시스템 상태**를 선택하고 **다음**을 클릭합니다.

10. 시스템 상태 복구 위치에서 **원래 위치**를 선택하고 **다음**을 클릭합니다.

11. 확인 세부 정보를 검토하고, 다시 부팅 설정을 확인하고, **복구**를 클릭하여 복원된 시스템 상태 파일을 적용합니다.

    ![복원된 시스템 상태 파일 시작](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Active Directory 서버에서 시스템 상태 복구에 대한 특별 고려 사항

시스템 상태 백업은 Active Directory 데이터를 포함합니다. 다음 단계를 사용하여 현재 상태에서 이전 상태로 AD DS(Active Directory Domain Service)를 복원합니다.

1. DSRM(디렉터리 서비스 복원 모드)로 도메인 컨트롤러를 다시 시작합니다.
2. Windows Server Backup cmdlet을 사용하여 AD DS를 복구하려면 [여기](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) 있는 단계를 수행합니다.


## <a name="troubleshoot-failed-system-state-restore"></a>실패한 시스템 상태 복원 문제 해결

시스템 상태를 적용하는 이전 프로세스가 성공적으로 완료되지 않으면 Win RE(Windows Recovery Environment)를 사용하여 Windows Server를 복구합니다. 다음 단계에서는 Win RE를 사용하여 복구하는 방법을 설명합니다. 시스템 상태 복원 후에 Windows Server가 정상적으로 부팅되지 않는 경우에만 이 옵션을 사용합니다. 다음 프로세스는 비 시스템 데이터를 지우고 주의를 사용합니다. 

1. Win RE(Windows Recovery Environment)로 Windows Server를 부팅합니다.

2. 사용 가능한 세 가지 옵션 중에서 문제 해결을 선택합니다.

    ![메뉴 열기](./media/backup-azure-restore-system-state/winre-1.png)

3. **고급 옵션** 화면에서 **명령 프롬프트**를 선택하고 서버 관리 사용자 이름 및 암호를 입력합니다.

   ![메뉴 열기](./media/backup-azure-restore-system-state/winre-2.png)

4. 서버 관리 사용자 이름 및 암호를 입력합니다.

    ![메뉴 열기](./media/backup-azure-restore-system-state/winre-3.png)

5. 관리자 모드에서 명령 프롬프트를 여는 경우 시스템 상태 백업 버전을 가져오는 다음 명령을 실행합니다.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![시스템 상태 백업 버전 가져오기](./media/backup-azure-restore-system-state/winre-4.png)

6. 백업에 사용할 수 있는 모든 볼륨을 가져오는 다음 명령을 실행합니다.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![시스템 상태 백업 버전 가져오기](./media/backup-azure-restore-system-state/winre-5.png)

7. 다음 명령은 시스템 상태 Backup이 포함된 모든 볼륨을 복구합니다. 이 단계는 시스템 상태가 포함된 중요한 볼륨만을 복구합니다. 모든 비 시스템 데이터가 지워집니다.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![시스템 상태 백업 버전 가져오기](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>다음 단계
* 파일과 폴더를 복구했으므로 [백업을 관리](backup-azure-manage-windows-server.md)할 수 있습니다.
