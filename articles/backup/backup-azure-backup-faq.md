---
title: Azure Backup FAQ | Microsoft 문서
description: 'Recovery Services 자격 증명 모음, 백업 대상, 작동 방식, 암호화 및 제한 등 Azure Backup 기능과 관련된 일반적인 질문에 대한 대답입니다. '
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: 백업 및 재해 복구; 백업 서비스
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/9/2018
ms.author: markgal;arunak;trinadhk;sogup;
ms.openlocfilehash: d16991d15d76caa496b2923c8d0210b6ccb10a9a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33939201"
---
# <a name="questions-about-the-azure-backup-service"></a>Azure Backup 서비스에 대한 질문
이 문서에서는 Azure Backup 구성 요소에 대한 일반적인 질문과 대답을 제공합니다. 대답 중 일부에는 포괄적인 정보를 포함하는 문서에 대한 링크가 있습니다. **의견**(오른쪽)을 클릭하여 Azure Backup에 대한 질문을 할 수 있습니다. 의견은 이 문서의 하단에 나타납니다. Livefyre 계정은 메모가 필수입니다. 또한 [토론 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)에 Azure Backup 서비스에 대한 질문도 게시할 수 있습니다.

이 문서의 섹션을 신속하게 검색하려면 **이 문서** 아래의 오른쪽에서 링크를 사용합니다.


## <a name="recovery-services-vault"></a>Recovery Services 자격 증명 모음

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>각 Azure 구독에 만들 수 있는 자격 증명 모음의 개수에 제한이 있나요? <br/>
예. 구독당 Azure Backup의 지원되는 지역당 최대 500개의 Recovery Services 자격 증명 모음을 만들 수 있습니다. 추가 자격 증명 모음이 필요한 경우 추가 구독을 만드세요.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>각 자격 증명 모음에 대해 등록할 수 있는 서버/컴퓨터 수에 제한이 있나요? <br/>
자격 증명 모음당 최대 1000대의 Azure 가상 머신을 등록할 수 있습니다. MAB Agent를 사용하면 자격 증명 모음당 최대 50대의 MAB Agent를 등록할 수 있습니다. 또한 자격 증명 모음에 50대의 MAB 서버/DPM 서버를 등록할 수 있습니다.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>내 조직에 하나의 자격 증명 모음이 있는 경우 데이터를 복원할 때 서버 간에 데이터를 어떻게 격리할 수 있나요?<br/>
동일한 자격 증명 모음에 등록된 모든 서버는 *동일한 암호를 사용*하는 다른 서버에서 백업된 데이터를 복구할 수 있습니다. 조직의 다른 서버에서 백업 데이터를 격리하고 싶은 서버가 있는 경우 해당 서버에 대해 지정된 암호를 사용합니다. 예를 들어 인사부 서버가 첫 번째 암호화 암호를 사용하고, 회계 서버가 두 번째, 저장소 서버가 세 번째 암호화 암호를 사용할 수 있습니다.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>구독 간에 내 백업 데이터 또는 자격 증명 모음을 “마이그레이션”할 수 있나요? <br/>
번호 자격 증명 모음은 구독 수준에서 만들어지며 생성된 후에는 다른 구독에 다시 할당할 수 없습니다.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>Recovery Services 자격 증명 모음은 Resource Manager에 기반합니다. Backup 자격 증명 모음도 계속 지원되나요? <br/>
Backup 자격 증명 모음은 Recovery Services 자격 증명 모음으로 변환되었습니다. Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 변환하지 않은 경우 Backup 자격 증명 모음은 Recovery Services 자격 증명 모음으로 변환되었습니다. 

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Recovery Services 자격 증명 모음에 Backup 자격 증명 모음을 마이그레이션할 수 있나요? <br/>
모든 Backup 자격 증명 모음은 Recovery Services 자격 증명 모음으로 변환되었습니다. Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 변환하지 않은 경우 Backup 자격 증명 모음은 Recovery Services 자격 증명 모음으로 변환되었습니다.

## <a name="azure-backup-agent"></a>Azure Backup 에이전트
자세한 질문 목록은 [Azure 파일 폴더 백업 FAQ](backup-azure-file-folder-backup-faq.md)에 있습니다.

## <a name="azure-vm-backup"></a>Azure VM 백업
자세한 질문 목록은 [Azure VM 백업 FAQ](backup-azure-vm-backup-faq.md)에 있습니다.

## <a name="back-up-vmware-servers"></a>VMware 서버 백업

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>VMware vCenter 서버를 Azure에 백업할 수 있나요?

