---
ms.topic: include
ms.openlocfilehash: 8e710bebf979b60f61552593ae550e95a8340d2b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34307569"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제

가상 머신에 대해 선불 결제하고 Azure Reserved VM(Virtual Machine) Instances를 사용하여 비용을 절감합니다. 자세한 내용은 [Azure Reserved Instances 제품](https://azure.microsoft.com/pricing/reserved-vm-instances/)을 참조하세요.

[Azure Portal](https://portal.azure.com)에서 Azure Reserved Instances를 구입할 수 있습니다. 예약 인스턴스를 구입하려면:
-   사용자가 하나 이상의 Enterprise 또는 종량제 구독에 대해 소유자 역할이어야 합니다.
-   Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 예약 인스턴스 구매가 활성화되어야 합니다.
-   CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트만 예약 인스턴스를 구매할 수 있습니다.

## <a name="buy-a-reserved-instance"></a>예약 인스턴스 구입
1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. **추가**를 선택하여 새 예약 인스턴스를 구입합니다.
4. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치하는 VM 인스턴스를 실행하면 예약 인스턴스 할인을 받을 수 있습니다. 할인을 받을 실제 VM 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

    | 필드      | 설명|
    |:------------|:--------------|
    |Name        |이 예약 인스턴스의 이름입니다.| 
    |구독|예약 인스턴스를 지불하는 데 사용되는 구독입니다. 구독 시 지불 방법은 예약 인스턴스에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업 계약(제품 번호: MS-AZR-0017P) 또는 종량제(제품 번호: MS-AZR-0003P)여야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.|    
    |범위       |예약 인스턴스의 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>단일 구독 - 예약 인스턴스 할인이 이 구독의 VM에 적용됩니다. </li><li>공유 - 예약 인스턴스 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 VM에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독(개발/테스트 구독 제외)을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</li></ul>|
    |위치    |예약 인스턴스의 범위에 해당하는 Azure 지역입니다.|    
    |VM 크기     |VM 인스턴스의 크기입니다.|
    |용어        |1년 또는 3년입니다.|
    |수량    |예약 인스턴스 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 VM 인스턴스의 수입니다. 예를 들어 미국 동부 지역에서 10개의 Standard_D2 VM을 실행 중인 경우 실행 중인 모든 컴퓨터에 대한 혜택을 극대화하려면 수량을 10으로 지정할 수 있습니다. |
5. **비용 계산**을 선택하면 예약 인스턴스 비용을 볼 수 있습니다.

    ![예약 인스턴스 구매를 제출하기 전 스크린샷](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. **구매**를 선택합니다.
7. 구매 상태를 보려면 **이 예약 보기**를 선택합니다.

    ![예약 인스턴스 구매를 제출한 후 스크린샷](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>다음 단계 
예약 인스턴스 할인은 예약 인스턴스 범위 및 특성과 일치하는 실행 중인 가상 머신 수에 자동으로 적용됩니다. [Azure Portal](https://portal.azure.com), PowerShell, CLI 또는 API를 통해 예약 인스턴스의 범위를 업데이트할 수 있습니다. 

예약 인스턴스를 관리하는 방법을 알아보려면 [Azure Reserved Instances 관리](../articles/billing/billing-manage-reserved-vm-instance.md)를 참조하세요.

Azure Reserved Instance에 대한 자세한 내용은 다음 아티클을 참조하세요.

- [예약 인스턴스를 사용하여 가상 머신에서 비용 절감](../articles/billing/billing-save-compute-costs-reservations.md)
- [Azure Reserved Instances 관리](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Reserved Instance 할인이 적용되는 방식 이해](../articles/billing/billing-understand-vm-reservation-charges.md)
- [종량제 구독에서 예약 인스턴스 사용량 이해](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Enterprise 등록의 예약 인스턴스 사용량 이해](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [예약 인스턴스를 포함하지 않는 Windows 소프트웨어 비용](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 예약 인스턴스](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.