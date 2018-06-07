---
title: 'Azure Backup: 가상 머신 백업 준비 | Microsoft Docs'
description: 환경이 Azure의 가상 머신을 백업할 준비가 되었는지 확인합니다.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: 백업; 백업;
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2018
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 489875e595c9f28a1e30cbb29cde078f1b716f7f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940573"
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Resource Manager 배포 가상 머신을 백업하기 위한 환경 준비

이 문서는 Azure Resource Manager 배포 VM(가상 머신)을 백업하기 위한 환경을 준비하는 단계를 제공합니다. 절차에 나와있는 단계는 Azure 포털을 사용합니다. 가상 머신을 백업하는 경우 백업 데이터 또는 복구 지점은 Recovery Services 자격 증명 모음에 저장됩니다. Recovery Services자격 증명 모음은 클래식 배포 및 Resource Manager 배포 가상 머신에 대한 백업 데이터를 저장합니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 배포 모델인 [리소스 관리자와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)모델이 있습니다.

Resource Manager 배포 가상 머신을 보호하거나 백업하기 전에 다음과 같은 필수 구성 요소가 있는지 확인합니다.

* *가상 머신과 동일한 지역에서* Recovery Services 자격 증명 모음을 만들거나 식별합니다.
* 시나리오를 선택하고, 백업 정책을 정의하고, 보호할 항목을 정의합니다.
* 가상 머신에서 VM 에이전트(확장)가 설치되었는지를 확인합니다.
* 네트워크 연결을 확인합니다.
* Linux VM의 경우 응용 프로그램 일치 백업을 위해 백업 환경을 사용자 지정하려면 [사전 스냅숏 및 사후 스냅숏 스크립트를 구성하는 단계](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)를 따르세요.

사용자 환경이 이러한 조건을 이미 갖춘 경우 [VM 백업](backup-azure-arm-vms.md) 문서로 진행합니다. 필수 구성 요소를 하나라도 설정하거나 확인해야 하는 경우 이 문서를 통해 단계를 수행합니다.

