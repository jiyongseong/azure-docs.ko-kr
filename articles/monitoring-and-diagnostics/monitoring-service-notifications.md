---
title: Azure Service Health 알림이란? | Microsoft Docs
description: 서비스 상태 알림을 통해 Microsoft Azure에서 게시한 서비스 상태 메시지를 볼 수 있습니다.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2017
ms.author: dukek
ms.openlocfilehash: 6821828d3e39a87b8c93f74e7e0583bf9fe1fe4a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Azure Portal을 사용하여 서비스 상태 알림 보기

서비스 상태 알림은 Azure에서 게시하며 구독 아래에 있는 리소스에 대한 정보를 포함합니다. 이러한 알림은 활동 로그 이벤트의 하위 클래스이며 활동 로그에서도 찾을 수 있습니다. 서비스 상태 알림은 클래스에 따라 정보만 제공하거나 실행할 수 있습니다.

다양한 서비스 상태 알림 클래스가 있습니다.  

- **작업 필요:** Azure에서는 계정에서 비정상적인 활동을 발견하고 이 문제를 해결하기 위해 사용자와 협력할 수 있습니다. Azure에서는 취해야 할 조치 또는 Azure 엔지니어링이나 지원에 문의하는 방법에 대한 자세한 설명과 함께 알림을 보내드립니다.  
- **복구 지원:** 이벤트가 발생하고 엔지니어가 여전히 해당 이벤트의 영향이 있음을 확인했습니다. Azure 엔지니어링에서 서비스를 정상상태로 복원하기 위해 사용자와 직접 작업해야 합니다.  
- **인시던트:** 서비스에 영향을 미치는 이벤트가 현재 구독에서 하나 이상의 리소스에 영향을 주고 있습니다.  
- **유지 관리:** 구독에서 하나 이상의 리소스에 영향을 줄 수 있는 계획된 유지 관리 작업입니다.  
- **정보:** 리소스 사용을 개선할 수 있는 잠재적인 최적화입니다. 
- **보안:** Azure에서 실행되는 솔루션에 대한 긴급한 보안 관련 정보입니다.

각 서비스 상태 알림은 범위 및 리소스에 대한 영향에 대한 세부 정보를 포함합니다. 세부 정보는 다음과 같습니다.

속성 이름 | 설명
-------- | -----------
channels | **관리자** 또는 **작업** 값 중 하나여야 합니다.
CorrelationId | 일반적으로 문자열 형식의 GUID입니다. 동일한 작업에 속하는 이벤트는 일반적으로 동일한 correlationId를 공유합니다.
eventDataId | 이벤트의 고유 식별자입니다.
eventName | 이벤트의 제목입니다.
level | 이벤트의 수준입니다.
resourceProviderName | 영향을 받는 리소스의 리소스 공급자 이름입니다.
resourceType| 영향을 받는 리소스의 리소스 형식입니다.
subStatus | 하지만 일반적으로 해당 REST 호출의 HTTP 상태 코드는 하위 상태를 설명하는 다른 문자열을 포함할 수도 있습니다. 예: 확인(HTTP 상태 코드: 200), 만듬 (HTTP 상태 코드: 201), 허용됨(HTTP 상태 코드: 202), 콘텐츠 없음(HTTP 상태 코드: 204), 잘못된 요청(HTTP 상태 코드: 400), 찾을 수 없음(HTTP 상태 코드: 404), 충돌(HTTP 상태 코드: 409), 내부 서버 오류(HTTP 상태 코드: 500), 서비스 사용할 수 없음(HTTP 상태 코드: 503) 및 게이트웨이 시간 제한(HTTP 상태 코드: 504)
eventTimestamp | 이벤트에 해당하는 요청을 처리하는 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다.
submissionTimestamp | 이벤트를 쿼리할 수 있게 되는 타임스탬프입니다.
subscriptionId | 이 이벤트가 로깅된 Azure 구독입니다.
status | 작업의 상태를 설명하는 문자열. 일반적인 값: **시작됨**, **진행 중**, **성공**, **Failed**, **활성** 및 **해결됨**
operationName | 작업의 이름입니다.
카테고리 | 이 속성은 항상 **ServiceHealth**입니다.
ResourceId | 영향을 받는 리소스의 리소스 ID입니다.
Properties.title | 이 통신에 대한 지역화된 제목입니다. 기본값은 영어입니다.
Properties.communication | HTML 태그와 통신에 대한 지역화된 세부 정보입니다. 기본값은 영어입니다.
Properties.incidentType | **ActionRequired**, **Information**, **Incident**, **Maintenance** 또는 **Security** 값 중 하나입니다.
Properties.trackingId | 이 이벤트가 연결된 인시던트입니다. 인시던트와 관련된 이벤트를 상호 연결할 때 사용합니다.
Properties.impactedServices | 인시던트에 의해 영향을 받는 서비스 및 지역을 설명하는 이스케이프된 JSON Blob입니다. 속성에는 각각 **ServiceName**이 포함된 서비스 목록 및 각각 **RegionName**이 포함된 영향 받는 지역 목록이 포함됩니다.
Properties.defaultLanguageTitle | 통신은 영어로 이루어집니다.
Properties.defaultLanguageContent | 영어로 통신은 HTML 태그 또는 일반 텍스트로 수행됩니다.
Properties.stage | **Incident**, 및 **Security**에 사용할 수 있는 값은 는 **Active**, **Resolved** 또는 **RCA**입니다. **ActionRequired** 또는 **Information**의 유일한 값은 **Active**입니다. **Maintenance**에 대해 가능한 값은 **Active**, **Planned**, **InProgress**, **Canceled**, **Rescheduled**, **Resolved** 또는 **Complete**입니다.
Properties.communicationId | 이 이벤트가 연결된 통신입니다.

