---
title: OMS Log Analytics에서 경고 만들기 | Microsoft Docs
description: Log Analytics의 경고는 OMS 저장소의 중요한 정보를 식별하며 문제를 미리 알리거나 작업을 호출하여 문제 해결을 시도합니다.  이 문서에서는 경고 규칙을 만드는 방법을 설명하고 규칙에서 실행할 수 있는 여러 가지 작업을 자세히 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: bwren
ms.openlocfilehash: b692822660ab12f89b274cea75727fb808d673f8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="working-with-alert-rules-in-log-analytics"></a>Log Analytics에서 경고 규칙 작업

> [!NOTE]
> 이 문서에서는 OMS 포털에서 관리되는 기본 Log Analytics 경고에 대해 설명합니다.  Log Analytics의 경고가 [Azure로 확장](../monitoring-and-diagnostics/monitoring-alerts-extend.md)되고 있습니다.  확장이 완료되면 Azure Portal에서 경고 규칙을 만들고 편집하며 이 문서의 절차를 사용하게 됩니다.


경고는 일정한 간격으로 로그 검색을 자동으로 실행하는 경고 규칙에 의해 만들어집니다.  결과가 특정 조건과 일치하는 경우 경고 레코드를 만듭니다.  그런 다음 규칙에 따라 하나 이상의 작업이 자동으로 실행되어 경고를 미리 알리거나 다른 프로세스를 호출할 수 있습니다.   

이 문서에서는 OMS 포털을 사용하여 경고 규칙을 만들고 편집하는 프로세스를 설명합니다.  다양한 설정 및 필요한 논리를 구현하는 방법에 대한 자세한 내용은 [Log Analytics의 경고 이해](log-analytics-alerts.md)를 참조하세요.


## <a name="create-an-alert-rule"></a>경고 규칙 만들기

OMS 포털을 사용하여 경고 규칙을 만들려면 경고를 호출해야 하는 레코드에 대한 로그 검색을 만드는 것으로 시작합니다.  그러면 **경고** 단추를 사용할 수 있으며 이 단추를 사용하여 경고 규칙을 만들고 구성할 수 있습니다.

>[!NOTE]
> 현재 Log Analytics 작업 영역에서 최대 250개의 경고 규칙을 만들 수 있습니다. 

