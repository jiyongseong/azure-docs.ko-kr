---
title: Azure Backup의 Recovery Services 자격 증명 모음으로 Backup 자격 증명 모음 업그레이드 | Microsoft Docs
description: Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 업그레이드하여 리소스 관리자 VM 백업, 보안 향상, VMware VM 백업, Windows Server 시스템 상태 백업 등의 새로운 기능 얻기
services: backup
documentationcenter: ''
author: trinadhk
manager: vijayts
editor: ''
keyword: backup vault; upgrade vault; recovery services vault
ms.assetid: d037a8bf-49f2-4578-974a-3471d87ca278
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: trinadhk, sogup
ms.openlocfilehash: ffc5f11a324b5ac65c872ca2c033f039c129c5f8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="backup-vault-upgraded-to-recovery-services-vault"></a>Recovery Services 자격 증명 모음으로 업그레이드된 Backup 자격 증명 모음 
이 문서에서는 Recovery Services 자격 증명 모음이 제공하는 것, 기존 Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 업그레이드하는 것과 관련한 질문과 대답, 업그레이드 후 단계에 대한 개요를 제공합니다. Recovery Services 자격 증명 모음은 백업 데이터를 보관하는 Backup 자격 증명 모음에 해당하는 Azure Resource Manager입니다. 일반적으로 데이터는 온-프레미스 또는 Azure에서 VM(가상 머신), 워크로드, 서버 또는 워크스테이션에 대한 구성 정보 또는 데이터의 사본입니다.

## <a name="what-is-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음이란?
Recovery Services 자격 증명 모음은 백업 복사본, 복구 지점 및 백업 정책과 같은 데이터를 보관하는 데 사용되는 Azure의 온라인 저장소 엔터티입니다. Recovery Services 자격 증명 모음을 사용하여 IaaS VM(Linux 또는 Windows) 및 Azure SQL Database와 같은 다양한 Azure 서비스에 대한 백업 데이터를 저장할 수 있습니다. Recovery Services 자격 증명 모음은 System Center DPM, Windows Server, Azure Backup Server 등을 지원합니다. Recovery Services 자격 증명 모음을 사용하면 관리 오버헤드를 최소화하면서 백업 데이터를 쉽게 구성할 수 있습니다.

## <a name="comparing-recovery-services-vaults-and-backup-vaults"></a>Recovery Services 자격 증명 모음 및 Backup 자격 증명 모음 비교
Recovery Services 자격 증명은 Azure의 Azure Resource Manager 모델에 기반한 반면 Backup 자격 증명 모음은 Azure 서비스 관리자 모델에 기반합니다. Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 업그레이드할 때 업그레이드 프로세스 전후의 백업 데이터는 그대로 유지됩니다. Recovery Services 자격 증명 모음은 다음과 같은 Backup 자격 증명 모음에 사용할 수 없는 기능을 제공합니다.

- **백업 데이터 보호 기능 향상**: Recovery Services 자격 증명 모음에서 Azure Backup은 클라우드 백업을 보호하는 보안 기능을 제공합니다. 이러한 보안 기능을 통해 백업을 보호하고 프로덕션 및 백업 서버가 손상된 경우에도 클라우드 백업에서 데이터를 안전하게 복구할 수 있습니다. [자세히 알아보기](backup-azure-security-feature.md)

