---
title: "Resource Manager로 배포된 가상 컴퓨터 백업 관리 | Microsoft Docs"
description: "Resource Manager로 배포된 가상 머신 백업을 관리하고 모니터링하는 방법을 알아봅니다."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: f3050283-d60f-472d-b464-cb844e70d67e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: trinadhk;markgal
ms.openlocfilehash: f4613746a427e6987366eeb46605524cd3aacbe2
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="manage-azure-virtual-machine-backups"></a>Azure 가상 머신 백업 관리

이 문서는 VM 백업 관리 지침을 제공하며, 포털 대시보드에서 사용할 수 있는 백업 경고 정보를 설명합니다. Recovery Services 자격 증명 모음이 있는 VM을 사용하는 경우 이 문서의 지침을 적용합니다. 이 문서에서는 가상 머신 만들기에 대해 다루거나 가상 머신을 보호하는 방법을 설명하지 않습니다. Recovery Services 자격 증명을 사용하여 Azure의 Azure Resource Manager로 배포된 VM을 보호하기 위한 입문서는 [먼저 보기: Recovery Services 자격 증명 모음에 VM 백업](backup-azure-vms-first-look-arm.md)을 참조하세요.

## <a name="manage-vaults-and-protected-virtual-machines"></a>자격 증명 모음 및 보호된 가상 머신 관리
Azure Portal에서 Recovery Services 자격 증명 모음 대시보드는 다음을 포함하는 자격 증명 모음에 대한 정보에 액세스를 제공합니다.

* 가장 최근 복원 지점이기도 한 가장 최근 백업 스냅숏
* 백업 정책
* 모든 백업 스냅숏의 총 크기
* 자격 증명 모음으로 보호되는 가상 머신의 수

가상 머신 백업을 사용하는 다양한 관리 작업은 대시보드에서 자격 증명 모음 열기로 시작합니다. 그러나 자격 증명 모음을 사용하여 여러 항목(또는 여러 VM)을 보호할 수 있으므로 특정 VM에 대한 세부 정보를 보려면 자격 증명 모음 항목 대시보드를 열어야 합니다. 다음 절차는 *자격 증명 모음 대시보드*를 열고 *자격 증명 모음 항목 대시보드*를 진행하는 방법을 보여 줍니다. 절차 모두에는 대시보드 명령에 고정을 사용하여 Azure 대시보드에 자격 증명 모음 및 자격 증명 모음 항목을 추가하는 방법을 알려주는 "팁"이 있습니다. 대시보드에 고정은 자격 증명 모음 또는 항목에 바로 가기를 만드는 방법입니다. 바로 가기에서 일반적인 명령을 실행할 수도 있습니다.

> [!TIP]
> 여러 대시보드와 블레이드를 연 경우 창의 아래쪽에 있는 진한 파란색 슬라이더를 사용하여 Azure 대시보드에서 앞뒤로 이동합니다.
>
>

![슬라이더가 있는 전체 보기](./media/backup-azure-manage-vms/bottom-slider.png)

### <a name="open-a-recovery-services-vault-in-the-dashboard"></a>대시보드에서 Recovery Services 자격 증명 모음을 엽니다.
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 허브 메뉴에서 **찾아보기**를 클릭하고 리소스 목록에서 **Recovery Services**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services 자격 증명 모음**을 클릭합니다.

    ![Recovery Services 자격 증명 모음 만들기 1단계](./media/backup-azure-manage-vms/browse-to-rs-vaults.png)

    Recovery Services 자격 증명 모음의 목록이 표시됩니다.

    ![Recovery Services 자격 증명 모음 목록 ](./media/backup-azure-manage-vms/list-o-vaults.png)

   > [!TIP]
   > Azure 대시보드에 자격 증명 모음을 고정하는 경우 Azure 포털을 열 경우 해당 자격 증명 모음에 즉시 액세스할 수 있습니다. 대시보드에 자격 증명 모음을 고정하려면 자격 증명 모음 목록에서 자격 증명 모음을 마우스 오른쪽 단추로 클릭하고 **대시보드에 고정**을 선택합니다.
   >
   >
