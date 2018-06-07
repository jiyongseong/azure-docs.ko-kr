---
title: "Azure Backup Server를 사용하여 Azure에 대한 SharePoint 팜 백업 | Microsoft Docs"
description: "Azure Backup Server를 사용하여 SharePoint 데이터를 백업 및 복원합니다. 이 문서에서는 원하는 데이터를 Azure에 저장할 수 있도록 SharePoint 팜을 구성하는 정보를 제공합니다. 디스크 또는 Azure에서 보호된 SharePoint 데이터를 복원할 수 있습니다."
services: backup
documentationcenter: 
author: pvrk
manager: shivamg
editor: 
ms.assetid: 34ba87a4-91f1-4054-a4a1-272af1e15496
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: pullabhk
ms.openlocfilehash: 3ed000affd326eb1bd7c99773ec021ad6e03cc3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Azure에 SharePoint 팜 백업
SharePoint 팜은 다른 데이터 원본을 백업하는 것과 같은 방법으로 Microsoft Azure Backup Server(MABS)를 사용하여 Microsoft Azure에 백업합니다. Azure 백업은 일간, 주간, 월간 혹은 연간 백업 지점을 생성하도록 백업 일정에 유연성을 제공하고 다양한 백업 지점에 관한 보존 정책 옵션을 제공합니다. 또한 빠른 복구 시간 목표(RTO)를 위해 로컬 디스크 복사본을 저장하는 기능과 경제적인 장기 보존을 위해 Azure에 사본을 복사하는 기능을 제공합니다.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint가 지원하는 버전 및 관련 보호 시나리오
DPM의 Azure 백업은 다음 시나리오들을 지원합니다.

| 워크로드 | 버전 | SharePoint 배포 | 보호 및 복구 |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint는 물리적 서버 또는 하이퍼-V/VMware 가상 컴퓨터로 배포됨  <br> -------------- <br> SQL AlwaysOn | SharePoint 팜 보호 복구 옵션: 복구 팜, 데이터베이스, 및 파일 또는 디스크 복구 지점의 목록 항목   Azure 복구 지점에서 팜 및 데이터베이스 복구 |

## <a name="before-you-start"></a>시작하기 전에
SharePoint 팜을 Azure에 백업하기 전에 몇 가지 확인이 필요합니다.

### <a name="prerequisites"></a>필수 조건
진행에 앞서, 워크로드를 보호하기 위해 [Azure Backup Server를 설치 및 준비](backup-azure-microsoft-azure-backup.md)해야 합니다.

### <a name="protection-agent"></a>보호 에이전트
보호 에이전트가 SharePoint를 실행하는 서버, SQL Server를 실행하는 서버, SharePoint 팜에 속하는 그 밖의 모든 서버에 설치되어야 합니다. 보호 에이전트를 설정하는 방법 대한 자세한 내용은 [보호 에이전트 설치](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx)를 참조하세요.  유일한 예외는 단일 WFE(웹 프런트엔드) 서버에만 에이전트를 설치하는 것입니다. DPM은 보호를 위한 진입점 용도로만 단일 WFE 서버의 에이전트가 필요합니다.

### <a name="sharepoint-farm"></a>Sharepoint 팜
팜의 모든 항목이 천만 개에 이르기 때문에, MABS 폴더의 위치에는 최소 2GB의 공간이 필요합니다. 이 공간은 카탈로그를 생성하는 데 필요 합니다. MABS가 특정 항목(사이트 모음, 사이트, 목록, 문서 라이브러리, 폴더, 개별 문서 및 목록 항목)을 복구할 때 카탈로그 생성이 각 콘텐츠 데이터베이스에 포함된 URL의 목록을 만듭니다. MABS 관리자 콘솔 안의 **복구** 작업 영역에서 복구 가능한 항목 창의 URL 목록을 볼 수 있습니다.

### <a name="sql-server"></a>SQL Server
MABS는 LocalSystem 계정으로 실행됩니다. SQL Server 데이터베이스를 백업하려면, MABS에 SQL Server를 실행하는 서버의 해당 계정에 대한 sysadmin 권한이 필요합니다. 백업하기 전에 SQL Server를 실행하는 서버에서 NT AUTHORITY\SYSTEM을 *sysadmin*으로 설정합니다.

SharePoint 팜에 SQL Server 별칭으로 구성 된 SQL Server 데이터베이스가 있는 경우, MABS가 보호할 프런트엔드 웹 서버에 SQL Server 클라이언트 구성 요소를 설치합니다.

