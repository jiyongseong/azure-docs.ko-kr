---
title: "Azure Log Analytics 경고로 이벤트에 응답 | Microsoft Docs"
description: "이 자습서는 Log Analytics의 경고를 통해 OMS 리포지토리의 중요한 정보를 식별하며 문제를 미리 알리거나 작업을 호출하여 문제 해결을 시도합니다."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fcfaa849f67ffcfa69672d116837e96d318c2124
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="respond-to-events-with-log-analytics-alerts"></a>Log Analytics 경고로 이벤트에 응답
Log Analytics의 경고는 Log Analytics 리포지토리에서 중요한 정보를 식별합니다. 경고는 정기적으로 로그 검색을 자동으로 실행하는 경고 규칙에 의해 작성되며 로그 검색 결과가 특정 기준과 일치하면 경고 레코드가 만들어지며 자동 응답을 수행하도록 구성할 수 있습니다.  이 자습서는 [Log Analytics 데이터의 대시보드 만들기 및 공유](log-analytics-tutorial-dashboards.md) 자습서와 이어집니다.   

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 경고 규칙 만들기
> * 전자 메일 알림을 보내도록 경고 규칙 구성

이 자습서의 예제를 완료하려면 [Log Analytics 작업 영역에 연결된](log-analytics-quick-collect-azurevm.md) 기존 가상 머신이 있어야 합니다.  

## <a name="log-in-to-azure-portal"></a>Azure Portal에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다. 

## <a name="create-alerts"></a>경고 만들기

경고는 일정한 간격으로 로그 검색을 자동으로 실행하는 경고 규칙에 의해 만들어집니다.  특정 성능 메트릭을 기반으로 알림을 만들거나 특정 이벤트가 생성되거나 이벤트가 없거나 특정 기간 내에 여러 이벤트가 만들어질 때 알림을 만들 수 있습니다.  예를 들어, 경고는 평균 CPU 사용량이 특정 임계값을 초과하거나 특정 Windows 서비스 또는 Linux 데몬이 실행 중이 아닌 상태에서 이벤트가 생성될 때 이를 알리는 데 사용할 수 있습니다.   로그 검색 결과가 특정 조건과 일치하는 경우 경고 레코드가 만들어집니다. 그런 다음 규칙에 따라 하나 이상의 작업이 자동으로 실행되어 경고를 미리 알리거나 다른 프로세스를 호출할 수 있습니다. 

다음 예제에서는 값이 90% 임계값을 초과하는 쿼리의 각 컴퓨터 개체에 대해 경고를 생성하는 메트릭 측정 경고 규칙을 작성합니다.

1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. OMS 포털을 선택하여 OMS 포털을 시작하고 **개요** 페이지에서 **로그 검색**을 선택합니다.  
3. 포털 맨 위에서 **즐겨찾기**를 선택하고 오른쪽의 **저장된 검색** 창에 쿼리 *Azure VM - 프로세서 사용률*을 선택합니다.  
4. 페이지 위쪽에서 **경고**를 클릭하여 **경고 규칙 추가** 화면을 엽니다.  
5. 다음 정보로 경고 규칙을 구성합니다.  
   a. 경고에 대한 **이름**을 제공합니다(예: *VM 프로세스 활용률 초과 >90*).  
   나. **기간**에서 쿼리에 대한 시간 범위를 지정합니다(예: *30*).  쿼리에서는 현재 시간의 이 범위 내에서 생성된 레코드만 반환합니다.  
   다. **경고 빈도**는 쿼리가 실행되는 빈도를 지정합니다.  이 예제에서는 *5*분을 지정하며 지정된 기간 내에 쿼리가 발생합니다.  
   d. **메트릭 측정**을 선택하고 **집계 값**에 대해 *90*을 입력하고 **경고 트리거 조건** 에 대해 *3*을 입력합니다.  
   e. **작업** 아래에서 전자 메일 알림을 사용하지 않도록 설정합니다.
6. **저장** 을 클릭하여 경고 규칙을 완료합니다. 그 즉시 실행이 시작됩니다.<br><br> ![경고 규칙 예제](media/log-analytics-tutorial-response/log-analytics-alert-01.png)

Log Analytics의 경고 규칙에 의해 만든 경고 레코드에는 **경고**의 유형 및 **OMS**의 SourceSystem이 있습니다.<br><br> ![생성된 경고 이벤트의 예](media/log-analytics-tutorial-response/log-analytics-alert-events-01.png)  

## <a name="alert-actions"></a>경고 작업
전자 메일 알림 생성, [Automation Runbook](../automation/automation-runbook-types.md) 시작, webhook를 사용하여 ITSM 인시던트 관리 시스템에서 인시던트 레코드 만들기 또는 경고 기준이 충족될 경우 응답으로 [IT Service Management Connector 솔루션](log-analytics-itsmc-overview.md) 사용과 같은 경고에 대한 고급 작업을 수행할 수 있습니다.   

전자 메일 작업은 한 명 이상의 수신자에게 경고의 세부 정보가 포함된 전자 메일을 보냅니다. 메일의 제목을 지정할 수 있지만 그 내용은 Log Analytics에서 구성한 표준 형식입니다.  이전에 만든 경고 규칙을 업데이트하고 로그 검색을 통해 알림 레코드를 적극적으로 모니터링하는 대신 전자 메일로 알림을 보내도록 구성해 보겠습니다.     

1. OMS 포털의 맨 위 메뉴에서 **설정**을 선택한 후 **경고**를 선택합니다.
2. 경고 규칙 목록에서 이전에 만든 경고 옆의 연필 아이콘을 클릭합니다.
3. **작업** 섹션 아래에서 전자 메일 알림을 사용하도록 설정합니다.
4. 전자 메일의 **제목**을 제공합니다(예: *프로세서 사용률 임계값 초가 > 90*).
5. **받는 사람** 필드에 받는 사람 이메일 주소를 한 명 이상 추가합니다.  둘 이상의 주소를 지정하는 경우 주소를 세미콜론(;)으로 구분합니다.
6. **저장** 을 클릭하여 경고 규칙을 완료합니다. 그 즉시 실행이 시작됩니다.<br><br> ![전자 메일 알림 포함 경고 규칙](media/log-analytics-tutorial-response/log-analytics-alert-02.png)

## <a name="next-steps"></a>다음 단계
이 자습서에서는 예약된 간격으로 로그 검색을 실행하고 특정 기준에 일치하는 경우 경고 규칙을 통해 사전에 문제를 식별하여 대응할 수 있는 방법에 대해 알아보았습니다.  

미리 빌드된 Log Analytics 스크립트 샘플을 보려면 이 링크를 따릅니다.  

> [!div class="nextstepaction"]
> [Log Analytics 스크립트 샘플](powershell-samples.md)