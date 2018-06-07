---
title: Azure 구독에 대한 청구 또는 크레딧 경고 설정 | Microsoft Docs
description: 갑작스러운 청구에 당황하지 않도록 Azure 청구에 대한 경고를 설정하는 방법을 설명합니다.
keywords: 크레딧 경고, 청구 경고
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/9/2017
ms.author: adpick
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa7d1853226647917925e8c75e01a1c83d84daeb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359053"
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>Microsoft Azure 구독에 대한 청구 또는 크레딧 경고 설정
Azure 구독의 계정 관리자인 경우 Azure 청구 경고 서비스를 사용하여 Azure 계정에 대한 청구 활동을 모니터링하고 관리하는 데 도움이 되는 사용자 지정된 청구 경고를 만들 수 있습니다.

이 서비스는 미리 보기이므로 먼저 미리 보기 기능 페이지에서 이 서비스를 사용하도록 설정해야 합니다.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="set-the-alert-threshold-and-email-recipients"></a>경고 임계값 및 메일 받는 사람 설정
1. [미리 보기 기능 페이지](https://account.windowsazure.com/PreviewFeatures)를 방문하여 **청구 경고 서비스**를 사용하도록 설정합니다.

1. 구독에 대해 청구 서비스가 설정되었다는 메일 확인을 받은 후에 계정 포털의 [구독 페이지](https://account.windowsazure.com/Subscriptions) 를 방문합니다. 모니터링할 구독을 클릭한 다음 **경고**를 클릭합니다.

    ![경고가 강조 표시된 Azure 계정 센터의 구독 보기 스크린샷][Image1]

2. 다음으로 **경고 추가**를 클릭하여 첫 번째 경고를 만듭니다. 구독당 최대 총 5개의 청구 경고를 설정할 수 있으며, 각 경고에 대해 서로 다른 임계값을 설정하고, 각 경고에 대해 최대 두 명의 메일 받는 사람을 설정할 수 있습니다.

    ![경고를 추가할 수 있는 경고 보기의 스크린샷][Image2]

3. 경고를 추가할 때 고유 이름을 지정하고, 지출 임계값을 선택하고, 경고를 전송할 메일 주소를 선택합니다. 임계값을 설정할 때 **경고 대상** 목록에서 **청구 금액 합계** 또는 **금액 크레딧**을 선택할 수 있습니다. 청구 금액 합계의 경우 경고는 구독 지출이 임계값을 초과하면 전송됩니다. 금액 크레딧의 경우 경고는 금액 크레딧이 한도 아래로 떨어지면 전송됩니다. 금액 크레딧은 일반적으로 무료 평가판 및 Visual Studio 구독에 적용됩니다.

    ![받는 사람을 구성할 수 있는 경고 추가 보기의 스크린샷][Image3]

Azure는 모든 메일 주소를 지원하지만 해당 메일 주소가 작동하는지 확인하지 않으므로 오타가 없는지 다시 한 번 확인하세요.

## <a name="check-on-your-alerts"></a>경고 확인
경고를 설정하고 나면 계정 센터에 해당 경고가 나열되고 더 설정할 수 있는 경고 횟수가 표시됩니다. 각 경고에 대해 전송된 날짜 및 시간, 청구 금액 합계에 대한 경고인지 금액 크레딧에 대한 경고인지 여부 및 설정한 한도가 표시됩니다. 날짜 및 시간 형식은 24시간 UTC(Universal Time Coordinate)이며 날짜는 yyyy-mm-dd 형식입니다. 경고를 편집하려면 목록에서 경고의 더하기 기호를 클릭하고, 경고를 삭제하려면 휴지통을 클릭합니다.

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>EA(기업 계약) 고객의 청구 경고
EA 구독은 이 서비스에서 지원되지 않지만, 그 대신 EA 고객이 지출 할당량을 설정하여 등록 상태인 각 부서에 대한 경고를 받을 수 있습니다. 시작 방법은 EA 포털에서 [부서 지출 할당량](https://ea.azure.com/helpdocs/departmentSpendingQuotas)을 참조하세요.

## <a name="learn-more-about-azure-cost-management"></a>Azure 비용 관리에 대한 자세한 정보
- [가격 계산기](https://azure.microsoft.com/pricing/calculator/), [총 소유 비용 계산기](https://aka.ms/azure-tco-calculator)를 사용하여 비용 및 서비스를 추가할 시기를 예측합니다.
- [Azure Portal에서 정기적으로 사용량 및 비용을 검토합니다](billing-getting-started.md#costs).
- [Azure Advisor 비용 권장 사항](../advisor/advisor-cost-recommendations.md)을 켭니다.

자세한 내용은 참조 [Azure 비용 관리 지침](billing-getting-started.md)을 참조하세요.

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