### <a name="sharepoint-server"></a>SharePoint Server
성능은 SharePoint 팜의 크기와 같은 여러 요인에 따라 달라지지만, 일반 지침에 의하면 MABS 하나가 25TB SharePoint 팜을 보호할 수 있습니다.

### <a name="whats-not-supported"></a>지원 되지않는 사항
* SharePoint 팜을 보호하는 MABS는 검색 인덱스 또는 응용 프로그램 서비스 데이터베이스를 보호하지 않습니다. 이러한 데이터베이스들은 별도로 보호를 구성해야 합니다.
* MABS는 스케일 아웃 파일 서버(SOFS) 공유에 호스트되는 SharePoint SQL Server 데이터베이스의 백업을 제공하지 않습니다.

## <a name="configure-sharepoint-protection"></a>SharePoint 보호 구성
MABS를 사용하여 SharePoint를 보호할 수 있으려면, **ConfigureSharePoint.exe**를 사용하여 SharePoint VSS 기록기 서비스(WSS 기록기 서비스)를 구성해야 합니다.

**ConfigureSharePoint.exe**는 프런트엔드 웹 서버의 [MABS 설치 경로]\bin 폴더에서 찾을 수 있습니다. 이 도구는 SharePoint 팜의 자격 증명을 사용하여 보호 에이전트를 제공합니다. 단일 WFE 서버에서 실행합니다. WFE 서버가 여러 개인 경우, 보호 그룹을 구성할 때 하나만 선택합니다.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>SharePoint VSS 기록기 서비스를 구성하려면
1. WFE 서버의 명령 프롬프트에서 [MABS 설치 위치] \bin\으로 이동
2. ConfigureSharePoint -EnableSharePointProtection을 입력합니다.
3. 팜 관리자 자격 증명을 입력 합니다. 이 계정은 WFE 서버에서 로컬 관리자 그룹의 구성원 이어야 합니다. 팜 관리자가 로컬 관리자가 아닌 경우, WFE 서버에 다음 권한을 부여 합니다.
   * DPM 폴더(%Program Files%\Microsoft Azure Backup\DPM)에 WSS_Admin_WPG 그룹 전체 제어 권한을 부여합니다.
   * DPM 레지스트리 키 (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager)에 WSS_Admin_WPG 그룹 읽기 액세스 권한을 부여 합니다.

> [!NOTE]
> SharePoint 팜 관리자 자격 증명의 변경이 있을 때마다 ConfigureSharePoint.exe를 다시 실행 해야 합니다.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>MABS를 사용하여 SharePoint 팜 백업
이전의 설명에 따라 MABS 및 SharePoint 팜을 구성한 후에는, SharePoint를 MABS로 보호할 수 있습니다.