## <a name="supported-operating-systems-for-backup"></a>백업에 지원되는 운영 체제
 * **Linux**: Azure Backup은 CoreOS Linux를 제외한 [Azure 인증 배포 목록](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 지원합니다. 
 
    > [!NOTE] 
    > 가상 머신에서 VM 에이전트를 사용할 수 있고 Python에 대한 지원이 있는 한 다른 Bring-Your-Own-Linux 배포가 작동할 수 있습니다. 그러나 이러한 배포는 지원되지 않습니다.
 * **Windows Server**: Windows Server 2008 R2 이전 버전은 지원되지 않습니다.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>VM 백업 및 복원 시의 제한 사항
환경을 준비하기 전에 다음과 같은 제한 사항을 이해해야 합니다.

* 16개 이상의 데이터 디스크가 있는 가상 머신의 백업은 지원되지 않습니다.
* 예약된 IP 주소가 있고 정의된 끝점이 없는 가상 머신의 백업은 지원되지 않습니다.
* LUKS(Linux 통합 키 설치) 암호화를 통해 암호화된 Linux VM을 백업하도록 지원하지 않습니다.
* CSV(클러스터 공유 볼륨) 또는 스케일 아웃 파일 서버 구성을 포함하는 VM을 백업하지 않는 것이 좋습니다. 완료된 경우 CSV 작성기의 실패가 예상됩니다. 스냅숏 작업 중에 클러스터 구성에 포함된 모든 VM이 포함되어야 합니다. Azure Backup은 다중 VM 일관성을 지원하지 않습니다. 
* Backup 데이터는 VM에 연결된 네트워크 탑재된 드라이브를 포함하지 않습니다.
* 복원하는 동안 기존 가상 머신의 교체는 지원되지 않습니다. VM이 존재하는 경우 VM 복원을 시도하면, 복원 작업이 실패합니다.
* 지역 간 백업 및 복원은 지원되지 않습니다.
* 백업을 구성하는 동안 **방화벽 및 가상 네트워크** 저장소 계정 설정에서 모든 네트워크의 액세스를 허용하는지 확인합니다.
* 선택한 네트워크에 대해 저장소 계정에 대한 방화벽 및 가상 네트워크 설정을 구성한 다음, Azure Backup 서비스가 네트워크 제한 저장소 계정에 액세스할 수 있도록 **신뢰할 수 있는 Microsoft 서비스가 이 저장소 계정에 액세스하도록 허용합니다.** 를 예외적으로 선택합니다. 네트워크 제한 저장소 계정에는 항목 수준 복구가 지원되지 않습니다.
* Azure의 모든 공영 지역에 있는 가상 머신을 백업할 수 있습니다. (지원되는 지역의 [검사 목록](https://azure.microsoft.com/regions/#services)을 참조하세요.) 찾는 지역이 현재 지원되지 않는 경우 자격 증명 모음을 만드는 동안 드롭다운 목록에 표시되지 않습니다.
* 다중 DC 구성의 일부인 도메인 컨트롤러(DC) VM 복원은 PowerShell을 통해서만 지원됩니다. 대해 자세히 알아보려면 [다중 DC 도메인 컨트롤러 복원](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)을 참조하세요.
* 다음과 같은 특수 네트워크 구성을 포함하는 가상 머신 복원은 PowerShell 통해서만 지원됩니다. UI에서 복원 워크플로를 통해 만든 VM은 복원 작업이 완료된 후 이러한 네트워크 구성을 갖지 않습니다. 자세한 내용은 [특수 네트워크 구성을 가진 VM 복원](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)을 참조하세요.
  * 부하 분산 장치 구성에서의 가상 머신(내부 및 외부)
  * 다중의 예약된 IP 주소가 있는 가상 머신
  * 다중 네트워크 어댑터가 있는 가상 머신

## <a name="create-a-recovery-services-vault-for-a-vm"></a>VM에 대한 Recovery Services 자격 증명 모음 만들기
Recovery Services 자격 증명 모음은 시간에 따라 생성된 모든 백업과 복구 지점을 저장하는 엔터티입니다. Recovery Services 자격 증명 모음에는 보호된 가상 머신과 연결된 백업 정책도 포함됩니다.

Recovery Services 자격 증명 모음을 만들려면:

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. **허브** 메뉴에서 **찾아보기**를 선택한 다음 **Recovery Services**를 입력합니다. 입력하기 시작하면 입력은 리소스 목록을 필터링합니다. **Recovery Services 자격 증명 모음**을 선택합니다.

    ![상자에 입력 및 결과에서 "Recovery Services 자격 증명 모음" 선택](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Recovery Services 자격 증명 모음의 목록이 표시됩니다.
3. **Recovery Services 자격 증명 모음** 메뉴에서 **추가**를 선택합니다.

    ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Recovery Services 자격 증명 모음** 창이 열립니다. **이름**, **구독**, **리소스 그룹** 및 **위치**에 대한 정보를 제공하라는 메시지가 표시됩니다.

    !["Recovery Services 자격 증명 모음" 창](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. **이름**에 자격 증명 모음을 식별하기 위한 이름을 입력합니다. 이름은 Azure 구독에 대해 고유해야 합니다. 2~50자 문자의 이름을 입력합니다. 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
5. **구독**을 선택하여 사용 가능한 구독 목록을 볼 수 있습니다. 사용할 구독을 잘 모르는 경우 기본(또는 제안된) 구독을 사용합니다. 회사 또는 학교 계정이 여러 Azure 구독과 연결된 경우에만 여러 항목을 선택할 수 있습니다.
6. **리소스 그룹**을 선택하여 사용 가능한 리소스 그룹 목록을 표시하거나 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다. 리소스 그룹에 대한 전체 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.
7. **위치**를 선택하여 자격 증명 모음에 대한 지리적 지역을 선택합니다. 자격 증명 모음은 *반드시* 보호하려는 가상 머신과 동일한 지역에 있어야 합니다.

   > [!IMPORTANT]
   > VM이 있는 위치를 정확히 알지 못하는 경우 자격 증명 모음 만들기 대화 상자를 닫고 포털에서 가상 머신의 목록으로 이동합니다. 가상 머신이 여러 지역에 있으면 각 지역에 Recovery Services 자격 증명 모음을 만들어야 합니다. 첫 번째 위치에서 다음 위치로 이동하기 전에 자격 증명 모음을 만듭니다. 백업 데이터를 저장할 저장소 계정을 지정하지 않아도 됩니다. Recovery Services 자격 증명 모음 및 Azure Backup 서비스는 이를 자동으로 처리합니다.
   >
   >

8. **만들기**를 선택합니다. Recovery Services 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다. 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다. 자격 증명 모음이 생성되면 Recovery Services 자격 증명 모음 목록에 표시됩니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

    ![백업 자격 증명 모음 목록](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

자격 증명 모음을 만들었으므로 저장소 복제를 설정하는 방법에 대해 알아보십시오.

## <a name="set-storage-replication"></a>저장소 복제 설정
저장소 복제 옵션을 사용하면 지역 중복 저장소와 로컬 중복 저장소 중에서 선택할 수 있습니다. 기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다. 옵션 설정이 주 백업에 대한 지역 중복 저장소로 지정된 상태로 둡니다. 오래 지속되지 않는 저렴한 옵션을 원하는 경우 로컬 중복 저장소를 선택합니다.

저장소 복제 설정을 편집하려면

1. **Recovery Services 자격 증명 모음** 창에서 자격 증명 모음을 선택합니다.
    자격 증명 모음을 선택하면 (맨 위에 자격 증명 모음의 이름이 표시된) **설정** 창 및 자격 증명 모음 세부 정보 창이 열립니다.

   ![백업 자격 증명 모음 목록에서 사용자의 자격 증명 모음을 선택합니다.](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. **설정** 창에서 세로 슬라이더를 사용하여 **관리** 섹션으로 아래로 스크롤하고 **백업 인프라**를 선택합니다. **일반** 섹션에서 **백업 구성**을 선택합니다. **백업 구성** 창에서 자격 증명 모음에 대한 저장소 복제 옵션을 선택합니다. 기본적으로 사용자 자격 증명 모음에는 지역 중복 저장소가 있습니다.

   ![백업 자격 증명 모음 목록](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Azure를 기본 백업 저장소 끝점으로 사용하는 경우 계속해서 지역 중복 저장소를 사용합니다. Azure를 주가 아닌 백업 저장소 끝점으로 사용하는 경우 로컬 중복 저장소를 선택합니다. 저장소 옵션에 대한 자세한 내용은 [Azure Storage 복제 개요](../storage/common/storage-redundancy.md)를 참조하세요.

3. 저장소 복제 형식을 변경한 경우 **저장**을 선택합니다.
    
자격 증명 모음에 대한 저장소 옵션을 선택하면 자격 증명 모음이 있는 VM에 연결할 준비가 됩니다. 연결을 시작하려면 Azure 가상 머신을 검색하고 등록해야 합니다.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>백업 목표 선택, 정책 설정, 보호할 항목 정의
Recovery Services 자격 증명 모음에 가상 머신을 등록하기 전에 검색 프로세스를 실행하여 구독에 추가된 새 가상 머신을 식별합니다. 검색 프로세스는 구독의 가상 머신 목록을 Azure에 쿼리합니다. 새 가상 머신이 검색되면 포털에 클라우드 서비스 이름 및 연결된 지역이 표시됩니다. Azure Portal에서 *시나리오*는 Recovery Services 자격 증명 모음에 입력한 것입니다. *정책*은 복구 지점이 발생하는 빈도 및 시기에 대한 일정입니다. 정책에는 복구 지점의 보존 범위(데이터 보존 기간)도 포함됩니다.

1. Recovery Services 자격 증명 모음이 이미 열려 있으면 2단계를 진행합니다. Recovery Services 자격 증명 모음이 열리지 않는 경우 [Azure Portal](https://portal.azure.com/)이 열립니다. **허브** 메뉴에서 **추가 서비스**를 선택합니다.

   a. 리소스 목록에서 **Recovery Services**를 입력합니다. 입력하기 시작하면 입력은 목록을 필터링합니다. **Recovery Services 자격 증명 모음**이 표시되면 이를 선택합니다.

      ![상자에 입력 및 결과에서 "Recovery Services 자격 증명 모음" 선택](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Recovery Services 자격 증명 모음의 목록이 표시됩니다. 구독에 자격 증명 모음이 없으면 이 목록은 비어 있습니다.

      ![Recovery Services 자격 증명 모음 목록 보기](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   나. Recovery Services 자격 증명 모음의 목록에서 자격 증명 모음을 선택합니다.

      선택한 자격 증명 모음의 **설정** 창 및 자격 증명 모음 대시보드가 열립니다.

      ![설정 창 및 자격 증명 모음 대시보드](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. 자격 증명 모음 대시보드 메뉴에서 **백업**을 선택합니다.

   ![백업 단추](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **백업** 및 **백업 목표** 창이 열립니다.

3. **백업 목표** 창에서 **Where is your workload running?**(워크로드 실행 위치)을 **Azure**로 설정하고, **What do you want to backup?**(백업할 항목)을 **가상 머신**으로 설정합니다. 그런 다음 **확인**을 선택합니다.

   ![백업 및 백업 목표 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   이 단계에서는 VM 확장을 자격 증명 모음에 등록합니다. **백업 목표** 창이 닫히고 **백업 정책** 창이 열립니다.

   !["백업" 및 "백업 정책" 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. **백업 정책** 창에서 자격 증명 모음에 적용할 백업 정책을 선택합니다.

   ![백업 정책 선택](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   드롭다운 메뉴 아래에 기본 정책의 상세 정보가 나열됩니다. 새 정책을 만들려는 경우 드롭다운 메뉴에서 **새로 만들기** 를 선택합니다. 백업 정책 정의에 대한 지침은 [백업 정책 정의](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)를 참조하세요.
    **확인**을 선택하여 백업 정책을 자격 증명 모음과 연결합니다.

   **백업 정책** 창이 닫히고 **가상 머신 선택** 창이 열립니다.
5. **가상 머신 선택** 창에서 지정된 정책과 연결할 가상 머신을 선택하고 **확인**을 선택합니다.

   !["가상 머신 선택" 창](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   선택한 가상 머신의 유효성이 검사됩니다. 예상되는 가상 머신이 표시되지 않으면 가상 머신이 Recovery Services 자격 증명 모음과 동일한 Azure 지역에 있는지 확인합니다. 그래도 가상 머신이 표시되지 않으면 다른 자격 증명 모음으로 아직 보호되지 않았는지 확인합니다. 자격 증명 모음 대시보드에는 Recovery Services 자격 증명 모음이 있는 지역이 표시됩니다.

6. 자격 증명 모음의 모든 설정을 정의했으므로 **백업** 창에서 **백업 사용**을 선택합니다. 이 단계에서는 정책을 자격 증명 모음 및 VM에 배포합니다. 이 단계에서는 가상 머신의 초기 복구 지점을 만들지 않습니다.

   !["백업 사용" 단추](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

백업을 성공적으로 활성화하면 백업 정책이 일정에 따라 실행됩니다. 가상 머신을 즉시 백업하는 주문형 백업 작업을 생성하려면 [백업 작업 트리거](./backup-azure-vms-first-look-arm.md#initial-backup)를 참조하세요.

가상 머신을 등록하는 데 문제가 있으면 VM 에이전트 설치 및 네트워크 연결에 대한 다음 정보를 참조하세요. Azure에서 만든 가상 머신을 보호하는 경우 아마도 다음 정보가 필요 없을 것입니다. 그러나 가상 머신을 Azure로 마이그레이션한 경우에는 VM 에이전트가 올바르게 설치되었으며 가상 머신이 가상 네트워크와 통신할 수 있는지 확인해야 합니다.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>가상 머신에 VM 에이전트 설치
사용할 백업 확장의 경우 Azure [VM 에이전트](../virtual-machines/extensions/agent-windows.md)는 Azure 가상 머신에 설치되어야 합니다. Azure Marketplace에서 VM을 만든 경우 VM 에이전트는 이미 가상 머신에 표시됩니다. 

Azure Marketplace에서 만든 VM을 사용하지 *않는* 경우에 다음 정보가 제공됩니다. 예를 들어 온-프레미스 데이터 센터에서 VM을 마이그레이션했습니다. 이런 경우, 가상 머신을 보호하기 위해 VM 에이전트를 설치해야 합니다.

Azure VM을 백업하는 데 문제가 있는 경우 다음 표를 사용하여 Azure VM 에이전트가 가상 머신에 올바르게 설치되었는지 확인합니다. 표에서는 Windows 및 Linux VM용 VM 에이전트에 대한 추가 정보를 제공합니다.

| **작업** | **Windows** | **Linux** |
| --- | --- | --- |
| VM 에이전트 설치 |[에이전트 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)를 다운로드하여 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. |<li> 최신 [Linux 에이전트](../virtual-machines/extensions/agent-linux.md)를 설치합니다. 설치를 완료하려면 관리자 권한이 있어야 합니다. 리포지토리에서 배포 에이전트를 설치하는 것이 좋습니다. GitHub에서 직접 Linux VM 에이전트를 설치하는 것은 **좋지 않습니다**.  |
| VM 에이전트 업데이트 |VM 에이전트 업데이트는 [VM 에이전트 이진](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)을 다시 설치하면 되는 간단한 작업입니다. <br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |[Linux VM 에이전트 업데이트](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 지침을 따르세요. 리포지토리에서 배포 에이전트를 업데이트하는 것이 좋습니다. GitHub에서 직접 Linux VM 에이전트를 업데이트하는 것은 **좋지 않습니다**.<br>VM 에이전트를 업데이트하는 동안 실행 중인 백업 작업이 없도록 합니다. |
| VM 에이전트 설치 유효성 검사 |<li>Azure VM에서 *C:\WindowsAzure\Packages* 폴더로 이동합니다. <li>WaAppAgent.exe 파일을 찾습니다.<li> 파일을 마우스 오른쪽 단추로 클릭하고 **속성**으로 이동한 다음 **세부 정보** 탭을 선택합니다. 제품 버전 필드가 2.6.1198.718 이상이어야 합니다. |해당 없음 |

### <a name="backup-extension"></a>Backup 확장
가상 머신에 VM 에이전트를 설치하면 Azure Backup 서비스는 VM 에이전트에 대한 백업 확장을 설치합니다. 백업 서비스가 백업 확장을 원활하게 업그레이드하고 패치합니다.

백업 서비스는 VM의 실행 여부와 상관 없이 백업 확장을 설치합니다. 실행 중인 VM은 응용 프로그램 일치 복구 지점을 확보할 수 있는 큰 기회를 제공합니다. 그러나 백업 서비스는 VM이 꺼져 확장을 설치할 수 없더라도 VM을 계속 백업합니다. *오프라인 VM*이라고 합니다. 이 경우 복구 지점은 *충돌 일치*가 됩니다.

## <a name="establish-network-connectivity"></a>네트워크 연결 설정
VM 스냅숏을 관리하려면 백업 확장에 Azure 공용 IP 주소에 대한 연결이 필요합니다. 올바른 인터넷 연결이 없으면, 가상 머신의 HTTP 요청 시간이 초과되고 백업 작업이 실패합니다. 배포에 액세스 제한이 있다면(예: NSG(네트워크 보안 그룹)를 통해), 백업 트래픽에 대해 명확한 경로를 제공하기 위해 다음 옵션 중 하나를 선택합니다.

* [Azure 데이터 센터 IP 범위 허용 목록](http://www.microsoft.com/en-us/download/details.aspx?id=41653)
* 트래픽 라우팅을 위해 HTTP 프록시 서버를 배포합니다.

사용할 옵션을 결정할 때 관리 효율성, 세부적인 제어 및 비용 사이에 균형을 유지합니다.

| 옵션 | 장점 | 단점 |
| --- | --- | --- |
| 허용 목록 IP 범위 |추가 비용 없음<br><br>NSG에서 액세스를 여는 경우 **Set-AzureNetworkSecurityRule** cmdlet을 사용합니다. |시간이 지남에 따라 영향을 받는 IP 범위가 변경되기 때문에 관리하기가 복잡합니다.<br><br>저장소뿐만 아니라 Azure 전체에 대한 액세스를 제공합니다. |
| HTTP 프록시 사용 |저장소 URL에 대한 프록시의 세부적인 제어가 허용됩니다.<br><br>VM에 대한 인터넷 액세스의 단일 지점<br><br>Azure IP 주소 변경이 적용되지 않음 |프록시 소프트웨어를 사용하여 VM을 실행하기 위한 추가 비용입니다. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Azure 데이터 센터 IP 범위 허용 목록
Azure 데이터 센터 IP 범위의 허용 목록을 만들려면 [Azure 웹 사이트](http://www.microsoft.com/en-us/download/details.aspx?id=41653)에서 IP 범위에 대한 자세한 내용과 지침을 참조하세요.

[서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하여 특정 지역의 저장소에 대한 연결을 허용할 수 있습니다. 인터넷 액세스를 차단하는 규칙보다 저장소 계정에 대한 액세스를 허용하는 규칙에 높은 우선 순위가 있는지 확인합니다. 

![지역에 대한 저장소 태그가 있는 NSG ](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

다음 비디오에서는 서비스 태그를 구성하는 단계별 절차를 안내합니다. 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> 저장소 서비스 태그는 특정 지역에서만 사용할 수 있으며 미리 보기 상태입니다. 지역 목록은 [저장소의 서비스 태그](../virtual-network/security-overview.md#service-tags)를 참조하세요.

### <a name="use-an-http-proxy-for-vm-backups"></a>VM 백업에 HTTP 프록시 사용
VM을 백업할 때 VM의 백업 확장이 HTTPS API를 사용하여 Azure Storage에 스냅숏 관리 명령을 보냅니다. 공용 인터넷에 액세스하도록 구성된 유일한 구성 요소이기 때문에 HTTP 프록시를 통해 백업 확장 트래픽을 라우팅합니다.

> [!NOTE]
> 특정 프록시 소프트웨어를 사용하도록 권장하지 않습니다. 다음 구성 단계와 호환되는 프록시를 선택하도록 합니다.
>
>

다음 예제 이미지는 HTTP 프록시를 사용하는 데 필요한 세 가지 구성 단계를 보여줍니다.

* 앱 VM은 프록시 VM을 통해 공용 인터넷으로 향하는 모든 HTTP 트래픽을 라우팅합니다.
* 프록시 VM은 가상 네트워크의 VM에서 들어오는 트래픽을 허용합니다.
* NSF-lockdown이라는 네트워크 보안 그룹에는 프록시 VM의 아웃바운드 인터넷 트래픽을 허용하는 보안 규칙이 필요합니다.

공용 인터넷과 통신하는 데 HTTP 프록시를 사용하려면 다음 단계를 완료합니다.

> [!NOTE]
> 이 단계에서는 이 예제에 대해 특정 이름과 값을 사용합니다. 코드에 자세한 내용을 입력(또는 붙여넣기)하는 경우 배포의 이름과 값을 사용합니다.

#### <a name="step-1-configure-outgoing-network-connections"></a>1단계: 나가는 네트워크 연결 구성
###### <a name="for-windows-machines"></a>Windows 컴퓨터의 경우
이 절차는 로컬 시스템 계정에 대한 프록시 서버 구성을 설정합니다.

1. [PsExec](https://technet.microsoft.com/sysinternals/bb897553)을 다운로드합니다.
2. 관리자 권한 프롬프트에서 다음 명령을 실행하여 Internet Explorer를 엽니다.

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. Internet Explorer에서 **도구** > **인터넷 옵션** > **연결** > **LAN 설정**으로 이동합니다.
4. 시스템 계정에 대한 프록시 설정을 확인합니다. 프록시 IP 및 포트를 설정합니다.
5. Internet Explorer를 닫습니다.

다음 스크립트는 시스템 수준의 프록시 구성을 설정하고 나가는 HTTP 또는 HTTPS 트래픽에 사용합니다. 현재 사용자 계정(로컬 시스템 계정이 아닌)으로 프록시 서버를 설정한 경우에는, 이 스크립트를 사용하여 SYSTEMACCOUNT에 적용합니다.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> 프록시 서버 로그에 "(407)프록시 인증 필요"가 발견되면 인증이 제대로 설정되었는지 확인합니다.
>
>

###### <a name="for-linux-machines"></a>Linux 컴퓨터의 경우
다음 줄을 ```/etc/environment``` 파일에 추가합니다.

```
http_proxy=http://<proxy IP>:<proxy port>
```

```/etc/waagent.conf``` 파일에 다음 줄을 추가합니다.

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>2단계: 프록시 서버에서 들어오는 연결 허용
1. 프록시 서버에서 Windows 방화벽을 엽니다. 방화벽에 액세스하는 가장 쉬운 방법은 **고급 보안이 포함된 Windows 방화벽**을 검색하는 것입니다.
2. **고급 보안이 포함된 Windows 방화벽** 대화 상자에서 **인바운드 규칙**을 마우스 오른쪽 단추로 클릭하고 **새 규칙**을 선택합니다.
3. 새 인바운드 규칙 마법사의 **규칙 형식** 페이지에서 **사용자 지정** 옵션을 선택하고 **다음**을 선택합니다.
4. **프로그램** 페이지에서 **모든 프로그램**을 선택하고 **다음**을 선택합니다.
5. **프로토콜 및 포트** 페이지에서 다음 정보를 입력하고 **다음**을 선택합니다.
   * **프로토콜 형식**에서 **TCP**를 선택합니다.
   * **로컬 포트**에서 **특정 포트**를 선택합니다. 다음 상자에서 구성된 프록시 포트 번호를 지정합니다.
   * **원격 포트**에서 **모든 포트**를 선택합니다.

마법사의 나머지 부분에서 끝까지 기본 설정을 적용합니다. 그런 다음 이 규칙의 이름을 지정합니다. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>3단계: NSG에 예외 규칙 추가
다음 명령은 NSG에 예외를 추가합니다. 이 예외는 10.0.0.5의 모든 포트에서 오는 TCP 트래픽을 포트 80(HTTP) 또는 443(HTTPS)의 모든 인터넷 주소에 허용합니다. 공용 인터넷에 특정 포트가 필요하면 해당 포트를 ```-DestinationPortRange```에 추가해야 합니다.

Azure PowerShell 명령 프롬프트에서 다음 명령을 입력합니다.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>질문이 있으십니까?
질문이 있거나 포함되었으면 하는 기능이 있는 경우 [의견을 보내 주세요](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>다음 단계
VM을 백업하기 위한 환경을 준비했으므로 이제 백업을 만들어야 합니다. 계획 문서에서는 VM 백업에 대한 보다 자세한 정보를 제공합니다.

* [가상 머신 설정](backup-azure-arm-vms.md)
* [VM 백업 인프라 계획](backup-azure-vms-introduction.md)
* [가상 컴퓨터 백업 관리](backup-azure-manage-vms.md)
