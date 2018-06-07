---
title: "Azure CDN의 실시간 통계 | Microsoft Docs"
description: "실시간 통계는 클라이언트에 콘텐츠를 제공하는 경우 Azure CDN의 성능에 대한 실시간 데이터를 제공합니다."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e9b9522de6b2c54dc794b00100ffe358296ecfdd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Microsoft Azure CDN의 실시간 통계
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>개요
이 문서에서는 Microsoft Azure CDN의 실시간 통계에 대해 설명합니다.  이 기능은 클라이언트에 콘텐츠를 제공하는 경우 대역폭, 캐시 상태 및 CDN 프로필에 대한 동시 연결과 같은 실시간 데이터를 제공합니다. 따라서 라이브 이벤트를 포함하여 언제든지 서비스의 상태를 계속 모니터링할 수 있습니다.

사용할 수 있는 그래프는 다음과 같습니다.

* [대역폭](#bandwidth)
* [상태 코드](#status-codes)
* [캐시 상태](#cache-statuses)
* [연결](#connections)

## <a name="accessing-real-time-stats"></a>실시간 통계에 액세스
1. [Azure 포털](https://portal.azure.com)에서 CDN 프로필로 이동합니다.
   
    ![CDN 프로필 블레이드](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. CDN 프로필 블레이드에서 **관리** 단추를 클릭합니다.
   
    ![CDN 프로필 블레이드 관리 단추](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    CDN 관리 포털이 열립니다.
3. **분석** 탭을 마우스로 가리킨 후 **실시간 통계** 플라이아웃을 마우스로 가리킵니다.  **HTTP 큰 개체**를 클릭합니다.
   
    ![CDN 관리 포털](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    실시간 통계 그래프가 표시됩니다.

페이지가 로드될 때 시작되는 그래프 각각은 선택한 시간 범위에 대한 실시간 통계를 표시합니다.  그래프는 몇 초 마다 자동으로 업데이트됩니다.  **그래프 새로 고침** 단추가 있는 경우 그래프를 지우고 이후 선택한 데이터만 표시됩니다.

## <a name="bandwidth"></a>대역폭
![대역폭 그래프](./media/cdn-real-time-stats/cdn-bandwidth.png)

**대역폭** 그래프는 선택한 시간 범위 동안 현재 플랫폼에 사용된 대역폭의 양을 표시합니다. 그래프의 음영 처리된 부분은 대역폭 사용량을 나타냅니다. 현재 사용 중인 대역폭의 정확한 양이 꺾은선형 그래프 바로 아래 표시됩니다.

## <a name="status-codes"></a>상태 코드
![상태 코드 그래프](./media/cdn-real-time-stats/cdn-status-codes.png)

**상태 코드** 그래프는 특정 HTTP 응답 코드가 선택한 시간 범위에 발생한 빈도를 나타냅니다.

> [!TIP]
> 각 HTTP 상태 코드 옵션에 대한 설명은 [Azure CDN HTTP 상태 코드](https://msdn.microsoft.com/library/mt759238.aspx)를 참조하세요.
> 
> 

HTTP 상태 코드의 목록은 그래프 바로 위에 표시됩니다. 이 목록은 꺾은선형 그래프에 포함할 수 있는 각 상태 코드와 해당 상태 코드에 대한 현재 초당 발생 수를 나타냅니다. 기본적으로 선은 그래프에서 이 상태 코드 각각에 대해 표시됩니다. 그러나 CDN 구성에 대해 특별한 의미가 있는 상태 코드만 모니터하도록 선택할 수 있습니다. 이렇게 하려면 원하는 상태 코드를 확인하고 다른 모든 옵션의 선택을 취소한 다음 **그래프 새로 고침**을 클릭합니다. 

특정 상태 코드에 대해 기록된 데이터를 임시로 숨길 수 있습니다.  그래프 바로 아래 범례에서 숨길 상태 코드를 클릭합니다. 상태 코드는 그래프에서 즉시 숨겨집니다. 해당 상태 코드를 다시 클릭하면 해당 옵션이 다시 표시됩니다.

## <a name="cache-statuses"></a>캐시 상태
![캐시 상태 그래프](./media/cdn-real-time-stats/cdn-cache-status.png)

**캐시 상태** 그래프는 특정 유형의 캐시 상태가 선택한 시간 범위에 발생한 빈도를 나타냅니다. 

> [!TIP]
> 각 캐시 상태 코드 옵션에 대한 설명은 [Azure CDN 캐시 상태 코드](https://msdn.microsoft.com/library/mt759237.aspx)를 참조하세요.
> 
> 

캐시 상태 코드 목록이 그래프 바로 위에 표시됩니다. 이 목록은 꺾은선형 그래프에 포함할 수 있는 각 상태 코드와 해당 상태 코드에 대한 현재 초당 발생 수를 나타냅니다. 기본적으로 선은 그래프에서 이 상태 코드 각각에 대해 표시됩니다. 그러나 CDN 구성에 대해 특별한 의미가 있는 상태 코드만 모니터하도록 선택할 수 있습니다. 이렇게 하려면 원하는 상태 코드를 확인하고 다른 모든 옵션의 선택을 취소한 다음 **그래프 새로 고침**을 클릭합니다. 

특정 상태 코드에 대해 기록된 데이터를 임시로 숨길 수 있습니다.  그래프 바로 아래 범례에서 숨길 상태 코드를 클릭합니다. 상태 코드는 그래프에서 즉시 숨겨집니다. 해당 상태 코드를 다시 클릭하면 해당 옵션이 다시 표시됩니다.

## <a name="connections"></a>연결
![연결 그래프](./media/cdn-real-time-stats/cdn-connections.png)

이 그래프는 에지 서버에 설정된 연결 수를 나타냅니다. CDN을 통과하는 자산에 대한 각 요청은 연결을 발생시킵니다.

## <a name="next-steps"></a>다음 단계
* [Azure CDN에서 실시간 경고](cdn-real-time-alerts.md)
* [고급 HTTP 보고서](cdn-advanced-http-reports.md)
* [사용 패턴](cdn-analyze-usage-patterns.md)

