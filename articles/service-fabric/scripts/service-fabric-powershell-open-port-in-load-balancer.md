---
title: Azure PowerShell 스크립트 샘플 - Load Balancer에서 응용 프로그램 포트 열기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - Service Fabric 응용 프로그램에 대한 Azure Load Balancer에서 응용 프로그램 포트 열기
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 0549f5f2b5b0f8fdfc18b8c091c1065d6137b8c6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366174"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Azure Load Balancer에서 응용 프로그램 포트 열기

Azure에서 실행되는 Service Fabric 응용 프로그램은 Azure Load Balancer 뒤에 있습니다. 이 샘플 스크립트는 Service Fabric 응용 프로그램이 외부 클라이언트와 통신할 수 있도록 Azure Load Balancer에서 포트를 엽니다. 필요에 따라 매개 변수를 사용자 지정합니다. 또한 클러스터가 네트워크 보안 그룹에 위치하는 경우 [인바운드 네트워크 보안 그룹 규칙을 추가](service-fabric-powershell-add-nsg-rule.md)하여 인바운드 트래픽을 허용합니다.

필요한 경우 [Service Fabric SDK](../service-fabric-get-started.md)를 사용하여 Service Fabric PowerShell 모듈을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Azure 리소스를 가져옵니다.  |
| [Get-AzureRmLoadBalancer](/powershell/module/azurerm.network/get-azurermloadbalancer) | Azure Load Balancer를 가져옵니다. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig) | Load Balancer에 프로브 구성을 추가합니다.|
| [Get-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/get-azurermloadbalancerprobeconfig) | Load Balancer에 대한 프로브 구성을 가져옵니다. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig) | Load Balancer에 규칙 구성을 추가합니다. |
| [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer) | Load Balancer에 대한 목표 상태를 설정합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
