---
title: "Azure Time Series Insights 액세스 및 관리를 위한 보안 구성 | Microsoft Docs"
description: "이 문서에서는 Azure Time Series Insights를 보호하기 위해 관리 액세스 정책 및 데이터 액세스 정책으로 보안 및 권한을 구성하는 방법을 설명합니다."
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: c7d4079c9106226e0d07aa97c4a52c16ddb257c3
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2018
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Azure Portal을 사용하여 Time Series Insights 환경에 대한 데이터 액세스 권한 부여

Time Series Insights 환경에는 다음과 같은 두 가지 독립적인 액세스 정책이 있습니다.

* 관리 액세스 정책
* 데이터 액세스 정책

두 정책 모두 Azure Active Directory 주체(사용자 및 앱)에게 특정 환경에 대한 다양한 권한을 부여합니다. 보안 주체(사용자 및 앱)는 환경을 포함하고 있는 구독과 연결된 활성 디렉터리(Azure 테넌트라고 함)에 속해 있어야 합니다.

관리 액세스 정책은
*   환경 생성 및 삭제, 이벤트 원본, 참조 데이터 집합 및
*   데이터 액세스 정책 관리 등 환경의 구성과 관련된 권한을 부여합니다.

데이터 액세스 정책은 데이터 쿼리를 실행하고 환경에서 참조 데이터를 조작하며 환경과 관련된 저장된 쿼리 및 관심 사항을 공유 할 수 있는 권한을 부여합니다.

두 종류의 정책은 환경 관리에 대한 액세스와 환경 내 데이터에 대한 액세스를 명확하게 구분합니다. 예를 들어, 환경의 소유자/생성자가 데이터 액세스에서 제거되도록 환경을 설정할 수 있습니다. 환경에서 데이터를 읽을 수 있는 사용자 및 서비스에도 환경의 구성에 대한 액세스 권한을 부여하지 않을 수 있습니다.

## <a name="grant-data-access"></a>데이터 액세스 권한 부여
다음 단계에 따라 사용자 계정에 대한 데이터 액세스를 부여합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Time Series Insights 환경을 찾습니다. **검색** 창에 **시계열**을 입력합니다. 검색 결과에서 **시계열 환경**을 선택합니다. 

3. 목록에서 Time Series Insights 환경을 선택합니다.
   
4. **데이터 액세스 정책**을 선택한 다음 **+ 추가**를 선택합니다.
  ![Time Series Insights 소스 관리 - 환경](media/data-access/getstarted-grant-data-access1.png)

5. **사용자 선택**을 선택합니다.  사용자 이름 또는 메일 주소를 검색하여 추가할 사용자를 찾습니다. **선택**을 클릭하여 선택 사항을 확인합니다. 

   ![Time Series Insights 소스 관리 - 추가](media/data-access/getstarted-grant-data-access2.png)

6. **역할 선택**을 선택합니다. 사용자에 대한 적절한 액세스 역할을 선택합니다.
   - 사용자가 참조 데이터를 변경하고 저장된 쿼리 및 큐브 뷰를 환경의 다른 사용자와 공유할 수 있게 허용하려면 **참가자**를 선택합니다. 
   - 또는 사용자가 환경의 데이터를 쿼리하고 개인(공유되지 않는) 쿼리를 환경에 저장할 수 있게 허용하려면 **읽기 권한자**를 선택합니다.

   **확인**을 선택하여 역할 선택을 확인합니다.

   ![Time Series Insights 소스 관리 - 사용자 선택](media/data-access/getstarted-grant-data-access3.png)

8. **사용자 역할 선택** 페이지에서 **확인**을 선택합니다.

   ![Time Series Insights 소스 관리 - 역할 선택](media/data-access/getstarted-grant-data-access4.png)

9. **데이터 액세스 정책** 페이지에는 사용자 및 각 사용자의 역할이 나열됩니다.

   ![Time Series Insights 소스 관리 - 결과](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>다음 단계
* [Azure Time Series Insights 환경에 이벤트 허브 이벤트 원본을 추가하는 방법](time-series-insights-how-to-add-an-event-source-eventhub.md)을 알아보세요.
* 이벤트 원본으로 [이벤트 보내기](time-series-insights-send-events.md)
* [Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경 보기
