---
title: Azure 청구서 및 일간 사용 현황 데이터 다운로드 | Microsoft Docs
description: Azure 청구서 및 일간 사용 현황 데이터를 다운로드하는 방법을 설명합니다.
keywords: 청구서, 청구서 다운로드, azure 청구서, azure 사용 현황
services: ''
documentationcenter: ''
author: genlin
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 6d568d1d-3bd6-4348-97d0-1098b5fe0661
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e57e3898c5e843dcc130746a10e78b0681303670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Azure 청구서 및 일간 사용 현황 데이터 다운로드 또는 보기
[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 청구서를 다운로드하거나 전자 메일로 전송할 수 있습니다. 일간 사용 현황을 다운로드하려면 [Azure Account Center](https://account.azure.com/Subscriptions)로 이동합니다. 계정 관리자와 같은 특정 역할만 청구서 및 사용 현황 정보를 확인할 권한이 있습니다. 청구 정보에 액세스하는 방법에 대한 자세한 내용은 [역할을 사용하여 Azure 청구에 대한 액세스 관리](billing-manage-access.md)를 참조하세요.

이 문서는 EA(기업 계약) 고객에게는 적용되지 않습니다. EA 고객인 경우 청구서는 등록 관리자에게 직접 전송됩니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="get-your-invoice-in-email-pdf"></a>전자 메일로 청구서 받기(.pdf)
Azure 청구서를 전자 메일로 받을 추가 수취인을 옵트인하고 구성할 수 있습니다. 지원 서비스, 기업 계약, Azure in Open 등의 특정 구독에는 이 기능이 제공되지 않을 수도 있습니다.

1. [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다. 소유하고 있는 각 구독에 대해 옵트인합니다. **청구서**를 클릭한 다음 **전자 메일로 청구서 받기**를 클릭합니다. 

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)
    
2. **옵트인**을 클릭하고 조건에 동의합니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)
 
3. 규약에 동의하면 추가 수취인을 구성할 수 있습니다.

    ![옵트인 흐름을 보여주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)
    
이 단계를 따른 후 전자 메일을 받지 못하면 [프로필의 통신 기본 설정](https://account.windowsazure.com/profile)에서 전자 메일 주소가 올바른지 확인합니다.

## <a name="download-invoice-from-azure-portal-pdf"></a>Azure Portal에서 청구서 다운로드(.pdf)

1. Azure Portal의 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 [청구서 액세스 권한이 있는 사용자](billing-manage-access.md)로서 구독을 선택합니다.

2. **청구서**를 선택합니다. 

    ![청구 및 사용 현황 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png) 

3. **청구서 다운로드**를 클릭하여 PDF 청구서 사본을 확인합니다. **사용할 수 없음**이 표시될 경우 [왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?](#noinvoice)를 참조하세요.

    ![청구 기간, 다운로드 옵션 및 각 청구 기간별 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. 청구 기간을 클릭하여 일간 사용 현황을 볼 수도 있습니다. 

청구서에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요. 비용 관리에 대한 도움말은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md)를 참조하세요.

## <a name="download-usage-from-the-account-center-csv"></a>계정 센터에서 사용 현황 다운로드(.csv)

1. [Azure 계정 센터](https://account.windowsazure.com/subscriptions)에 계정 관리자로 로그인합니다.

2. 원하는 송장 및 사용 정보에 대한 구독을 선택합니다.

3. **청구 내역**을 선택합니다. 

    ![청구 내역 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. 이전 6개의 청구 기간 및 현재 미결제한 기간의 명세서를 볼 수 있습니다. 

    ![청구 기간, 청구서 및 일간 사용 현황 다운로드 옵션, 각 청구 기간에 대한 총 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. **현재 명세서 보기** 를 선택하여 예상 요금을 볼 수 있습니다. 이 정보는 매일 업데이트만 될 뿐 모든 사용량을 포함하지 않습니다. 월별 청구서는 이 예상과 다를 수 있습니다.

    ![현재 명세서 보기 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![현재 예상 요금을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. **사용량 다운로드**를 선택하여 일간 사용 데이터를 CSV 파일로 다운로드 할 수 있습니다. 사용할 수 있는 두 가지 버전이 표시되면 버전 2를 다운로드합니다.

    ![사용 현황 다운로드 옵션을 보여 주는 스크린샷](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

계정 관리자만 Azure 계정 센터에 액세스할 수 있습니다. 소유자 등의 기타 청구 관리자는 [청구 API](billing-usage-rate-card-overview.md)를 사용하여 사용량 정보를 볼 수 있습니다.

일간 사용 현황에 대한 자세한 내용은 [Microsoft Azure 청구서 이해](billing-understand-your-bill.md)를 참조하세요. 비용 관리에 대한 도움말은 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](billing-getting-started.md)를 참조하세요.

## <a name="noinvoice"></a> 왜 마지막 청구 기간에 대한 청구서가 보이지 않습니까?

몇 가지 이유로 인해 청구서가 보이지 않을 수 있습니다.

- 구독에 월별 크레딧 금액이 있고 이 금액을 초과하지 않았거나 무료 평가판을 사용하는 경우입니다. 청구서는 비용을 미납한 경우에만 생성됩니다.

- Azure를 구독한 지 30일 이내입니다.

- 청구서가 아직 생성되지 않았습니다. 청구 기간이 끝날 때까지 기다립니다.

- 계정 관리자가 아닐 경우 이전 청구서를 사용하지 못할 수 있습니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

