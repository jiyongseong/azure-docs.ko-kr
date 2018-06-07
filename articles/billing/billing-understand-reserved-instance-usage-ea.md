---
title: Enterprise용 Azure Reserved Instance 사용량 이해 - Azure Billing | Microsoft Docs
description: Enterprise 등록에서 Azure Reserved VM Instance의 적용 방법을 이해하기 위해 사용량을 판독하는 방법을 알아봅니다.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301317"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>Enterprise 등록의 Azure Reserved Instance 사용량 이해
[예약 페이지](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)의 **ReservationId** 및 [EA 포털](https://ea.azure.com)의 사용량 파일을 사용하여 Reserved Instance의 활용을 이해합니다. [EA 포털](https://ea.azure.com)의 사용량 요약 섹션에서도 Reserved Instance 사용량을 파악할 수 있습니다.

>[!NOTE]
>종량제 청구 컨텍스트에서 Reserved Instance를 구매한 경우 [종량제 구독에서 Reserved Instance 사용량 이해](billing-understand-reserved-instance-usage.md)를 참조하세요.

다음 섹션에서는 미국 동부 지역에서 Standard_D1_v2 Windows VM을 실행하고 Reserved Instance 정보가 다음 표와 같다고 가정합니다.

| 필드 | 값 |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|수량 |1|
|SKU | Standard_D1|
|지역 | eastus |

## <a name="reserved-instance-application"></a>Reserved Instance 응용 프로그램

배포된 VM이 Reserved Instance 특성과 일치하기 때문에 VM의 하드웨어 부분이 포함됩니다. Reserved Instance에 포함되지 않는 Windows 소프트웨어를 확인하려면 Azure Reserved VM Instances 소프트웨어 비용, [Azure Reserve VM Instances Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)으로 이동하세요.


### <a name="reserved-instance-usage-in-csv"></a>csv의 Reserved Instance 사용량
EA 포털에서 EA 사용량 csv를 다운로드할 수 있습니다. 다운로드한 csv 파일에서 추가 정보를 필터링하고 **ReservationID**를 입력합니다. 다음 스크린샷에서는 Reserved Instance와 관련된 필드를 보여줍니다.

![Azure Reserved Instance에 대한 EA(기업 계약) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. 추가 정보 필드의 **ReservationId**는 VM에 혜택을 적용하는 데 사용된 Reserved Instance를 나타냅니다.
2. ConsumptionMeter는 VM에 대한 MeterId입니다.
3. VM 실행 비용이 이미 Reserved Instance에서 결제되었기 때문에 $0인 Reservation Meter입니다. 
4. Standard_D1은 하나의 vCPU VM이며 해당 VM은 Azure Hybrid Benefit 없이 배포됩니다. 따라서 이 미터는 Windows 소프트웨어의 추가 요금을 포함합니다. [Azure Reserve VM Instances Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)을 참조하여 D 시리즈 1 코어 VM에 해당하는 미터 찾기 Azure Hybrid Benefit을 사용하면 이 추가 비용이 적용되지 않습니다.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>EA 포털 사용량 요약 페이지의 Reserved Instance 사용량

Reserved Instance 사용량은 EA 포털: ![EA(기업 계약) 사용량 요약](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)의 사용량 요약 섹션에도 표시됩니다.

1. VM의 하드웨어 구성 요소는 Reserved Instance에서 처리되므로 이에 대해서는 청구되지 않습니다. 
2. Azure Hybrid Benefit을 사용하지 않으면 Windows 소프트웨어에 대해서 청구됩니다. 

## <a name="next-steps"></a>다음 단계
Azure Reserved Instance에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Reserved Instance를 사용하여 가상 머신에서 비용 절감](billing-save-compute-costs-reservations.md)
- [Reserved Instance를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Reserved Instance 관리](billing-manage-reserved-vm-instance.md)
- [Reserved Instance 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독에서 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage.md)
- [예약 인스턴스를 포함하지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.