3. 자격 증명 모음 목록에서 자격 증명 모음을 선택하여 대시보드를 엽니다. 자격 증명 모음을 선택할 경우 자격 증명 모음 대시보드 및 **설정** 블레이드를 엽니다. 다음 이미지에서 **Contoso-vault** 대시보드가 강조 표시됩니다.

    ![자격 증명 모음 대시보드 열기 및 설정 블레이드](./media/backup-azure-manage-vms/full-view-rs-vault.png)

### <a name="open-a-vault-item-dashboard"></a>자격 증명 모음 항목 대시보드 열기
이전 절차에서 자격 증명 모음 대시보드를 열었습니다. 자격 증명 모음 항목 대시보드를 열려면:

1. 자격 증명 모음 대시보드의 **Backup 항목** 타일에서 **Azure Virtual Machines**를 클릭합니다.

    ![백업 항목 열기 타일](./media/backup-azure-manage-vms/contoso-vault-1606.png)

    **Backup 항목** 블레이드에서 각 항목에 대한 마지막 백업 작업을 나열합니다. 이 예제에는 자격 증명이 모음에 의해 보호되는 하나의 가상 머신인 demovm-markgal이 있습니다.  

    ![Backup 항목 타일](./media/backup-azure-manage-vms/backup-items-blade.png)

   > [!TIP]
   > 쉽게 액세스하기 위해 자격 증명 모음 항목을 Azure 대시보드에 고정할 수 있습니다. 자격 증명 모음을 고정하려면 자격 증명 모음 항목 목록에서 항목을 마우스 오른쪽 단추로 클릭하고 **대시보드에 고정**을 선택합니다.
   >
   >
