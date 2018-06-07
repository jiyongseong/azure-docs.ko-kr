---
title: Azure DevTest Labs에서 월간 예상 랩 비용 추세 보기 | Microsoft 문서
description: Azure DevTest Labs의 월간 예상 비용 추세 차트에 대해 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Azure DevTest Labs에서 월간 예상 랩 비용 추세 보기
DevTest Labs의 비용 관리 기능은 랩의 비용을 추적하는 데 도움이 됩니다. 이 문서는 **월간 예상 비용 추세** 차트를 사용하여 이번 달의 현재 예상 비용 합계 뿐만 아니라 이번 달의 월말 추정 비용을 보는 방법을 보여줍니다. 또한 이 문서에서는 설정된 값에 도달하면 결과를 보고하도록 DevTest Labs를 트리거하여 랩 비용을 효율적으로 관리하는 방법을 보여줍니다.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Monthly Estimated Cost Trend(월간 예상 비용 추세) 차트 보기
월간 예상 비용 추세 차트를 보려면 다음 단계를 수행합니다. 

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. 필요한 경우 **모든 서비스**를 선택하고 목록에서 **DevTest Labs**를 선택합니다. 대시보드의 **모든 리소스** 아래에 랩이 이미 표시되어 있을 수 있습니다.
1. 랩 목록에서 원하는 랩을 탭합니다.  
1. 랩의 **개요** 영역에서 **구성 및 정책**을 선택합니다.   
1. **비용 추적**의 왼쪽 아래에서 **비용 추세**를 선택합니다.

   다음 스크릿샷은 비용 차트의 예를 보여줍니다. 
   
    ![비용 차트](./media/devtest-lab-configure-cost-management/graph.png)

**예상 비용** 값은 현재 월의 현재까지의 예상 비용입니다. **예측 비용** 은 이번 달 전체의 예상 비용으로, 이전 5일 동안의 랩 비용을 사용해서 계산합니다.

금액은 다음 정수로 올림됩니다. 예:  

* 5.01은 6으로 올림됩니다. 
* 5.50은 6으로 올림됩니다.
* 5.99는 6으로 올림됩니다.

위의 차트에 언급되어 있듯이, 기본적으로 차트에 보이는 비용은 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 제안 요율을 사용하여 *예상된* 비용입니다. [랩의 비용 목표를 관리](#managing-cost-targets-for-your-lab)하여 차트에 표시되는 지출 목표를 설정할 수도 있습니다.

또한 다음 항목은 비용 계산에 포함되지 *않습니다* .

* Azure DevTest Labs에서는 [Azure 청구 API](../billing/billing-usage-rate-card-overview.md) 를 사용하여 랩 비용을 계산하며 CSP 또는 Dreamspark 구독을 지원하지 않으므로 이러한 구독 방식이 현재 지원되지 않습니다.
* 사용자에 대한 제안 요율. 현재는 Microsoft 또는 Microsoft 파트너와 협상한 제안 요율(구독 아래에 표시됨)을 사용할 수 없습니다. 오직 종량제 요율만 사용됩니다.
* 사용자 세금
* 사용자 할인
* 사용자의 청구 통화. 현재는, 랩 비용이 USD 통화로만 표시됩니다.

## <a name="managing-cost-targets-for-your-lab"></a>랩의 비용 목표 관리
DevTest Labs에서 지출 목표를 설정하고 이후에 월별 예상 비용 추세 차트에서 지출 목표를 살펴보면 랩의 비용을 보다 효율적으로 관리할 수 있습니다. 또한 지정된 지출 목표 또는 임계값에 도달하면 DevTest Labs에서 알림을 보냅니다. 

1. 랩의 **개요** 창에서 **구성 및 정책**을 선택합니다.
1. **비용 추적**의 왼쪽 아래에서 **비용 추세**를 선택합니다.

    ![목표 관리 단추](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. **비용 추세** 창에서 **목표 관리**를 선택합니다.

    ![목표 관리 단추](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. 목표 관리 창에서 원하는 지출 목표 및 임계값을 지정합니다. 또한 선택한 각 임계값을 비용 추세 차트에 보고할 것인지 아니면 웹후크 알림을 통해 보고할 것인지 선택할 수 있습니다.

    ![목표 관리 창](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - 비용 목표를 추적할 기간을 선택합니다.
      - **월별**: 비용 목표를 월 단위로 추적합니다.
      - **고정**: 시작 날짜 및 종료 날짜 필드에서 지정한 날짜 범위의 비용 목표를 추적합니다. 일반적으로 이 값은 프로젝트가 실행되도록 예약된 시간과 일치합니다.
   - **목표 비용**을 지정합니다. 예를 들어 정의된 기간 내에 이 랩에 얼마를 소비할 것인지 계획할 수 있습니다.
   - 지정된 **비용 목표**의 최대 125%까지 25% 단위로 보고서를 받을 수 있도록 임계값을 사용하거나 사용하지 않도록 선택합니다.
      - **알림**: 이 임계값에 도달하면 지정한 웹후크 URL을 통해 알림이 제공됩니다.
      - **차트에 그리기**: 이 임계값에 도달하면 [월간 예상 비용 추세 차트 보기](#viewing-the-monthly-estimated-cost-trend-chart)의 설명처럼 사용자가 볼 수 있는 비용 추세 그래프에 결과가 표시됩니다.
   - 임계값에 도달하면 **알림**을 받기로 선택하는 경우 웹후크 URL을 지정해야 합니다. 비용 통합 영역에서 **통합을 추가하려면 여기를 클릭하세요.** 를 선택합니다.

      구성 알림 창에서 웹후크 URL을 입력한 다음 **확인**을 선택합니다.

       ![알림 창 구성](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - **알림**을 지정하는 경우 웹후크 URL을 정의해야 합니다.
      - 마찬가지로, 웹후크 URL을 정의하는 경우 비용 임계값 창에서 **알림**을 **켜기**로 설정해야 합니다.
      - 여기에 입력하기 전에 웹후크를 만들어야 합니다.  

      웹후크에 대한 자세한 내용은 [웹후크 또는 API Azure Function 만들기](../azure-functions/functions-create-a-web-hook-or-api-function.md)를 참조하세요. 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>관련 블로그 게시물
* [DevTest Labs에서 비용을 유지하기 하기 위한 두 가지 추가 항목](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [비용 임계값을 사용하는 이유는 무엇일까요?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>다음 단계
다음으로 수행해 볼 작업은 아래와 같습니다.

* [랩 정책 정의](devtest-lab-set-lab-policy.md) – 랩 및 해당 VM의 사용 방식을 관리하는 데 사용되는 다양한 정책을 설정하는 방법을 알아봅니다. 
* [사용자 지정 이미지 만들기](devtest-lab-create-template.md) - VM을 만들 때 사용자 지정 이미지 또는 Marketplace 이미지 중에서 기본 이미지를 지정할 수 있습니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
* [마켓플레이스 이미지 구성](devtest-lab-configure-marketplace-images.md) - DevTest Labs에서 Azure Marketplace 이미지에 기반하여 VM을 만들 수 있습니다. 이 문서에서는 랩에서 VM을 만들 때 사용할 수 있는 Azure Marketplace 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
* [랩에서 VM 만들기](devtest-lab-add-vm.md) - 기본 이미지(사용자 지정 또는 Marketplace 이미지)에서 VM을 만드는 방법 및 VM에서 아티팩트 작업 방법에 대해 설명합니다.

