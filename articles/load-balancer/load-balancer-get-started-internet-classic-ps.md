---
title: 인터넷 연결 부하 분산 장치 만들기 - Azure PowerShell 클래식 | Microsoft Docs
description: PowerShell을 사용하여 클래식 모드에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 07d3658ff86a46875a57cb3359a60661911e0c8b
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>PowerShell에서 인터넷 연결 부하 분산 장치(클래식) 만들기 시작

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Azure 리소스로 작업하기 전에 Azure에는 현재 Azure Resource Manager와 클래식 모드의 두 가지 배포 모델이 있다는 것을 이해해야 합니다. Azure 리소스로 작업하기 전에 [배포 모델 및 도구](../azure-classic-rm.md) 를 이해해야 합니다. 이 문서의 윗부분에 있는 탭을 클릭하여 다양한 도구에 대한 설명서를 볼 수 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>PowerShell을 사용하여 부하 분산 장치 설정

Powershell을 사용하여 부하 분산 장치를 설정하려면 다음 단계를 완료합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.
2. 가상 머신을 만든 후 PowerShell cmdlet을 사용하여 동일한 클라우드 서비스 내에서 가상 머신으로 부하 분산 장치를 추가할 수 있습니다.

다음 예제에서는 "webfarm"이라고 하는 부하 분산 장치 집합을 클라우드 서비스 "mytestcloud"(또는 myctestcloud.cloudapp.net)에 추가하여, 부하 분산 장치의 끝점을 "web1" 및 "web2"라는 가상 머신에 추가합니다. 부하 분산 장치는 포트 80에서 트래픽을 받고 TCP를 사용하여 로컬 끝점(이 경우 포트 80)에서 정의된 가상 머신들 간의 부하를 분산합니다.

### <a name="step-1"></a>1단계

첫 번째 VM "web1"을 위한 부하 분산 끝점 만들기

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>2단계

동일한 부하 분산 장치 집합 이름을 사용하여 두 번째 VM "web2"를 위한 다른 끝점 만들기

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>부하 분산 장치에서 가상 머신 제거

Remove-AzureEndpoint를 사용하여 부하 분산 장치에서 가상 머신 끝점을 제거할 수 있습니다.

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치를 시작](load-balancer-get-started-ilb-classic-ps.md)하고 특정 부하 분산 장치 네트워크 트래픽 동작에 대한 [배포 모드](load-balancer-distribution-mode.md) 유형을 구성할 수도 있습니다.

응용 프로그램이 부하 분산 장치 뒤의 서버에 대한 연결을 유지해야 하는 경우 [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정](load-balancer-tcp-idle-timeout.md)에 대해 자세히 이해할 수 있습니다. Azure Load Balancer를 사용하는 경우 유휴 연결 동작에 대해 알아보는 데 도움이 됩니다.
