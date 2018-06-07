---
title: Azure Virtual Network 서비스 엔드포인트를 사용하여 Azure Cosmos DB 계정에 보안 액세스 | Microsoft Docs
description: 이 문서에서는 Azure Cosmos DB 가상 네트워크 서비스 엔드포인트에 필요한 단계를 설명합니다.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 05/07/2018
ms.author: govindk
ms.openlocfilehash: b07a159e69a11656555a8550b807cce0b2c9ef6c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="secure-access-to-an-azure-cosmos-db-account-by-using-azure-virtual-network-service-endpoint"></a>Azure Virtual Network 서비스 엔드포인트를 사용하여 Azure Cosmos DB 계정에 보안 액세스

Azure Virtual Network의 특정 서브넷에서만 액세스할 수 있도록 Azure CosmosDB 계정을 구성할 수 있습니다. Virtual Network 및 해당 서브넷에서 Azure CosmosDB에 대한 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 사용하도록 설정하면 트래픽에서 Azure Cosmos DB에 대한 최적의 안전한 라우팅이 보장됩니다.  

Azure Cosmos DB는 전 세계에 배포된 다중 모델 데이터베이스 서비스입니다. 여러 영역에 Azure Cosmos DB 계정에 있는 데이터를 복제할 수 있습니다. Azure Cosmos DB를 가상 네트워크 서비스 엔드포인트로 구성하면 특정 서브넷에 속하는 IP에서 각 가상 네트워크에 액세스할 수 있습니다. 다음 이미지에는 가상 네트워크 서비스 엔드포인트를 활성화한 Azure Cosmos DB의 일러스트레이션이 나와 있습니다.

![가상 네트워크 서비스 엔드포인트 아키텍처](./media/vnet-service-endpoint/vnet-service-endpoint-architecture.png)

가상 네트워크 서비스 엔드포인트로 Azure Cosmos DB 계정을 구성하면 지정된 서브넷에서만 액세스할 수 있으며, 모든 공용/인터넷 액세스가 제거됩니다. 서비스 엔드포인트에 대해 자세히 알아보려면 Azure [가상 네트워크 서비스 엔드포인트 개요](../virtual-network/virtual-network-service-endpoints-overview.md) 문서를 참조하세요.

> [!NOTE]
> 현재 Azure Cosmos DB SQL API 또는 Mongo API 계정에 대해 Virtual Network 서비스 엔드포인트를 구성할 수 있습니다. Azure Germany 또는 Azure Government와 같은 소버린 클라우드 및 다른 API에 대해 서비스 엔드포인트를 구성하는 기능은 곧 제공될 예정입니다. Azure Cosmos DB 계정에 대해 구성된 기존 IP 방화벽이 있는 경우 방화벽 구성에 대해 주의하고, IP 방화벽을 제거한 다음, 서비스 엔드포인트 ACL을 구성합니다. 서비스 엔드포인트를 구성한 후 필요한 경우에 IP 방화벽을 다시 활성화할 수 있습니다.

## <a name="configure-service-endpoint-by-using-azure-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트 구성
### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 Azure Cosmos DB에 대한 서비스 엔드포인트를 구성할 가상 네트워크를 찾습니다. **서비스 엔드포인트** 블레이드로 이동하고 가상 네트워크의 서브넷이 “Azure.CosmosDB” 서비스 엔드포인트에 대해 사용하도록 설정되었는지 확인합니다.  

   ![사용하도록 설정된 서비스 엔드포인트 확인](./media/vnet-service-endpoint/confirm-service-endpoint-enabled.png)

2. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.  

3. 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하기 전에 나중에 사용할 수 있도록 Azure Cosmos DB 계정과 연결된 IP 방화벽 정보를 복사합니다. 서비스 엔드포인트를 구성한 후 IP 방화벽을 다시 설정할 수 있습니다.  

4. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택하고 **선택된 네트워크**에서 액세스 허용을 선택합니다.  

3. 기존 가상 네트워크의 서브넷에 대한 액세스를 허용하려면 가상 네트워크에서 **기존 Azure Virtual Network 추가**를 선택합니다.  

