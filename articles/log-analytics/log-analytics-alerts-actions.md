---
title: Azure Log Analytics에서 경고에 대한 응답 | Microsoft Docs
description: Log Analytics의 경고는 Azure 작업 영역의 중요한 정보를 식별하며 문제를 미리 알리거나 작업을 호출하여 문제 해결을 시도합니다.  이 문서에서는 경고 규칙을 만드는 방법을 설명하고 규칙에서 실행할 수 있는 여러 가지 작업을 자세히 설명합니다.
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
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 717adf1b19b9de8542ec507df3a01b187d0df8a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="add-actions-to-alert-rules-in-log-analytics"></a>Log Analytics에서 경고 규칙에 작업 추가

> [!NOTE]
> Log Analytics의 경고가 [Azure로 확장](../monitoring-and-diagnostics/monitoring-alerts-extend.md)되고 있습니다.  Azure의 경고는 이 문서의 정보 대신 [작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)을 사용하여 해당 작업을 정의합니다.


[경고가 Log Analytics에서 생성](log-analytics-alerts.md)될 때 하나 이상의 작업을 수행하는 [경고 규칙 구성](log-analytics-alerts.md)의 옵션이 있습니다.  이 문서에서는 사용 가능한 다양한 작업 및 각 종류를 구성하는 세부 정보를 설명합니다.

