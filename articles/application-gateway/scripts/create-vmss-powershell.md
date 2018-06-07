---
title: Azure PowerShell 스크립트 샘플 - 웹 트래픽 관리 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 응용 프로그램 게이트웨이 및 가상 머신 확장 집합을 사용하여 웹 트래픽을 관리합니다.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ef6a4171c582e3eb82fbcc73171797e1c806de05
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Azure PowerShell을 사용하여 웹 트래픽 관리

이 스크립트는 백 엔드 서버에 가상 머신 확장 집합을 사용하는 응용 프로그램 게이트웨이를 만듭니다. 그런 다음, 웹 트래픽을 관리하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 스크립트를 실행한 후에는 공용 IP 주소를 사용하여 응용 프로그램 게이트웨이를 테스트할 수 있습니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 응용 프로그램 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. |
| [새-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | 서브넷 구성을 사용하여 가상 네트워크를 만듭니다. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | 응용 프로그램 게이트웨이의 공용 IP 주소를 만듭니다. |
| [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) | 서브넷을 응용 프로그램 게이트웨이에 연결하는 구성을 만듭니다. |
| [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) | 응용 프로그램 게이트웨이에 공용 IP 주소를 할당하는 구성을 만듭니다. |
| [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) | 응용 프로그램 게이트웨이에 액세스하는 데 사용할 포트를 할당합니다. |
| [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) | 응용 프로그램 게이트웨이의 백 엔드 풀을 만듭니다. |
| [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) | 백 엔드 풀에 대한 설정을 구성합니다. |
| [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) | 수신기를 만듭니다. |
| [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) | 라우팅 규칙을 만듭니다. |
| [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) | 응용 프로그램 게이트웨이의 계층 및 용량을 지정합니다. |
| [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) | Application Gateway를 만듭니다. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | 확장 집합의 저장소 프로필을 만듭니다. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | 확장 집합의 운영 체제를 정의합니다. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | 확장 집합의 네트워크 인터페이스를 정의합니다. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm) | 가상 머신 확장 집합을 만듭니다. |
| [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | 응용 프로그램 게이트웨이의 공용 IP 주소를 가져옵니다. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. | 

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 응용 프로그램 게이트웨이 PowerShell 스크립트 샘플은 [Azure Application Gateway 설명서](../powershell-samples.md)에서 찾을 수 있습니다.