4. Azure Virtual Network를 추가할 **구독**을 선택합니다. Azure Cosmos DB 계정에 대한 액세스를 제공하려는 Azure **가상 네트워크** 및 **서브넷**을 차례로 선택합니다. 다음으로 **사용**을 선택하여 선택한 네트워크가 “Microsoft.AzureCosmosDB”에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 완료되면 **추가**를 선택합니다.  

   ![Virtual Network 및 서브넷 선택](./media/vnet-service-endpoint/choose-subnet-and-vnet.png)

   > [!NOTE]
   > Azure Cosmos DB에 대한 서비스 엔드포인트가 선택한 Azure Virtual Network 및 서브넷에 대해 미리 구성되지 않은 경우 이 작업을 수행하면서 구성할 수 있습니다. 액세스 활성화는 완료하는 데 최대 15분이 걸릴 수 있습니다. 

   ![성공적으로 구성된 가상 네트워크 및 서브넷](./media/vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

이제 Azure Cosmos DB 계정은 여기서 선택한 서브넷의 트래픽만 허용하게 됩니다. 이전에 IP 방화벽을 활성화한 경우 이전 정보를 사용하여 다시 활성화하세요.

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>새로운 Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.  

2. 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하기 전에 나중에 사용할 수 있도록 Azure Cosmos DB 계정과 연결된 IP 방화벽 정보를 복사합니다. 서비스 엔드포인트를 구성한 후 IP 방화벽을 다시 설정할 수 있습니다.  

3. 설정 메뉴에서 **방화벽 및 Azure Virtual Network**를 선택하고 **선택된 네트워크**에서 액세스 허용을 선택합니다.  

4. 새로운 Azure Virtual Network에 대한 액세스를 허용하려면 가상 네트워크에서 **새로운 가상 네트워크 추가**를 선택합니다.  

5. 새 가상 네트워크를 만드는 데 필요한 세부 정보를 제공한 다음, 만들기를 선택합니다. “Microsoft.AzureCosmosDB”가 활성화된 서비스 엔드포인트와 함께 서브넷이 생성됩니다.

   ![새 가상 네트워크에 대한 가상 네트워크 및 서브넷을 선택합니다.](./media/vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

## <a name="allow-access-from-azure-portal"></a>Azure Portal에서 액세스 허용

Azure Cosmos DB 데이터베이스 계정에 대해 Azure Virtual Network 서비스 엔드포인트를 사용하도록 설정한 후, 포털 또는 기타 Azure 서비스에서의 액세스는 기본적으로 비활성화되어 있습니다. 포털에서의 액세스를 포함하여, 구성된 서브넷 외부 컴퓨터에서 Azure Cosmos DB 데이터베이스 계정에 대한 액세스는 차단됩니다.

![포털에서의 액세스 허용](./media/vnet-service-endpoint/allow-access-from-portal.png)

Azure Search와 같은 다른 Azure 서비스에서 Azure Cosmos DB 계정을 사용하거나 Stream 분석 또는 Power BI에서 액세스하는 경우 **Azure Services에 액세스 허용**을 선택하여 액세스를 허용합니다.

포털에서 Azure Cosmos DB 메트릭에 대한 액세스 권한이 있는지 확인하려면 **Azure Portal에 액세스 허용** 옵션을 사용하도록 설정해야 합니다. 이러한 옵션에 대해 자세히 알아보려면 [Azure Portal에서 연결](firewall-support.md#connections-from-the-azure-portal) 및 [Azure PaaS 서비스의 연결](firewall-support.md#connections-from-other-azure-paas-services) 섹션을 참조하세요. 액세스를 선택한 후 **저장**을 선택하여 설정을 저장합니다.

## <a name="remove-a-virtual-network-or-subnet"></a>가상 네트워크 또는 서브넷 제거 

1. **모든 리소스** 블레이드에서 서비스 엔드포인트를 할당한 Azure Cosmos DB 계정을 찾습니다.  

2. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택합니다.  

3. 가상 네트워크 또는 서브넷 규칙을 제거하려면 가상 네트워크 또는 서브넷 옆에 있는 “...”를 선택하고 **제거**를 선택합니다.

   ![가상 네트워크 제거](./media/vnet-service-endpoint/remove-a-vnet.png)

4.  **저장**을 클릭하여 변경 내용을 적용합니다.

## <a name="configure-service-endpoint-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 서비스 엔드포인트 구성 

Azure PowerShell을 사용하여 Azure Cosmos DB 계정에 서비스 엔드포인트를 구성하려면 다음 단계를 사용합니다.  

1. 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.  계정에 대한 서비스 엔드포인트를 활성화하기 전에 IP 방화벽 설정을 확인하고 IP 방화벽을 완전히 삭제합니다.

2. 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하기 전에 나중에 사용할 수 있도록 Azure Cosmos DB 계정과 연결된 IP 방화벽 정보를 복사합니다. 서비스 엔드포인트를 구성한 후 IP 방화벽을 다시 설정할 수 있습니다.  

3. 가상 네트워크의 기존 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

4. 가상 네트워크 및 서브넷에서 서비스 엔드포인트가 Azure Cosmos DB에 대해 사용하도록 설정되었는지 확인하여 CosmosDB 계정에서 ACL의 활성화를 준비합니다.

   ```powershell
   $subnet = Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName  | Get-AzureRmVirtualNetworkSubnetConfig -Name $sname
   $vnProp = Get-AzureRmVirtualNetwork `-Name $vnName  -ResourceGroupName $rgName
   ```

5. 다음 cmdlet을 실행하여 Azure Cosmos DB 계정의 속성을 가져옵니다.  

   ```powershell
   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

6. 나중에 사용할 수 있도록 변수를 초기화합니다. 기존 계정 정의에서 모든 변수를 설정하고, 여러 위치가 있는 경우 배열의 일환으로 추가해야 합니다. 이 단계에서는 또한 “accountVNETFilterEnabled” 변수를 “True”로 설정하여 가상 네트워크 서비스 엔드포인트를 구성합니다. 이 값은 나중에 “isVirtualNetworkFilterEnabled” 매개 변수에 할당됩니다.  

   ```powershell
   $locations = @(@{})
   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $subnetName  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

7. 다음 cmdlet을 실행하여 새 구성으로 Azure Cosmos DB 계정 속성을 업데이트합니다. 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource ``
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

8. 다음 명령을 실행하여 이전 단계에서 구성한 가상 네트워크 서비스 엔드포인트로 Azure Cosmos DB 계정이 업데이트되었는지 확인합니다.

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a name="add-virtual-network-service-endpoint-for-an-azure-cosmos-db-account-that-has-ip-firewall-enabled"></a>IP 방화벽이 사용하도록 설정된 Azure Cosmos DB 계정에 대해 가상 네트워크 서비스 엔드포인트 추가

1. 먼저 Azure Cosmos DB 계정에 대한 IP 방화벽 액세스를 사용하지 않도록 설정합니다.  

2. 이전 섹션에서 설명한 방법 중 하나를 사용하여 Azure Cosmos DB 계정에 대한 가상 네트워크 엔드포인트를 사용하도록 설정합니다.  

3. IP 방화벽 액세스를 다시 설정합니다. 

## <a name="test-the-access"></a>액세스 테스트

Azure Cosmos DB에 대한 서비스 엔드포인트가 예상대로 구성되었는지 확인하려면 다음 단계를 사용합니다.

* 프런트 엔드 및 백 엔드로 가상 네트워크에서 2개의 서브넷을 설정하고, 백 엔드 서브넷에 대한 Cosmos DB 서비스 엔드포인트를 사용하도록 설정합니다.  

* 백 엔드 서브넷에 대한 Cosmos DB 계정의 액세스를 사용하도록 설정합니다.  

* 백 엔드 서브넷과 프런트 엔드 서브넷에 하나씩 두 개의 가상 머신을 만듭니다.  

* 두 가상 머신에서 Azure Cosmos DB 계정에 액세스를 시도합니다. 프런트 엔드 서브넷에 만든 가상 머신이 아닌 백 엔드 서브넷에 만든 가상 머신에서 연결할 수 있어야 합니다. 요청은 프런트 서브넷에서 연결을 시도하는 경우 가상 네트워크 서비스 엔드포인트를 사용하여 Azure Cosmos DB에 대한 액세스가 안전한지 확인하는 404 오류가 발생합니다. 백 엔드 서브넷에 있는 컴퓨터는 제대로 작동할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-happens-when-you-access-an-azure-cosmos-db-account-that-has-virtual-network-access-control-list-acl-enabled"></a>가상 네트워크 ACL(액세스 제어 목록)이 사용하도록 설정된 Azure Cosmos DB 계정에 액세스하면 어떻게 되나요?  

HTTP 404 오류가 반환됩니다.  

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-cosmos-db-account-in-another-region-for-example-if-azure-cosmos-db-account-is-in-west-us-or-east-us-and-virtual-networks-are-in-multiple-regions-can-the-virtual-network-access-azure-cosmos-db"></a>다른 지역에서 만든 가상 네트워크의 서브넷은 다른 지역에 있는 Azure Cosmos DB 계정에 액세스를 허용하나요? 예를 들어 Azure Cosmos DB 계정이 미국 서부 또는 미국 동부에 있고 가상 네트워크가 여러 지역에 있는 경우 가상 네트워크가 Azure Cosmos DB에 액세스할 수 있나요?  

예, 다른 지역에서 만든 가상 네트워크는 새로운 기능으로 액세스할 수 있습니다. 

### <a name="can-an-azure-cosmos-db-account-have-both-virtual-network-service-endpoint-and-a-firewall"></a>Azure Cosmos DB 계정에 가상 네트워크 서비스 엔드포인트와 방화벽이 모두 있을 수 있나요?  

예, Virtual Network 서비스 엔드포인트와 방화벽은 공존할 수 있습니다. 일반적으로 컨테이너와 관련된 메트릭을 볼 수 있도록 가상 네트워크 서비스 엔드포인트를 구성하기 전에 포털에 대한 액세스가 항상 사용하도록 허용되었는지 확인해야 합니다.

### <a name="can-i-allow-access-to-other-azure-services-from-a-given-azure-region-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>서비스 엔드포인트 액세스가 Azure Cosmos DB에 사용하도록 설정된 경우 “다른 Azure 서비스에 대한 주어진 Azure 지역의 액세스를 허용”할 수 있나요?  

이는 Azure Data Factory, Azure Search 또는 주어진 Azure 지역에 배포되는 다른 서비스와 같이 다른 Azure 자사 서비스에서 Azure Cosmos DB 계정에 액세스하려는 경우에만 필요합니다.

### <a name="how-many-virtual-network-service-endpoints-are-allowed-for-azure-cosmos-db"></a>Azure Cosmos DB에 대해 얼마나 많은 가상 네트워크 서비스 엔드포인트가 허용되나요?  

64개의 가상 네트워크 서비스 엔드포인트가 Azure Cosmos DB 계정에 허용됩니다.

### <a name="what-is-the-relationship-of-service-endpoint-with-respect-to-network-security-group-nsg-rules"></a>NSG(네트워크 보안 그룹) 규칙에 대해 서비스 엔드포인트의 관계는 무엇입니까?  

NSG의 Azure Cosmos DB 규칙은 Azure Cosmos DB IP 주소 범위에 대한 액세스만 제한할 수 있습니다.
  
### <a name="what-is-relationship-between-an-ip-firewall-and-virtual-network-service-endpoint-capability"></a>IP 방화벽 및 Virtual Network 서비스 엔드포인트 기능 사이의 관계는 무엇입니까?  

이러한 두 가지 기능은 Azure Cosmos DB 자산의 격리를 보장하고 보안하기 위해 서로를 보완합니다. IP 방화벽을 사용하여 고정 IP가 Azure Cosmos DB 계정에 액세스할 수 있는지 확인합니다.  

### <a name="can-an-on-premise-devices-ip-address-that-is-connected-through-azure-virtual-network-gatewayvpn-or-express-route-gateway-access-azure-cosmos-db-account"></a>Azure Virtual Network 게이트웨이(VPN) 또는 Express 경로 게이트웨이를 통해 연결되는 온-프레미스 장치의 IP 주소가 Azure Cosmos DB 계정에 액세스할 수 있나요?  

온-프레미스 장치의 IP 주소 또는 IP 주소 범위는 Azure Cosmos DB 계정에 액세스하기 위해 고정 IP 목록에 추가됩니다.  

### <a name="what-happens-if-you-delete-a-virtual-network-that-has-service-endpoint-setup-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 서비스 엔드포인트가 설정된 가상 네트워크를 삭제하면 어떻게 되나요?  

가상 네트워크가 삭제되면 Azure Cosmos DB와 연결된 ACL 정보가 삭제되고 가상 네트워크와 Azure Cosmos DB 계정 간의 상호 작용이 제거됩니다. 

### <a name="what-happens-if-an-azure-cosmos-db-account-that-has-virtual-network-service-endpoint-enabled-is-deleted"></a>가상 네트워크 서비스 엔드포인트를 사용하도록 설정된 Azure Cosmos DB 계정이 삭제되면 어떻게 되나요?

특정 Azure Cosmos DB 계정과 연결된 메타데이터가 서브넷에서 삭제됩니다. 또한 사용되는 서브넷 및 가상 네트워크를 삭제해야 하는 것은 최종 사용자의 책임입니다.

### <a name="can-i-use-a-peered-virtual-network-to-create-service-endpoint-for-azure-cosmos-db"></a>피어링된 가상 네트워크를 사용하여 Azure Cosmos DB에 대한 서비스 엔드포인트를 만들 수 있나요?  

아니요. 직접 가상 네트워크 및 해당 서브넷만 Azure Cosmos DB 서비스 엔드포인트를 만들 수 있습니다.

### <a name="what-happens-to-the-source-ip-address-of-resource-like-virtual-machine-in-the-subnet-that-has-azure-cosmos-db-service-endpoint-enabled"></a>Azure Cosmos DB 서비스 엔드포인트가 사용하도록 설정된 서브넷에서 가상 머신과 같은 리소스의 원본 IP 주소는 어떻게 되나요?

가상 네트워크 서비스 엔드포인트를 사용하도록 설정한 경우 사용자의 가상 네트워크 서브넷에 있는 리소스의 원본 IP 주소는 Azure Cosmos DB에 대한 트래픽에 대해 공용 IPV4 주소를 사용하는 것에서 Azure 가상 네트워크의 개인 주소로 전환됩니다.

### <a name="does-azure-cosmos-db-reside-in-the-azure-virtual-network--provided-by-the-customer"></a>Azure Cosmos DB는 고객에 의해 제공되는 Azure Virtual Network에 상주하나요?  

Azure Cosmos DB는 공용 IP 주소를 사용하는 다중 테넌트 서비스입니다. 서비스 엔드포인트 기능을 사용하여 Azure Virtual Network의 서브넷에 대한 액세스를 제한하면 지정된 Azure Virtual Network 및 해당 서브넷을 통한 사용자의 Azure Cosmos DB 계정으로 액세스가 제한됩니다.  Azure Cosmos DB 계정은 해당 Azure Virtual Network에 상주하지 않습니다. 

### <a name="what-if-anything-will-be-logged-in-log-analyticsoms-if-it-is-enabled"></a>Log Analytics/OMS(활성화된 경우)에 로그인하면 어떻게 되나요?  

Azure Cosmos DB는 ACL에 의해 차단된 요청에 대해 403 상태와 함께 IP 주소(마지막 8진수 없음)를 사용하여 로그를 푸시합니다.  

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](firewall-support.md) 문서를 참조하세요.

