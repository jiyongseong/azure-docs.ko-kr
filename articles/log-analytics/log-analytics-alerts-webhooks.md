---
title: "OMS Log Analytics에서 웹후크 경고 작업 샘플 | Microsoft Docs"
description: "Log Analytics 경고에 응답하여 실행할 수 있는 작업 중 하나는 단일 HTTP 요청을 통해 외부 프로세스를 호출할 수 있는 *웹후크*입니다. 이 문서에서는 Slack을 사용하여 Log Analytics 경고에 웹후크 작업을 만드는 예제를 연습합니다."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 13c39f0f-fd3c-472d-8324-ddf7538be45e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: bwren
ms.openlocfilehash: 55b66132f7ec5c26c0a7cac1ec0a5c403dbd1082
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-alert-webhook-action-in-oms-log-analytics-to-send-message-to-slack"></a>OMS Log Analytics에서 Slack에 메시지를 보내는 경고 웹후크 작업 만들기
[Log Analytics 경고](log-analytics-alerts.md) 에 응답하여 실행할 수 있는 작업 중 하나는 단일 HTTP 요청을 통해 외부 프로세스를 호출할 수 있는 *웹후크*입니다.  [Log Analytics의 경고](log-analytics-alerts.md)

이 문서에서는 메시지 서비스인 Slack을 사용하여 Log Analytics 경고에 웹후크 작업을 만드는 예를 연습합니다.

> [!NOTE]
> 이 샘플을 완료하려면 Slack 계정이 있어야 합니다.  [slack.com](http://slack.com)에서 무료 계정을 등록할 수 있습니다.
> 
> 

## <a name="step-1---enable-webhooks-in-slack"></a>1단계 - Slack에서 웹후크를 사용하도록 설정
1. [slack.com](http://slack.com)에서 Slack에 로그인합니다.
2. 왼쪽 창의 **채널** 섹션에서 채널을 선택합니다.  메시지를 받게 될 채널입니다.  **일반** 또는 **임의**와 같은 기본 채널 중 하나를 선택할 수 있습니다.  프로덕션 시나리오에서는 **criticalservicealerts**와 같은 특수 채널을 만들 가능성이 높습니다. <br>
   
   ![Slack 채널](media/log-analytics-alerts-webhooks/oms-webhooks01.png)
3. **앱 또는 사용자 지정 통합 추가** 를 클릭하여 앱 디렉터리를 엽니다.
4. 검색 상자에 *웹후크* 를 입력하고 **들어오는 웹후크**를 선택합니다. <br>
   
   ![Slack 채널](media/log-analytics-alerts-webhooks/oms-webhooks02.png)
5. 팀 이름 옆의 **설치** 를 클릭합니다.
6. **구성 추가**를 클릭합니다.
7. 이 예제에 사용할 채널을 선택한 다음 **들어오는 웹후크 통합 추가**를 클릭합니다.  
8. **웹후크 URL**을 복사합니다.  복사한 내용을 경고 구성에 붙여넣습니다. <br>
   
    ![Slack 채널](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>2 단계 - Log Analytics에서 경고 규칙 만들기
1. [경고 규칙을 만듭니다](log-analytics-alerts.md) .
   * 쿼리: ```    Type=Event EventLevelName=error ```
   * 이 경고 확인 간격: 5 분
   * 결과 수: 10보다 큼
   * 이 기간 동안: 60 분
   * **웹후크**에 대해 **예**를 선택하고 다른 작업에 대해 **아니오**를 선택합니다.
2. **웹후크 URL** 필드에 Slack URL을 붙여넣습니다.
3. **사용자 지정 JSON 페이로드를 포함할**옵션을 선택합니다.
4. Slack은 *text*라는 매개 변수를 사용하여 JSON 형식으로 설정된 페이로드를 예상합니다.  이는 생성하는 메시지에 표시할 텍스트입니다.  다음 예와 같이 *#* 기호를 사용하여 경고 매개 변수 중 하나 이상을 사용할 수 있습니다.
   
    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```
   
    ![예제 JSON 페이로드](media/log-analytics-alerts-webhooks/oms-webhooks07.png)
5. **저장** 을 클릭하여 경고 규칙을 저장합니다.
6. 경고가 생성될 때까지 충분한 시간 동안 기다린 다음 다음과 유사한 메시지에 대해 Slack을 확인합니다.
   
   ![Slack의 예제 웹후크](media/log-analytics-alerts-webhooks/oms-webhooks08.png)

### <a name="advanced-webhook-payload-for-slack"></a>Slack을 위한 고급 웹후크 페이로드
Slack을 사용하여 인바운드 메시지를 광범위하게 사용자 지정할 수 있습니다. 자세한 내용은 Slack 웹 사이트의 [들어오는 웹후크](https://api.slack.com/incoming-webhooks) 를 참조하세요. 다음은 서식을 가진 풍부한 메시지를 만들기 위한 더 복잡한 페이로드입니다.

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


이는 Slack에 다음과 유사한 메시지를 생성합니다.

![Slack의 예제 메시지](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>요약
이 경고 규칙이 적용되는 상태에서 조건을 만족할 때마다 메시지를 Slack에 보내도록 할 수 있습니다.  

이는 경고에 응답하여 만들 수 있는 작업의 한 예일 뿐입니다.  다른 외부 서비스를 호출하는 웹후크 작업, Azure 자동화에서 Runbook을 시작하는 Runbook 작업 또는 본인과 다른 받는 사람에게 메일을 보내는 전자 메일 작업을 만들 수 있습니다.   

## <a name="next-steps"></a>다음 단계
* 다른 작업을 포함하여 [Log Analytics의 경고 작업](log-analytics-alerts-actions.md)에 관하여 알아봅니다.


