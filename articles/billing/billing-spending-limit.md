---
title: Azure 지출 한도 이해 | Microsoft Docs
description: Azure 지출 한도의 작동 방식 및 제거 방법을 설명합니다.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 6049e3614b63bfabee6721dcaa83008eb3306493
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>Azure 지출 한도의 작동 방식 및 제거 방법 이해

Azure에서 지출 한도는 크레딧 액수를 초과하여 지출하는 것을 방지하기 위해 존재합니다. 평가판이나 여러 달의 크레딧을 포함하는 제품에 등록하는 모든 신규 고객은 기본적으로 지출 한도가 설정됩니다. 지출 한도는 $0입니다. 이 값은 변경할 수 없습니다. 종량제 구독 및 약정 플랜과 같은 구독 유형에는 지출 한도를 사용할 수 없습니다. [전체 Azure 제품 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)를 참조하세요.

**결제 경고를 찾고 있습니까?** [Azure 구독에 대한 청구 또는 크레딧 경고 설정](billing-set-up-alerts.md)을 참조하세요.

## <a name="what-happens-when-i-reach-the-spending-limit"></a>지출 한도에 도달하면 어떻게 되나요?

구독에 포함된 월별 할당량을 모두 사용할 경우 해당 청구 기간의 나머지 기간 동안 배포한 서비스가 비활성화됩니다. 

예를 들어 구독에 포함된 모든 크레딧을 소비하는 경우 배포한 Cloud Services가 프로덕션에서 제거되고 Azure 가상 머신이 중지되고 할당이 취소됩니다. 저장소 계정 및 데이터베이스의 데이터는 읽기 전용으로 사용할 수 있습니다.

다음 청구 기간이 시작될 때 구독에 여러 달에 대한 크레딧이 포함될 경우 구독이 다시 사용되도록 자동으로 설정됩니다. 그런 다음 Cloud Services를 다시 배포하고 저장소 계정 및 데이터베이스에 대한 모든 권한을 가질 수 있습니다.

구독에 대한 지출 한도에 도달할 경우 전자 메일 알림을 보냅니다. [계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인하고 지출 한도에 도달한 구독에 대한 알림이 표시됩니다.

평가판이 있으며 지출 한도에 도달하는 경우 [종량제로 업그레이드](billing-upgrade-azure-subscription.md)하여 지출 한도를 제거하고 구독을 다시 사용하도록 자동으로 설정할 수 있습니다.

<a id="remove"></a>

## <a name="remove-the-spending-limit-in-account-center"></a>계정 센터에서 지출 한도 제거

유효한 결제 방법이 구독과 연결되어 있는 경우 언제든 지출 한도를 제거할 수 있습니다. 여러 달의 크레딧을 포함하는 제품의 경우 다음 청구 기간이 시작할 때 지출 한도를 다시 사용하도록 설정할 수도 있습니다.

지출 한도를 제거하려면 다음 단계를 수행합니다.

1. [계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인합니다.
1. 구독을 선택합니다.
에서도 확인할 수 있습니다. 지출 한도에 도달하여 구독이 비활성화된 경우 "구독이 지출 한도에 도달하여 요금이 부과되지 않도록 비활성화되었습니다."라는 알림을 클릭합니다. 그렇지 않으면 **구독 상태** 영역에서 **지출 한도 제거**를 클릭합니다.
1. 적절한 옵션을 선택합니다.

![지출 한도 제거를 위한 옵션 선택](./media/billing-spending-limit/remove-spending-limit.PNG)

|옵션|결과|
|-------|-----|
|지출 한도 무기한 제거|다음 청구 기간 시작 시 자동으로 설정되지 않도록 지출 한도를 제거합니다.|
|현재 청구 기간에 대한 지출 한도 제거|다음 청구 기간 시작 시 자동으로 다시 설정되도록 지출 한도를 제거합니다.|

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-would-i-want-to-remove-the-spending-limit"></a>지출 한도를 제거하려는 이유는 무엇입니까?

지출 한도에 도달하면 특정 타사 및 Microsoft 서비스를 배포 또는 사용할 수 없게 됩니다. 다음은 구독에 대한 지출 한도를 제거해야 하는 시나리오입니다.

* Oracle, Visual Studio Team Services 등의 서비스와 같은 자사 이미지를 배포할 계획인 경우. 이 경우 지출 한도가 거의 순식간에 초과되어 구독이 비활성화됩니다.
* 중단하면 안 되는 서비스가 있는 경우
* 손실되지 않아야 하는 가상 IP 주소와 같은 설정을 사용하는 서비스 및 리소스가 있는 경우. 서비스 및 리소스의 할당이 취소되면 이러한 설정이 손실됩니다.

### <a name="how-do-i-turn-on-the-spending-limit-after-removing-it"></a>제거한 후에 지출 한도를 설정하려면 어떻게 하나요?

이 기능은 지출 한도가 무기한으로 제거된 경우에만 사용할 수 있습니다. 다음 청구 기간이 시작될 때 자동으로 설정하도록 변경합니다.

1. [계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인합니다.
1. 지출 한도 옵션을 변경하려면 노란색 배너를 클릭합니다.
1. **다음 청구 기간에서 지출 한도 설정\<청구 기간의 시작 날짜\>** 선택

### <a name="how-do-i-set-a-custom-spending-limit"></a>사용자 지정 지출 한도를 설정하려면 어떻게 하나요?

현재 사용자 지정 지출 한도는 없습니다. 그러나 [지출을 제어하도록 청구 경고를 사용](billing-set-up-alerts.md)하도록 선택할 수 있습니다.

### <a name="does-the-spending-limit-prevent-all-charges-from-azure"></a>지출 한도는 Azure의 모든 요금을 방지하나요?

[Azure Marketplace에 게시된 일부 외부 서비스](billing-understand-your-azure-marketplace-charges.md)는 구독 크레딧을 사용할 수 없으며, 따라서 지출 한도가 설정되어 있더라도 별도의 요금이 발생할 수 있습니다. Visual Studio 라이선스, Azure Active Directory 프리미엄, 지원 플랜 및 대부분의 타사 브랜드 서비스를 예로 들 수 있습니다. 새 외부 서비스를 프로비전할 때 서비스 요금이 별도로 청구됨을 알리는 경고가 표시됩니다.

![Marketplace 구매 경고](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