1. OMS 개요 페이지에서 **로그 검색**을 클릭합니다.
2. 새 로그 검색 쿼리를 만들거나 저장된 로그 검색을 선택합니다. 
3. 페이지 위쪽에서 **경고**를 클릭하여 **경고 규칙 추가** 화면을 엽니다.
4. 아래 [경고 규칙의 세부 정보](#details-of-alert-rules)의 정보를 사용하여 경고 규칙을 구성합니다.
6. **저장** 을 클릭하여 경고 규칙을 완료합니다.  실행을 즉시 시작합니다.


## <a name="edit-an-alert-rule"></a>경고 규칙 편집
Log Analytics **설정**의 **경고** 메뉴에서 모든 경고 규칙의 목록을 가져올 수 있습니다.  

![경고 관리](./media/log-analytics-alerts/configure.png)

1. OMS 콘솔에서 **설정** 타일을 선택합니다.
2. **경고**를 선택합니다.

이 뷰에서 여러 작업을 수행할 수 있습니다.

* 옆에 있는 **끄기** 를 선택하여 규칙을 사용하지 않도록 설정합니다.
* 옆에 있는 연필 아이콘을 클릭하여 경고 규칙을 편집합니다.
* 옆에 있는 **X** 아이콘을 클릭하여 경고 규칙을 제거합니다. 

## <a name="details-of-alert-rules"></a>경고 규칙의 세부 정보
OMS 포털에서 경고 규칙을 만들거나 편집할 때 **경고 규칙 추가** 또는 **경고 규칙 편집** 페이지로 작업합니다.  다음 표는 이 화면의 필드를 설명합니다.

![경고 규칙](media/log-analytics-alerts/add-alert-rule.png)

### <a name="alert-information"></a>경고 정보
이는 경고 규칙에 대한 기본 설정 및 생성한 경고입니다.

| 자산 | 설명 |
|:--- |:---|
| Name | 경고 규칙을 식별하는 고유 이름입니다. 이 이름은 규칙에 의해 생성된 모든 경고에 포함됩니다.  |
| 설명 | 경고 규칙에 대한 선택적 설명입니다. |
| 심각도 |이 규칙에 의해 생성된 모든 경고의 심각도입니다. |

### <a name="search-query-and-time-window"></a>검색 쿼리 및 기간
경고를 만들어야 하는지 결정하도록 평가되는 레코드를 반환하는 검색 쿼리 및 기간입니다.

| 자산 | 설명 |
|:--- |:---|
| 검색 쿼리 | 실행될 쿼리입니다.  이 쿼리에서 반환된 레코드는 경고를 생성할지 여부를 결정하는 데 사용됩니다.<br><br>**현재 검색 쿼리 사용** 을 선택하여 현재 쿼리를 사용하거나 목록에서 기존 저장된 검색을 선택합니다.  쿼리 구문이 텍스트 상자에 제공되며 필요하면 텍스트 상자의 내용을 수정할 수 있습니다. |
| 기간 |쿼리에 대한 시간 범위를 지정합니다.  쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다.  이는 5 분에서 24 시간 사이의 임의 값일 수 있습니다.  이는 경고 빈도보다 크거나 같아야 합니다.  <br><br> 예를 들어 기간을 60 분으로 설정했는데 오후 1시 15분에 쿼리를 실행하면 오후 12시 15분부터 오후 1시 15분 사이의 레코드만 반환됩니다. |

경고 규칙에 대한 기간을 제공하는 경우 해당 기간에 대한 검색 조건과 일치하는 기존 레코드 수가 표시됩니다.  이 정보는 예상하는 결과 수를 제공할 빈도를 결정하는 데 도움이 될 수 있습니다.

### <a name="schedule"></a>일정
검색 쿼리를 실행하는 빈도를 정의합니다.

| 자산 | 설명 |
|:--- |:---|
| 경고 빈도 | 쿼리를 실행해야 하는 빈도를 지정합니다. 5 분에서 24 시간 사이의 임의 값일 수 있습니다. 기간보다 작거나 같아야 합니다.  값이 기간보다 큰 경우 레코드가 누락될 위험이 있습니다.<br><br>예를 들어 30분의 기간과 60분의 빈도를 사용하는 것이 좋습니다.  쿼리가 1:00에 실행되면 오후 12:30 및 1:00 사이의 레코드를 반환합니다.  1:30 및 2:00 사이의 레코드를 반환하게 된다면 다음으로 쿼리가 실행되는 시간은 2:00입니다.  1:00 및 1:30 사이에 생성된 레코드는 평가되지 않습니다. |


### <a name="generate-alert-based-on"></a>경고 생성 조건
검색 쿼리 결과에 대해 평가할 조건을 정의하여 경고를 만들어야 하는지 결정합니다.  이러한 세부 정보는 선택하는 경고 규칙의 유형에 따라 달라집니다.  [Log Analytics의 경고 이해](log-analytics-alerts.md)에서 다양한 경고 규칙 유형에 대한 세부 정보를 얻을 수 있습니다.

| 자산 | 설명 |
|:--- |:---|
| 알림 표시 안 함 | 경고 규칙 표시 안 함 기능을 켜면 규칙에 대한 작업이 새 경고를 만든 후 정의된 기간 동안 비활성화됩니다. 규칙은 여전히 실행되고 있으며 조건을 만족하면 경고 레코드를 생성합니다. 이 기능을 사용하여 중복 작업을 실행하지 않고 문제를 해결할 시간 여유를 갖게 됩니다. |

#### <a name="number-of-results-alert-rules"></a>결과 수 경고 규칙

| 자산 | 설명 |
|:--- |:---|
| 결과의 수 |쿼리에서 반환된 레코드의 수가 제공한 값**보다 큰** 또는 값**보다 작은** 경우 경고가 생성됩니다.  |

#### <a name="metric-measurement-alert-rules"></a>미터법 경고 규칙

| 자산 | 설명 |
|:--- |:---|
| 집계 값 | 결과의 각 집계 값이 위반으로 간주되기 위해 초과해야 하는 임계값입니다. |
| 경고 트리거 조건 | 만들려는 경고를 만들기 위한 위반 횟수입니다.  결과 집합에서 모든 위반의 조합에 대해 **총 위반**을 지정하거나 **연속 위반**을 지정하여 연속된 샘플에서 위반이 발생해야 한다고 요구할 수 있습니다. |

### <a name="actions"></a>작업
경고 규칙은 임계값이 충족되는 경우 항상 [경고 레코드](#alert-records)를 만듭니다.  또한 전자 메일 보내기 또는 Runbook 시작과 같은 하나 이상의 응답을 실행하도록 정의할 수 있습니다.



#### <a name="email-actions"></a>전자 메일 작업
전자 메일 작업은 한 명 이상의 수신자에게 경고의 세부 정보가 포함된 전자 메일을 보냅니다.

| 자산 | 설명 |
|:--- |:---|
| 메일 알림 |경고가 트리거될 때 전자 메일을 보내려면 **예** 를 지정합니다. |
| 제목 |전자 메일의 제목입니다.  메일의 본문을 수정할 수 없습니다. |
| 받는 사람 |모든 전자 메일 받는 사람의 주소입니다.  둘 이상의 주소를 지정하는 경우 주소를 세미콜론(;)으로 구분합니다. |

#### <a name="webhook-actions"></a>웹후크 작업
웹후크 작업을 사용하여 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다.

| 자산 | 설명 |
|:--- |:---|
| 웹후크 |경고가 트리거될 때 웹후크를 호출하려면 **예** 를 지정합니다. |
| Webhook URL |웹후크의 URL입니다. |
| 사용자 지정 JSON 페이로드 포함 |기본 페이로드를 사용자 지정 페이로드로 바꾸려면 이 옵션을 선택합니다. |
| 사용자 지정 JSON 페이로드 입력 |웹후크에 대한 사용자 지정 페이로드입니다.  자세한 내용은 이전 섹션을 참조하세요. |

#### <a name="runbook-actions"></a>Runbook 작업
Runbook 작업은 Azure Automation에서 Runbook을 시작합니다. 

>[!NOTE]
> 이 작업을 활성화하려면 작업 영역에 Automation 솔루션이 설치되어 있어야 합니다. 


| 자산 | 설명 |
|:--- |:---|
| Runbook | 경고가 트리거될 때 Azure Automation Runbook을 시작하려면 **예** 를 지정합니다.  |
| Automation 계정 | Runbook을 선택할 Automation 계정을 지정합니다.  작업 영역에 연결된 작업 계정입니다. |
| Runbook 선택 | 경고가 생성될 때 시작하려는 Runbook을 선택합니다. |
| 실행 | 클라우드에서 Runbook을 실행하려면 **Azure**를 선택합니다.  [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md )가 설치된 에이전트에서 Runbook을 실행할 **Hybrid Worker**를 선택합니다.  |




## <a name="next-steps"></a>다음 단계
* SCOM(System Center Operations Manager)에서 수집한 경고와 함께 Log Analytics에서 만든 경고를 분석하려면 [경고 관리 솔루션](log-analytics-solution-alert-management.md) 을 설치합니다.
* 경고를 생성할 수 있는 [로그 검색](log-analytics-log-searches.md) 에 대해 자세한 내용을 읽습니다.
* 경고 규칙을 사용하여 [웹후크를 구성](log-analytics-alerts-webhooks.md) 하는 연습을 완료합니다.  
* 경고에 의해 식별된 문제를 수정하는 [Azure Automation의 Runbook](https://azure.microsoft.com/documentation/services/automation) 을 작성하는 방법에 대해 알아봅니다.

