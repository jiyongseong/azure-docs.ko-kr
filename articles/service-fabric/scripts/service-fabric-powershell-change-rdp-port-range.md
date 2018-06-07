---
title: Azure PowerShell 스크립트 샘플 - RDP 포트 범위 변경 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 배포된 클러스터의 RDP 포트 범위를 변경합니다.
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
ms.date: 03/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 83fb6cc03f605a60b06f31fa6ddd82cd4e3e899e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="update-the-rdp-port-range-values"></a>RDP 포트 범위 값 업데이트

이 샘플 스크립트는 클러스터가 배포된 후 클러스터 노드 VM에서 RDP 포트 범위 값을 변경합니다.  Azure PowerShell이 사용되므로 기본 VM을 끄지 않습니다.  이 스크립트는 클러스터 리소스 그룹의 `Microsoft.Network/loadBalancers` 리소스를 가져오고 `inboundNatPools.frontendPortRangeStart` 및 `inboundNatPools.frontendPortRangeEnd` 값을 업데이트합니다. 필요에 따라 매개 변수를 사용자 지정합니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/overview)에 있는 지침을 사용하여 Azure PowerShell을 설치합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | `Microsoft.Network/loadBalancers` 리소스를 가져옵니다. |
|[Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource)|`Microsoft.Network/loadBalancers` 리소스를 업데이트합니다.|

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

Azure Service Fabric에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../service-fabric-powershell-samples.md)에서 확인할 수 있습니다.
