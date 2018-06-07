---
title: Azure Portal에서 Azure Scheduler 시작 | Microsoft Docs
description: Azure Portal에서 Azure Scheduler 시작
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: f03ddb475835b30e9b931b7f057c062b57ac45f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Azure Portal에서 Azure Scheduler 시작
Azure Scheduler에서 예약된 작업을 만드는 것은 쉽습니다. 이 자습서에서는 작업을 만드는 방법을 알아봅니다. Scheduler의 모니터링 및 관리 기능도 알아봅니다.

## <a name="create-a-job"></a>작업 만들기
1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.  
2. **+새로 만들기**를 클릭하고 > 검색 상자에 *Scheduler*를 입력하고 > 결과에서 **Scheduler**를 선택하고 > **만들기**를 클릭합니다.
   
    ![][marketplace-create]
3. 간단히 GET 요청으로 http://www.microsoft.com/을 히트하는 작업을 만들어 보겠습니다. **Scheduler 작업** 화면에서 다음 정보를 입력합니다.
   
   1. **이름:** `getmicrosoft`  
   2. **구독:** Azure 구독입니다.   
   3. **작업 컬렉션:** 기존 작업 컬렉션을 선택하거나 **새로 만들기**를 클릭하고 > 이름을 입력합니다.
4. 다음으로 **작업 설정**에서 다음 값을 정의합니다.
   
   1. **작업 유형:** ` HTTP`  
   2. **메서드:** `GET`  
   3. **URL:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. 마지막으로, 일정을 정의해 보겠습니다. 작업은 일회성 작업으로 정의할 수 있지만 되풀이 일정을 선택해 보겠습니다.
   
   1. **되풀이**: `Recurring`
   2. **시작**: 오늘 날짜
   3. **되풀이 간격**: `12 Hours`
   4. **종료 날짜**: 오늘 날짜부터 이틀 후  
      
      ![][recurrence-schedule]
6. **만들기**

## <a name="manage-and-monitor-jobs"></a>작업 관리 및 모니터링
작업이 만들어지면 기본 Azure 대시보드에 표시됩니다. 작업을 클릭하면 다음 탭을 제공하는 새 창이 열립니다.

1. properties  
2. 작업 설정  
3. 일정  
4. 기록
5. 사용자
   
   ![][job-overview]

### <a name="properties"></a>properties
이러한 읽기 전용 속성은 Scheduler 작업에 대한 관리 메타데이터를 설명합니다.

   ![][job-properties]

### <a name="action-settings"></a>작업 설정
**작업** 화면에서 작업을 클릭하면 해당 작업을 구성할 수 있습니다. 이렇게 하면 빠른 만들기 마법사에서 구성하지 않은 경우 고급 설정을 구성할 수 있습니다.

모든 동작 유형의 경우 다시 시도 정책 및 오류 동작을 변경할 수 있습니다.

HTTP 및 HTTPS 작업 동작 유형에서, 메서드를 허용되는 HTTP 동사로 변경할 수 있습니다. 헤더와 기본 인증 정보를 추가, 삭제 또는 변경할 수도 있습니다.

저장소 큐 동작 유형의 경우 저장소 계정, 큐 이름, SAS 토큰 및 본문을 변경할 수 있습니다.

서비스 버스 동작 유형의 네임스페이스, 토픽/큐 경로, 인증 설정, 전송 유형, 메시지 속성 및 메시지 본문을 변경할 수 있습니다.

   ![][job-action-settings]

### <a name="schedule"></a>일정
이렇게 하면 빠른 만들기 마법사에서 만든 일정을 변경하지 않으려는 경우 일정을 다시 구성할 수 있습니다.

[작업에서 복잡한 일정 및 고급 되풀이](scheduler-advanced-complexity.md)

시작 날짜와 시간, 되풀이 일정 및 종료 날짜와 시간(작업이 되풀이될 경우)을 변경할 수 있습니다.

   ![][job-schedule]

### <a name="history"></a>기록
**기록** 탭은 선택한 작업에 대해 시스템의 모든 작업 실행에 선택한 메트릭을 표시합니다. 이러한 메트릭은 Scheduler 상태와 관련된 실시간 값을 제공합니다.

1. 상태  
2. 세부 정보  
3. 다시 시도 횟수
4. 발생 빈도: 첫 번째, 두 번째, 세 번째 등
5. 실행 시작 시간  
6. 실행 종료 시간
   
   ![][job-history]

**기록 세부 정보**보기 실행을 클릭하여 각 실행의 전체 응답을 살펴볼 수 있습니다. 이 대화 상자에서는 응답을 클립보드에 복사할 수도 있습니다.

   ![][job-history-details]

### <a name="users"></a>사용자
Azure RBAC(역할 기반 Access Control)를 통해 Azure Scheduler에 대한 세밀한 액세스 관리가 가능합니다. 사용자 탭을 사용하는 방법을 알아보려면 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)

## <a name="see-also"></a>참고 항목
 [Scheduler란?](scheduler-intro.md)

 [Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure Scheduler의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법](scheduler-advanced-complexity.md)

 [Scheduler REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Scheduler PowerShell Cmdlet 참조](scheduler-powershell-reference.md)

 [Scheduler 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Scheduler 아웃바운드 인증](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
