---
title: Azure Migrate의 종속성 시각화 | Microsoft Docs
description: Azure Migrate 서비스의 평가 계산에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 2b6b3e78436a215e64894b1102c4e109f72c896d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="dependency-visualization"></a>종속성 시각화

[Azure Migrate](migrate-overview.md) 서비스는 Azure로의 마이그레이션에 대한 온-프레미스 컴퓨터 그룹을 평가합니다. 컴퓨터를 그룹화하려면 종속성 시각화를 사용하면 됩니다. 이 문서에서는 이 기능에 대한 정보를 제공합니다.


## <a name="overview"></a>개요

Azure Migrate의 종속성 시각화를 사용하면 좀 더 신뢰를 갖고 마이그레이션 자산에 대한 그룹을 만들 수 있습니다. 종속성 시각화를 사용하면 특정 컴퓨터 또는 컴퓨터 그룹 전체의 네트워크 종속성을 볼 수 있습니다. 이 기능이 없는 되도록 유용 하거나 손실 (또는 기억 하는 컴퓨터) 앱 및 워크로드가 여러 컴퓨터에서 실행되는 경우 마이그레이션 프로세스 프로세스에서 기능이 없거나 손실되는(또는 컴퓨터가 잊혀지는) 일이 없도록 보장하는 데 유용합니다.  

## <a name="how-does-it-work"></a>작동 원리

Azure Migrate는 종속성 시각화에 [Log Analytics](../log-analytics/log-analytics-overview.md)의 [서비스 맵](../operations-management-suite/operations-management-suite-service-map.md) 솔루션을 사용합니다.
- Azure Migration 프로젝트를 만들면 구독에 Log Analytics 작업 영역이 생성됩니다.
- 작업 영역 이름은 사용자가 마이그레이션 프로젝트에 대해 지정한 이름에 **migrate-** 를 붙이고, 필요에 따라 접미사로 숫자를 붙여서 만듭니다. 
- 프로젝트 **개요** 페이지의 **Essentials** 섹션에서 Log Analytics 작업 영역으로 이동합니다.
- 생성된 작업 영역에는 **MigrateProject** 키와 **프로젝트 이름** 값이 태그로 지정됩니다. Azure Portal에서 검색할 때 이 태그를 사용할 수 있습니다.  

    ![Log Analytics 작업 영역](./media/concepts-dependency-visualization/oms-workspace.png)

종속성 시각화를 사용하려면 분석할 각 온-프레미스 컴퓨터에 에이전트를 다운로드하여 설치해야 합니다.  

## <a name="do-i-need-to-pay-for-it"></a>요금이 발생하나요?

Azure Migrate는 추가 요금 없이 사용할 수 있습니다. Azure Migrate의 종속성 시각화 기능을 사용하려면 서비스 맵이 필요합니다. Azure Migrate 프로젝트를 만들 때 Azure Migrate에서 새로운 Log Analytics 작업 영역이 자동으로 만들어집니다.

> [!NOTE]
> 종속성 시각화 기능은 Log Analytics 작업 영역을 통해 서비스 맵을 사용합니다. 2018년 2월 28일 이후로 Azure Migrate의 일반 공급이 발표되면서 이 기능을 추가 비용 없이 사용할 수 있습니다. 무료 사용량 작업 영역을 사용하려면 새 프로젝트를 만들어야 합니다. 일반 공급 이전의 기존 작업 영역은 여전히 청구가 가능하기 때문에 새 프로젝트로 이동하는 것이 좋습니다.

1. 이 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 표준 Log Analytics 요금이 발생합니다. 
2. 마이그레이션 시나리오를 추가 비용 없이 지원하기 위해서, Azure Migrate 프로젝트를 만든 후 180일 동안 서비스 맵 솔루션에 요금이 부과되지 않으며 그 후에는 표준 요금이 적용됩니다.
3. 프로젝트 생성의 일환으로 만든 작업 영역만 무료로 사용이 가능합니다.

작업 영역에 에이전트를 등록하는 경우에는 에이전트 설치 단계 페이지에서 프로젝트에 제공된 ID와 키를 사용합니다. 기존 작업 영역을 사용하면 Azure Migrate 프로젝트와 연결할 수 없습니다.

Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트 삭제 후에는 서비스 맵 사용이 무료가 아니며, Log Analytics 작업 영역의 유료 계층에 따라 노드마다 요금이 부과됩니다.

[여기](https://azure.microsoft.com/pricing/details/azure-migrate/)에서 Azure Migrate 가격 책정에 대해 자세히 알아보세요. 

## <a name="how-do-i-manage-the-workspace"></a>작업 영역은 어떻게 관리하나요?

Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다. 작업 영역이 생성된 마이그레이션 프로젝트를 삭제해도 작업 영역은 삭제되지 않습니다. 작업 영역이 더 이상 필요 없는 경우 수동으로 [작업 영역을 삭제](../log-analytics/log-analytics-manage-access.md)해야 합니다.

마이그레이션 프로젝트를 삭제하지 않는 한 Azure Migrate가 만든 작업 영역을 삭제하지 마세요. 삭제하면 종속성이 예상대로 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

[컴퓨터 종속성을 사용하여 컴퓨터 그룹화](how-to-create-group-machine-dependencies.md)