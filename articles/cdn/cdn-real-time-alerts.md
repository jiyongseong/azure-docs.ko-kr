---
title: "Azure CDN 실시간 경고 | Microsoft Docs"
description: "Microsoft Azure CDN의 실시간 경고입니다. 실시간 경고는 CDN 프로필에서 끝점의 성능에 대한 알림을 제공합니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 51dce1680be5f5f4387c2ba02827195bcdbe9b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Microsoft Azure CDN의 실시간 경고
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>개요
이 문서에서는 Microsoft Azure CDN의 실시간 경고에 대해 설명합니다. 이 기능은 CDN 프로필에서 끝점의 성능에 대한 실시간 알림을 제공합니다.  다음을 기반으로 전자 메일 또는 HTTP 경고를 설정할 수 있습니다.

* 대역폭
* 상태 코드
* 캐시 상태
* 연결

## <a name="creating-a-real-time-alert"></a>실시간 경고 만들기
1. [Azure Portal](https://portal.azure.com)에서 CDN 프로필로 이동합니다.
   
    ![CDN 프로필](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 관리 단추](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
3. **분석** 탭을 마우스로 가리킨 후 **실시간 통계** 플라이아웃을 마우스로 가리킵니다.  **실시간 경고**를 클릭합니다.
   
    ![CDN 관리 포털](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    기존 경고 구성(있는 경우)의 목록이 표시됩니다.
4. **경고 추가** 단추를 클릭합니다.
   
    ![경고 추가 단추](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    새 경고를 만드는 양식이 표시됩니다.
   
    ![새 경고 양식](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. **저장**을 클릭할 때 이 경고를 활성화하려면 **경고 사용** 확인란을 선택합니다.
6. **이름** 필드에 경고에 대한 설명이 포함된 이름을 입력합니다.
7. **미디어 유형** 드롭다운에서 **HTTP LOB(Large Object)**를 선택합니다.
   
    ![HTTP LOB(Large Object)를 선택한 미디어 유형](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > **HTTP LOB(Large Object)**를 **미디어 형식**으로 선택해야 합니다.  다른 옵션은 **Verizon의 Azure CDN**에서 사용하지 않습니다.  **HTTP LOB(Large Object)**를 선택하지 않으면 경고가 절대로 트리거되지 않게 됩니다.
   > 
   > 
8. **메트릭**, **연산자** 및 **트리거 값**을 선택하여 모니터링하기 위해 **식**을 만듭니다.
   
   * **메트릭**의 경우 모니터링하려는 조건의 유형을 선택합니다.  **대역폭 Mbps** 는 초당 메가비트인 대역폭 양입니다.  **총 연결** 은 에지 서버에 대한 동시 HTTP 연결의 수입니다.  다양한 캐시 상태 및 상태 코드의 정의는 [Azure CDN 캐시 상태 코드](https://msdn.microsoft.com/library/mt759237.aspx) 및 [Azure CDN HTTP 상태 코드](https://msdn.microsoft.com/library/mt759238.aspx)를 참조하세요.
   * **연산자** 는 메트릭과 트리거 값 간의 관계를 설정하는 수학 연산자입니다.
   * **트리거 값**은 알림이 전송되기 전에 충족해야 하는 임계값입니다.
     
     다음 예제에서 만들어진 식이 404 상태 코드의 수가 25보다 큰 경우 알림이 전송됨을 나타냅니다.
     
     ![실시간 경고 샘플 식](./media/cdn-real-time-alerts/cdn-expression.png)
9. **간격**에 식을 평가하려는 빈도를 입력합니다.
10. **알림 대상** 드롭다운에서 식이 true일 떼 알림을 받으려는 경우를 선택합니다.
    
    * **조건 시작**은 지정된 조건이 처음 감지될 때 알림이 전송되지 않는다는 것을 나타냅니다.
    * **조건 종료**는 지정된 조건이 더 이상 감지되지 않을 때 알림이 전송되지 않는다는 것을 나타냅니다. 네트워크 모니터링 시스템이 지정된 조건이 발생했음을 감지한 후에만 이 알림이 트리거될 수 있습니다.
    * **연속**은 네트워크 모니터링 시스템이 지정된 조건을 감지할 때마다 알림이 전송된다는 것을 나타냅니다. 네트워크 모니터링 시스템은 지정된 조건의 간격으로만 검사를 수행합니다.
    * **조건 시작 및 종료**는 처음으로 지정된 상태가 감지된 때와 조건이 더 이상 감지되지 않을 때 다시 한 번 알림을 전송한다는 것을 나타냅니다.
1. 전자 메일로 알림을 수신하려는 경우 **전자 메일 알림** 확인란을 선택합니다.  
    
    ![전자 메일로 알림 양식](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    **받는 사람** 필드에 알림을 전송하려는 전자 메일 주소를 입력합니다. **제목** 및 **본문**에 기본값을 그대로 두거나 메시지를 보낼 때 동적으로 경고 데이터를 삽입하려면 **사용할 수 있는 키워드** 목록을 사용하여 메시지를 사용자 지정할 수 있습니다.
    
    > [!NOTE]
    > **테스트 알림** 단추를 클릭하여 전자 메일 알림을 테스트할 수 있지만 경고 구성을 저장한 후에 가능합니다.
    > 
    > 
12. 웹 서버에 알림을 게시하려면 **HTTP 게시로 알림** 확인란을 선택합니다.
    
    ![HTTP 게시로 알림 양식](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    **URL** 필드에 HTTP 메시지를 게시할 URL을 입력합니다. **헤더** 텍스트 상자에 요청에서 보낼 HTTP 헤더를 입력합니다.  **본문**에 메시지를 보낼 때 동적으로 경고 데이터를 삽입하려면 **사용할 수 있는 키워드** 목록을 사용하여 메시지를 사용자 지정할 수 있습니다.  **헤더** 및 **본문**은 다음 예제와 유사한 XML 페이로드에 대한 기본값입니다.
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > **테스트 알림** 단추를 클릭하여 HTTP 게시 알림을 테스트할 수 있지만 경고 구성을 저장한 후에 가능합니다.
    > 
    > 
13. **저장** 단추를 클릭하여 경고 구성을 저장합니다.  5단계에서 **경고 사용** 을 선택한 경우 경고는 지금 활성화됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure CDN의 실시간 통계](cdn-real-time-stats.md)
* [고급 HTTP 보고서](cdn-advanced-http-reports.md)
* [사용 패턴](cdn-analyze-usage-patterns.md)

