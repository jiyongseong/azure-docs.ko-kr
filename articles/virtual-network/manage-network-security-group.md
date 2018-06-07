---
title: Azure 네트워크 보안 그룹 만들기, 변경 또는 삭제 | Microsoft 문서
description: 네트워크 보안 그룹을 만들거나, 변경하거나, 삭제하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 7a244a5dbb86b076f99975ad477d4062699270b5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894252"
---
# <a name="create-change-or-delete-a-network-security-group"></a>네트워크 보안 그룹을 만들기, 변경 또는 삭제

네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 네트워크 보안 그룹을 잘 모르는 경우 [네트워크 보안 그룹 개요](security-overview.md)를 참조하여 자세히 알아보고 네트워크 보안 그룹에 대한 경험을 하려면 [네트워크 트래픽 필터링](tutorial-filter-network-traffic.md) 자습서를 완료합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서 설명하는 모든 섹션의 단계를 수행하기 전에 다음 작업을 완료해야 합니다.

- 아직 Azure 계정이 없으면 [평가판 계정](https://azure.microsoft.com/free)에 등록합니다.
- 포털을 사용하는 경우 https://portal.azure.com을 열고 Azure 계정으로 로그인합니다.
- 이 문서의 작업을 완료하기 위해 PowerShell 명령을 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/powershell)에서 명령을 실행하거나 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 이 자습서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.
- 이 문서의 작업을 완료하기 위해 Azure CLI(명령줄 인터페이스)를 사용하는 경우 [Azure Cloud Shell](https://shell.azure.com/bash)에서 명령을 실행하거나 컴퓨터에서 CLI를 실행합니다. 이 자습서에는 Azure CLI 버전 2.0.28 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

Azure에 로그인하거나 연결할 때 사용하는 계정이 [권한](#permissions)에 나열된 적절한 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)이나 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할에 할당되어야 합니다.

## <a name="work-with-network-security-groups"></a>네트워크 보안 그룹으로 작업

네트워크 보안 그룹의 만들기, [모두 보기](#view-all-network-security-groups), [세부 정보 보기](#view-details-of-a-network-security-group), [변경](#change-a-network-security-group) 및 [삭제](#delete-a-network-security-group)를 수행할 수 있습니다. 네트워크 인터페이스 또는 서브넷에서 네트워크 보안 그룹을 [연결 또는 분리](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource)할 수 있습니다.

### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

Azure 위치와 구독별로 만들 수 있는 네트워크 보안 그룹 수에 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

1. 포털의 왼쪽 상단 모서리에서 **+ 리소스 만들기**를 선택합니다.
2. **네트워킹**을 선택한 다음, **네트워크 보안 그룹**을 선택합니다.
3. 네트워크 보안 그룹의 **이름**을 입력하고 **구독**을 선택하고 새 **리소스 그룹**을 만들거나 기존 리소스 그룹을 선택하고 **위치**를 선택한 다음, **만들기**를 선택합니다.

**명령**

- Azure CLI: [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>모든 네트워크 보안 그룹 보기

포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다. 구독에 있는 네트워크 보안 그룹이 나열됩니다.

**명령**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>네트워크 보안 그룹 세부 정보 보기

1. 포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다.
2. 목록에서 세부 정보를 볼 네트워크 보안 그룹을 선택합니다. **설정**에서 **인바운드 보안 규칙** 및 **아웃바운드 보안 규칙**, **네트워크 인터페이스** 및 네트워크 보안 그룹이 연결된 **서브넷**을 볼 수 있습니다. 또한 **진단 로그**를 사용하거나 사용하지 않도록 설정하고 **효과적인 보안 규칙**을 볼 수 있습니다. 자세한 정보는 [진단 로그](virtual-network-nsg-manage-log.md) 및 [효과적인 보안 규칙](virtual-network-nsg-troubleshoot-portal.md)을 참조합니다.
3. 나열된 일반적인 Azure 설정에 대한 자세한 내용은 다음 문서를 참조하세요.
    *   [활동 로그](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [액세스 제어(IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [태그](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [잠금](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automation 스크립트](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**명령**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>네트워크 보안 그룹 변경

1. 포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다.
2. 변경하려는 네트워크 보안 그룹을 선택합니다. 가장 일반적인 변경은 보안 규칙을 [추가](#create-a-security-rule) 또는 [제거](#delete-a-security-rule)하고 [서브넷 또는 네트워크 인터페이스에서 네트워크 보안 그룹 연결 또는 분리](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)하는 것입니다.

**명령**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>네트워크 인터페이스 또는 서브넷에서 네트워크 보안 그룹 연결 또는 분리

네트워크 보안 그룹을 네트워크 인터페이스에 연결하거나 분리하려면 [네트워크 인터페이스에 네트워크 보안 그룹 연결 또는 분리](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)를 참조합니다. 네트워크 보안 그룹을 서브넷에 연결하거나 분리하려면 [서브넷 설정 변경](virtual-network-manage-subnet.md#change-subnet-settings)을 참조합니다.

### <a name="delete-a-network-security-group"></a>네트워크 보안 그룹 삭제

서브넷 또는 네트워크 인터페이스에 네트워크 보안 그룹이 연결되어 있는 경우 삭제할 수 없습니다. 네트워크 보안 그룹을 삭제하기 전에 모든 서브넷 및 네트워크 인터페이스에서 [분리](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource)합니다.

1. 포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다.
2. 목록에서 삭제하려는 네트워크 보안 그룹을 선택합니다.
3. **삭제**를 선택한 후 **예**를 선택합니다.

**명령**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>보안 규칙으로 작업

네트워크 보안 그룹은 0개 이상의 보안 규칙을 포함합니다. 보안 규칙의 만들기, [모두 보기](#view-all-security-rules), [세부 정보 보기](#view-details-of-a-security-rule), [변경](#change-a-security-rule) 및 [삭제](#delete-a-security-rule)를 수행할 수 있습니다.

### <a name="create-a-security-rule"></a>보안 규칙 만들기

Azure 위치와 구독별로 만들 수 있는 네트워크 보안 그룹당 규칙 수에 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.

1. 포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다.
2. 목록에서 보안 규칙을 추가하려는 네트워크 보안 그룹을 선택합니다.
3. **설정**에서 **인바운드 보안 규칙**을 선택합니다. 기존 규칙이 여러 개 나열되어 있습니다. 추가할 수 없는 일부 규칙입니다. 네트워크 보안 그룹을 만들 때 해당 보안 그룹 안에 몇 가지 기본 보안 규칙이 만들어집니다. 자세한 내용은 [기본 보안 규칙](security-overview.md#default-security-rules)을 참조하세요.  기본 보안 규칙을 삭제할 수 없으나 더 높은 우선 순위의 규칙으로 재정의할 수 있습니다.
4. **+ 추가**를 <a name = "security-rule-settings"></a>선택합니다.  다음 설정에 대한 값을 추가하거나 선택한 다음, **확인**을 선택합니다.
    
    |설정  |값  |세부 정보  |
    |---------|---------|---------|
    |원본     | **모든**, **IP 주소** 또는 **서비스 태그**를 선택합니다.        | **IP 주소**를 선택하는 경우 **원본 IP 주소/CIDR 범위**를 지정해야 합니다. 단일 값 또는 쉼표로 구분된 다중 값 목록을 지정할 수 있습니다. 다중 값의 예는 10.0.0.0/16, 192.188.1.1입니다. 지정할 수 있는 값의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요. **서비스 태그**를 선택하는 경우 서비스 태그 하나를 선택해야 합니다. 서비스 태그는 IP 주소 범주에 대한 사전 정의된 식별자입니다. 사용할 수 있는 서비스 태그 및 각 태그의 의미에 대한 자세한 내용은 [서비스 태그](security-overview.md#service-tags) 참조        |
    |원본 포트 범위     | 80 같은 단일 포트, 1024-65535 같은 포트 범위 또는 80 및 1024-65535 같이 쉼표로 구분된 단일 포트 및/또는 포트 범위를 지정합니다. 모든 포트에 트래픽을 허용하려면 별표를 입력합니다. | 포트 및 범위는 규칙이 허용 또는 거부하는 포트 트래픽을 지정합니다. 지정할 수 있는 포트의 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)을 참조하세요.  |
    |대상     | **모든**, **IP 주소** 또는 **가상 네트워크**를 선택합니다.        | **IP 주소**를 선택하는 경우 **대상 IP 주소/CIDR 범위**를 지정해야 합니다. **원본** 및 **원본 IP 주소/CIDR 범위**와 비슷한 경우 단일 또는 여러 주소나 범위를 지정할 수 있으며 지정할 수 있는 수에는 제한이 있습니다. 서비스 태그인 **가상 네트워크**를 선택하면 가상 네트워크의 주소 공간 내의 모든 IP 주소에 트래픽이 허용됩니다.        |
    |대상 포트 범위     | 단일 값 또는 쉼표로 구분된 값의 목록을 지정합니다. | **원본 포트 범위**와 비슷한 경우 단일 또는 여러 포트 및 범위를 지정할 수 있으며 지정할 수 있는 수에는 제한이 있습니다. |
    |프로토콜     | **모든**, **TCP** 또는 **UDP**를 선택합니다.        |         |
    |조치     | **허용** 또는 **거부**를 선택합니다.        |         |
    |우선 순위     | 네트워크 보안 그룹 내의 모든 보안 규칙에 대해 100-4096 사이의 고유한 값을 입력합니다. |규칙은 우선 순위에 따라 처리됩니다. 번호가 낮을수록 우선 순위가 높습니다. 규칙을 만들 때 100, 200, 300 같이 우선 순위 번호 사이의 간격을 그대로 두는 것이 좋습니다. 간격을 그대로 두면 향후 기존 규칙보다 우선 순위가 더 높거나 더 낮게 만들 필요가 있는 규칙을 추가하기가 더 쉬워집니다.         |
    |Name     | 네트워크 보안 그룹 내에서 규칙에 대한 고유한 이름입니다.        |  이름은 최대 80자까지 가능합니다. 이름은 영문, 숫자, 밑줄, 마침표 또는 하이픈만 포함할 수 있습니다. 단 영문 또는 숫자로 시작하고 영문, 숫자 또는 밑줄로 끝나야 합니다.       |
    |설명     | 선택적 설명입니다.        |         |

    포털을 사용하면 **원본** 또는 **대상** 설정에 대한 [응용 프로그램 보안 그룹](#work-with-application-security-groups)을 지정할 수 없습니다. 그러나 Azure CLI 또는 PowerShell을 사용하면 지정할 수 있습니다. **아웃 바운드 보안 규칙**에 대한 설정도 비슷하므로 별도로 다루지 않습니다.

**명령**

- Azure CLI: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>모든 보안 규칙 보기

네트워크 보안 그룹은 0개 이상의 규칙을 포함합니다. 경로를 볼 때 나열되는 정보에 대한 자세한 내용은 [네트워크 보안 그룹 개요](security-overview.md)를 참조합니다.

1. 포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다.
2. 목록에서 규칙을 보려는 네트워크 보안 그룹을 선택합니다.
3. **설정**에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙**을 선택합니다.

목록은 만든 모든 규칙 및 네트워크 보안 그룹 [기본 보안 규칙](security-overview.md#default-security-rules)을 포함합니다.

**명령**

- Azure CLI: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>보안 규칙의 세부 정보 보기

1. 포털 맨 위에 있는 검색 상자에 *네트워크 보안 그룹*를 입력합니다. 검색 결과에 표시된 **네트워크 보안 그룹**을 선택합니다.
2. 보안 규칙의 세부 정보를 보려는 네트워크 보안 그룹을 선택합니다.
3. **설정**에서 **인바운드 보안 규칙** 또는 **아웃바운드 보안 규칙**을 선택합니다.
4. 세부 정보를 보려는 규칙을 선택합니다. 모든 설정에 대한 자세한 내용은 [보안 규칙 설정](#security-rule-settings)을 참조합니다.

**명령**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>보안 규칙 변경

1. [보안 규칙 세부 정보 보기](#view-details-of-a-security-rule)에서 단계를 완료합니다.
2. 필요에 따라 설정을 변경한 다음, **저장**을 선택합니다. 모든 설정에 대한 자세한 내용은 [보안 규칙 설정](#security-rule-settings)을 참조합니다.

**명령**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>보안 규칙 삭제

1. [보안 규칙 세부 정보 보기](#view-details-of-a-security-rule)에서 단계를 완료합니다.
2. **삭제**를 선택한 후 **예**를 선택합니다.

**명령**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzureRmSecurityRuleConfig](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>응용 프로그램 보안 그룹으로 작업

응용 프로그램 보안 그룹에는 0개 이상의 네트워크 인터페이스가 포함되어 있습니다. 자세한 내용은 [응용 프로그램 보안 그룹](security-overview.md#application-security-groups)을 참조하세요. 포털에서 응용 프로그램 보안 그룹으로 작업할 수 없지만 Azure CLI 또는 PowerShell은 사용할 수 있습니다. 응용 프로그램 보안 그룹의 모든 네트워크 인터페이스는 동일한 가상 네트워크에 있어야 합니다. 응용 프로그램 보안 그룹에 추가된 첫 번째 네트워크 인터페이스는 모든 후속 네트워크 인터페이스가 있어야 할 가상 네트워크를 결정합니다. 응용 프로그램 보안 그룹에 네트워크 인터페이스를 추가하는 방법을 알아보려면 [응용 프로그램 보안 그룹에 네트워크 인터페이스 추가](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)를 참조합니다.

### <a name="create-an-application-security-group"></a>응용 프로그램 보안 그룹 만들기

- Azure CLI: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>모든 응용 프로그램 보안 그룹 보기

- Azure CLI: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>특정 응용 프로그램 보안 그룹의 세부 정보 보기

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>응용 프로그램 보안 그룹 변경

기존 응용 프로그램 보안 그룹에 대한 권한과 태그 등의 일부 설정을 변경할 수 있지만 해당 이름이나 위치를 변경할 수는 없습니다.

- Azure CLI: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: PowerShell cmdlet이 없습니다.

### <a name="delete-an-application-security-group"></a>응용 프로그램 보안 그룹 삭제

응용 프로그램 보안 그룹에 모든 네트워크 인터페이스가 있는 경우 해당 보안 그룹은 삭제할 수 없습니다. 네트워크 인터페이스 설정을 변경하거나 네트워크 인터페이스를 삭제하여 응용 프로그램 보안 그룹에서 모든 네트워크 인터페이스를 제거해야 합니다. 자세한 내용은 [응용 프로그램 보안 그룹에서 네트워크 인터페이스 추가 또는 제거](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) 또는 [네트워크 인터페이스 삭제](virtual-network-network-interface.md#delete-a-network-interface)를 참조합니다.

**명령**

- Azure CLI: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>권한

네트워크 보안 그룹, 보안 규칙 및 응용 프로그램 보안 그룹에서 작업을 수행하려면 다음 표에 나열된 적절한 사용 권한이 할당된 [네트워크 기여자](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) 역할 또는 [사용자 지정](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 역할에 계정을 할당해야 합니다.

### <a name="network-security-groups"></a>네트워크 보안 그룹

| 조치                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/ruleTables/read                             |   네트워크 보안 그룹 가져오기                                          |
| Microsoft.Network/ruleTables/write                            |   네트워크 보안 그룹 만들기 또는 업데이트                             |
| Microsoft.Network/ruleTables/delete                           |   네트워크 보안 그룹 삭제                                       |
| Microsoft.Network/ruleTables/join/action                      |   네트워크 보안 그룹을 서브넷 또는 네트워크 인터페이스에 연결 |
| Microsoft.Network/ruleTables/rules/read                       |   규칙 가져오기                                                            |
| Microsoft.Network/ruleTables/rules/write                      |   규칙 만들기 또는 업데이트                                               |
| Microsoft.Network/ruleTables/rules/delete                     |   규칙 삭제                                                         |
| Microsoft.Network/networkInterfaces/effectiveruleTable/action |   네트워크 인터페이스 유효 네트워크 보안 그룹 가져오기              |
| Microsoft.Network/networkWatchers/nextHop/action              |   VM에서 다음 홉 가져오기                                         |

### <a name="application-security-groups"></a>응용 프로그램 보안 그룹

| 조치                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP 구성을 응용 프로그램 보안 그룹에 조인|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | 보안 규칙을 응용 프로그램 보안 그룹에 조인    |
| Microsoft.Network/applicationSecurityGroups/read                           | 응용 프로그램 보안 그룹 가져오기                        |
| Microsoft.Network/applicationSecurityGroups/write                          | 응용 프로그램 보안 그룹 만들기 또는 업데이트           |
| Microsoft.Network/applicationSecurityGroups/delete                         | 응용 프로그램 보안 그룹 삭제                     |

## <a name="next-steps"></a>다음 단계

- [PowerShell](powershell-samples.md) 또는 [Azure CLI](cli-samples.md) 샘플 스크립트를 사용하거나 Azure [Resource Manager 템플릿](template-samples.md)을 사용하여 네트워크 또는 응용 프로그램 보안 그룹 만들기
- 가상 네트워크에 대한 [Azure 정책](policy-samples.md) 만들기 및 적용