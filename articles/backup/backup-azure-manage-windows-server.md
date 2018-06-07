---
title: Azure Recovery Services 자격 증명 모음 및 서버 관리 | Microsoft Docs
description: 이 문서를 사용하여 Recovery Services 자격 증명 모음 및 서버를 관리합니다.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 18ab960c3d86f4154f7cbbaa3dd4909b9b16b697
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Windows 컴퓨터용 Azure 복구 서비스 자격 증명 모음 및 서버 모니터링 및 관리

이 문서에는 Azure Portal 및 Microsoft Azure Backup 에이전트를 통해 사용할 수 있는 백업 모니터링 및 관리 작업의 개요가 포함되어 있습니다. 이 문서에서는 이미 Azure 구독이 있으며 하나 이상의 Recovery Services Vault 를 만들었다고 가정합니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Recovery Services Vault 열기

Recovery Services Vault 대시보드에는 Recovery Services Vault의 세부 정보 또는 특성이 표시됩니다.

1. Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스**를 클릭합니다. 

3. Recovery Services Vault를 열려고 합니다. 대화 상자에서 **Recovery Services** 입력을 시작합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services Vault**를 클릭하여 구독의 Recovery Services Vault 목록을 표시합니다.

     ![Recovery Services Vault 목록 열기 1단계](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

    Recovery Services Vault 목록이 열립니다.

    ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Vault 목록에서 열려는 Recovery Services Vault의 이름을 선택합니다. Recovery Services 자격 증명 모음 대시보드 메뉴가 열립니다.

    ![복구 서비스 자격 증명 모음 대시보드](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Recovery Services Vault를 열었으므로 모니터링 또는 관리 작업을 시도해 보세요.

## <a name="monitor-backup-jobs-and-alerts"></a>백업 작업 및 경고 모니터링

다음 정보가 표시되는 Recovery Services 자격 증명 모음 대시보드에서 작업 및 경고를 모니터링합니다.

* Backup 경고 세부 정보
* 파일 및 폴더, 그리고 클라우드에 보호되는 Azure Virtual Machines
* Azure에서 사용되는 총 저장소
* Backup 작업 상태

![Backup 대시보드 작업](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

이러한 각 타일의 정보를 클릭하면 관련 작업을 관리하는 연결된 메뉴가 열립니다.

대시보드의 맨 위에서:

* 설정 - 백업 작업에 사용할 수 있는 액세스를 제공합니다.
* 백업 - 새 파일 및 폴더(또는 Azure VM)을 Recovery Services 자격 증명 모음에 백업할 수 있습니다.
* 삭제 - 복구 서비스 자격 증명 모음을 더 이상 사용하지 않는 경우 삭제하여 저장소 공간을 확보할 수 있습니다. 삭제는 등록된 모든 보호된 서버가 자격 증명 모음에서 삭제된 후에만 사용할 수 있습니다.

![Backup 대시보드 작업](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Azure 백업 에이전트에 의한 백업 경고
| 경고 수준 | 전송되는 경고 |
| --- | --- |
| 중요 | 백업 실패, 복구 실패, 삭제 지연. 누군가 데이터 삭제에서 보호 중지 |
| Warning | 백업이 완료되었지만 경고가 발생했습니다(손상 문제로 인해 100개 미만의 파일이 백업되지 않았고 1,000,000개 이상의 파일이 성공적으로 백업된 경우). |
| 정보 제공 | 현재 Azure 백업 에이전트에 사용할 수 있는 정보 경고 없음 |

## <a name="manage-backup-alerts"></a>Backup 경고 관리
**백업 경고** 타일을 클릭하여 **백업 경고** 메뉴를 열고 경고를 관리합니다.

![Backup 경고](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

Backup 경고 타일은 다음 사항의 수를 표시합니다.

* 지난 24 시간 동안 확인되지 않은 중요한 경고
* 지난 24 시간 동안 확인되지 않은 경고

링크를 클릭하면 해당 경고(위험 또는 경고)로 필터링된 보기로 **백업 경고** 메뉴가 표시됩니다.

백업 경고 메뉴에서 다음을 수행합니다.

* 경고와 함께 포함할 적절한 정보를 선택합니다.

    ![열 선택](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* 심각도, 상태 및 시작/종료 시간으로 경고를 필터링합니다.

    ![경고 필터링](./media/backup-azure-manage-windows-server/filter-alerts.png)
* 심각도, 빈도 및 받는 사람에 대한 알림을 구성하며 경고를 켜거나 끕니다.

    ![경고 필터링](./media/backup-azure-manage-windows-server/configure-notifications.png)

**경보별**을 **알림** 빈도로 선택한 경우 전자 메일의 그룹화 또는 축소가 발생하지 않습니다. 모든 경고는 하나의 알림(기본 설정)으로 처리되며 해결 전자 메일이 즉시 전송됩니다.

**시간별 요약**을 **알림** 빈도로 선택하면 지난 1시간 동안 해결되지 않은 경고가 생성되었음을 알려주는 전자 메일 하나가 사용자에게 전송됩니다. 한 시간이 끝날 때 확인 메일이 전송됩니다.

다음 심각도 수준의 경고를 보낼 수 있습니다.

* 중요
* Warning
* 정보

작업 세부 정보 메뉴에서 **비활성화** 단추로 경고를 비활성화합니다. 비활성화를 클릭하면 확인 참고 사항을 제공할 수 있습니다.

**열 선택** 단추를 사용하여 경고의 일부로 나타낼 열을 선택합니다.

> [!NOTE]
> **설정** 메뉴에서 **모니터링 및 보고서 > 경고 및 이벤트 > 백업 경고**를 선택한 다음 **필터** 또는 **알림 구성**을 클릭하여 백업 경고를 관리합니다.
>
>

## <a name="manage-backup-items"></a>Backup 항목 관리
온-프레미스 백업 관리는 이제 관리 포털에서 사용할 수 있습니다. 대시보드의 Backup 섹션에서 **Backup 항목** 타일은 자격 증명 모음에 보호된 백업 항목 수를 표시합니다.

Backup 항목 타일에서 **파일-폴더** 타일을 클릭합니다.

![Backup 항목 타일](./media/backup-azure-manage-windows-server/backup-items-tile.png)

백업 항목 메뉴가 열리고 필터가 나열된 각 특정 백업 항목을 볼 수 있는 파일-폴더로 설정됩니다.

![Backup 항목](./media/backup-azure-manage-windows-server/backup-item-list.png)

목록에서 특정 백업 항목을 선택하면 해당 항목에 대한 필수 세부 정보를 볼 수 있습니다.

> [!NOTE]
> **설정** 메뉴에서 **보호된 항목 > 백업 항목**을 선택하고 드롭다운 메뉴에서 **파일-폴더**를 선택하여 파일 및 폴더를 관리합니다.
>
>

![설정에서 항목 Backup](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Backup 작업 관리
온-프레미스(온-프레미스 서버가 Azure에 백업하는 경우)와 Azure Backup 둘 다에 대한 백업 작업을 대시보드에서 볼 수 있습니다.

대시보드의 Backup 섹션에서 Backup 작업 타일에 작업 수를 표시합니다.

* 진행 중
* 지난 24시간 동안 실패한 작업.

백업 작업을 관리하려면 **백업 작업** 타일을 클릭하여 백업 작업 메뉴를 엽니다.

![설정에서 항목 Backup](./media/backup-azure-manage-windows-server/backup-jobs.png)

페이지 맨 위의 **열 선택** 단추로 백업 작업 메뉴에 사용할 수 있는 정보를 수정합니다.

**필터** 단추를 사용하여 파일과 폴더 및 Azure 가상 머신 백업 중에서 선택합니다.

백업 파일 및 폴더가 보이지 않으면 페이지 맨 위의 **필터** 단추를 클릭하고 항목 유형 메뉴에서 **파일 및 폴더**를 선택합니다.

> [!NOTE]
> **설정** 메뉴에서 **모니터링 및 보고서 > 작업 > 백업 작업**을 선택한 다음 드롭다운 메뉴에서 **파일-폴더**를 선택하여 백업 작업을 관리합니다.
>
>

## <a name="monitor-backup-usage"></a>Backup 사용 모니터링
대시보드의 Backup 섹션에서 Backup 사용 타일이 Azure에서 사용한 저장소를 보여줍니다. 다음에 대한 저장소 사용량이 제공됩니다.

* 자격 증명 모음과 연결된 클라우드 LRS 저장소 사용량
* 자격 증명 모음과 연결된 클라우드 GRS 저장소 사용량

## <a name="manage-your-production-servers"></a>프로덕션 서버 관리
프로덕션 서버를 관리하려면 **설정**을 클릭합니다.

관리 아래에서 **Backup 인프라 > 프로덕션 서버**를 클릭합니다.

프로덕션 서버 메뉴에 사용 가능한 프로덕션 서버가 모두 나열됩니다. 목록에서 서버를 클릭하면 서버 세부 정보가 열립니다.

![보호된 항목](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Azure Backup 에이전트 열기
**Microsoft Azure Backup 에이전트**를 엽니다(*Microsoft Azure Backup*에 대한 컴퓨터를 검색하여 찾을 수 있음).

![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/snap-in-search.png)

백업 에이전트 콘솔의 오른쪽에 있는 **작업**에서 다음 관리 작업을 수행할 수 있습니다.

* 서버 등록
* Backup 예약
* 지금 백업
* 속성 변경

![Microsoft Azure Backup 에이전트 콘솔 작업](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> **데이터를 복구**하려면 [Windows 서버 또는 Windows 클라이언트 컴퓨터로 파일 복원](backup-azure-restore-windows-server.md)을 참조하세요.
>
>

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="modify-the-backup-schedule"></a>백업 일정 수정
1. Microsoft Azure Backup 에이전트에서 **Backup 예약**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. **백업 예약 마법사**에서 **백업 항목 또는 시간 변경** 옵션을 선택된 상태로 두고 **다음**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. 항목을 추가하거나 변경하려면 **백업할 항목 선택** 화면에서 **항목 추가**를 클릭합니다.

    마법사의 이 페이지에서 **제외 설정**을 지정할 수도 있습니다. 파일 또는 파일 형식을 제외하려면 [제외 설정](#manage-exclusion-settings)추가 절차를 읽어보세요.
4. 백업할 파일 및 폴더를 선택하고 **확인**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. **백업 일정**을 지정하고 **다음**을 클릭합니다.

    매일(하루에 최대 3회) 또는 매주 백업을 예약할 수 있습니다.

    ![Windows Server Backup에 대한 항목](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > 백업 일정을 지정하는 방법은 [문서](backup-azure-backup-cloud-as-tape.md)에 자세히 설명되어 있습니다.
   >

6. 백업 복사본에 대한 **재방문 주기 정책**을 선택하고 **다음**을 클릭합니다.

    ![Windows Server Backup에 대한 항목](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. **확인** 화면에서 정보를 검토하고 **마침**을 클릭합니다.
8. 마법사가 **백업 일정** 생성을 완료하면 **닫기**를 클릭합니다.

    보호를 수정한 후 **작업** 탭으로 이동해 변경 내용이 백업 작업에 반영되는지 확인하여 백업이 올바르게 트리거되는지 확인할 수 있습니다.

## <a name="enable-network-throttling"></a>네트워크 제한 사용

Azure Backup 에이전트는 데이터 전송 중에 네트워크 대역폭이 사용되는 방식을 제어할 수 있는 제한 탭을 제공합니다. 근무 시간에 데이터를 백업해야 하는데 백업 프로세스가 다른 인터넷 트래픽을 방해하지 말아야 할 때 유용한 기능입니다. 데이터 전송 제한은 백업 및 복원 작업에 적용됩니다.  

제한을 사용하려면

1. **Backup 에이전트**에서 **속성 변경**을 클릭합니다.
2. **제한 탭에서 **백업 작업에 인터넷 대역폭 사용 제한 사용**을 선택합니다.

    ![네트워크 제한](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    제한을 사용하도록 설정했으면 **근무 시간** 및 **휴무 시간** 중에 백업 데이터 전송에 허용되는 대역폭을 지정합니다.

    대역폭 값은 초당 512Kb(Kbps)에서 시작하여 최대 초당 1023Mb(Mbps)까지 증가할 수 있습니다. 또한 **근무 시간**의 시작 및 완료 시간과 근무일로 간주되는 요일을 지정할 수도 있습니다. 지정된 근무 시간 이외의 시간은 휴무 시간으로 간주됩니다.
3. **확인**을 클릭합니다.

## <a name="manage-exclusion-settings"></a>제외 설정 관리
1. **Microsoft Azure Backup 에이전트**를 엽니다(*Microsoft Azure Backup*에 대한 컴퓨터를 검색하여 찾을 수 있음).

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. Microsoft Azure Backup 에이전트에서 **Backup 예약**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. Backup 예약 마법사에서 **백업 항목 또는 시간 변경** 옵션을 선택된 상태로 두고 **다음**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. **제외 설정**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. **제외 추가**를 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. 위치를 선택하고 **확인**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. **파일 형식** 필드에서 파일 확장명을 추가합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    .mp3 확장명 추가

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    다른 확장명을 추가하려면 **제외 추가**를 클릭하고 다른 파일 형식 확장명(.jpeg 확장명 추가)을 입력합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. 모든 확장을 추가했으면 **확인**을 클릭합니다.
9. **확인 페이지**가 나타날 때까지 **다음**을 클릭하여 Backup 예약 마법사를 계속 진행한 후 **마침**을 클릭합니다.

    ![Windows Server Backup 예약](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>질문과 대답
**Q1. 백업 작업 상태가 Azure 백업 에이전트에서 완료된 것으로 표시되는데, 포털에 즉시 반영되지 않는 이유는 무엇인가요?**

A1. 백업 작업 상태가 Azure 백업 에이전트와 Azure 포털에 반영될 때까지 최대 15분이 지연됩니다.

**Q.2 백업 작업이 실패하는 경우, 경고가 발생할 때까지 얼마나 걸리나요?**

A.2 경고는 Azure 백업 실패 후 20분 이내에 발생합니다.

**Q3. 알림이 구성된 경우 메일이 전송되지 않는 경우가 있나요?**

A3. 아래는 경고 노이즈를 줄이기 위해 알림이 전송되지 않는 경우의 사례입니다.

* 알림이 매시간 구성되고 알림이 발생하고 한 시간 이내에 확인되는 경우
* 작업이 취소됩니다.
* 원래 백업 작업이 진행 중이므로 두 번째 백업 작업이 실패했습니다.

## <a name="troubleshooting-monitoring-issues"></a>모니터링 문제 해결
**문제:** 포털에서 Azure Backup 에이전트의 작업과 경고가 표시되지 않습니다.

**문제 해결 단계:** ```OBRecoveryServicesManagementAgent``` 프로세스에서 Azure Backup 서비스 작업 및 경고 데이터를 보냅니다. 때때로 이 프로세스가 멈추거나 종료될 수 있습니다.

1. 프로세스가 실행되고 있지 않은지 확인하려면 **작업 관리자**를 열고 ```OBRecoveryServicesManagementAgent``` 프로세스가 실행 중인지 확인합니다.
2. 프로세스가 실행되고 있지 않으면 **제어판**을 열고 서비스 목록을 검색합니다. **Microsoft Azure Recovery Services 관리 에이전트**를 시작하거나 다시 시작합니다.

    자세한 내용은 다음 위치에 있는 로그를 검색합니다.<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` 예:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>다음 단계
* [Azure에서 Windows Server 또는 Windows 클라이언트 복원](backup-azure-restore-windows-server.md)
* Azure Backup에 대한 자세한 내용은 [Azure Backup 개요](backup-introduction-to-azure-backup.md)
* [Azure Backup 포럼](http://go.microsoft.com/fwlink/p/?LinkId=290933)
