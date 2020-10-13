---
title: 가상 컴퓨터에 대한 Azure 자동 관리(Azure Automanage)
description: 가상 컴퓨터에 대한 Azure 자동 관리에 대해 알아봅니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 8e48a5c896c4927b82f7d77f31b7f1c47fd156c5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940864"
---
# <a name="azure-automanage-for-virtual-machines"></a>가상 컴퓨터에 대한 Azure 자동 관리

이 문서에서는 다음과 같은 이점을 제공 하는 가상 머신에 대한 Azure 자동 관리에 대해 설명 합니다.

- 가상 머신을 지능적으로 선택 하여 모범 사례 Azure 서비스 선택
- Azure 모범 사례 마다 자동으로 각 서비스를 구성 합니다.
- 검색된 경우 드리프트를 모니터링 하고 수정 합니다.
- 간단한 환경을 제공 합니다 (점, 클릭, 설정, 잊어버린).


## <a name="overview"></a>개요

가상 컴퓨터에 대한 Azure 자동 관리는 Azure에서 가상 컴퓨터에 유용한 특정 서비스를 검색 하고, 등록 하는 방법을 파악 하고, Azure에서 특정 서비스를 구성 하는 방법을 파악 하지 않아도 되는 서비스입니다. 이러한 서비스는 가상 컴퓨터에 대한 안정성, 보안 및 관리를 개선 하는 데 도움이 되며, [azure 업데이트 관리](../automation/update-management/update-mgmt-overview.md) 및 [Azure Backup](../backup/backup-overview.md) 와 같은 azure 모범 사례 서비스로 간주 되어 이름을 지정할 수 있습니다.

Azure 자동 관리에 가상 머신을 온 보 딩 하면 자동으로 각 모범 사례 서비스가 권장 설정으로 구성 됩니다. 모범 사례는 각 서비스 마다 다릅니다. Azure Backup 예를 들어, 하루에 한 번 가상 머신을 백업 하 고 6 개월의 보존 기간을 설정 하는 것이 가장 좋습니다.

또한 Azure 자동 관리는 검색 된 경우 자동으로 드리프트를 모니터링 하 고 수정 합니다. 즉, 가상 머신이 Azure 자동 관리에 등록 Azure 모범 사례에 따라 구성할 수는 없지만, 컴퓨터를 모니터링 하여 전체 수명 주기 동안 이러한 모범 사례를 계속 준수 하는지 확인 합니다. 가상 머신이 이러한 사례를 드리프트 하거나 이에 대한 작업을 수행 하는 경우 이를 수정 하고 컴퓨터를 원하는 상태로 다시 끌어옵니다.

마지막으로 경험이 매우 간단 합니다.


## <a name="prerequisites"></a>사전 요구 사항

가상 머신에서 Azure 자동 관리를 사용 하도록 설정 하기 전에 고려해야 할 몇 가지 필수 구성 요소가 있습니다.

- Windows Server Vm에만 해당
- Vm이 실행 되 고 있어야 함
- 확장 되지 않은 집합 Vm만
- Vm은 지원 되는 지역에 있어야 합니다.
- 사용자에 게 올바른 사용 권한이 있어야 합니다.
- Vm은 다른 구독의 log analytics 작업 영역에 연결 하면 안 됩니다.

자동 관리를 사용 하도록 설정 하려면 **소유자** 역할 또는 **참가자** 와 **사용자 액세스 관리자** 역할을 사용 하도록 설정 하려면 다음 RBAC 권한이 필요 합니다.

자동 관리는 유럽 서부, 미국 동부, 미국 서부 2, 캐나다 중부, 미국 서부 및 지역에 있는 Windows VM만 지원 한다는 점에 유의 해야 합니다.

## <a name="participating-services"></a>참여 하는 서비스

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services.png" alt-text="지능적으로 서비스를 등록 합니다.":::