| 조치 | 설명 |
|:--|:--|
| [Email](#email-actions) | 한 명 이상의 수신자에게 경고의 세부 정보가 포함된 전자 메일을 보냅니다. |
| [웹후크](#webhook-actions) | 단일 HTTP POST 요청을 통해 외부 프로세스를 호출합니다. |
| [Runbook](#runbook-actions) | Azure Automation에서 Runbook을 시작합니다. |


## <a name="email-actions"></a>전자 메일 작업
전자 메일 작업은 한 명 이상의 수신자에게 경고의 세부 정보가 포함된 전자 메일을 보냅니다.  메일의 제목을 지정할 수 있지만 그 내용은 Log Analytics에서 구성한 표준 형식입니다.  메일은 로그 검색에서 반환된 10개까지의 레코드에 대한 세부 정보 외에 경고의 이름과 같은 요약 정보를 포함하고 있습니다.  또한 쿼리에서 전체 레코드 집합을 반환할 Log Analytics의 로그 검색에 대한 링크를 포함하고 있습니다.   메일의 보낸 사람은 *Microsoft Operations Management Suite Team &lt;noreply@oms.microsoft.com&gt;* 입니다. 

전자 메일 작업에는 다음 표의 속성이 필요합니다.

| 자산 | 설명 |
|:--- |:--- |
| 제목 |전자 메일의 제목입니다.  메일의 본문을 수정할 수 없습니다. |
| 받는 사람 |모든 전자 메일 받는 사람의 주소입니다.  둘 이상의 주소를 지정하는 경우 주소를 세미콜론(;)으로 구분합니다. |


## <a name="webhook-actions"></a>웹후크 작업

웹후크 작업을 사용하여 단일 HTTP POST 요청을 통해 외부 프로세스를 호출할 수 있습니다.  호출 중인 서비스는 웹후크를 지원하고 수신하는 페이로드를 사용하는 방법을 결정해야 합니다.  또한 요청이 API에서 이해하는 형식으로 되어 있다면 웹후크를 명시적으로 지원하지 않는 REST API를 호출할 수도 있습니다.  경고에 대한 응답으로 웹후크를 사용하는 예는 [Slack](http://slack.com)에서 메시지를 보내거나 [PagerDuty](http://pagerduty.com/)에서 인시던트를 만드는 것입니다.  Slack을 호출하는 웹후크를 사용하여 경고 규칙을 생성하는 전체 연습은 [Log Analytics 경고의 웹후크](log-analytics-alerts-webhooks.md)에서 사용할 수 있습니다.

웹후크 작업에는 다음 표의 속성이 필요합니다.

| 자산 | 설명 |
|:--- |:--- |
| Webhook URL |웹후크의 URL입니다. |
| 사용자 지정 JSON 페이로드 |웹후크와 함께 보낼 사용자 지정 페이로드입니다.  자세한 내용은 다음을 참조하세요. |


웹후크는 URL 및 외부 서비스에 보낸 데이터인 JSON 형식의 페이로드를 포함하고 있습니다.  기본적으로 페이로드는 다음 표의 값을 포함합니다.  이 페이로드를 자신만의 사용자 지정 페이로드로 바꾸도록 선택할 수 있습니다.  그러한 경우 각 매개 변수에 대한 표의 변수를 사용하여 해당 값을 사용자 지정 페이로드에 포함할 수 있습니다.


| 매개 변수 | 변수 | 설명 |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |경고 규칙의 이름입니다. |
| AlertThresholdOperator |#thresholdoperator |경고 규칙에 대한 임계값 연산자입니다.  *보다 큼* 또는 *보다 작음*. |
| AlertThresholdValue |#thresholdvalue |경고 규칙에 대한 임계값입니다. |
| LinkToSearchResults |#linktosearchresults |경고를 생성한 쿼리에서 레코드를 반환하는 Log Analytics 로그 검색에 대한 링크입니다. |
| ResultCount |#searchresultcount |검색 결과의 레코드 수입니다. |
| SearchIntervalEndtimeUtc |#searchintervalendtimeutc |UTC 형식의 쿼리에 대한 종료 시간입니다. |
| SearchIntervalInSeconds |#searchinterval |경고 규칙에 대한 기간입니다. |
| SearchIntervalStartTimeUtc |#searchintervalstarttimeutc |UTC 형식의 쿼리에 대한 시작 시간입니다. |
| SearchQuery |#searchquery |경고 규칙에서 사용하는 로그 검색 쿼리입니다. |
| SearchResults |아래 참조 |JSON 형식의 쿼리에서 반환된 레코드입니다.  처음 5,000 개의 레코드로 제한됩니다. |
| WorkspaceID |#workspaceid |Log Analytics 작업 영역의 ID입니다. |

예를 들어 *text*라는 단일 매개변수를 포함하는 다음과 같은 사용자 지정 페이로드를 지정할 수 있습니다.  이 웹후크가 호출하는 서비스는 이 매개 변수를 예상합니다.

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }

이 예제 페이로드는 웹후크에 보낼 때 다음과 같은 내용으로 확인될 수 있습니다.

    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }

검색 결과를 사용자 지정 페이로드에 포함하려면 JSON 페이로드의 최상위 속성으로 다음 줄을 추가합니다.  

    "IncludeSearchResults":true

예를 들어 경고 이름과 검색 결과만 포함하는 사용자 지정 페이로드를 만들려면 다음을 사용할 수 있습니다. 

    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }


웹후크를 통해 경고 규칙을 생성하여 [Log Analytics에서 Slack에 메시지를 보내는 경고 웹후크 작업 만들기](log-analytics-alerts-webhooks.md)에서 외부 서비스를 시작하는 전체 예제를 연습할 수 있습니다.


## <a name="runbook-actions"></a>Runbook 작업
Runbook 작업은 Azure Automation에서 Runbook을 시작합니다.  이 유형의 작업을 사용하려면 Log Analytics 작업 영역에 [Automation 솔루션](log-analytics-add-solutions.md) 을 설치하고 구성해야 합니다.  Automation 솔루션에서 구성한 자동화 계정의 Runbook 중에서 선택할 수 있습니다.

Runbook 작업에는 다음 표의 속성이 필요합니다.

| 자산 | 설명 |
|:--- |:---|
| Runbook | 경고가 생성될 때 시작하려는 Runbook입니다. |
| 실행 | 클라우드에서 Runbook을 실행하려면 **Azure**를 지정합니다.  [Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md )가 설치된 에이전트에서 Runbook을 실행할 **Hybrid Worker**를 지정합니다.  |

Runbook 작업은 [웹후크](../automation/automation-webhooks.md)를 사용하여 Runbook을 시작합니다.  경고 규칙을 만들 때 이름 **OMS 경고 수정** 에 이어 GUID를 가진 Runbook에 대해 새 웹후크가 자동으로 생성됩니다.  

Runbook의 매개 변수를 직접 채울 수는 없으나 [$WebhookData 매개 변수](../automation/automation-webhooks.md)는 해당 경고를 생성한 로그 검색의 결과를 포함한 경고의 세부 정보를 포함합니다.  Runbook이 경고의 속성에 액세스하려면 **$WebhookData**를 매개 변수로 정의해야 합니다.  경고 데이터는 **$WebhookData**의 **RequestBody** 속성에 있는 **SearchResult**(표준 페이로드를 사용한 Runbook 작업 및 webhook 작업) 또는 **SearchResults**(**IncludeSearchResults":true**를 포함하여 사용자 지정 페이로드를 사용한 webhook 작업)라는 단일 속성에 JSON 형식으로 제공됩니다.  이 항목은 다음 표의 속성을 가집니다.

>[!NOTE]
> 작업 영역을 [새 Log Analytics 쿼리 언어](log-analytics-log-search-upgrade.md)로 업그레이드한 경우에는 Runbook 페이로드가 변경됩니다.  형식의 세부 내용은 [Azure Log Analytics REST API](https://aka.ms/loganalyticsapiresponse)에 있습니다.  아래 [샘플](#sample-payload)에서 예를 볼 수 있습니다.  

| 노드 | 설명 |
|:--- |:--- |
| id |경로와, 검색의 GUID입니다. |
| __metadata |레코드 수 및 검색 결과 상태를 포함하는 경고 관련 정보입니다. |
| 값 |검색 결과의 각 레코드에 대해 개별 항목입니다.  항목의 세부 정보는 레코드의 속성 및 값과 일치합니다. |

예를 들어 다음 Runbook은 로그 검색에서 반환된 레코드를 추출하고 각 레코드 유형에 따라 서로 다른 속성을 할당합니다.  Runbook은 JSON에서 **RequestBody**를 변환하여 시작되므로 PowerShell의 개체로 작동할 수 있습니다.

>[!NOTE]
> 이 Runbook은 모두 표준 페이로드를 사용한 Runbook 작업 및 webhook 작업의 결과를 포함하는 속성인 **SearchResult**를 사용합니다.  사용자 지정 페이로드를 사용하여 webhook 응답에서 Runbook이 호출된 경우 이 속성을 **SearchResults**로 변경해야 합니다.

    param ( 
        [object]$WebhookData
    )

    $RequestBody = ConvertFrom-JSON -InputObject $WebhookData.RequestBody

    # Get all metadata properties    
    $AlertRuleName = $RequestBody.AlertRuleName
    $AlertThresholdOperator = $RequestBody.AlertThresholdOperator
    $AlertThresholdValue = $RequestBody.AlertThresholdValue
    $AlertDescription = $RequestBody.Description
    $LinktoSearchResults =$RequestBody.LinkToSearchResults
    $ResultCount =$RequestBody.ResultCount
    $Severity = $RequestBody.Severity
    $SearchQuery = $RequestBody.SearchQuery
    $WorkspaceID = $RequestBody.WorkspaceId
    $SearchWindowStartTime = $RequestBody.SearchIntervalStartTimeUtc
    $SearchWindowEndTime = $RequestBody.SearchIntervalEndtimeUtc
    $SearchWindowInterval = $RequestBody.SearchIntervalInSeconds

    # Get detailed search results
    if($RequestBody.SearchResult -ne $null)
    {
        $SearchResultRows    = $RequestBody.SearchResult.tables[0].rows 
        $SearchResultColumns = $RequestBody.SearchResult.tables[0].columns;

        foreach ($SearchResultRow in $SearchResultRows)
        {   
            $Column = 0
            $Record = New-Object –TypeName PSObject 
        
            foreach ($SearchResultColumn in $SearchResultColumns)
            {
                $Name = $SearchResultColumn.name
                $ColumnValue = $SearchResultRow[$Column]
                $Record | Add-Member –MemberType NoteProperty –Name $name –Value $ColumnValue -Force
                        
                $Column++
            }

            # Include code to work with the record. 
            # For example $Record.Computer to get the computer property from the record.
            
        }
    }



## <a name="sample-payload"></a>샘플 페이로드
이 섹션에서는 웹후크와 Runbook 작업에 대한 샘플 페이로드를 살펴봅니다.

### <a name="webhook-actions"></a>웹후크 작업
이 예제는 모두 표준 페이로드를 사용한 웹후크 작업의 결과를 포함하는 속성인 **SearchResult**를 사용합니다.  webhook가 검색 결과를 포함하는 사용자 지정 페이로드를 사용한 경우 이 속성은 **SearchResults**가 됩니다.

다음은 웹후크 작업에 대한 샘플 페이로드입니다.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "WebhookAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "workspaceID",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
                "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-workspaceID",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Low"
    }


### <a name="runbooks"></a>Runbook

다음은 Runbook 작업에 대한 샘플 페이로드입니다.

    {
    "WorkspaceId": "workspaceID",
    "AlertRuleName": "AutomationAlert",
    "SearchQuery": "Usage",
    "SearchResult": {
        "tables": [
        {
            "name": "PrimaryResult",
            "columns": [
            {
                "name": "TenantId",
                "type": "string"
            },
            {
                "name": "Computer",
                "type": "string"
            },
            {
                "name": "TimeGenerated",
                "type": "datetime"
            },
            {
                "name": "SourceSystem",
                "type": "string"
            },
            {
                "name": "StartTime",
                "type": "datetime"
            },
            {
                "name": "EndTime",
                "type": "datetime"
            },
            {
                "name": "ResourceUri",
                "type": "string"
            },
            {
                "name": "LinkedResourceUri",
                "type": "string"
            },
            {
                "name": "DataType",
                "type": "string"
            },
            {
                "name": "Solution",
                "type": "string"
            },
            {
                "name": "BatchesWithinSla",
                "type": "long"
            },
            {
                "name": "BatchesOutsideSla",
                "type": "long"
            },
            {
                "name": "BatchesCapped",
                "type": "long"
            },
            {
                "name": "TotalBatches",
                "type": "long"
            },
            {
                "name": "AvgLatencyInSeconds",
                "type": "real"
            },
            {
                "name": "Quantity",
                "type": "real"
            },
            {
                "name": "QuantityUnit",
                "type": "string"
            },
            {
                "name": "IsBillable",
                "type": "bool"
            },
            {
                "name": "MeterId",
                "type": "string"
            },
            {
                "name": "LinkedMeterId",
                "type": "string"
            },
            {
                "name": "Type",
                "type": "string"
            }
            ],
            "rows": [
            [
                "861bd466-5400-44be-9552-5ba40823c3aa",
                "-",
                "2017-09-26T13:59:59Z",
                "OMS",
                "2017-09-26T13:00:00Z",
                "2017-09-26T13:59:59Z",
            "/subscriptions/SubscriptionID/resourcegroups/ResourceGroupName/providers/microsoft.operationalinsights/workspaces/workspace-861bd466-5400-44be-9552-5ba40823c3aa",
                null,
                "Operation",
                "LogManagement",
                8,
                0,
                0,
                8,
                0,
                0.002502,
                "MBytes",
                false,
                "a4e29a95-5b4c-408b-80e3-113f9410566e",
                "00000000-0000-0000-0000-000000000000",
                "Usage"
            ]
            ]
        }
        ]
    },
    "SearchIntervalStartTimeUtc": "2017-09-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2017-09-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 1,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2017-09-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Critical"
    }



## <a name="next-steps"></a>다음 단계
- 경고 규칙을 사용하여 [웹후크를 구성](log-analytics-alerts-webhooks.md) 하는 연습을 완료합니다.  
- 경고에 의해 식별된 문제를 수정하는 [Azure Automation의 Runbook](https://azure.microsoft.com/documentation/services/automation) 을 작성하는 방법에 대해 알아봅니다.
