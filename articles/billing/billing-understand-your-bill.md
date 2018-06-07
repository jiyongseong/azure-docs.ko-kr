---
title: Azure 요금 청구 방식 이해
description: Azure 구독에 대한 사용량 및 청구서를 읽고 이해하는 방법에 대해 알아봅니다.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: f3e0e3eeab88ad8ad0c4a21eb69a6340dbbe0441
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="understand-your-bill-for-microsoft-azure"></a>Microsoft Azure 청구서 이해
Azure 청구서를 이해하려면 청구서를 자세한 일별 사용 현황 파일 및 Azure Portal의 비용 관리 보고서와 비교합니다.

>[!NOTE]
>이 문서는 EA(기업 계약) 고객에게는 적용되지 않습니다. EA 고객인 경우 [엔터프라이즈 포털에서 청구서 설명서를 찾을 수 있습니다.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

청구서 PDF 및 자세한 일별 사용 현황 파일 CSV 사본을 다운로드하려면 [Azure 청구서 및 일별 사용 현황 데이터 가져오기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. 

청구서 및 자세한 일별 사용 현황 파일의 세부 용어 및 설명은 [Microsoft Azure 청구서의 용어 이해](billing-understand-your-invoice.md) 및 [Microsoft Azure의 자세한 사용 현황의 용어 이해](billing-understand-your-usage.md)를 참조하세요. 

비용 관리 보고서에 대한 세부 정보는 [Azure Portal 비용 관리](https://docs.microsoft.com/azure/billing/billing-getting-started)를 참조하세요.

## <a name="charges"></a>청구서의 요금이 올바른지 확인하는 방법은 무엇인가요?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

청구서에 자세히 알아보려는 요금이 있는 경우 다음과 같은 두 가지 옵션을 통해 확인할 수 있습니다.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>옵션 1: 청구서를 검토하고 사용 현황 및 비용을 자세한 사용 현황 CSV 파일과 비교

자세한 사용 현황 CSV 파일은 청구 기간 및 일별 사용 현황별로 청구 요금을 보여 줍니다. 자세한 사용 현황 CSV 파일을 가져오려면 [Azure 청구서 및 일별 사용 현황 데이터 가져오기](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date)를 참조하세요.

사용 요금은 미터 수준으로 표시됩니다. 다음 용어는 청구서와 자세한 사용 현황 파일에서 동일한 의미로 사용됩니다. 예를 들어 청구서의 청구 주기는 자세한 사용 현황 파일에 표시된 요금 청구 기간과 같습니다.

 | 청구서(PDF) | 자세한 사용 현황(CSV)|
 | --- | --- |
|청구 주기 | 청구 기간 |
 |이름 |미터 범주 |
 |유형 |미터 하위 범주 |
 |리소스 |미터 이름 |
 |지역 |미터 영역 |
 |사용 |사용량 |
 |포함 |포함된 수량 |
 |청구 대상 |초과량 |

청구서의 **사용 요금** 섹션에는 청구 기간 동안 사용된 각 미터의 총 값이 나와 있습니다. 예를 들어, 다음 스크린샷은 Azure Scheduler 서비스에 대한 사용 요금을 보여줍니다.

![청구서 사용 요금](./media/billing-understand-your-bill/1.png)

자세한 사용 현황 CSV의 **명세서** 섹션에도 동일한 요금이 표시됩니다. *사용* 부분과 *값* 부분 모두가 청구서와 일치합니다.

![CSV 사용 요금](./media/billing-understand-your-bill/2.png)

일일 단위로 이 요금의 세부 내역을 확인하려면 CSV의 **일일 사용량** 섹션으로 이동하세요. *미터 범주*에서 "Scheduler"로 필터링하면 미터가 사용된 기간(일)과 사용량을 확인할 수 있습니다. 비교를 위해 *리소스*와 *리소스 그룹* 정보도 나열됩니다. *사용* 값은 모두 합산되어 청구서에 표시된 금액이 되어야 합니다.

![CSV의 일일 사용량 섹션](./media/billing-understand-your-bill/3.png)

일별 비용을 가져오려면 *사용* 금액을 **명세서** 섹션의 *요율* 값으로 곱합니다.

청구서에 대해 자세히 알아 보려면 [Azure 청구서 이해](billing-understand-your-invoice.md)를 참조하세요.

CSV에 있는 각 열에 대한 자세한 내용은 [Azure의 자세한 사용 현황 이해](billing-understand-your-invoice.md)를 참조하세요.

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>옵션 2: 청구서를 검토하고 Azure Portal에서 사용 현황 및 비용과 비교

Azure Portal에서 요금을 확인할 수도 있습니다. Azure Portal은 청구서의 사용 현황 및 요금의 간략한 개요를 나타내는 비용 관리 차트를 제공합니다.

위의 예로 계속하려면 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동하여 구독을 선택한 다음, **비용 분석**을 선택합니다. 이어서 기간을 지정하고 Azure Scheduler 서비스에 대한 사용 요금을 확인할 수 있습니다.

![Azure Portal의 비용 분석 보기](./media/billing-understand-your-bill/4.png)

**비용 내역**에서 일별 비용 세부 내용을 확인하려면 원하는 행을 클릭합니다.

![Azure Portal의 비용 내역 보기](./media/billing-understand-your-bill/5.png)

자세히 알아보려면 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md#costs)를 참조하세요.

## <a name="external"></a>외부 서비스 요금은 어떤가요?
외부 서비스(또는 Azure Marketplace 주문)는 독립 서비스 공급업체에서 제공하며 별도로 청구됩니다. 이 요금은 Azure 청구서에 표시되지 않습니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)를 참조하세요.

## <a name="payment"></a>지불하려면 어떻게 할까요?

결제 방법으로 신용 카드 또는 직불 카드를 설정한 경우 청구 기간 종료 후 10일 이내에 결제가 자동으로 이루어집니다. 신용 카드 명세서에는 **MSFT Azure** 항목으로 표시됩니다.

[청구서 결제](billing-how-to-pay-by-invoice.md)의 경우 청구서 하단에 기재된 위치로 결제 금액을 송금합니다. 자세한 도움은 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>신용 카드 결제 상태는 어떻게 확인하나요?

[지원 티켓을 작성](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 결제 상태 확인을 문의합니다. 

## <a name="tips-for-cost-management"></a>비용 관리 팁
- [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)와 [총 소유 비용 계산기](https://aka.ms/azure-tco-calculator)를 사용하여 비용을 산정하고 [각 서비스에 대한 자세한 요금 정보](https://azure.microsoft.com/pricing/)를 가져옵니다.
- [청구 알림을 설정](billing-set-up-alerts.md)합니다.
- [Azure Portal에서 정기적으로 사용량 및 비용을 검토](billing-getting-started.md#costs)합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
