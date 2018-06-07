---
title: Azure Reserved Instances 관리 - Azure 청구 | Microsoft Docs
description: 구독 범위를 변경하고 Azure Reserved VM Instances에 대한 액세스 권한을 관리하는 방법에 대해 알아봅니다.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: fc473906be9c572e6d6549c85f9faa8fe7566b86
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303065"
---
# <a name="manage-reserved-instances"></a>예약 인스턴스 관리

Azure Reserved VM Instance를 구입한 다음, 구매 중에 지정된 구독이 아닌 다른 구독에 예약 인스턴스를 적용하는 것이 좋습니다. 또한 동일한 가상 머신이 여러 구독에서 실행 중인 경우 공유할 예약 인스턴스 범위를 변경하는 것이 좋습니다. 예약 인스턴스 할인을 최대화하려면 구입한 인스턴스 수가 특성 및 실행 중인 가상 머신 수와 일치하는지 확인합니다. Azure Reserved Instances에 대해 자세히 알아보려면 [Azure 가상 머신을 사전 지불하여 비용 절감](https://go.microsoft.com/fwlink/?linkid=862121)을 참조하세요.

## <a name="change-the-scope-for-a-reserved-instance"></a>예약 인스턴스의 범위 변경
 예약 인스턴스 할인은 예약 인스턴스와 일치하고 예약 인스턴스 범위 내에서 실행되는 가상 머신에 적용됩니다. 예약 인스턴스의 범위는 청구 컨텍스트에서 단일 구독이나 모든 구독일 수 있습니다. 단일 구독으로 범위를 설정하는 경우 예약 인스턴스는 선택한 구독에서 실행 중인 가상 머신과 일치됩니다. 범위를 공유로 설정하는 경우 Azure에서는 예약 인스턴스를 청구 컨텍스트 내에서 모든 구독에서 실행되는 가상 머신에 일치시킵니다. 청구 컨텍스트는 예약 인스턴스를 구입하는 데 사용되는 구독에 따라 달라집니다. 자세한 내용은 [예약 인스턴스를 사용하여 VM 사전 지불](https://go.microsoft.com/fwlink/?linkid=861721)을 참조하세요.

예약 인스턴스의 범위를 업데이트하려면: 
1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 예약 인스턴스를 선택합니다.
4. **설정** > **구성**을 선택합니다.
5. 범위를 변경합니다. 범위를 공유에서 단일 범위로 변경하는 경우 소유자인 구독만을 선택할 수 있습니다. 동일한 청구 컨텍스트 내에서 예약 인스턴스인 구독만을 선택할 수 있습니다. 청구 컨텍스트는 예약 인스턴스를 구입했을 때 선택한 구독에 의해 결정됩니다. 범위는 종량제 제품 MS-AZR-0003P 구독 및 엔터프라이즈 제품 MS-AZR-0017P 구독에만 적용됩니다. 기업 계약의 경우 개발/테스트 구독은 예약 인스턴스 할인에 해당하지 않습니다.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>예약 인스턴스를 두 개의 예약 인스턴스로 분할
 둘 이상의 인스턴스를 구입한 다음, 예약 인스턴스 내의 인스턴스를 다른 구독에 할당하는 것이 좋습니다. 기본적으로 모든 인스턴스(구매 중에 지정된 수량)에는 단일 구독 또는 공유 중 하나의 범위가 지정됩니다. 예를 들어 10개의 표준 D2 VM을 구입하고, 범위를 구독 A로 지정했습니다. 이제 7개의 예약 인스턴스의 범위를 구독 A로 변경하고 남은 3개의 예약 인스턴스를 구독 B로 변경하려고 합니다. 예약 인스턴스를 분할하면 세분화된 범위 관리에 대한 인스턴스를 배포할 수 있습니다. 공유 범위를 선택하여 구독에 대한 할당을 간소화할 수 있습니다. 하지만 비용 관리 또는 예산 목적으로 인해 특정 구독에 수량을 할당할 수 있습니다.

 예약 인스턴스를 PowerShell, CLI 또는 API를 통해 두 개의 예약 인스턴스로 분할할 수 있습니다.

### <a name="split-a-reserved-instance-by-using-powershell"></a>PowerShell을 사용하여 예약 인스턴스 분할
1. 다음 명령을 실행하여 예약 인스턴스 주문 ID를 가져옵니다.

    ```powershell
    # Get the Reserved Instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. 예약 인스턴스의 세부 정보를 가져옵니다.

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. 예약 인스턴스를 두 개로 분할하고 인스턴스를 배포합니다.

    ```powershell
    # Split the Reserved Instance. The sum of the Reserved Instances, the quantity, must equal the total number of instances in the Reserved Instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. 다음 명령을 실행하여 범위를 업데이트할 수 있습니다.

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>예약 인스턴스를 관리할 수 있는 사용자 추가 또는 변경
예약 인스턴스에 대한 역할에 사용자를 추가하여 예약 인스턴스의 관리를 위임할 수 있습니다. 기본적으로 예약 인스턴스를 구입한 사용자 및 계정 관리자에게는 예약 인스턴스에 대한 소유자 역할이 있습니다. 

예약 인스턴스 할인을 받은 구독에서 독립적으로 예약 인스턴스에 대한 액세스 권한을 관리할 수 있습니다. 예약 인스턴스를 관리하는 사용 권한을 부여할 때 구독을 관리하는 권한을 제공하는 것은 아닙니다. 또한 누군가에게 예약 인스턴스의 범위 내에서 구독을 관리하는 사용 권한을 부여할 경우 예약 인스턴스를 관리하는 권한을 제공하는 것은 아닙니다.
 
예약 인스턴스에 대한 액세스 관리를 위임하려면: 
1.  [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2.  **모든 서비스** > **예약**을 선택하여 액세스할 수 있는 예약 인스턴스를 나열합니다.
3.  다른 사용자에게 액세스 권한을 위임하려는 예약 인스턴스를 선택합니다.
4.  메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
5.  **추가** > **역할** > **소유자**(또는 제한된 액세스 권한을 부여하려는 경우 다른 역할)를 선택합니다. 
6. 소유자로 추가할 사용자의 이메일 주소를 입력하세요. 
7. 사용자를 선택한 다음 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계
Azure Reserved Instance에 대한 자세한 내용은 다음 아티클을 참조하세요.

- [Azure Reserved Instance를 사용하여 가상 머신에서 비용 절감](billing-save-compute-costs-reservations.md)
- [Reserved Instance를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Reserved Instance 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독에서 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage.md)
- [Enterprise 등록의 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [예약 인스턴스를 포함하지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