- **하이브리드 IT 환경을 위한 중심 모니터링**: Recovery Services 자격 증명 모음에서 [Azure IaaS VM](backup-azure-manage-vms.md)뿐만 아니라 중앙 포털에서 [온-프레미스 자산](backup-azure-manage-windows-server.md#manage-backup-items)도 모니터링할 수 있습니다. [자세히 알아보기](http://azure.microsoft.com/blog/alerting-and-monitoring-for-azure-backup)

- **RBAC(역할 기반 Access Control)**: RBAC는 Azure에서 세밀한 액세스 관리 제어를 제공합니다. [Azure는 다양한 기본 제공 역할을 제공](../role-based-access-control/built-in-roles.md)하고 Azure Backup에는 3가지 [복구 지점을 관리하는 기본 제공 역할](backup-rbac-rs-vault.md)이 포함됩니다. Recovery Services 자격 증명 모음은 RBAC와 호환되어 백업을 제한하고 정의된 집합의 사용자 역할에 대한 액세스를 복원합니다. [자세히 알아보기](backup-rbac-rs-vault.md)

- **Azure Virtual Machines의 모든 구성 보호**: Recovery Services 자격 증명 모음은 프리미엄 디스크, Managed Disks 및 암호화된 VM을 비롯한 Resource Manager 기반 VM을 보호합니다. Backup 자격 증명 모음을 Recovery Services 자격 증명 모음으로 업그레이드하면 서비스 관리자 기반 VM을 Resource Manager 기반 VM으로 업그레이드할 수 있습니다. 자격 증명 모음을 업그레이드하는 동안 서비스 관리자 기반 VM 복구 지점을 유지하고 업그레이드된(Resource Manager 사용 가능) VM에 대한 보호를 구성할 수 있습니다. [자세히 알아보기](http://azure.microsoft.com/blog/azure-backup-recovery-services-vault-ga)

- **IaaS VM에 대한 인스턴트 복원**: Recovery Services 자격 증명 모음을 사용하여 전체 VM을 복원하지 않고 IaaS VM의 파일 및 폴더를 복원할 수 있습니다. 그러면 복원 시간이 빨라집니다. IaaS VM에 대한 인스턴트 복원은 Windows 및 Linux VM 모두에서 제공됩니다. [자세히 알아보기](http://azure.microsoft.com/blog/instant-file-recovery-from-azure-linux-vm-backup-using-azure-backup-preview)

> [!NOTE]
> 2.0.9083.0 이전의 MARS 에이전트를 통해 Backup 자격 증명 모음에 등록된 항목이 있는 경우, 모든 Recovery Services 자격 증명 모음의 이점을 누릴 수 있게 [최신 MARS 에이전트]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe) 버전을 다운로드합니다.  
> 

## <a name="managing-your-recovery-services-vaults"></a>Recovery Services 자격 증명 모음 관리
다음 화면에서는 Azure Portal에서 Backup 자격 증명 모음에서 업그레이드된 새 Recovery Services 자격 증명 모음을 표시합니다. 업그레이드된 자격 증명 모음은 이름이 “Default-RecoveryServices-ResourceGroup-geo인 기본 리소스 그룹에 있습니다. 예: 백업 자격 증명 모음이 West US에 있는 경우 이름이 Default-RecoveryServices-ResourceGroup-westus인 기본 RG에 있습니다.
> [!NOTE]
> CPS 표준 고객의 경우 자격 증명 업그레이드 후에 리소스 그룹이 변경되지 않으며 업그레이드 전과 동일하게 유지됩니다.

첫 번째 화면에서는 자격 증명 모음에 대한 주요 엔터티를 표시하는 자격 증명 모음 대시보드를 보여줍니다.
![Backup 자격 증명 모음에서 업그레이드된 Recovery Services 자격 증명 모음의 예](./media/backup-azure-upgrade-backup-to-recovery-services/upgraded-rs-vault-in-dashboard.png)

두 번째 화면에서는 Recovery Services 자격 증명 모음을 사용하기 시작하는 데 사용할 수 있는 도움말 링크를 보여줍니다.

![빠른 시작 블레이드의 도움말 링크](./media/backup-azure-upgrade-backup-to-recovery-services/quick-start-w-help-links.png)

## <a name="post-upgrade-steps"></a>업그레이드 후 단계
Recovery Services 자격 증명 모음은 백업 정책에서 표준 시간대 정보를 지정하도록 지원합니다. 자격 증명 모음이 성공적으로 업그레이드되면 자격 증명 모음 설정 메뉴에서 Backup 정책으로 이동하고 자격 증명 모음에 구성된 각 정책에 대한 표준 시간대 정보를 업데이트합니다. 이 화면에서는 정책을 만든 경우 사용되는 로컬 표준 시간대당 지정된 백업 일정 시간을 표시합니다. 

## <a name="enhanced-security"></a>향상된 보안
Recovery Services 자격 증명 모음에 Backup 자격 증명 모음을 업그레이드할 때 해당 자격 증명 모음에 대한 보안 설정은 자동으로 켜집니다. 보안 설정이 켜지면 백업 삭제 또는 암호 변경과 같은 특정 작업에는 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md) PIN이 필요합니다. 향상된 보안에 대한 자세한 내용은 [하이브리드 백업을 보호하는 보안 기능](backup-azure-security-feature.md) 문서를 참조하세요. 향상된 보안이 사용 설정된 경우 볼트에서 복구 지점 데이터가 삭제되고 최대 14일 후까지 데이터가 유지됩니다. 고객에게 이 보안 데이터의 저장에 대한 비용이 청구됩니다. 보안 이터 보존은 Azure Backup 에이전트, Azure Backup Server 및 System Center Data Protection Manager에 대해 가져온 복구 지점에 적용됩니다. 

## <a name="gather-data-on-your-vault"></a>자격 증명 모음에서 데이터 수집
Recovery Services 자격 증명 모음으로 업그레이드하면 Azure Backup(IaaS VM 및 MARS(Microsoft Azure Recovery Services) 에이전트의 경우)에 대한 보고서를 구성하고 Power BI를 사용하여 보고서에 액세스할 수 있습니다. 데이터 수집에 대한 자세한 내용은 [Azure Backup 보고서 구성](backup-azure-configure-reports.md)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

**업그레이드 계획이 진행 중인 백업에 영향을 주나요?**</br>
번호 진행 중인 백업은 업그레이드 전후에도 중단되지 않고 계속됩니다.

**이 업그레이드로 기존 도구는 어떻게 되나요?**</br>
기존 자동화 또는 도구를 리소스 관리자 배포 모델로 업데이트하여 업그레이드 후에도 계속 작동되도록 해야 합니다. [리소스 관리자 배포 모델](backup-client-automation.md)에 대한 PowerShell cmdlet 참조를 확인하세요.

**업그레이드한 후에 롤백할 수 있나요?**</br>
번호 리소스를 성공적으로 업그레이드한 후에는 롤백이 지원되지 않습니다.

**클래식 자격 증명 모음을 업그레이드한 이후를 볼 수 있나요?**</br>
번호 클래식 자격 증명을 업그레이드한 이후를 보거나 관리할 수 없습니다. 자격 증명 모음에 있는 모든 관리 작업에 새 Azure Portal을 사용할 수만 있습니다.

**내 업그레이드된 자격 증명 모음에서 MARS 에이전트로 보호되는 서버가 표시되지 않는 이유**</br>
자격 증명 모음에서 MARS 에이전트로 보호되는 모든 서버를 보려면 최신 MARS 에이전트를 설치해야 합니다. 최신 에이전트 버전은 [여기]( http://download.microsoft.com/download/F/4/B/F4B06356-150F-4DB0-8AD8-95B4DB4BBF7C/MARSAgentInstaller.exe)에서 다운로드할 수 있습니다.

**업그레이드 후 MARS 에이전트로 보호되는 서버에 대해 Backup 정책을 볼 수 없음**</br>
자격 증명 모음의 백업 정책이 만료되어 업그레이드된 자격 증명 모음과 동기화할 수 없는 경우일 수 있습니다. 업그레이드된 자격 증명 모음에서 정책이 계속 표시되도록 정책을 업데이트합니다.
정책을 업데이트하려면 MARS 에이전트로 이동하고 구성된 백업 정책을 업데이트합니다.

**업그레이드 후 내 Backup 정책을 업데이트할 수 없습니다.**</br>
이 상황은 구형 백업 에이전트를 사용하고 최소 보존 기간을 허용된 최소값보다 적게 선택한 경우에 발생합니다. Recovery Services 자격 증명 모음에 Backup 자격 증명 모음을 업그레이드할 때 해당 자격 증명 모음에 대한 보안 설정은 자동으로 켜집니다. 항상 유효한 수의 복구 지점을 사용할 수 있도록 일부 최소 보존 기간을 보안 기능에 따라 유지 관리해야 합니다. 자세한 내용은 [여기](backup-azure-security-feature.md)를 참조하세요.
또한 Azure Backup의 최신 기능이 제공하는 이점을 사용할 수 있게 Azure Backup 에이전트를 최신 버전으로 업데이트해야 합니다.

**내 에이전트를 업데이트했지만 업그레이드 후 며칠이 지나도 개체가 동기화되는 것 같지 않습니다.**</br>
동일한 컴퓨터를 여러 자격 증명 모음에 등록했는지 확인합니다. MARS 에이전트가 등록된 것과 같은 자격 증명 모음을 찾고 있는지 확인합니다. MARS 에이전트가 등록된 자격 증명 모음을 확인하려면 Windows 레지스트리를 열고 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config에서 값을 확인합니다. MARS 에이전트에 등록된 자격 증명 모음이 여기에 표시됩니다. ServiceResourceName 키가 시스템에 표시되지 않는 경우 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config의 ResourceId 및 MachineId 키 값을 알려 주시면 문제 해결을 도와 드립니다.

**업그레이드 이후에 리소스에 대한 작업 정보를 볼 수 없는 이유는 무엇인가요?**</br>
백업(MARS 에이전트 및 IaaS)에 대한 모니터링은 Recovery Services 자격 증명 모음으로 백업 자격 증명 모음을 업그레이드할 때 얻을 수 있는 새로운 기능입니다. 모니터링 정보는 서비스와 동기화되는 데 최대 12시간이 걸립니다.

**문제를 보고하려면 어떻게 해야 하나요?**</br>
자격 증명 모음 업그레이드가 일부 실패하면 오류에 나열된 OperationId를 적어둡니다. Microsoft 지원은 문제를 해결하기 위해 사전 예방적으로 작동합니다. 지원에 문의하거나 구독 ID, 자격 증명 모음 이름 및 OperationId를 적은 이메일을 rsvaultupgrade@service.microsoft.com으로 보내주세요. 최대한 빨리 문제를 해결하려고 합니다. Microsoft에서 분명히 지시하지 않은 경우 작업을 다시 시도하지 마세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 사용하세요.</br>
[IaaS VM 백업](backup-azure-arm-vms-prepare.md)</br>
[Azure Backup Server 백업](backup-azure-microsoft-azure-backup.md)</br>
[Windows Server 백업](backup-configure-vault.md)