참여 하는 Azure 서비스의 전체 목록 및 지원 되는 구성 프로필에 대한 [Virtual machines에 대한 Azure 자동 관리 모범 사례](virtual-machines-best-practices.md)를 참조 하세요.

 이러한 참여 하는 서비스에 자동으로 등록 됩니다. [클라우드 채택 프레임 워크](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 반드시 필요 합니다.

이러한 모든 서비스에 대해 자동으로 등록 하고, 자동으로 구성 하고, 드리프트에 대해 모니터링 하고, 드리프트가 검색 되면 중재 합니다.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure Portal에서 VM에 자동 관리 사용

Azure Portal에서 기존 가상 컴퓨터에 대해 자동 관리를 사용 하도록 설정 하거나 새 가상 컴퓨터를 만들 수 있습니다. 이 프로세스에 대한 간결한 단계는 [빠른 시작: Azure Portal에서 가상 머신용 Azure 자동 관리 사용](quick-create-virtual-machines-portal.md)시작을 참조 하세요.

VM에 대해 자동 관리를 처음 사용 하는 경우 **자동 관리 – Azure 가상 머신 모범 사례**에 대한 Azure Portal에서 검색할 수 있습니다. **기존 VM에서 사용**을 클릭 하고, 등록 하려는 VM을 선택 하고, **선택**, **사용**을 차례로 클릭 한 후 완료 합니다.

이러한 서비스를 관리 하기 위해이 VM과 상호 작용 해야 하는 경우에는 VM을 수정 하려고 했지만 그렇게 하지 못한 이벤트에 있습니다. VM을 성공적으로 재구성 한 경우 경고를 표시 하지 않고 다시 준수 상태로 전환 합니다.


## <a name="configuration-profiles"></a>구성 프로필

가상 컴퓨터에 대해 자동 관리를 사용 하도록 설정 하는 경우 구성 프로필이 필요 합니다. 구성 프로필은 이 서비스의 기반이 됩니다. 컴퓨터를 등록 하는 서비스와 해당 서비스의 구성에 대한 범위를 정확 하게 정의 합니다.

### <a name="default-configuration-profiles"></a>기본 구성 프로필

현재 사용할 수 있는 두 가지 구성 프로필이 있습니다.

- **Azure 가상 머신 모범 사례-** 개발/테스트 구성 프로필은 개발/테스트 컴퓨터용으로 설계 되었습니다.
- **Azure 가상 머신 모범 사례-** 프로덕션 구성 프로필은 프로덕션용입니다.

이러한 차이점은 실행 하는 작업에 따라 특정 서비스가 권장 되기 때문입니다. 예를 들어 프로덕션 컴퓨터에서 자동으로 Azure Backup으로 등록 됩니다. 그러나 개발/테스트 컴퓨터는 일반적으로 비즈니스에 영향을 미치기 때문에 개발/테스트 컴퓨터의 경우에는 불필요한 비용을 줄일 수 있습니다.

### <a name="customizing-a-configuration-profile-using-preferences"></a>기본 설정을 사용 하여 구성 프로필 사용자 지정

사용자가 등록 하는 표준 서비스 외에도 기본 설정의 특정 하위 집합을 구성할 수 있습니다. 이러한 기본 설정은 모범 사례를 위반 하지 않는 다양한 구성 옵션 내에서 허용 됩니다. 예를 들어 Azure Backup의 경우 백업 빈도와 백업 빈도를 정의하고 이를 수행 하는 요일을 지정할 수 있습니다. 그러나 Azure Backup 완전히 전환 하는 것은 허용 *되지* 않습니다.

> [!NOTE]
> 개발/테스트 구성 프로필에서는 VM을 전혀 백업 하지 않습니다.

기본 설정을 통해 기본 구성 프로필의 설정을 조정할 수 있습니다. [여기](virtual-machines-custom-preferences.md)에서 기본 설정을 만드는 방법을 알아보세요.

> [!NOTE]
> 자동 관리를 사용 하는 동안에는 VM에서 구성 프로필을 변경할 수 없습니다. 해당 VM에 대해 자동 관리를 사용 하지 않도록 설정한 다음 원하는 구성 프로필 및 기본 설정으로 자동 관리를 다시 사용 하도록 설정 해야 합니다.


## <a name="automanage-account"></a>계정 자동 관리

자동 관리 계정은 자동화 된 작업이 수행 되는 보안 컨텍스트 또는 id입니다. 일반적으로 자동 관리 계정 옵션을 선택 하는 것은 필요 하지 않지만 자동화 된 관리를 나눌 수 있는 위임 시나리오 (두 시스템 관리자)가 있는 경우이 옵션을 사용 하면 각 관리자에 대해 Azure id를 정의할 수 있습니다.

Azure Portal 환경에서 Vm에 대해 자동 관리를 사용 하도록 설정 하는 경우 자동 관리 계정을 할당 하거나 수동으로 만들 수 있는 **AZURE VM 모범 사례 사용** 블레이드에서 고급 드롭다운이 있습니다.

> [!NOTE]
> 자동 관리를 사용 하도록 설정 하려면 **소유자** 역할 또는 **참가자** 와 **사용자 액세스 관리자** 역할을 사용 하도록 설정 하려면 다음 RBAC 권한이 필요 합니다.


## <a name="status-of-vms"></a>VM의 상태

Azure Portal에서 자동 관리 되는 모든 VM을 나열 하는 **자동 관리 – Azure 가상 머신 모범 사례** 페이지로 이동 합니다. 여기에서 각 가상 머신의 전반적인 상태를 확인할 수 있습니다.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="구성된 가상 컴퓨터 목록입니다.":::

나열된 각 VM에 대해 이름, 구성 프로필, 구성 기본 설정, 상태, 계정, 구독 및 리소스 그룹 세부 정보가 표시 됩니다.

**상태** 열에는 다음 상태가 표시 될 수 있습니다.
- *진행 중* -VM을 사용 하도록 설정 하고 구성 하는 중입니다.
- *구성* 됨-VM이 구성 되 고 드리프트가 검색 되지 않습니다.
- *실패* -VM이 데이터베이스가 드리프트을 수정할 수 없습니다.

**상태가** *실패*로 표시 되는 경우 VM이 있는 리소스 그룹을 통해 배포 문제를 해결할 수 있습니다. **리소스 그룹**으로 이동 하여 리소스 그룹을 선택 하고 **배포** 를 클릭 한 다음 오류 정보와 함께 *실패* 상태를 확인 합니다.


## <a name="disabling-automanage-for-vms"></a>VM에 대해 자동 관리를 사용 하지 않도록 설정

특정 VM에서 자동 관리를 사용 하지 않도록 설정할 수 있습니다. 예를 들어, 컴퓨터에서 매우 중요한 보안 워크 로드를 실행 하고 있으므로 Azure 보다 더 많이 잠가야 합니다. 따라서 Azure 모범 사례 외부에서 컴퓨터를 구성 해야 합니다.

Azure Portal에서이 작업을 수행 하려면 자동 관리 되는 모든 VM을 나열 하는 **자동 관리 – Azure 가상 머신 모범 사례** 페이지로 이동 합니다. 자동 관리에서 사용 하지 않도록 설정할 가상 컴퓨터 옆의 확인란을 선택 하고 **자동 관리 사용 안 함** 단추를 클릭 합니다.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="가상 머신에서 자동 관리를 사용 하지 않도록 설정":::

**비활성화**에 동의 하기 전에 결과 팝업의 메시지를 자세히 읽습니다.

```
Disabling automanagement in a VM results in the following behavior:

1.    The configuration of the VM and the services it's onboarded to will not be changed
2.    Any changes incurred by those services will remain billable and will continue to be incurred
3.    Any Automanage behaviors will stop immediately
```

무엇 보다도, 우리가 등록 구성된 서비스에서 가상 머신을 오프 보드 하지 않습니다. 따라서 해당 서비스에 의해 발생 하는 모든 요금은 계속 청구 가능 상태로 유지 됩니다. 필요한 경우에는 보드를 꺼야 합니다. 모든 자동 관리 동작은 즉시 중지 됩니다. 예를 들어 더 이상 VM에서 드리프트를 모니터링 하지 않습니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 머신에 대한 자동 관리에서 모범 사례 Azure 서비스를 알고, 등록 및 구성 하지 않아도 되는 경우를 제거할 수 있는 방법을 배웠습니다. 또한 구성 프로필에서 가상 머신 상태에 대해 자동 관리 등록 컴퓨터를 다시 준수 상태로 자동 전환 합니다.

Azure Portal에서 가상 컴퓨터에 대해 자동 관리를 사용 하도록 설정 하세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 컴퓨터에 대해 자동 관리를 사용 하도록 설정](quick-create-virtual-machines-portal.md)
