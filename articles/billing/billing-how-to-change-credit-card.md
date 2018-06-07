---
title: Azure에 대한 신용 카드 변경 | Microsoft Docs
description: Azure 구독 지불에 사용하는 신용 카드를 변경하는 방법에 대해 설명합니다.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: 15252ced-1841-4a66-ae79-2e58af1d3370
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6b0db12da7c187ce80f1d55ce8b9900601033068
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="add-update-or-remove-a-credit-or-debit-card-for-azure"></a>Azure에 대한 신용 카드 또는 직불 카드 추가, 업데이트 또는 제거

계정 센터에서 새 신용 카드를 추가하고, 기존 신용 카드를 업데이트하거나 사용하지 않는 신용 카드를 삭제할 수 있습니다. 이러한 변경을 수행하려면 [계정 관리자](billing-subscription-transfer.md#whoisaa)여야 합니다.

**청구서로 지불로 전환하시겠습니까?** [청구서로 Azure 구독 비용 지불](billing-how-to-pay-by-invoice.md)을 참조하세요.
 
<a id="addcard"></a>

## <a name="add-a-new-credit-or-debit-card"></a>새 신용 카드 또는 직불 카드 추가

1. [계정 센터](https://account.windowsazure.com/Subscriptions)에 계정 관리자로 로그인합니다.
1. 구독을 선택합니다.
1. 페이지의 오른쪽에서 **결제 방법 관리**를 선택합니다.

    ![선택한 결제 방법 관리 옵션을 보여 주는 스크린샷](./media/billing-how-to-change-credit-card/changesub_new.png)
1. "+"를 선택하여 카드를 추가합니다.

    ![결제 방법 옆에 있는 편집 옵션을 보여 주는 스크린샷](./media/billing-how-to-change-credit-card/editcard_new.png)
1. 신용 카드 또는 직불 카드 정보를 입력합니다.
1. **저장**을 선택합니다. 

신용 카드를 추가한 후 오류가 표시되면 [Azure 등록 시 신용 카드 거부](billing-credit-card-fails-during-azure-sign-up.md)를 참조하세요.

## <a name="update-existing-credit-or-debit-card"></a>기존 신용 카드 또는 직불 카드 업데이트

신용 카드가 갱신되고 번호가 동일하게 유지되면 만료 날짜처럼 기존 신용 카드 세부 정보를 업데이트합니다. 카드 분실, 도난 또는 만료로 인해 카드 번호가 변경되는 경우 [결제 방법으로 신용 카드 추가](#addcard) 섹션의 단계를 따릅니다. CVV를 업데이트할 필요가 없습니다.

1. [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 계정 관리자로 로그인합니다.
1. 카드에 연결된 구독을 선택합니다.
1. **결제 방법 관리**를 선택합니다.
1. 업데이트하려는 카드 옆의 **편집**을 선택합니다.
1. 신용 카드 또는 직불 카드 정보를 업데이트합니다.
1. **저장**을 선택합니다.

## <a name="use-a-different-credit-card-for-the-azure-subscription"></a>Azure 구독에 다른 신용 카드 사용

1. [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 계정 관리자로 로그인합니다.
1. 카드에 연결된 구독을 선택합니다.
1. 페이지의 오른쪽에서 **결제 방법 관리**를 선택합니다.
1. 사용하려는 카드 옆의 **대신 사용**을 클릭합니다. 현재 이 카드와 연결된 다른 구독도 업데이트합니다. 

## <a name="remove-a-credit-or-debit-card-from-the-account"></a>계정에서 신용 카드 또는 직불 카드 제거

1. [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 계정 관리자로 로그인합니다.
1. 카드에 연결된 구독을 선택합니다.
3. 페이지의 오른쪽에서 **결제 방법 관리**를 선택합니다.
4. 삭제하려는 신용 카드에 대해 **삭제**를 클릭합니다.

신용 카드가 다른 활성 Microsoft 구독과 연결된 경우 Azure 계정에서 제거할 수 없습니다. Microsoft에 있는 모든 활성 구독에서 신용 카드를 제거하고 다시 시도합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="my-subscription-is-disabled-why-cant-i-remove-my-credit-card-now"></a>내 구독을 사용할 수 없습니다. 지금 내 신용 카드를 제거할 수 없는 이유는 무엇입니까?

구독을 비활성화하거나 취소한 후 구독을 영구적으로 삭제하기 전에 90일 동안 기다립니다. 구독을 다시 활성화하려는 경우에 대비해서 보존 기간 동안 파일에 결제 방법을 유지합니다. 그 후 구독이 완전히 삭제됩니다.

90일의 보존 기간이 끝나기 전에 신용 카드 또는 직불 카드를 제거해야 할 경우 [구독을 다시 활성화](billing-subscription-become-disable.md)합니다. 다시 활성화할 수 없는 경우 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>“로그인 세션이 만료되었습니다. 다시 로그인하려면 여기를 클릭하세요.” 메시지를 계속해서 받는 이유는 무엇입니까?

로그아웃하고 다시 로그인한 경우에도 이 오류 메시지가 유지되는 경우 개인 검색 세션으로 다시 시도하세요.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>보유한 각 구독에 대해 서로 다른 카드를 사용하려면 어떻게 할까요?

아쉽게도 구독이 동일한 카드를 이미 사용 중인 경우 서로 다른 카드를 사용하도록 구분할 수 없습니다. 그러나 새 구독에 등록할 때 해당 구독에 대해 새 지불 방법을 사용하도록 선택할 수 있습니다.

### <a name="how-do-i-make-payments"></a>지불하려면 어떻게 해야 할까요?

결제 방법으로 신용 카드 또는 직불 카드를 설정한 경우 각 청구 기간 후 요금이 해당 카드로 자동으로 청구됩니다. 수행할 작업은 없습니다.

[청구서로 지불](billing-how-to-pay-by-invoice.md)하는 경우 청구서 아래쪽에 나열된 위치로 결제 금액을 보냅니다.

### <a name="how-do-i-make-a-one-time-payment"></a>일회성 결제를 어떻게 해야 할까요?

아쉽게도 Azure는 현재 신용 카드 또는 직불 카드에 대한 일회성 결제를 지원하지 않습니다. 

### <a name="how-do-i-change-the-tax-id"></a>세금 ID를 변경하려면 어떻게 할까요?

세금 ID를 추가 또는 업데이트하려면 [Azure 계정 센터에서 **프로필**로 이동](https://account.azure.com/Profile)한 다음 **세금 레코드**를 선택합니다. 이 세금 ID는 면세 계산에 사용되며 청구서에 표시됩니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
