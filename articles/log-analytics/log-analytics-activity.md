---
title: Log Analytics에서 Azure 활동 로그 수집 및 분석 | Microsoft Docs
description: Azure 활동 로그 솔루션을 사용하여 모든 Azure 구독에서 Azure 활동 로그를 분석하고 검색할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: magoedte
ms.openlocfilehash: b6e823d9338d76a350569091d6794e3ac4a2eae9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics"></a>Log Analytics에서 Azure 활동 로그 수집 및 분석

![Azure 활동 로그 기호](./media/log-analytics-activity/activity-log-analytics.png)

활동 로그 분석 솔루션은 모든 Azure 구독에서 [Azure 활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)를 분석하고 검색할 수 있게 해줍니다. Azure 활동 로그는 구독의 리소스에서 수행된 작업에 대한 자세한 정보를 제공하는 로그입니다. 활동 로그는 구독에 대한 이벤트를 보고하므로 이전에는 *감사 로그* 또는 *작업 로그*라고 했습니다.

활동 로그를 통해 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 *무엇을*, *누가*, *언제* 썼는지 확인할 수 있습니다. 작업과 기타 관련 속성의 상태도 이해할 수 있습니다. 활동 로그에는 읽기(GET) 작업 또는 클래식 배포 모델을 사용하는 리소스에 대한 작업이 포함되지 않습니다.

Azure 활동 로그를 Log Analytics에 연결하면 다음 작업을 수행할 수 있습니다.

- 미리 정의된 보기를 사용하여 활동 로그 분석
- 여러 Azure 구독의 활동 로그를 분석하고 검색
- 활동 로그를 90일 이상 보관<sup>1</sup>
- 활동 로그와 다른 Azure 플랫폼 및 응용 프로그램 데이터 간에 상관 관계 지정
- 상태에 따라 집계된 운영 활동 보기
- 각 Azure 서비스에서 발생하는 활동의 추세 보기
- 모든 Azure 리소스에 대한 권한 부여 변경 내용 보고
- 리소스에 영향을 미치는 운영 중단 또는 서비스 상태 문제 식별
- 로그 검색을 사용하여 사용자 활동, 자동 크기 조정 작업, 권한 부여 변경, 서비스 상태와 사용자 환경의 다른 로그 또는 메트릭 간에 상관 관계 지정

<sup>1</sup>기본적으로 Log Analytics는 무료 계층에서도 Azure 활동 로그를 90일 동안 보관합니다. 작업 영역 보존 설정이 90일 미만으로 설정된 경우에도 마찬가지입니다. 작업 영역 보존 설정이 90일보다 긴 경우 작업 영역의 보존 기간에 따라 활동 로그가 보관됩니다.

Log Analytics는 무료로 활동 로그를 수집하고 90일 동안 무료로 로그를 저장합니다. 로그를 90일 이상 저장하는 경우 90일 넘게 저장된 데이터에 대해 데이터 보존 요금이 발생합니다.

무료 가격 책정 계층을 사용하는 경우 활동 로그가 일일 데이터 사용량에 적용되지 않습니다.

## <a name="connected-sources"></a>연결된 소스

대부분의 다른 Log Analytics 솔루션과는 달리, 에이전트에서 활동 로그에 대한 데이터를 수집하지 않습니다. 솔루션에서 사용하는 모든 데이터는 Azure에서 직접 옵니다.

| 연결된 소스 | 지원됨 | 설명 |
| --- | --- | --- |
| [Windows 에이전트](log-analytics-windows-agent.md) | 아니오 | 솔루션이 Windows 에이전트에서 정보를 수집하지 않습니다. |
| [Linux 에이전트](log-analytics-linux-agents.md) | 아니요 | 솔루션이 Linux 에이전트에서 정보를 수집하지 않습니다. |
| [SCOM 관리 그룹](log-analytics-om-agents.md) | 아니요 | 솔루션이 연결된 SCOM 관리 그룹의 에이전트에서 정보를 수집하지 않습니다. |
| [Azure 저장소 계정](log-analytics-azure-storage.md) | 아니요 | 솔루션이 Azure 저장소에서 정보를 수집하지 않습니다. |

## <a name="prerequisites"></a>필수 조건

- Azure 활동 로그 정보에 액세스하려면 Azure 구독이 있어야 합니다.

## <a name="configuration"></a>구성

