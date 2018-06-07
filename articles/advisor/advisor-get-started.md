---
title: Azure Advisor 시작 | Microsoft Docs
description: Azure Adviser를 시작합니다.
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
ms.openlocfilehash: cd64515beabec43a5209d62dccf2b55b21702c16
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-advisor"></a>Azure Adviser 시작

Azure Portal을 통해 Advisor에 액세스하고, 권장 사항을 검색하고, 권장 사항을 구현하는 방법을 알아봅니다.

## <a name="get-advisor-recommendations"></a>Advisor 권장 사항 가져오기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **Advisor**를 클릭합니다.  왼쪽 창에 Advisor가 표시되지 않으면 **모든 서비스**를 클릭합니다.  서비스 메뉴 창의 **모니터링 및 관리** 아래에서 **Advisor**를 클릭합니다.
 Advisor 대시보드가 표시됩니다.

   ![Azure Portal을 사용하여 Azure Advisor 액세스](./media/advisor-get-started/advisor-portal-menu.png) 

4. Advisor 대시보드에는 선택한 모든 구독에 대한 권장 사항 요약이 표시됩니다.  구독 필터 드롭다운을 사용하여 권장 사항을 표시할 구독을 선택할 수 있습니다.

5. 특정 범주에 대한 권장 사항을 얻으려면 **고가용성**, **보안**, **성능** 또는 **비용** 탭 중 하나를 클릭합니다. 

  ![Azure Advisor 대시보드](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-advisor-recommendation-details-and-implement-a-solution"></a>자세한 Advisor 권장 사항 가져오기 및 솔루션 구현

Advisor에서 권장 사항을 선택하여 권장 사항 작업 및 영향을 받는 리소스와 같은 추가 세부 사항을 보고 권장 사항에 대한 솔루션을 구현할 수 있습니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2. 해당 범주 내의 권장 사항 목록을 표시하려면 권장 사항 범주를 선택하고 **모두** 탭을 선택하여 모든 권장 사항을 표시합니다.

3. 자세하게 검토하려는 권장 사항을 클릭합니다.

4. 권장 사항과 해당 권장 사항이 적용되는 리소스에 대한 정보를 검토합니다.

5. 권장 사항을 구현하려면 **권장 작업**을 클릭합니다.

## <a name="filter-advisor-recommendations"></a>Advisor 권장 사항 필터링

권장 사항을 필터링하여 가장 중요한 항목으로 드릴다운할 수 있습니다.  구독, 리소스 유형 또는 권장 사항 상태별로 필터링할 수 있습니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드의 드롭다운을 사용하여 구독, 리소스 유형 또는 권장 사항 상태별로 필터링합니다.

    ![Advisor 검색 필터 조건](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-advisor-recommendations"></a>Advisor 권장 사항 연기 또는 해제

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2. 연기하거나 해제할 권장 사항으로 이동합니다.

3. 권장 사항을 클릭합니다.

4. **연기**를 클릭합니다. 

5. 연기 기간을 지정하거나, 권장 사항을 해제하려면 **안 함**을 선택합니다.

## <a name="exclude-subscriptions-or-resource-groups-from-advisor"></a>Advisor에서 구독 또는 리소스 그룹 제외

Advisor 권장 사항을 수신하지 않으려는 리소스 그룹 또는 구독이 있을 수 있습니다(예: '테스트' 리소스).  특정 구독 및 리소스 그룹에 대한 권장 사항만 생성하도록 Advisor를 구성할 수 있습니다.

> [!NOTE]
> Advisor에서 구독 또는 리소스 그룹을 포함 또는 제외하려면 구독 소유자여야 합니다.  구독 또는 리소스 그룹에 대해 필요한 사용 권한이 없는 경우 사용자 인터페이스에서 포함 또는 제외 옵션을 사용할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2. 작업 모음에서 **구성**을 클릭합니다.

3. Advisor 권장 사항을 수신하지 않으려는 구독 또는 리소스 그룹의 선택을 취소합니다.

    ![Advisor 구성 리소스 예제](./media/advisor-get-started/advisor-configure-resources.png)

4. **적용** 단추를 클릭합니다.

## <a name="configure-the-average-cpu-utilization-rule-for-the-low-usage-virtual-machine-recommendation"></a>사용량이 낮은 가상 머신 권장 사항에 대한 평균 CPU 사용률 규칙 구성

Advisor는 14일 동안 가상 머신 사용량을 모니터링하고 사용률이 낮은 가상 머신을 식별합니다. 4일 이상 평균 CPU 사용률이 5% 이하이고 네트워크 사용량이 7MB 이하인 가상 머신은 사용률이 낮은 가상 머신으로 간주됩니다.

사용량이 낮은 가상 머신을 더 적극적으로 식별하려면 구독당 평균 CPU 사용률 규칙을 조정할 수 있습니다.  평균 CPU 사용률 규칙은 5%, 10%, 15% 또는 20%로 설정할 수 있습니다.

> [!NOTE]
> 사용량이 낮은 가상 머신을 식별하기 위한 평균 CPU 사용률 규칙을 조정하려면 구독 *소유자*여야 합니다.  구독 또는 리소스 그룹에 대해 필요한 사용 권한이 없는 경우 사용자 인터페이스에서 포함 또는 제외 옵션을 사용할 수 없습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2. 작업 모음에서 **구성**을 클릭합니다.

3. **규칙** 탭을 클릭합니다.

4. 평균 CPU 사용률 규칙을 조정할 구독을 선택한 다음 **편집**을 클릭합니다.

5. 원하는 평균 CPU 사용률 값을 선택하고 **적용**을 클릭합니다.

6. 새 평균 CPU 사용률 규칙을 사용하도록 기존 권장 사항을 업데이트하려면 **권장 사항 새로 고침**을 클릭합니다. 

   ![Advisor 구성 권장 사항 규칙 예제](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-your-advisor-recommendations"></a>Advisor 권장 사항 다운로드

Advisor는 권장 사항 요약을 다운로드할 수 있습니다.  권장 사항을 PDF 파일 또는 CSV 파일로 다운로드할 수 있습니다.  권장 사항을 다운로드하면 동료와 쉽게 공유하거나 권장 사항 데이터를 바탕으로 고유한 분석을 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2. 작업 모음에서 **CSV로 다운로드** 또는 **PDF로 다운로드**를 클릭합니다.

다운로드 옵션은 Advisor 대시보드에 적용한 모든 필터를 반영합니다.  특정 권장 사항 범주 또는 권장 사항을 보면서 다운로드 옵션을 선택하면 다운로드한 요약에 해당 범주 또는 권장 사항에 대한 정보만 포함됩니다. 

## <a name="next-steps"></a>다음 단계

Advisor에 대해 자세히 알아보려면 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 고가용성 권장 사항](advisor-high-availability-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
-  [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
