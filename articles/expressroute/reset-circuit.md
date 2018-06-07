---
title: '실패한 Azure ExpressRoute 회로 다시 설정: PowerShell | Microsoft Docs'
description: 이 문서는 실패한 상태의 ExpressRoute 회로를 다시 설정하는 데 도움이 됩니다.
documentationcenter: na
services: expressroute
author: anzaman
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 423bc1d6409e5b7fe02339a05d0775f4ff42de49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="reset-a-failed-expressroute-circuit"></a>실패한 Azure ExpressRoute 회로 다시 설정

ExpressRoute 회로의 작업이 성공적으로 완료되지 않으면 회로가 '실패' 상태가 될 수 있습니다. 이 문서는 실패한 Azure ExpressRoute 회로를 다시 설정하는 데 도움이 됩니다.

## <a name="reset-a-circuit"></a>회로 다시 설정

1. 최신 버전의 Azure Resource Manager PowerShell cmdlet을 설치합니다. 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/install-azurerm-ps)을 참조하세요.

2. 상승된 권한으로 PowerShell 콘솔을 열고 계정에 연결합니다. 연결에 도움이 되도록 다음 예제를 사용합니다.

  ```powershell
  Connect-AzureRmAccount
  ```
3. Azure 구독이 여러 개인 경우 계정의 구독을 확인합니다.

  ```powershell
  Get-AzureRmSubscription
  ```
4. 사용할 구독을 지정합니다.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. 다음 명령을 실행하여 실패한 상태의 회로를 다시 설정합니다.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

회로가 이제 정상이 됩니다. 회로가 여전이 실패한 상태이면 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)으로 지원 티켓을 엽니다.

## <a name="next-steps"></a>다음 단계

여전히 문제가 해결되지 않을 경우 [Microsoft 지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 으로 지원 티켓을 엽니다.