예. VMware vCenter 및 ESXi를 Azure에 백업하는 데 Azure Backup Server를 사용할 수 있습니다. 지원되는 VMware 버전에 대 한 자세한 내용은 [Azure Backup Server 보호 매트릭스](backup-mabs-protection-matrix.md)를 참조하세요. 단계별 지침은 [Azure Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)을 참조하세요.


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Azure Backup Server 및 System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Azure Backup 서버를 사용하여 물리적 서버에 대한 BMR(완전 복구) 백업을 만들 수 있나요? <br/>
예.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>여러 자격 증명 모음에 DPM 서버를 등록할 수 있나요? <br/>
번호 DPM 또는 MABS 서버는 하나의 자격 증명 모음에만 등록할 수 있습니다.

### <a name="which-version-of-system-center-data-protection-manager-is-supported"></a>지원되는 System Center Data Protection Manager의 버전은 무엇인가요?

System Center DPM(Data Protection Manager)용 최신 업데이트 롤업(UR)에 [최신](http://aka.ms/azurebackup_agent) Azure Backup 에이전트를 설치하는 것이 좋습니다. 
- System Center DPM 2012 R2의 경우 [Update Rollup 14](https://support.microsoft.com/help/4043315/update-rollup-14-for-system-center-2012-r2-data-protection-manager)가 최신 업데이트입니다.
- System Center DPM 2016의 경우 [Update Rollup 2](https://support.microsoft.com/en-us/help/3209593)가 최신 업데이트입니다.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-protect-on-premises-applicationvm-workloads-to-azure"></a>내 파일 및 폴더를 보호하기 위해 Azure Backup 에이전트를 설치했습니다. System Center DPM을 설치하여 Azure에 온-프레미스 응용 프로그램/VM 워크로드를 보호할 수 있나요?

예. 단, System Center DPM(Data Protection Manager)과 함께 Azure Backup을 사용하려면 먼저 DPM을 설치한 다음, Azure Backup 에이전트를 설치합니다. Azure Backup 구성 요소를 이 순서대로 설치하면 Azure Backup 에이전트는 DPM과 함께 작동합니다. DPM을 설치하기 전에 Azure Backup 에이전트를 설치하는 것이 권장되거나 지원되지 않습니다.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Azure Stack에서 DPM을 사용하여 앱을 백업할 수 있나요?

번호 Azure Backup을 사용하여 Azure Stack을 보호할 수 있지만 Azure Backup은 DPM을 사용하여 Azure Stack에 있는 앱을 백업하는 기능은 지원하지 않습니다.

## <a name="how-azure-backup-works"></a>Azure Backup 작동 방식
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>백업이 시작된 후 백업 작업을 취소하면 전송된 백업 데이터가 삭제되나요? <br/>
번호 백업 작업이 취소되기 전에 자격 증명 모음으로 전송된 모든 데이터는 자격 증명 모음에 유지됩니다. Azure Backup은 백업하는 동안 백업 데이터에 검사점을 간혹 추가하는 검사점 메커니즘을 사용합니다. 백업 데이터에 검사점이 있기 때문에 다음 백업 프로세스에서는 파일의 무결성을 유효성 검사할 수 있습니다. 다음 백업 작업은 이전에 백업한 데이터에 대해 증분됩니다. 증분 백업은 새 데이터 또는 변경된 데이터만 전송하므로 대역폭의 사용률을 개선합니다.

Azure VM에 대한 백업 작업을 취소하면 모든 전송된 데이터는 무시됩니다. 다음 백업 작업은 마지막으로 성공한 백업 작업에서 증분 데이터를 전송합니다.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>백업 작업을 예약할 수 있는 시간 또는 횟수에 제한이 있나요?<br/>
예. Windows Server 또는 Windows 워크스테이션에서는 하루 최대 3번까지 백업 작업을 실행할 수 있습니다. System Center DPM에서는 하루 2번까지 백업 작업을 실행할 수 있습니다. IaaS VM의 경우 하루에 한 번 백업 작업을 실행할 수 있습니다. Windows Server 또는 Windows 워크스테이션에 대해 예약 정책을 사용하여 일별 또는 주별 일정을 지정합니다. System Center DPM을 사용하여 일별, 주별, 월별, 연도별로 일정을 지정할 수 있습니다.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Recovery Services 자격 증명 모음에 전송된 데이터가 백업한 데이터의 크기보다 작은 이유는 무엇인가요?<br/>
 Azure Backup 에이전트 또는 SCDPM 또는 Azure Backup 서버에서 백업된 모든 데이터를 전송하기 전에 압축하고 암호화합니다. 압축 및 암호화를 적용하면 Recovery Services 자격 증명 모음에 있는 데이터 크기가 30-40% 줄어듭니다.

## <a name="what-can-i-back-up"></a>어떤 것을 백업할 수 있나요?
### <a name="which-operating-systems-does-azure-backup-support-br"></a>Azure Backup에서 지원하는 운영 체제는 무엇인가요? <br/>
Azure Backup은 Azure Backup Server 및 System Center DPM(Data Protection Manager)을 사용하여 보호되는 파일과 폴더 및 워크로드 응용 프로그램의 백업을 위한 다음 목록의 운영 체제를 지원합니다.

| 운영 체제 | 플랫폼 | SKU |
|:--- | --- |:--- |
| Windows 8 및 최신 SP |64비트 |Enterprise, Pro |
| Windows 7 및 최신 SP |64비트 |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 및 최신 SP |64비트 |Enterprise, Pro |
| 윈도우 10 |64비트 |Enterprise, Pro, Home |
| Windows Server 2016 |64비트 |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 및 최신 SP |64비트 |Standard, Datacenter, Foundation |
| Windows Server 2012 및 최신 SP |64비트 |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 및 최신 SP |64비트 |Standard, Workgroup | 
| Windows Storage Server 2012 R2 및 최신 SP |64비트 |Standard, Workgroup |
| Windows Storage Server 2012 및 최신 SP |64비트 |Standard, Workgroup |
| Windows Server 2012 R2 및 최신 SP |64비트 |Essential |
| Windows Server 2008 R2 SP1 |64비트 |Standard, Enterprise, Datacenter, Foundation |

**Azure VM 백업의 경우:**

* **Linux**: Azure Backup은 Core OS Linux를 제외한 [Azure 인증 배포 목록](../virtual-machines/linux/endorsed-distros.md)을 지원합니다.  가상 컴퓨터에서 VM 에이전트를 사용할 수 있고 Python에 대한 지원이 지속하는 한 기타 Bring-Your-Own-Linux 배포도 작동합니다.
* **Windows Server**: Windows Server 2008 R2 이전 버전은 지원되지 않습니다.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>백업하는 각 데이터 원본의 크기에 제한이 있나요? <br/>
Azure Backup은 데이터 원본에 대해 최대 크기를 적용하지만 원본에 대한 제한 사항은 큽니다. 2015년 8월 기준으로, 지원되는 운영 체제의 최대 크기 데이터 원본은 다음과 같습니다.

| S.No | 운영 체제 | 데이터 원본의 최대 크기 |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 이상 |54,400GB |
| 2 |Windows 8 이상 |54,400GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700GB |
| 4 |Windows 7 |1700GB |

다음 표에서는 각 데이터 원본 크기가 어떻게 결정되는지를 설명합니다.

| 데이터 원본 | 세부 정보 |
|:---:|:--- |
| 볼륨 |서버 또는 클라이언트 컴퓨터의 단일 볼륨에서 백업되는 데이터 양 |
| Hyper-V 가상 머신 |백업되는 가상 머신의 모든 VHD 데이터 합계 |
| Microsoft SQL Server 데이터베이스 |백업되는 단일 SQL Database 크기 |
| Microsoft SharePoint |백업되는 SharePoint 팜 내의 콘텐츠 및 구성 데이터베이스 합계 |
| Microsoft Exchange |백업되는 Exchange 서버의 모든 Exchange 데이터베이스 합계 |
| BMR/시스템 상태 |백업되는 컴퓨터의 각 개별 BMR 복사본 또는 시스템 상태 |

Azure IaaS VM 백업의 경우 각 VM은 최대 16개의 데이터 디스크를 가질 수 있으며 각 데이터 디스크는 최대 4095GB일 수 있습니다.

### <a name="is-there-a-limit-on-the-amount-of-data-held-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 저장된 데이터의 양에 제한이 있나요?
Recovery Services 자격 증명 모음에 백업할 수 있는 데이터의 양에는 제한이 없습니다.

## <a name="retention-policy-and-recovery-points"></a>보존 정책 및 복구 지점
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>DPM과 Windows Server/클라이언트(즉, DPM이 없는 Windows Server)에 대한 보존 정책 간에 차이가 있나요?<br/>
아니요, DPM 및 Windows Server/클라이언트 모두 일별, 주별, 월별, 연도별 보존 정책을 포함합니다.

### <a name="can-i-configure-my-retention-policies-selectively--that-is-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>내 보존 정책을 선택적으로 구성할 수 있나요? 즉, 연도별, 월별 정책은 구성하지 않고 주별, 일별 정책을 구성할 수 있나요?<br/>
예, Azure Backup 보존 구조를 사용하여 사용자의 요구 사항에 따라 유연하게 보존 정책을 정의할 수 있습니다.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>오후 6시에 "백업을 예약"하고 다른 시간에 "보존 정책"을 지정할 수 있나요?<br/>
번호 보존 정책은 백업 지점에만 적용할 수 있습니다. 다음 이미지에서는 보존 정책이 오전 12시와 오후 6시에 수행되는 백업에 지정됩니다. <br/>

![Backup 일정 및 보존](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>오랜 시간 동안 백업을 유지하면 오래된 데이터 지점을 복구하는 데 시간이 더 걸리나요? <br/>
아니요 - 가장 오래된 지점이나 최신 지점을 복구하는 시간은 같습니다. 각 복구 지점은 전체 지점처럼 동작합니다.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>각 복구 지점이 전체 지점과 같은 경우 총 청구 가능 백업 저장소에 영향을 주나요?<br/>
일반적인 장기 보존 지점 제품은 백업 데이터를 전체 지점으로 저장합니다. 전체 지점은 저장소를 *비효율적* 으로 사용하지만 복원은 쉽고 빠릅니다. 증분 복사본은 저장소를 *효율적* 으로 사용하지만 데이터 체인을 복원해야 하며 이는 복구 시간에 영향을 줍니다. Azure Backup 저장소 아키텍처는 데이터를 빠르게 복원할 수 있게 최적화하여 저장하고 저장소 비용을 낮춰 두 가지 이점을 모두 제공합니다. 이 데이터 저장소 방법을 사용하면 수신 및 발신 대역폭이 효율적으로 사용됩니다. 데이터를 복구하는 데 필요한 데이터 저장소와 시간의 양이 최소로 유지됩니다. [증분 백업](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/)이 얼마나 효율적인지 자세히 알아보세요.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>만들 수 있는 복구 지점의 수에 제한이 있나요?<br/>
보호된 인스턴스당 최대 9999개의 복구 지점을 만들 수 있습니다. 보호된 인스턴스는 Azure에 데이터를 백업하도록 구성된 컴퓨터, 서버(실제 또는 가상) 또는 워크로드입니다. 자세한 내용은 [Backup 및 보존](./backup-introduction-to-azure-backup.md#backup-and-retention)과 [보호된 인스턴스란 무엇인가요](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?의 설명을 참조하세요.

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Azure에 백업된 데이터는 몇 번 복구할 수 있나요?<br/>
Azure Backup에서 수행할 수 있는 복구 횟수에는 제한이 없습니다.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>데이터를 복원할 때 Azure의 송신 트래픽에 대해 요금을 납부하나요? <br/>
번호 복구는 무료이며 송신 트래픽에 요금이 부과되지는 않습니다.

### <a name="what-happens-when-i-change-my-backup-policy"></a>백업 정책을 변경하면 어떻게 되나요?
새 정책을 적용하면 새 정책의 일정 및 보존을 따릅니다. 보존 기간을 늘리면 기존 복구 지점이 새 정책에 따라 유지되도록 표시됩니다. 보존 기간을 줄이면 다음 정리 작업에서 정리(prune) 표시되고 결과적으로 삭제됩니다.

## <a name="azure-backup-encryption"></a>Azure Backup 암호화
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Azure에 전송되는 데이터는 암호화되나요? <br/>
예. 데이터는 온-프레미스 서버/클라이언트/SCDPM 컴퓨터에서 AES256으로 암호화되어 보안 HTTPS 링크를 통해 전송됩니다.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Azure의 백업 데이터도 암호화되나요?<br/>
예. Azure에 전송되는 데이터는 암호화 상태(휴지 상태)를 유지합니다. Microsoft는 어떠한 경우에도 백업 데이터를 암호 해독하지 않습니다. Azure VM을 백업할 때 Azure Backup은 가상 머신의 암호화가 필요합니다. 예를 들어, Azure Disk Encryption 또는 다른 암호화 기술을 사용하여 VM을 암호화하는 경우 Azure Backup은 해당 암호화를 사용하여 데이터를 보호합니다.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>백업 데이터를 암호화하는 데 사용되는 암호화 키의 최소 길이는 어느 정도인가요? <br/>
Azure 백업 에이전트를 사용하는 경우 암호화 키는 16자 이상이어야 합니다. Azure VM의 경우 Azure Key Vault에서 사용하는 키의 길이는 제한되지 않습니다. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>암호화 키를 잃어버리면 어떻게 되나요? 제가 데이터를 복구할 수 있나요? 아니면 Microsoft가 데이터를 복구할 수 있나요? <br/>
백업 데이터를 암호화하는 데 사용되는 키는 고객 사업장에만 존재합니다. Microsoft는 Azure에 복사본을 유지하지 않으며 키에 대한 어떠한 액세스 권한도 없습니다. 고객이 키를 잃어버릴 경우 Microsoft는 백업 데이터를 복구할 수 없습니다.
