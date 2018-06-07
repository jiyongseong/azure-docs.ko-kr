---
title: 예기치 않은 비용 방지, 청구 관리 - Azure | Microsoft Docs
description: Azure 청구서에 예기치 않은 요금이 부과되지 않도록 하는 방법을 알아봅니다. Microsoft Azure 구독에 대한 비용 추적 및 관리 기능을 사용합니다.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: tonguyen
ms.openlocfilehash: 47a0f2e1cdb873dc907748ed2f3def29efccaa98
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Azure 청구 및 비용 관리를 사용하여 예기치 않은 비용 방지

Azure에 등록하면 몇 가지 방법을 통해 지출을 보다 명확히 파악할 수 있습니다. [가격 계산기](https://azure.microsoft.com/pricing/calculator/)는 Azure 리소스를 만들기 전에 예상 비용을 제공할 수 있습니다. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)은 사용자 구독에 대한 현재 비용 분석 및 예측을 제공합니다. 여러 다른 프로젝트 또는 팀의 비용을 그룹화하고 파악하려는 경우 [리소스 태그](../azure-resource-manager/resource-group-using-tags.md)를 살펴봅니다. 선호하는 보고 시스템이 조직에 있는 경우 [청구 API](billing-usage-rate-card-overview.md)를 확인하세요. 

- 구독이 EA(기업 계약)인 경우 Azure Portal에서 비용을 확인하기 위한 공개 미리 보기를 사용할 수 있습니다. CSP(클라우드 솔루션 공급자) 또는 Azure 스폰서쉽을 통한 구독인 경우 다음 기능 중 일부는 적용되지 않습니다. 자세한 내용은 [EA, CSP 및 스폰서쉽에 대한 추가 리소스](#other-offers)를 참조하세요.

- 구독이 평가판, [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), AIO(Azure in Open) 또는 BizSpark인 경우, 크레딧을 모두 사용하면 구독이 자동으로 사용되지 않도록 설정됩니다. 구독이 예상치 않게 사용할 수 없도록 설정되는 것을 방지하려면 [지출 한도](#spending-limit)에 대해 자세히 알아보세요.

- [Azure 체험 계정](https://azure.microsoft.com/free/)에 등록하는 경우 [12개월 동안 체험용으로 가장 널리 사용되는 Azure 서비스의 일부를 사용할 수 있습니다](billing-create-free-services-included-free-account.md). 아래에 나열된 권장 사항과 함께 [체험 계정에서 요금 청구 방지](billing-avoid-charges-free-account.md)를 참조하세요.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Azure 서비스를 추가하기 전에 예상 비용 얻기

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>가격 계산기를 사용하여 온라인으로 비용 예측

[가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 확인하여 관심 있는 서비스의 월별 비용을 예측하세요. 예상 비용을 얻기 위해 첫 번째 파티 Azure 리소스를 추가할 수 있습니다.

![가격 계산기 메뉴의 스크린샷](./media/billing-getting-started/pricing-calc.png)

예를 들어 A1 Windows VM(Virtual Machine)은 계속 실행 상태를 유지할 경우 월별 계산 시간이 $66.96로 예측됩니다.

![A1 Windows VM이 예상 월별 비용이 $66.96 USD임을 보여 주는 가격 계산기 스크린샷](./media/billing-getting-started/pricing-calcVM.png)

가격 책정에 대한 자세한 내용은 이 [FAQ](https://azure.microsoft.com/pricing/faq/)를 참조하세요. 또는 Azure 판매원에게 문의하려면 1-800-867-1389번으로 전화하세요.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

일반적으로 Azure Portal에서 서비스를 추가하는 경우 유사한 월별 예상 비용이 표시되는 보기가 제공됩니다. 예를 들어 Windows VM의 크기를 선택하는 경우 계산 시간에 대해 예상되는 월별 비용이 표시됩니다.

![예제: A1 Windows VM의 월별 비용이 $66.96 USD로 예측됨](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>청구 경고 설정

사용 비용이 지정한 금액을 초과하는 경우 전자 메일을 받도록 청구 경고를 설정합니다. 월별 크레딧이 있는 경우 지정된 금액을 다 사용하는 경우에 대해 경고를 설정합니다. 자세한 내용은 [Microsoft Azure 구독에 대한 청구 경고 설정](billing-set-up-alerts.md)을 참조하세요.

![청구 경고 전자 메일 스크린 샷](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> 이 기능은 미리 보기 상태이므로 정기적으로 사용 현황을 확인해야 합니다.

가격 계산기의 예상 비용을 첫 번째 경고에 대한 지침으로 사용할 수도 있습니다.

### <a name="spending-limit"></a> 지출 한도가 설정되어 있는지 확인

크레딧을 사용하는 구독이 있는 경우 기본적으로 지출 한도가 켜져 있습니다. 이러한 방식으로 모든 크레딧을 지출하면 신용 카드에 요금이 청구되지 않습니다. [전체 Azure 제품 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)을 참조하세요.

그러나 지출 한도에 도달하면 서비스는 사용되지 않도록 설정됩니다. 즉, VM이 할당 취소됩니다. 서비스 가동 중지를 방지하려면 지출 한도를 해제해야 합니다. 초과분이 파일의 신용 카드에 부과됩니다. 

지출 한도가 있는지 확인하려면 [계정 센터의 구독 보기](https://account.windowsazure.com/Subscriptions)로 이동합니다. 지출 한도가 켜져 있으면 배너가 표시됩니다.

![계정 센터에서 지출 한도가 설정되어 있다는 사실에 대한 경고를 표시하는 스크린 샷](./media/billing-getting-started/spending-limit-banner.PNG)

배너를 클릭하고 지출 한도 제거 지침을 따릅니다. 등록 시 신용 카드 정보를 입력하지 않은 경우 지출 한도를 제거하려면 해당 정보를 입력해야 합니다. 자세한 내용은 [Azure 지출 한도 – 작동 방식 및 사용 또는 제거하는 방법](https://azure.microsoft.com/pricing/spending-limits/)을 참조하세요.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Azure 서비스를 사용할 때 비용을 모니터링하는 방법

### <a name="tags"></a> 리소스에 태그를 추가하여 청구 데이터 그룹화

태그를 사용하여 지원되는 서비스에 대한 청구 데이터를 그룹화할 수 있습니다. 예를 들어 여러 다른 팀에 대한 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터(HR, 마케팅, 재무) 또는 환경(프로덕션, 프로덕션 전 테스트)별로 비용을 분류할 수 있습니다. 

![포털의 태그 설정을 보여 주는 스크린 샷](./media/billing-getting-started/tags.PNG)

태그는 여러 다른 비용 보고 보기에 표시됩니다. 예를 들어 지금 바로 [비용 분석 보기](#costs)에 표시되거나 첫 번째 청구 기간 후에 [상세 사용 현황 .csv](#invoice-and-usage)에 표시됩니다.

자세한 내용은 [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/resource-group-using-tags.md)을 참조하세요.

### <a name="costs"></a> 포털에서 정기적으로 비용 분석 및 진행 속도 확인

서비스가 실행 중일 때 부과되는 요금을 정기적으로 확인합니다. Azure Portal에서 현재 사용량 및 진행 속도를 확인할 수 있습니다. 

1. [Azure Portal의 구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 방문하여 구독을 선택합니다.

2. 팝업 블레이드에 비용 분석 및 진행 속도가 표시됩니다. 사용하는 제품에서 해당 기능이 지원되지 않을 수도 있습니다(위쪽에 경고 표시).

    ![Azure Portal의 진행 속도 및 분석 스크린 샷](./media/billing-getting-started/burn-rate.PNG)

3. 왼쪽 목록에서 **비용 분석**을 클릭하여 리소스별 비용 분석을 표시합니다. 서비스를 추가하고 24시간 정도 데이터가 채워질 때까지 기다립니다.

    ![Azure Portal의 비용 분석 보기의 스크린 샷](./media/billing-getting-started/cost-analysis.PNG)

4. [태그](#tags), 리소스 그룹 및 시간 간격 등의 다양한 속성별로 필터링할 수 있습니다. 보기를 쉼표로 구분된 값(.csv) 파일로 내보내려면 **적용**을 클릭하여 필터를 확인하고 **다운로드**를 클릭합니다.

5. 또한 리소스를 클릭하여 매일 사용 기록과 일일 리소스 비용을 확인할 수 있습니다.

    ![Azure Portal의 사용 기록 보기 스크린 샷](./media/billing-getting-started/costhistory.PNG)

표시되는 비용과 서비스를 선택할 때 확인한 예측 비용을 비교하는 것이 좋습니다. 비용이 예측 비용과 크게 다른 경우 리소스에 대해 선택한 가격 책정 계획(예: A1 및 A0 VM)을 다시 확인합니다. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>VM에 대한 자동 종료와 같은 비용 절감에 기능 사용 고려

시나리오에 따라 Azure Portal에서 VM에 대한 자동 종료를 구성할 수 있습니다. 자세한 내용은 [Azure Resource Manager를 사용한 VM에 대한 자동 종료](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)를 참조하세요.

![포털의 자동 종료 옵션 스크린 샷](./media/billing-getting-started/auto-shutdown.PNG)

자동 종료는 전원 옵션을 사용하여 VM 내에서 종료할 때와는 다릅니다. 자동 종료는 VM을 중지한 후 할당 취소하여 추가 요금 부과를 중지합니다. 자세한 내용은 VM 상태에 대한 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)의 가격 책정 FAQ를 참조하세요.

개발 및 테스트 환경에 대한 추가 비용 절감에 기능에 대해서는 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/)를 확인하세요.

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Azure Advisor 권장 지침 설정 및 확인

[Azure Advisor](../advisor/advisor-overview.md)는 사용량이 낮은 리소스를 식별하여 비용을 절감하는 데 도움을 주는 미리 보기 기능입니다. Azure Portal에서 이 기능을 켭니다.

![Azure Portal의 Azure Advisor 단추 스크린 샷](./media/billing-getting-started/advisor-button.PNG)

Advisor 대시보드의 **비용** 탭에서 실행 가능한 권장 지침을 얻을 수 있습니다.

![Advisor cost 권장 지침 예제 스크린 샷](./media/billing-getting-started/advisor-action.PNG)

자세한 내용은 [Advisor 비용 권장 사항](../advisor/advisor-cost-recommendations.md)을 참조하세요.

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>대금 청구 주기 종료 시 비용 검토

대금 청구 주기가 종료되면 청구서를 사용할 수 있게 됩니다. [과거 청구서 및 자세한 사용 현황 파일을 다운로드](billing-download-azure-invoice-daily-usage-date.md)하여 요금이 정확하게 부과되었는지 확인할 수 있습니다. 청구서에서 일간 사용 현황 비교에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요.

### <a name="billing-api"></a>청구 API

청구 API를 사용하여 프로그래밍 방식으로 사용 현황 데이터를 가져옵니다. RateCard API 및 사용 현황 API를 함께 사용하여 청구된 사용량을 확인합니다. 자세한 내용은 [Microsoft Azure 리소스 소비에 대한 통찰력 얻기](billing-usage-rate-card-overview.md)를 참조하세요.

## <a name="other-offers"></a> 추가 리소스 및 특수 사례

### <a name="ea-csp-and-sponsorship-customers"></a>EA, CSP 및 스폰서쉽 고객
계정 관리자 또는 Azure 파트너에 시작하도록 알립니다.

| 제안 | 리소스 |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA(기업 계약) | [EA 포털](https://ea.azure.com/), [도움말 문서](https://ea.azure.com/helpdocs) 및 [Power BI 보고서](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| CSP(클라우드 솔루션 공급자) | 공급자에게 알림 |
| Azure 스폰서쉽 | [스폰서쉽 포털](https://www.microsoftazuresponsorships.com/) |

대기업의 IT를 관리하는 경우 [Azure 엔터프라이즈 스 캐폴드](../azure-resource-manager/resource-manager-subscription-governance.md) 및 [엔터프라이즈 IT 백서](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf)(.pdf 다운로드, 영어 버전만 제공)를 읽어보세요.

#### <a name="EA"></a> Azure Portal 내에서 기업 계약 비용 보기 미리 보기 

엔터프라이즈 비용 보기는 현재 공개 미리 보기 상태입니다. 참고할 항목:
- 구독 비용은 사용을 기반으로 하며 사전에 지불한 금액, 초과분, 포함된 수량, 조정 및 세금은 고려되지 않습니다. 실제 요금은 등록 수준에서 계산됩니다. 
- Azure Portal 내에 표시되는 금액은 엔터프라이즈 포털에 있는 값에 비해 지연될 수 있습니다.  
- 비용이 표시되지 않는 경우 다음 이유 중 하나 때문일 수 있습니다.
    - 구독 수준에서 RBAC 권한이 충분하지 않습니다. Enterprise 비용 보기를 표시하려면 구독 수준에서 청구 읽기 권한자, 읽기 권한자, 참가자 또는 소유자여야 합니다.
    - 사용자는 계정 소유자이며 등록 관리자가 "AO 보기 요금" 설정을 비활성화했습니다.  등록 관리자에게 비용에 대한 액세스 권한을 문의하세요. 
    - 사용자는 부서 관리자이며 등록 관리자가 "DA 보기 요금" 설정을 비활성화했습니다.  등록 관리자에게 액세스 권한을 문의하세요. 
    - 채널 파트너를 통해 Azure를 구매했고 파트너가 가격 정보를 릴리스하지 않았습니다.  
- 엔터프라이즈 포털 내에서 비용 액세스와 관련된 설정이 업데이트되는 경우 Azure Portal에 변경 내용이 반영되기까지 몇 분 간의 지연이 있습니다.
- 지출 한도, 청구 경고 및 송장 지침은 EA 구독에 적용되지 않습니다.

### <a name="check-your-subscription-and-access"></a>구독 및 액세스 권한 확인

비용을 보려면 [대금 청구 정보에 대한 구독 수준 액세스](billing-manage-access.md) 권한이 필요하지만 계정 관리자만 [계정 센터](https://account.azure.com/Subscriptions)에 액세스하고, 청구 정보를 변경하고, 구독을 관리할 수 있습니다. 계정 관리자는 등록 프로세스를 거친 사용자입니다. 자세한 내용은 [구독 또는 서비스를 관리하는 Azure 관리자 역할 추가 또는 변경](billing-add-change-azure-subscription-administrator.md)을 참조하세요.

계정 관리자인 경우 [Azure Portal의 구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동한 후 액세스 권한이 있는 구독 목록을 찾습니다. **내 역할** 아래에서 확인합니다. *계정 관리자*가 표시되면 문제가 없는 것입니다. *소유자* 등이 표시되면 모든 권한이 있는 것은 아닙니다.

![Azure Portal의 구독 보기에 표시되는 역할의 스크린 샷](./media/billing-getting-started/sub-blade-view.PNG)

계정 관리자가 아니면 경우 누군가가 [Azure Active Directory RBAC(역할 기반 Access Control)](../role-based-access-control/role-assignments-portal.md)를 통해 부분적인 액세스 권한을 부여한 것입니다. 구독을 관리하고 청구 정보를 변경하려면 [계정 관리자를 찾고](billing-subscription-transfer.md#whoisaa) 작업을 수행하거나 [구독을 전송](billing-subscription-transfer.md)하도록 요청합니다.

계정 관리자가 더 이상 조직에 있지 않은 상태에서 청구를 관리해야 할 경우 [지원 서비스에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. 
## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
