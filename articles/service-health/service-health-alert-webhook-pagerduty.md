---
title: PagerDuty를 사용하여 Azure 서비스 상태 경고 구성 | Microsoft Docs
description: 서비스 상태 이벤트에 대한 개인 설정 알림을 PagerDuty 인스턴스로 가져옵니다.
author: shawntabrizi
manager: scotthit
editor: ''
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 6e9fcf20d368e270f9af4551c539acd873335498
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>PagerDuty를 사용하여 서비스 상태 경고 구성

이 문서에서는 웹후크를 사용하여 PagerDuty를 통해 Azure 서비스 상태 알림을 설정하는 방법을 보여 줍니다. [PagerDuty](https://www.pagerduty.com/)의 사용자 지정 Microsoft Azure 통합 형식을 사용하여 기존 또는 새 PagerDuty 서비스에 서비스 상태 경고를 손쉽게 추가할 수 있습니다.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>PagerDuty에서 서비스 상태 통합 URL 만들기
1.  [PagerDuty](https://www.pagerduty.com/) 계정에 등록하고 로그인했는지 확인합니다.

2.  PagerDuty에서 **서비스** 섹션으로 이동합니다.

    ![PagerDuty에서 "서비스" 섹션](./media/webhook-alerts/pagerduty-services-section.png)

3.  **새 서비스 추가**를 선택하거나 설정한 기존 서비스를 엽니다.

4.  **통합 설정**에서 다음을 선택합니다.

    a. **통합 형식**: Microsoft Azure

    나. **통합 이름**: \<이름\>

    ![PagerDuty에서 "통합 설정"](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  다른 필수 필드를 작성하고 **추가**를 선택합니다.

6.  이 새 통합을 열고 **통합 URL**을 복사 및 저장합니다.

    ![PagerDuty에서 "통합 URL"](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Azure Portal에서 PagerDuty를 사용하여 경고 만들기
### <a name="for-a-new-action-group"></a>새 작업 그룹의 경우:
1. [Azure Portal을 사용하여 새 작업 그룹에 대한 서비스 상태 알림의 경고 만들기](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)에서 1단계부터 8단계까지 수행합니다.

2. **작업** 목록에서 다음을 정의합니다.

    a. **작업 유형:** *웹후크*

    나. **세부 정보:** 이전에 저장한 PagerDuty **통합 URL**입니다.

    다. **이름:** 웹후크의 이름, 별칭 또는 식별자입니다.

3. 경고 만들기가 완료되면 **저장**을 선택합니다.

### <a name="for-an-existing-action-group"></a>기존 작업 그룹의 경우:
1. [Azure Portal](https://portal.azure.com/)에서 **모니터**를 선택합니다.

2. **설정** 섹션에서 **작업 그룹**을 선택합니다.

3. 편집하려는 작업 그룹을 찾아 선택합니다.

4. **작업** 목록에 다음을 추가합니다.

    a. **작업 유형:** *웹후크*

    나. **세부 정보:** 이전에 저장한 PagerDuty **통합 URL**입니다.

    다. **이름:** 웹후크의 이름, 별칭 또는 식별자입니다.

5. 작업 그룹 업데이트가 완료되면 **저장**을 선택합니다.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 웹후크 통합 테스트
1. 보낼 서비스 상태 페이로드를 만듭니다. [Azure 활동 로그 경고에 대한 웹후크](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)에서 예제 서비스 상태 웹후크 페이로드를 찾을 수 있습니다.

2. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. "이벤트 ID"가 포함된 메시지와 함께 `202 Accepted`가 표시됩니다.

4. [PagerDuty](https://www.pagerduty.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [활동 로그 경고 웹후크 스키마](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)를 검토하세요. 
- [서비스 상태 알림](../monitoring-and-diagnostics/monitoring-service-notifications.md)에 대해 자세히 알아보세요.
- [작업 그룹](../monitoring-and-diagnostics/monitoring-action-groups.md)에 대해 자세히 알아보세요.