### <a name="details-on-service-health-level-information"></a>서비스 상태 수준 정보에 대한 세부 정보
  <ul>
    <li><b>작업 필요</b>(properties.incidentType == ActionRequired) <dl>
            <dt>정보 제공</dt>
            <dd>기존 서비스에 미치는 영향을 방지하는 데 필요한 관리자 작업</dd>
        </dl>
    </li>
    <li><b>유지 관리</b>(properties.incidentType == Maintenance) <dl>
            <dt>경고</dt>
            <dd>응급 유지 관리<dd>
            <dt>정보 제공</dt>
            <dd>계획된 표준 유지 관리</dd>
        </dl>
    </li>
    <li><b>정보</b>(properties.incidentType == Information) <dl>
            <dt>정보 제공</dt>
            <dd>관리자는 기존 서비스에 미치는 영향을 방지해야 할 수 있습니다.</dd>
        </dl>
    </li>
    <li><b>보안</b>(properties.incidentType == Security) <dl>
            <dt>오류</dt>
            <dd>여러 지역의 여러 서비스에 액세스할 때 나타나는 다양한 문제가 광범위한 고객에게 영향을 미치고 있습니다.</dd>
            <dt>경고</dt>
            <dd>특정 서비스 및/또는 특정 지역에 액세스할 때 나타나는 문제가 일부 고객에게 영향을 미치고 있습니다.</dd>
            <dt>정보 제공</dt>
            <dd>서비스 가용성이 아닌, 관리 작업 및/또는 대기 시간에 영향을 미치는 문제</dd>
        </dl>
    </li>
    <li><b>서비스 문제</b>(properties.incidentType == Incident) <dl>
            <dt>오류</dt>
            <dd>여러 지역의 여러 서비스에 액세스할 때 나타나는 다양한 문제가 광범위한 고객에게 영향을 미치고 있습니다.</dd>
            <dt>경고</dt>
            <dd>특정 서비스 및/또는 특정 지역에 액세스할 때 나타나는 문제가 일부 고객에게 영향을 미치고 있습니다.</dd>
            <dt>정보 제공</dt>
            <dd>서비스 가용성이 아닌, 관리 작업 및/또는 대기 시간에 영향을 미치는 문제</dd>
        </dl>
    </li>
  </ul>

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal에서 서비스 상태 알림 보기
1.  [Azure Portal](https://portal.azure.com)에서 **모니터**를 선택합니다.

    ![모니터를 선택한 Azure Portal 메뉴 스크린샷](./media/monitoring-service-notifications/home-monitor.png)

    Azure Monitor는 모든 모니터링 설정과 데이터를 하나의 통합 보기로 모읍니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.  **경고**를 선택합니다.

    ![경고를 선택한 활동 로그 모니터 스크린샷](./media/monitoring-service-notifications/service-health-summary.png)
4. **+활동 로그 경고 추가**를 선택하고 알림을 구성하여 향후 서비스 알림을 받을 수 있습니다. 자세한 내용은 [서비스 알림에 대한 활동 로그 경고 만들기](monitoring-activity-log-alerts-on-service-notifications.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[서비스 상태 알림이 게시될 때마다 경고 알림](monitoring-activity-log-alerts-on-service-notifications.md)을 수신합니다.  
[활동 로그 경고](monitoring-activity-log-alerts.md)에 대해 자세히 알아봅니다.
