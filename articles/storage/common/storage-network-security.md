---
title: Azure Storage 방화벽 및 Virtual Network 구성 | Microsoft Docs
description: 저장소 계정에 대한 계층화된 네트워크 보안을 구성합니다.
services: storage
documentationcenter: ''
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 52d904e7a7e8e5d520d2abd799ef0ae7e99b9894
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192879"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Azure Storage 방화벽 및 Virtual Network 구성
Azure Storage는 계층화된 보안을 제공하여 허용되는 특정 네트워크 집합에만 연결되도록 저장소 계정을 보호할 수 있도록 합니다.  네트워크 규칙이 구성된 경우 허용되는 네트워크의 응용 프로그램만 저장소 계정에 액세스할 수 있습니다.  허용되는 네트워크에서 호출되면 응용 프로그램은 저장소 계정에 액세스하기 위한 적절한 인증(유효한 액세스 키 또는 SAS 토큰)을 계속 요구합니다.

> [!IMPORTANT]
> 저장소 계정에 대한 방화벽 규칙을 설정하면 다른 Azure 서비스에서 들어오는 요청을 포함하여, 들어오는 데이터 요청에 대한 액세스가 차단됩니다.  여기에는 포털 사용, 로그 작성 등이 포함됩니다.  서비스에 참여하기 위해 아래의 [예외](#Exceptions) 섹션에서 기능을 사용하도록 설정할 수 있습니다.  포털에 액세스하려면 설정한 신뢰할 수 있는 경계(IP 또는 VNet) 내의 컴퓨터에서 액세스해야 합니다.
>

## <a name="scenarios"></a>시나리오
기본적으로 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 저장소 계정을 구성할 수 있습니다.  특정 Azure Virtual Network의 트래픽에 대한 액세스 권한을 부여하여 응용 프로그램에 대한 네트워크 보안 경계를 구축하도록 할 수 있습니다.  공용 인터넷 IP 주소 범위에 대한 액세스 권한을 부여하여 특정 인터넷 또는 온-프레미스 클라이언트의 연결을 사용하도록 설정할 수도 있습니다.

네트워크 규칙은 REST 및 SMB를 포함하여 Azure Storage에 대한 모든 네트워크 프로토콜에 적용됩니다.  네트워크 규칙이 강제로 적용될 때 Azure Portal, 저장소 탐색기 및 AZCopy 같은 도구에서 데이터에 액세스하려면 액세스를 허가하는 명시적인 네트워크 규칙이 필요합니다.

네트워크 규칙은 기존 저장소 계정에 적용될 수도 있고, 새 저장소 계정을 만드는 동안 적용될 수도 있습니다.

일단 네트워크 규칙이 적용되면 모든 요청에 대해 적용됩니다.  특정 IP 주소 서비스에 대한 액세스 권한을 부여하는 SAS 토큰은 토큰 소유자의 액세스를 **제한**하지만 구성된 네트워크 규칙 이외의 새 액세스 권한을 부여하지는 않습니다. 

Virtual Machine 디스크 트래픽(탑재 및 분리 작업 및 디스크 IO 포함)은 네트워크 규칙의 영향을 받지 **않습니다**.  페이지 Blob에 대한 REST 액세스는 네트워크 규칙에 의해 보호됩니다.

클래식 저장소 계정은 Firewall 및 Virtual Network를 지원하지 **않습니다**.

네트워크 규칙이 적용된 저장소 계정에서 관리되지 않는 디스크를 사용한 Virtual Machines의 백업 및 복원은 이 문서의 [예외](/storage/common/storage-network-security#exceptions) 섹션에 설명된 대로 예외 만들기를 통해 지원됩니다.  Azure에 의해 이미 관리되므로 방화벽 예외는 Managed Disks를 사용하여 적용되지 않습니다.

## <a name="change-the-default-network-access-rule"></a>기본 네트워크 액세스 규칙 변경
기본적으로 저장소 계정은 네트워크에 있는 모든 클라이언트로부터의 연결을 허용합니다.  선택한 네트워크에 대한 액세스를 제한하려면 먼저 기본 동작을 변경해야 합니다.

> [!WARNING]
> 네트워크 규칙을 변경하면 Azure Storage에 연결하는 응용 프로그램의 기능에 영향을 미칠 수 있습니다.  기본 네트워크 규칙을 **거부**로 설정할 경우 *허가*하는 특정 네트워크 규칙도 적용되는 경우가 아니면 데이터에 대한 모든 액세스가 차단됩니다.  액세스를 거부하도록 기본 규칙을 변경하기 전에 네트워크 규칙을 사용하여 허용된 모든 네트워크에 대한 액세스를 허가해야 합니다.
>

#### <a name="azure-portal"></a>Azure portal
1. 보호하려는 저장소 계정으로 이동합니다.  

2. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.
3. 기본적으로 액세스를 거부하려면 '선택한 네트워크'의 액세스를 허용하도록 선택합니다.  모든 네트워크의 트래픽을 허용하려면 '모든 네트워크'의 액세스를 허용하도록 선택합니다.
4. *저장*을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell
1. 최신 [Azure PowerShell](/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.

2. 저장소 계정에 대한 기본 규칙의 상태를 표시합니다.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0을 설치](/cli/azure/install-azure-cli)하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.
2. 저장소 계정에 대한 기본 규칙의 상태를 표시합니다.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. 기본적으로 네트워크 액세스를 거부하도록 기본 규칙을 설정합니다.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. 기본적으로 네트워크 액세스를 허용하도록 기본 규칙을 설정합니다.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>가상 네트워크의 액세스 허가
특정 Azure Virtual Network의 액세스만 허용하도록 저장소 계정을 구성할 수 있습니다. 

Virtual Network 내에서 Azure Storage에 대한 [서비스 끝점](/azure/virtual-network/virtual-network-service-endpoints-overview)을 사용하도록 설정하면 트래픽이 Azure Storage 서비스에 대한 최적 경로로 전송됩니다. 가상 네트워크 및 서브넷의 ID 또한 각 요청과 함께 전송됩니다.  관리자는 저장소 계정에 대해 Virtual Network의 특정 서브넷에서 요청이 수신될 수 있도록 하는 네트워크 규칙을 구성할 수 있습니다.  이러한 네트워크 규칙을 통해 액세스가 허가된 클라이언트는 데이터에 액세스하기 위해 저장소 계정의 인증 요구 사항을 계속 충족해야 합니다.

각 저장소 계정은 [IP 네트워크 규칙](#grant-access-from-an-internet-ip-range)과 결합될 수 있는 최대 100개의 가상 네트워크 규칙을 지원할 수 있습니다.

### <a name="available-virtual-network-regions"></a>사용 가능한 가상 네트워크 지역
일반적으로 서비스 끝점은 가상 네트워크와 같은 Azure 지역의 서비스 인스턴스 간에 작동합니다.  Azure Storage에서 서비스 끝점이 사용될 경우 이 범위는 [쌍으로 연결된 지역](/azure/best-practices-availability-paired-regions)을 포함하도록 확장됩니다.  이를 통해 지역별 장애 조치(Failover) 동안 연속성이 유지될 뿐 아니라 읽기 전용 지역 중복 저장소(RA-GRS) 인스턴스에 원활하게 액세스할 수 있습니다.  가상 네트워크에서 저장소 계정으로의 액세스를 허가하는 네트워크 규칙은 모든 RA-GRS 인스턴스에 대한 액세스도 허가합니다.

지역 가동 중단 동안 재해 복구를 계획할 때 쌍으로 연결된 지역에서 Virtual Network를 미리 프로비전해야 합니다. Azure Storage에 대한 서비스 끝점이 사용되도록 설정되어야 하고 이러한 대체 Virtual Network의 액세스를 허가하는 네트워크 규칙을 지역 중복 저장소 계정에 적용해야 합니다.

> [!NOTE]
> 서비스 끝점은 Virtual Network 지역 및 지정된 지역 쌍 외부의 트래픽에는 적용되지 않습니다.  Virtual Network에서 저장소 계정으로의 액세스를 허가하는 네트워크 규칙은 저장소 계정의 기본 지역 또는 지정된 쌍으로 연결된 지역의 Virtual Network에만 적용될 수 있습니다.
>

### <a name="required-permissions"></a>필요한 사용 권한
저장소 계정에 Virtual Network 규칙을 적용하려면 추가되는 서브넷에 대해 *서브넷에 서비스 가입* 권한이 사용자에게 있어야 합니다.  이 권한은 *저장소 계정 참가자* 기본 제공 역할에 포함되며 사용자 지정 역할 정의에 추가할 수 있습니다.

저장소 계정 및 Virtual Network를 통해 허가된 액세스 권한은 다른 구독에 **적용될 수 있지만** 해당 구독은 동일한 Azure Active Directory 테넌트의 일부여야 합니다.

### <a name="managing-virtual-network-rules"></a>Virtual Network 규칙 관리
Azure Portal, PowerShell 또는 CLIv2를 통해 저장소 계정에 대한 Virtual Network 규칙을 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal
1. 보호하려는 저장소 계정으로 이동합니다.  
2. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.
3. ‘선택한 네트워크’의 액세스를 허용하도록 선택했는지 확인합니다.
4. 새 네트워크 규칙을 사용하여 Virtual Network에 대한 액세스를 허가하려면 "Virtual Network"에서 "기존 항목 추가"를 클릭하여 기존 Virtual Network 및 서브넷을 선택한 후 *추가*를 클릭합니다.  새 Virtual Network를 만들고 액세스 권한을 부여하려면 *새로 추가*를 클릭하고 새 Virtual Network를 만드는 데 필요한 정보를 제공하고 *만들기*를 클릭합니다.

> [!NOTE]
> Azure Storage에 대한 서비스 끝점이 선택한 Virtual Network 및 서브넷에 대해 미리 구성되지 않은 경우 이 작업을 수행하면서 구성할 수 있습니다.
>

5. Virtual Network 또는 서브넷 규칙을 제거하려면 Virtual Network 또는 서브넷에 대한 상황에 맞는 메뉴를 열고 "..."를 클릭한 후 "제거"를 클릭합니다.
6. *저장*을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell
1. 최신 [Azure PowerShell](/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.
2. Virtual Network 규칙을 나열합니다.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Virtual Network 및 서브넷에서 Azure Storage에 대한 서비스 끝점을 사용하도록 설정합니다.
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Virtual Network 및 서브넷에 대한 네트워크 규칙을 추가합니다.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Virtual Network 및 서브넷에 대한 네트워크 규칙을 제거합니다.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 거부로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.
>

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0을 설치](/cli/azure/install-azure-cli)하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.
2. Virtual Network 규칙을 나열합니다.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Virtual Network 및 서브넷에서 Azure Storage에 대한 서비스 끝점을 사용하도록 설정합니다.
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Virtual Network 및 서브넷에 대한 네트워크 규칙을 추가합니다.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Virtual Network 및 서브넷에 대한 네트워크 규칙을 제거합니다. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 거부로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.
>

## <a name="grant-access-from-an-internet-ip-range"></a>인터넷 IP 범위의 액세스 허가
특정 공용 인터넷 IP 주소 범위의 액세스를 허용하도록 저장소 계정을 구성할 수 있습니다.  이 구성은 일반 인터넷 트래픽은 차단하면서, 특정 인터넷 기반 서비스 및 온-프레미스 네트워크의 액세스를 허가합니다.

허용되는 인터넷 주소 범위는 *16.17.18.0/24* 형식의 [CIDR 표기법](https://tools.ietf.org/html/rfc4632)을 사용해서 또는 *16.17.18.19* 같은 개별 IP 주소로 제공할 수 있습니다.

> [!NOTE]
> "/31" 또는 "/32" 접두사 크기를 사용하는 작은 주소 범위는 지원되지 않습니다.  이러한 범위는 개별 IP 주소 규칙을 사용하여 구성해야 합니다.
>

IP 네트워크 규칙은 **공용 인터넷** IP 주소에 대해서만 허용됩니다.  개인 네트워크용으로 예약된 IP 주소 범위(RFC 1918에 정의)는 IP 규칙에서 허용되지 않습니다.  개인 네트워크는 *10.\**, *172.16.\** 및 *192.168\** 로 시작하는 주소를 포함합니다.

현재 IPv4 주소만 지원됩니다.

각 저장소 계정은 [Virtual Network 규칙](#grant-access-from-a-virtual-network)과 결합될 수 있는 최대 100개의 IP 네트워크 규칙을 지원할 수 있습니다.

### <a name="configuring-access-from-on-premises-networks"></a>온-프레미스 네트워크에서의 액세스 구성
IP 네트워크 규칙을 사용하여 온-프레미스 네트워크에서 저장소 계정으로의 액세스를 허가하려면 네트워크에서 사용되는 인터넷 연결 IP 주소를 식별해야 합니다.  네트워크 관리자에게 도움을 요청합니다.

네트워크가 [ExpressRoute](/azure/expressroute/expressroute-introduction)를 사용하여 Azure 네트워크에 연결된 경우 각 회로는 Microsoft Edge에서 [Azure 공용 피어링](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)을 사용하여 Azure Storage와 같은 Microsoft 서비스에 연결하는 데 사용되는 2개의 공용 IP 주소로 구성됩니다.  회로에서 Azure Storage로의 통신을 허용하려면 회로의 공용 IP 주소에 대한 IP 네트워크 규칙을 만들어야 합니다.  ExpressRoute 회로 공용 P 주소를 찾기 위해 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).


### <a name="managing-ip-network-rules"></a>IP 네트워크 규칙 관리
Azure Portal, PowerShell 또는 CLIv2를 통해 저장소 계정에 대한 IP 네트워크 규칙을 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal
1. 보호하려는 저장소 계정으로 이동합니다.  
2. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.
3. ‘선택한 네트워크’의 액세스를 허용하도록 선택했는지 확인합니다.
4. 인터넷 IP 범위에 액세스를 허가하려면 방화벽, 주소 범위에서 IP 주소 또는 주소 범위(CIDR 형식)를 입력합니다.
5. IP 네트워크 규칙을 제거하려면 "..."를 클릭하여 규칙에 대한 상황에 맞는 메뉴를 열고 "제거"를 클릭합니다.
6. *저장*을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell
1. 최신 [Azure PowerShell](/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.
2. IP 네트워크 규칙을 나열합니다.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 거부로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.
>

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0을 설치](/cli/azure/install-azure-cli)하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.
2. IP 네트워크 규칙을 나열합니다.
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. 개별 IP 주소에 대한 네트워크 규칙을 추가합니다.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. IP 주소 범위에 대한 네트워크 규칙을 추가합니다.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. 개별 IP 주소에 대한 네트워크 규칙을 제거합니다.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. IP 주소 범위에 대한 네트워크 규칙을 제거합니다.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 거부로 설정해야 합니다. 그렇지 않으면 네트워크 규칙이 적용되지 않습니다.
>

## <a name="exceptions"></a>예외
네트워크 규칙은 대부분의 시나리오에 대해 보안 네트워크 구성을 사용하도록 설정할 수 있지만, 전체 기능을 사용하도록 설정하기 위해 예외를 적용해야 하는 경우도 있습니다.  저장소 계정은 신뢰할 수 있는 Microsoft 서비스에 대한 예외와 저장소 분석 데이터에 대한 액세스로 구성할 수 있습니다.

### <a name="trusted-microsoft-services"></a>신뢰할 수 있는 Microsoft 서비스
저장소 계정과 상호 작용하는 일부 Microsoft 서비스는 네트워크 규칙을 통해 액세스를 허가할 수 없는 네트워크에서 작동합니다. 

이러한 유형의 서비스가 의도한 대로 작동하도록 하려면 신뢰할 수 있는 Microsoft 서비스 집합이 네트워크 규칙을 무시하도록 허용할 수 있습니다. 이러한 서비스는 강력한 인증을 사용하여 저장소 계정에 액세스합니다.

"신뢰할 수 있는 Microsoft 서비스" 예외를 사용하도록 설정하면 다음과 같은 서비스(구독에 등록)는 저장소 계정에 대한 액세스가 허가됩니다.

|서비스|리소스 공급자 이름|목적|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|IAAS 가상 머신에서 관리되지 않는 디스크의 백업 및 복원을 수행합니다. (관리되는 디스크에 필요 없음). [자세히 알아보기](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|사용자 지정 이미지 만들기 및 아티팩트 설치.  [자세히 알아보기](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Blob Storage 이벤트 게시를 사용하도록 설정합니다.  [자세히 알아보기](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Event Hubs 캡처로 데이터를 보관합니다.  [자세한 정보](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Azure 네트워킹|Microsoft.Networking|네트워크 트래픽 로그를 저장 및 분석합니다.  [자세히 알아보기](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>저장소 분석 데이터 액세스
경우에 따라 네트워크 경계 밖에서 진단 로그 및 메트릭을 읽을 수 있는 권한이 필요합니다.  저장소 계정 로그 파일이나 메트릭 테이블 또는 둘 다에 대해 읽기 액세스를 허용하도록 네트워크 규칙에 대한 예외를 허가할 수 있습니다. [저장소 분석 작업에 대한 자세한 정보](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>예외 관리
Azure Portal, PowerShell 또는 Azure CLI v2를 통해 네트워크 규칙 예외를 관리할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal
1. 보호하려는 저장소 계정으로 이동합니다.  
2. **Firewall 및 Virtual Network**이라는 설정 메뉴를 클릭합니다.
3. ‘선택한 네트워크’의 액세스를 허용하도록 선택했는지 확인합니다.
4. 예외에서 허가할 예외를 선택합니다.
5. *저장*을 클릭하여 변경 내용을 적용합니다.

#### <a name="powershell"></a>PowerShell
1. 최신 [Azure PowerShell](/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](/powershell/azure/authenticate-azureps)합니다.
2. 저장소 계정 네트워크 규칙에 대한 예외를 표시합니다.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. 저장소 계정 네트워크 규칙에 대한 예외를 구성합니다.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. 저장소 계정 네트워크 규칙에 대한 예외를 제거합니다.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 거부로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 소용이 없습니다.
>

#### <a name="cliv2"></a>CLIv2
1. [Azure CLI 2.0을 설치](/cli/azure/install-azure-cli)하고 [로그인](/cli/azure/authenticate-azure-cli)합니다.
2. 저장소 계정 네트워크 규칙에 대한 예외를 표시합니다.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. 저장소 계정 네트워크 규칙에 대한 예외를 구성합니다.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. 저장소 계정 네트워크 규칙에 대한 예외를 제거합니다.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> 반드시 [기본 규칙](#change-the-default-network-access-rule)을 거부로 설정해야 합니다. 그렇지 않으면 예외를 제거해도 소용이 없습니다.
>

## <a name="next-steps"></a>다음 단계
[서비스 끝점](/azure/virtual-network/virtual-network-service-endpoints-overview)에서 Azure 네트워크 서비스 끝점에 대해 자세히 알아봅니다.

[Azure Storage 보안 가이드](storage-security-guide.md)에서 Azure Storage 보안에 대해 자세히 알아봅니다.