다음 단계를 수행하여 작업 영역에 대해 Activity Log Analytics 솔루션을 구성합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 Activity Log Analytics 솔루션을 사용하도록 설정합니다.
2. Log Analytics 작업 영역으로 이동하도록 활동 로그를 구성합니다.
    1. Azure Portal에서 작업 영역을 선택한 다음 **Azure 활동 로그**를 클릭합니다.
    2. 각 구독에 대해 구독 이름을 클릭합니다.  
        ![구독 추가](./media/log-analytics-activity/add-subscription.png)
    3. *SubscriptionName* 블레이드에서 **연결**을 클릭합니다.  
        ![구독 연결](./media/log-analytics-activity/subscription-connect.png)

OMS 포털을 사용하여 솔루션을 추가하면 다음 타일이 표시됩니다. Azure Portal에 로그인하여 Azure 구독을 작업 영역에 연결합니다.  
![평가 수행](./media/log-analytics-activity/tile-performing-assessment.png)

## <a name="using-the-solution"></a>솔루션 사용

Activity Log Analytics 솔루션을 작업 영역에 추가하면 개요 대시보드에 **Azure 활동 로그** 타일이 추가됩니다. 이 타일에는 솔루션에서 액세스할 수 있는 Azure 구독의 Azure 활동 레코드 수가 표시됩니다.

![Azure 활동 로그 타일](./media/log-analytics-activity/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Azure 활동 로그 보기

**Azure 활동 로그** 타일을 클릭하여 **Azure 활동 로그** 대시보드를 엽니다. 대시보드에는 다음 테이블의 블레이드가 포함되어 있습니다. 각 블레이드에는 지정된 범위 및 시간 범위에 대한 해당 블레이드의 기준과 일치하는 항목이 최대 10개까지 나열됩니다. 블레이드 맨 아래에서 **모두 보기**를 클릭하거나 블레이드 헤더를 클릭하여 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.

활동 로그 데이터는 솔루션으로 이동하도록 활동 로그를 구성한 *후*에만 표시되므로 그 전에는 데이터를 볼 수 없습니다.

| 블레이드 | 설명 |
| --- | --- |
| Azure 활동 로그 항목 | 사용자가 선택한 날짜 범위에 해당하는 상위 Azure 활동 로그 항목 레코드의 가로 막대형 차트를 표시하고 상위 10개 활동 호출자 목록을 표시합니다. 가로 막대형 차트를 클릭하면 <code>AzureActivity</code>에 대한 로그 검색이 실행됩니다. 호출자 항목을 클릭하면 해당 항목에 대한 모든 활동 로그 항목을 반환하는 로그 검색이 실행됩니다. |
| 상태별 활동 로그 | 사용자가 선택한 날짜 범위에 해당하는 Azure 활동 로그 상태의 도넛형 차트를 보여 줍니다. 상위 10개 상태 레코드 목록도 보여 줍니다. 차트를 클릭하면 <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>에 대한 로그 검색이 실행됩니다. 상태 항목을 클릭하면 해당 상태 레코드에 대한 모든 활동 로그 항목을 반환하는 로그 검색이 실행됩니다. |
| 리소스별 활동 로그 | 활동 로그와 함께 총 리소스 수를 표시하고 각 리소스에 대한 레코드 수와 함께 상위 10개 리소스를 나열합니다. 전체 영역을 클릭하여 <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>에 대해 로그 검색을 실행하면 솔루션에 사용할 수 있는 모든 Azure 리소스가 표시됩니다. 리소스를 클릭하면 해당 리소스에 대한 모든 활동 레코드를 반환하는 로그 검색이 실행됩니다. |
| 리소스 공급자별 활동 로그 | 활동 로그를 생성하는 총 리소스 공급자 수를 표시하고 상위 10개 리소스 공급자를 나열합니다. 전체 영역을 클릭하여 <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>에 대해 로그 검색을 실행하면 모든 Azure 리소스 공급자가 표시됩니다. 리소스 공급자를 클릭하면 해당 공급자에 대한 모든 활동 레코드를 반환하는 로그 검색이 실행됩니다. |

![Azure 활동 로그 대시보드](./media/log-analytics-activity/activity-log-dash.png)

## <a name="next-steps"></a>다음 단계

- 특정 활동이 발생하면 [경고](log-analytics-alerts-creating.md)를 만듭니다.
- [로그 검색](log-analytics-log-searches.md)을 사용하여 활동 로그의 자세한 정보를 봅니다.