2. **Backup 항목** 블레이드에서 항목을 클릭하여 자격 증명 모음 항목 대시보드를 엽니다.

    ![Backup 항목 타일](./media/backup-azure-manage-vms/backup-items-blade-select-item.png)

    자격 증명 모음 항목 대시보드 및 해당 **설정** 블레이드가 열립니다.

    ![설정 블레이드에서 Backup 항목 대시보드](./media/backup-azure-manage-vms/item-dashboard-settings.png)

    자격 증명 모음 항목 대시보드에서 다음과 같은 다양한 키 관리 작업을 완료할 수 있습니다.

   * 정책 변경 또는 새 백업 정책 만들기
   * 복원 지점 확인 및 해당 일관성 상태 확인
   * 가상 컴퓨터의 주문형 백업
   * 가상 머신 보호 중지
   * 가상 머신 보호 재개
   * 백업 데이터(또는 복구 지점) 삭제
   * [백업 디스크 복원](backup-azure-arm-restore-vms.md#restore-backed-up-disks)

다음 절차의 경우 시작점은 자격 증명 모음 항목 대시보드입니다.

## <a name="manage-backup-policies"></a>백업 정책 관리
1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **모든 설정**을 클릭하여 **설정** 블레이드를 엽니다.

    ![Backup 정책 블레이드](./media/backup-azure-manage-vms/all-settings-button.png)
2. **설정** 블레이드에서 **Backup 정책**을 클릭하여 해당 블레이드를 엽니다.

    블레이드에서 백업 빈도 및 보존 범위 세부 정보가 표시됩니다.

    ![Backup 정책 블레이드](./media/backup-azure-manage-vms/backup-policy-blade.png)
3. **백업 정책 선택** 메뉴에서:

   * 정책을 변경하려면 다른 정책을 선택하고 **저장**을 클릭합니다. 자격 증명 모음에 새 정책이 즉시 적용됩니다.
   * 정책을 만들려는 경우 **새로 만들기**를 선택합니다.

     ![가상 머신 백업](./media/backup-azure-manage-vms/backup-policy-create-new.png)

     백업 정책을 만드는 지침은 [백업 정책 정의](backup-azure-manage-vms.md#defining-a-backup-policy)를 참조하세요.

[!INCLUDE [backup-create-backup-policy-for-vm](../../includes/backup-create-backup-policy-for-vm.md)]

> [!NOTE]
> 백업 정책을 관리하는 동안 최적의 백업 성능을 위해 [모범 사례](backup-azure-vms-introduction.md#best-practices)를 따라야 합니다.
>
>

## <a name="on-demand-backup-of-a-virtual-machine"></a>가상 머신의 주문형 백업
보호를 위해 구성한 후에는 가상 컴퓨터의 주문형 백업을 수행할 수 있습니다. 초기 백업이 보류 중인 경우 주문형 백업은 Recovery Services 자격 증명 모음에 가상 컴퓨터의 전체 복사본을 만듭니다. 초기 백업이 완료되면 주문형 백업은 이전 스냅숏에서 Recovery Services 자격 증명 모음에 변경 내용만 보냅니다. 즉, 이후의 백업은 항상 증분형입니다.

> [!NOTE]
> 주문형 백업에 대한 보존 범위는 정책에서 매일 백업 지점에 대해 지정된 보존 값입니다. 매일 백업 시점을 선택하지 않은 경우 주간 백업 지점을 사용합니다.
>
>

가상 머신의 주문형 백업을 트리거하려면:

* [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **지금 Backup**을 클릭합니다.

    ![지금 Backup 아이콘](./media/backup-azure-manage-vms/backup-now-button.png)

    포털에서는 주문형 백업 작업을 시작하려는지 확인합니다. **예** 를 클릭하여 백업 작업을 시작합니다.

    ![지금 Backup 아이콘](./media/backup-azure-manage-vms/backup-now-check.png)

    백업 작업에서 복구 지점이 생성됩니다. 복구 지점의 재방문 주기 범위는 가상 머신과 연결된 정책에 지정된 보존 범위와 같습니다. 작업에 대한 진행률을 추적하려면 자격 증명 모음 대시보드에서 **Backup 작업** 타일을 클릭합니다.  

## <a name="stop-protecting-virtual-machines"></a>가상 머신 보호 중지
가상 머신에 대한 보호를 중지하려는 경우 복구 지점을 보존할지를 묻는 메시지가 표시됩니다. 두 가지 방법으로 가상 머신 보호를 정지할 수 있습니다.

* 모든 미래의 백업 작업을 정지하고 모든 복구 지점을 삭제, 또는
* 모든 미래의 백업 작업을 정지하고 복구 지점을 유지 

저장소에서 복구 지점을 유지하는 데 관련된 비용이 듭니다. 그러나 복구 지점을 유지하는 장점은 나중에 원하는 경우 가상 머신을 복원할 수 있다는 것입니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/backup/)를 참조하세요. 모든 복구 지점을 삭제하려는 경우 가상 머신을 복원할 수 없습니다.

가상 머신에 대한 보호를 중지하려면:

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **백업 중지**를 클릭합니다.

    ![백업 중지 단추](./media/backup-azure-manage-vms/stop-backup-button.png)

    Backup 중지 블레이드가 열립니다.

    ![백업 중지 블레이드](./media/backup-azure-manage-vms/stop-backup-blade.png)
2. **Backup 중지** 블레이드에서 백업 데이터를 유지할지 혹은 삭제할지 여하를 선택합니다. 정보 상자에서는 선택한 항목에 대한 세부 정보를 제공합니다.

    ![보호 중지](./media/backup-azure-manage-vms/retain-or-delete-option.png)
3. 백업 데이터를 유지하기로 선택한 경우 4단계로 건너뜁니다. 백업 데이터 삭제를 삭제하기로 선택하고 백업 작업을 중지하고 복구 지점을 삭제하려는 경우 항목의 이름을 입력합니다.

    ![백업 확인](./media/backup-azure-manage-vms/item-verification-box.png)

    항목 이름을 모르는 경우 이름을 보려면 느낌표 위로 마우스를 가져갑니다. 또한 항목의 이름은 블레이드 맨 위에 있는 **Backup 중지** 아래에 있습니다.
4. 필요에 따라 **이유** 또는 **주석**을 제공합니다.
5. 현재 항목에 대한 백업 작업을 중지하려면 ![백업 중지 단추](./media/backup-azure-manage-vms/stop-backup-button-blue.png)를 클릭합니다.

    알림 메시지를 통해 백업 작업이 중지되었음을 알 수 있습니다.

    ![보호 중지 확인](./media/backup-azure-manage-vms/stop-message.png)

## <a name="resume-protection-of-a-virtual-machine"></a>가상 머신 보호 재개
가상 머신에 대한 보호가 중지되었을 때 **Backup 데이터 보관** 옵션을 선택한 경우 보호를 다시 시작할 수 있습니다. **Backup 데이터 삭제** 옵션을 선택한 경우 가상 컴퓨터에 대한 보호를 다시 시작할 수 없습니다.

가상 머신에 대한 보호를 다시 시작하려면

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **백업 다시 시작**을 클릭합니다.

    ![보호 다시 시작](./media/backup-azure-manage-vms/resume-backup-button.png)

    Backup 정책 블레이드가 열립니다.

   > [!NOTE]
   > 가상 머신을 다시 보호하는 경우 가상 머신이 처음에 보호된 정책과 다른 정책을 선택할 수 있습니다.
   >
   >
2. [백업 정책 관리](backup-azure-manage-vms.md#manage-backup-policies)의 단계에 따라 가상 머신에 대한 정책을 할당합니다.

    백업 정책이 가상 머신에 적용되면 다음과 같은 메시지가 표시됩니다.

    ![성공적으로 보호된 VM](./media/backup-azure-manage-vms/success-message.png)

## <a name="delete-backup-data"></a>Backup 데이터 삭제
**백업 중지** 작업 중에 또는 백업이 완료된 후 언제든지 가상 머신과 연결된 백업 데이터를 삭제할 수 있습니다. 복구 지점을 삭제하기 전에 며칠 또는 몇 주 기다리는 것이 도움이 될 수도 있습니다. 백업 데이터를 삭제하는 경우 복구 지점을 복원하는 작업과 달리 특정 복구 지점을 삭제하도록 선택할 수 없습니다. 백업 데이터를 삭제하려는 경우 항목에 연결된 모든 복구 지점을 삭제합니다.

다음 절차에서는 가상 머신에 대한 Backup 작업이 중지 또는 비활성화되었다고 가정합니다. Backup 작업을 사용하지 않는 경우 자격 증명 모음 항목 대시보드에서 **백업 다시 시작** 및 **백업 삭제** 옵션을 사용할 수 있습니다.

![다시 시작 및 삭제 단추](./media/backup-azure-manage-vms/resume-delete-buttons.png)

*Backup을 사용하지 않는*가상 머신의 백업 데이터를 삭제하려면:

1. [자격 증명 모음 항목 대시보드](backup-azure-manage-vms.md#open-a-vault-item-dashboard)에서 **백업 삭제**를 클릭합니다.

    ![VM 유형](./media/backup-azure-manage-vms/delete-backup-buttom.png)

    **백업 데이터 삭제** 블레이드가 열립니다.

    ![VM 유형](./media/backup-azure-manage-vms/delete-backup-blade.png)
2. 항목의 이름을 입력하여 복구 지점을 삭제한다고 확인합니다.

    ![백업 확인](./media/backup-azure-manage-vms/item-verification-box.png)

    항목 이름을 모르는 경우 이름을 보려면 느낌표 위로 마우스를 가져갑니다. 또한 항목의 이름은 블레이드 맨 위에 있는 **Backup 데이터 삭제** 아래에 있습니다.
3. 필요에 따라 **이유** 또는 **주석**을 제공합니다.
4. 현재 항목에 대한 백업 데이터를 삭제하려면 ![백업 중지 단추](./media/backup-azure-manage-vms/delete-button.png)를 클릭합니다.

    알림 메시지를 통해 백업 데이터가 삭제되었음을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계
복구 지점에서 가상 머신을 다시 만드는 방법에 대한 내용은 [Azure VM 복원](backup-azure-arm-restore-vms.md)을 확인하세요. 가상 머신을 보호하는 방법에 대한 정보가 필요한 경우 [먼저 보기: Recovery Services 자격 증명 모음에 VM 백업](backup-azure-vms-first-look-arm.md)을 참조하세요. 이벤트 모니터링에 대한 자세한 내용은 [Azure 가상 머신 백업에 대한 경고 모니터링](backup-azure-monitor-vms.md)을 참조하세요.
