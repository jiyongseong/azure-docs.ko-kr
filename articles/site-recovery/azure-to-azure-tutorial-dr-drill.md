---
title: Azure Site Recovery를 사용하여 보조 Azure 지역에 Azure VM의 재해 복구 훈련 실행
description: Azure Site Recovery 서비스를 사용하여 보조 Azure 지역에 Azure VM의 재해 복구 훈련을 실행하는 방법을 알아봅니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: fb1c41e6fe254fbcbee8cb91a177777ed4e37fbb
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208977"
---
# <a name="run-a-disaster-recovery-drill-for-azure-vms-to-a-secondary-azure-region"></a>보조 Azure 지역에 Azure VM의 재해 복구 훈련 실행

[Azure Site Recovery](site-recovery-overview.md) 서비스는 계획된 정전 및 계획되지 않은 정전 중 비즈니스 앱 작동을 유지하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. Site Recovery는 복제, 장애 조치(failover), 복구를 포함하여 온-프레미스 컴퓨터 및 Azure VM(Virtual Machines)의 재해 복구를 오케스트레이션합니다.

이 자습서에서는 테스트 장애 조치(failover)를 사용하여 한 Azure 지역에서 다른 Azure 지역으로 Azure VM의 재해 복구 훈련을 실행하는 방법을 보여줍니다. 훈련은 데이터 손실이나 가동 중지 시간 없이 복제 전략의 유효성을 검사하며 프로덕션 환경에 영향을 미치지 않습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 필수 구성 요소 확인
> * VM에 대해 테스트 장애 조치(failover) 실행

Azure에서 Azure로의 복제는 현재 미리 보기에 있습니다.

## <a name="prerequisites"></a>필수 조건

- 테스트 장애 조치(failover)를 실행하기 전에 VM 속성을 확인하여 모두 올바르게 되어 있는지 확인하는 것이 좋습니다.  **복제 항목**에서 VM 속성에 액세스합니다. **Essentials** 블레이드는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.
- 복제를 활성화할 때 설정된 기본 네트워크가 아니라 테스트 장애 조치에 대한 별도의 Azure VM 네트워크를 사용하는 것이 좋습니다.


## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1. **설정** > **복제된 항목**에서 VM **+테스트 장애 조치** 아이콘을 클릭합니다.

2. **테스트 장애 조치**에서 장애 조치에 사용할 복구 지점을 선택합니다.

   - **가장 최근에 처리됨**: VM을 Site Recovery 서비스에서 처리된 최신 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
   - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다.
   - **사용자 지정**: 복구 지점을 선택합니다.

3. 장애 조치가 발생한 후에 보조 지역의 Azure VM을 연결할 대상 Azure 가상 네트워크를 선택합니다.

4. 장애 조치를 시작하려면 **확인**을 클릭합니다. 진행률을 추적하려면 VM을 클릭하여 해당 속성을 엽니다. 또는 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**에서 **테스트 장애 조치** 작업을 클릭하면 됩니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM이 실행 중이고, 규모가 적절히 조정되었으며, 적절한 네트워크에 연결되어 있는지 확인하세요.
6. 테스트 장애 조치 중에 만든 VM을 삭제하려면 복제된 항목 또는 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [프로덕션 장애 조치(failover) 실행](azure-to-azure-tutorial-failover-failback.md)