### <a name="to-protect-a-sharepoint-farm"></a>SharePoint 팜을 보호하려면
1. MABS 관리자 콘솔의 **보호**탭에서 **새로 만들기**를 클릭합니다.
    ![새 보호 탭](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. **새 보호 그룹 만들기** 마법사의 **보호 그룹 종류 선택** 페이지에서, **서버**를 선택하고 **다음**을 클릭합니다.

    ![선택하는 보호 그룹 종류](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. **그룹 구성원 선택** 화면에서, 보호할 SharePoint 서버의 확인란을 선택하고 **다음**을 클릭합니다.

    ![그룹 구성원 선택](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > 보호 에이전트를 설치하면, 마법사에서 서버를 볼 수 있습니다. 또한 MABS에서는 해당 구조를 보여줍니다. ConfigureSharePoint.exe를 실행했기 때문에, MABS가 SharePoint VSS 기록기 서비스 및 해당 SQL Server 데이터베이스와 통신하고 SharePoint 팜 구조, 연결된 콘텐츠 데이터베이스 및 모든 해당 항목을 인식합니다.
   >
   >
4. **데이터 보호 방법 선택** 페이지에서 **보호 그룹**의 이름을 입력하고 선호하는 *보호 방법*을 선택합니다. **다음**을 누릅니다.

    ![데이터 보호 방법 선택](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > 디스크 보호 방법은 짧은 복구 시간 목표를 충족하는 데 유용합니다.
   >
   >
5. **단기 목표 지정** 페이지에서, 원하는 **보존 범위**를 선택하고 백업을 수행할 때를 식별합니다.

    ![단기 목표 지정](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > 이 예제의 경우, 대부분의 복구가 경과된 지 5일이 안된 데이터에 대해 요구되므로, 디스크의 보존 범위를 5일로 선택했고 백업은 프로덕션 시간을 피해 수행되도록 했습니다.
   >
   >
6. 보호 그룹에 할당된 저장소 풀 디스크 공간을 검토하고 **다음**을 클릭합니다.
7. 모든 보호 그룹에 대해 MABS는 복사본을 저장하고 관리하기 위해 디스크 공간을 할당합니다. 이 시점에서, MABS는 선택된 데이터의 복사본을 만들어야 합니다. 복사본을 만들 방법 및 날짜를 선택하고 **다음**을 클릭합니다.

    ![복제본 만들기 방법 선택](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > 네트워크 트래픽에 영향을 주지 않기 위해, 프로덕션 시간을 피해 시간을 선택합니다.
   >
   >
8. MABS는 복사본에서 일관성 확인을 수행하여 데이터 무결성을 보장합니다. 사용할 수 있는 두 가지 옵션이 있습니다. 일관성 확인을 실행하는 일정을 정의하거나 복제본이 일관되지 않을 때마다 DPM이 복제본에 자동으로 일관성 확인을 실행할 수 있습니다. 원하는 옵션을 선택하고 **다음**을 클릭합니다.

    ![일관성 확인](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. **온라인 보호 데이터 지정** 페이지에서 보호할 SharePoint 팜을 선택하고 **다음**을 클릭합니다.

    ![DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. **온라인 백업 일정 지정** 페이지에서 선호하는 일정을 선택하고 **다음**을 클릭합니다.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS는 사용 가능한 최신 디스크 백업 지점에서 Azure에 최대 두 번의 일일 백업을 제공합니다. Azure 백업은 [Azure Backup 네트워크 제한](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling)을 사용하여 사용량이 최고인 시간과 적은 시간의 백업에 사용될 수 있는 WAN 대역폭 양을 제어할 수도 있습니다.
    >
    >
11. 선택한 백업 일정에 따라 **온라인 보존 정책을 지정** 페이지에서 매일, 매주, 매월 및 매년 백업 지점에 대한 보존 정책을 선택합니다.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS는 다른 백업 지점에 대해 다른 보전 정책을 사용하는 조부-아버지-아들 보존 체계를 사용합니다.
    >
    >
12. 디스크와 마찬가지로, Azure에서 초기 참조 지점을 만들어야 합니다. Azure에 대한 초기 백업 복사본을 만드는 옵션을 선택하고 **다음**을 클릭합니다.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. **요약** 페이지에서 선택한 설정을 검토하고 **그룹 만들기**를 클릭합니다. 보호 그룹이 생성된 후에 성공 메시지가 나타납니다.

    ![요약](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>MABS를 사용하여 디스크에서 SharePoint 항목 복원
아래 예제에서는 실수로 삭제한 *SharePoint 복구 항목*을 복구해야 합니다.
![MABS SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. **DPM 관리자 콘솔**을 엽니다. DPM에서 보호되는 모든 SharePoint 팜이 **보호** 탭에 표시 됩니다.

    ![MABS SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. 항목 복구를 시작하려면 **복구** 탭을 선택합니다.

    ![MABS SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. 복구 지점 범위 내에서 와일드카드 기반 검색을 사용하여 *SharePoint 항목을 복구할* SharePoint를 검색합니다.

    ![MABS SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. 검색 결과에서 적절한 복구 지점을 선택하고, 마우스 오른쪽 단추로 클릭하여, **복구**를 선택합니다.
5. 다양한 복구 지점을 살펴보고 복구할 데이터베이스 또는 항목을 선택할 수 있습니다. **날짜 > 복구 시간**을 선택한 다음 올바른 **데이터베이스 > SharePoint 팜 > 복구 지점 > 항목**을 선택합니다.

    ![MABS SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. 항목을 마우스 오른쪽 단추로 클릭하고 **복구**를 선택하여 **복구 마법사**를 엽니다. **다음**을 누릅니다.

    ![복구 선택 사항 확인](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. 수행할 복구 유형을 선택한 후 **다음**을 클릭합니다.

    ![복구 유형](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > 예제에서는 **원본 사이트에 복구** 를 선택하여, 원본 SharePoint 사이트에 항목이 복구됩니다.
   >
   >
8. 사용할 **복구 프로세스** 를 선택합니다.

   * SharePoint 팜이 변경되지 않고 복원 중인 복구 지점과 같은 경우 **복구 팜을 사용하지 않고 복구** 를 선택합니다.
   * SharePoint 팜 복구 지점이 생성 된 이후 변경 된 경우 **복구 팜을 사용 하 여 복구** 를 선택합니다.

     ![복구 프로세스](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. 임시로 데이터베이스를 복구할 스테이징 SQL Server 인스턴스 위치를 제공하고, 항목을 복구하기 위해 SharePoint를 실행하는 서버 및 MABS에 준비 파일 공유를 제공합니다.

    ![스테이징 위치 1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS는 SharePoint 항목을 호스트하는 콘텐츠 데이터베이스를 임시 SQL Server 인스턴스에 연결합니다. 콘텐츠 데이터베이스에서, MABS는 항목을 복구하여 MABS의 준비 파일 위치에 넣습니다. 이제 준비 위치에 있는 복구된 항목을 SharePoint 팜의 준비 위치로 내보내야 합니다.

    ![스테이징 Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. **복구 옵션 지정**을 선택하고, SharePoint 팜에 보안 설정을 적용하거나 복구 지점의 보안 설정을 적용합니다. **다음**을 누릅니다.

    ![복구 옵션](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > 네트워크 대역폭 사용량을 제한 하도록 선택할 수 있습니다. 따라서 프로덕션 시간 동안 프로덕션 서버에 미치는 영향이 최소화 됩니다.
    >
    >
11. 요약 정보를 검토하고 **복구** 를 클릭하여 파일 복구를 시작합니다.

    ![복구 요약](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. 이제 **MABS 관리자 콘솔**의 **모니터링** 탭을 선택하여 복구의 **상태**를 봅니다.

    ![복구 상태](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > 이제 파일이 복원됩니다. 복원된 파일을 확인하려면 SharePoint 사이트를 새로 고침하세요.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>DPM을 사용하여 Azure에서 SharePoint 데이터베이스 복원
1. SharePoint 콘텐츠 데이터베이스를 복구하려면, 다양한 복구 지점을 살펴보고(이전과 같이), 복원할 복구 지점을 선택합니다.

    ![MABS SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. SharePoint 복구 지점을 두번 클릭하면 사용할 수 있는 SharePoint 카탈로그 정보가 나타납니다.

   > [!NOTE]
   > SharePoint 팜은 Azure에서 장기 보존으로 보호되기 때문에 MABS에서 사용할 수 있는 카탈로그 정보(메타데이터)가 없습니다. 따라서, 지정 시간 SharePoint 콘텐츠 데이터베이스 복구가 필요할 때마다, SharePoint 팜 카탈로그를 다시 만들어야 합니다.
   >
   >
3. **카탈로그 다시 만들기**를 클릭합니다.

    ![MABS SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **클라우드 카탈로그 다시 만들기** 상태 창이 열립니다.

    ![MABS SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    카탈로그 만들기가 완료되면, 상태가 *성공*으로 변경됩니다. **닫기**를 클릭합니다.

    ![MABS SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. MABS **복구** 탭의 SharePoint 개체를 클릭하여 콘텐츠 데이터베이스 구조를 가져옵니다. 항목을 마우스 오른쪽 단추로 클릭한 다음 **복구**를 클릭합니다.

    ![MABS SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 이 지점에서 [이 문서 앞쪽의 복구 단계](#restore-a-sharepoint-item-from-disk-using-dpm) 를 수행하여 디스크에서 SharePoint 콘텐츠 데이터베이스를 복구합니다.

## <a name="faqs"></a>FAQ
Q: SharePoint가 SQL AlwaysOn을 사용하여 구성된 경우(디스크 보호를 사용하여), SharePoint 항목을 원본 위치로 복구할 수 있나요?<br>
A: 예, 항목을 원본 SharePoint 사이트로 복구할 수 있습니다.

Q: SharePoint가 SQL AlwaysOn을 사용하여 구성된 경우 SharePoint 데이터베이스를 원본 위치로 복구할 수 있나요?<br>
A: SharePoint 데이터베이스가 SQL AlwaysOn 에서 구성되었으므로, 가용성 그룹을 제거하지 않으면 수정할 수 없습니다. 따라서 MABS는 원래 위치로 데이터베이스를 복원할 수 없습니다. SQL Server 데이터베이스를 다른 SQL Server 인스턴스로 복구할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* SharePoint의 MABS 보호에 대한 자세한 정보는 [SharePoint의 DPM 보호를 위한 비디오 시리즈](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)를 참조하세요.
