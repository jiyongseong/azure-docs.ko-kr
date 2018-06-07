---
title: 영역 중복 Azure 응용 프로그램 게이트웨이 만들기
description: 각 영역에 별도의 게이트웨이가 필요 없는 영역 중복 응용 프로그램 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: amsriva
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 5/8/2018
ms.author: victorh
ms.openlocfilehash: 03bc58db813534fdd17c9567f6425ab7357ed901
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937706"
---
# <a name="create-a-zone-redundant-azure-application-gateway---private-preview"></a>영역 중복 Azure 응용 프로그램 게이트웨이 만들기 - 비공개 미리 보기

응용 프로그램 게이트웨이 영역 중복 플랫폼은 기존 응용 프로그램 게이트웨이 SKU에 대해 다음과 같은 다수의 향상된 기능을 제공하는 새로운 SKU입니다.
- **영역 복원력** - 응용 프로그램 게이트웨이 배포가 이제 여러 가용성 영역으로 확장될 수 있기 때문에 Traffic Manager로 각 영역에서 별도의 응용 프로그램 게이트웨이 인스턴스를 프로비전하고 고정할 필요가 없습니다. 응용 프로그램 게이트웨이 인스턴스가 배포되는 단일 영역 또는 여러 영역을 선택할 수 있기 때문에 영역 장애 복원력이 보장됩니다. 응용 프로그램에 대한 백 엔드 풀을 가용성 영역 전반에 유사하게 배포할 수 있습니다.
- **성능 향상**
- **정적 VIP** - 응용 프로그램 게이트웨이 VIP가 이제 정적 VIP 유형만 독점적으로 지원합니다. 따라서 응용 프로그램 게이트웨이와 연결된 VIP가 다시 시작한 후에도 변경되지 않습니다.
- **고객 SSL 인증서에 대한 Key Vault 통합**
- **신속한 배포 및 업데이트 시간**

> [!NOTE]
> 영역 중복 응용 프로그램 게이트웨이는 현재 비공개 미리 보기 상태입니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="supported-regions"></a>지원되는 지역

영역 중복 응용 프로그램 게이트웨이는 현재 미국 동부 2 지역에 지원됩니다. 더 많은 지역이 곧 추가될 예정입니다.

## <a name="topologies"></a>토폴로지

현재 릴리스에서는 영역 중복을 확보하기 위해 영역이 고정된 응용 프로그램 게이트웨이를 만들 필요가 없습니다. 이제 동일한 응용 프로그램 게이트웨이 배포가 여러 영역으로 확장될 수 있습니다.

세 영역 모두에 분산되도록 하려면 최소한 세 개의 인스턴스가 필요합니다. 더 많은 인스턴스가 추가되면서 응용 프로그램 게이트웨이는 여러 영역에 걸쳐 인스턴스를 배포합니다.

이전 영역 중복 토폴로지는 다음 다이어그램과 같은 모양입니다.

![이전 중복 토폴로지](media/create-zone-redundant/old-redundant.png)

새로운 영역 중복 토폴로지는 다음 다이어그램과 같은 모양입니다.

![새 영역 중복 토폴로지](media/create-zone-redundant/new-redundant.png)

## <a name="deployment"></a>배포

### <a name="prerequisites"></a>필수 조건

현재 영역 중복 기능은 비공개 미리 보기에서만 사용할 수 있습니다. 허용 목록에 포함되려면 appgwxzone@microsoft.com에 이메일을 보내야 합니다. 확인 메시지를 받으면 다음 단계를 진행할 수 있습니다. 허용 목록에 포함되기 위한 이메일에 다음 정보를 포함합니다.

- 구독 ID
- 지역 이름
- 필요한 응용 프로그램 게이트웨이의 대략적인 개수

### <a name="resource-manager-template-deployment"></a>Resource Manager 템플릿 배포

1. 앞서 언급했듯이 사용하는 구독이 허용 목록에 포함되어야 합니다.
2. 확인 메시지를 받으면 Azure 계정에 로그인하고 구독이 둘 이상 있으면 적절한 구독을 선택합니다. 허용 목록에 포함된 구독을 선택해야 합니다.

   ```PowerShell
   Login-AzureRmAccount

   Select-AzureRmSubscription -Subscription "<whitelisted subscription name>”
   ```
3. 새 리소스 그룹 만들기

   ``` PowerShell
   New-AzureRmResourceGroup -Name <resource group name> -Location "East US 2"
   ```
4. [GitHub](https://github.com/amitsriva/CrossZonePreview)에서 템플릿을 다운로드하고 템플릿을 저장한 폴더를 적어둡니다.
5. 만들어둔 리소스 그룹에 새 배포를 만듭니다. 배포하기 전에 템플릿 및 매개 변수 파일을 적절하게 수정합니다. 

   다음 다이어그램은 Azure Portal에서 테넌트 ID를 검색할 수 있는 위치를 보여줍니다.

   ![포털의 테넌트 ID](media/create-zone-redundant/tenant-id.png)

이 템플릿에서 만드는 리소스는 다음과 같습니다.

- **사용자 할당 ID** - 응용 프로그램 게이트웨이 인스턴스가 Key Vault에 액세스하고 사용자가 저장한 인증서를 검색할 수 있도록 설정하는 데 사용됩니다.
- **Key Vault** – 사용자의 인증서가 저장되는 Key Vault입니다. 기존에 있는 Key Vault일수도 있습니다.
- **비밀** – Key Vault에 저장된 개인 키입니다.
- **액세스 정책** – 응용 프로그램 게이트웨이 배포가 사용자 인증서를 검색할 수 있도록 사용자 할당 ID를 사용하여 권한을 부여하는 Key Vault에 적용된 액세스 정책입니다.
- **공용 IP 주소** – 응용 프로그램 게이트웨이에 액세스하는 데 사용되는 예약된 IP 주소입니다. 이 IP 주소는 응용 프로그램 게이트웨이의 수명주기 내내 절대로 변경되지 않습니다.
- **네트워크 보안 그룹** – 구성된 수신기에서 포트 트래픽을 여는 응용 프로그램 게이트웨이 서브넷에 자동으로 생성된 NSG입니다. NSG가 암시적이었던 이전 SKU에 비해 새 SKU에서는 명시적으로 만들어지고 관리됩니다.
- **Virtual Network** – 응용 프로그램 게이트웨이와 응용 프로그램이 배포되는 VNet입니다.
- **Application Gateway** – 필요한 영역에 인스턴스가 있는 응용 프로그램 게이트웨이를 만듭니다. 기본적으로 모든 영역(1,2,3)이 선택됩니다. SKU 이름은 *Standard_v2*로 변경됩니다. 이 SKU 이름은 변경될 수 있습니다. 현재 자동 크기 조정 구성에는 필요한 수의 인스턴스에 대한 최댓값 및 최솟값이 설정되어 있습니다. 자동 크기 조정이 활성화되면 이 값을 조정할 수 있습니다.

```PowerShell
New-AzureRmResourceGroupDeployment -Name Deployment1 -ResourceGroupName AmitVMSSLinuxTest9 -TemplateFile <complete path to template.json> -TemplateParameterFile <complete path to parameters.json>
```
### <a name="powershell-deployment"></a>PowerShell 배포

1. 필수 구성 요소에서 앞서 언급했듯이 사용되는 구독이 허용 목록이 포함되어 있어야 합니다.
2. [GitHub](https://github.com/amitsriva/CrossZonePreview/blob/master/Azure-Cmdlets-5.7.0.19009-x64.msi)에서 이전 PowerShell MSI를 다운로드하여 설치합니다.
3. 미리 보기 등록 확인 이메일에 언급된 위치에서 비공개 PowerShell zip 파일을 다운로드합니다. 드라이브에 파일 압축을 풀고 위치를 적어둡니다.
4. 미리 보기가 활성화되면 계정에 로그인하기 전에 미리 보기 모듈을 먼저 로드합니다.

   ```PowerShell
   $azurePSPath = "<complete path to Azure-PowerShell folder>"
   import-module "$azurePSPath\AzureRM.Profile\AzureRM.Profile.psd1"
   import-module "$azurePSPath\Azure.Storage\Azure.Storage.psd1"
   import-module "$azurePSPath\AzureRM.Resources\AzureRM.Resources.psd1"
   import-module "$azurePSPath\AzureRM.Network\AzureRM.Network.psd1"
   import-module "$azurePSPath\AzureRM.KeyVault\AzureRM.KeyVault.psd1"
   ```

4. Azure 계정에 로그인하고 구독이 둘 이상 있으면 원하는 구독을 선택합니다. 허용 목록에 포함된 적절한 구독을 선택해야 합니다.
5. 다음 명령을 실행하여 생성되는 대부분의 엔터티에 대한 이름을 포함하는 공통 상수를 설정합니다. 

   이름 지정 기본 설정에 필요한대로 항목을 수정합니다.

   ```PowerShell
   $location = "eastus2"
   $version = "300"

   $rgname = "RG_A_$version"
   $appgwName = "AGW_A_$version"
   $vaultName = "KVA$version"
   $userAssignedIdentityName = "UI_A_$version"
   $certificateName = "KVCA$version"
   $nsgName = "NSG_A_$version"
   $vnetName = "VN_A_$version"
   $gwSubnetName = "SN_A_$version"
   $gipconfigname = "GC_A_$version"
   $publicIpName = "PIP_A_$version"
   $fipconfig01Name = "FC_A_$version"
   $poolName = "BP_A_$version"
   $frontendPort01Name = "FP1_A_$version"
   $frontendPort02Name = "FP2_A_$version"
   $poolSetting01Name = "BS_A_$version"
   $listener01Name = "HL1_A_$version"
   $listener02Name = "HL2_A_$version"
   $rule01Name = "RR1_A_$version"
   $rule02Name = "RR2_A_$version"
   $AddressPrefix = "111.111.222.0" 
   ```
6. 리소스 그룹을 만듭니다.

   ```PowerShell
   $resourceGroup = New-AzureRmResourceGroup -Name $rgname -Location $location -Force
   ```
7. Key Vault에서 인증서를 검색하기 위해 응용 프로그램 게이트웨이에 대한 액세스 권한을 부여하는 데 사용되는 사용자 할당 ID를 만듭니다.

   ```PowerShell
   $userAssignedIdentity = New-AzureRmResource -ResourceGroupName $rgname -Location $location -ResourceName $userAssignedIdentityName -ResourceType Microsoft.ManagedIdentity/userAssignedIdentities -Force
   ```
8. 인증서를 저장하는 데 사용할 Key Vault를 만듭니다.

   ```PowerShell
   $keyVault = New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgname -Location $location -EnableSoftDelete
   ```
9. Key Vault에 인증서를 비밀로 업로드합니다.

   ```PowerShell
   $securepfxpwd = ConvertTo-SecureString -String "<password>" -AsPlainText -Force

   $cert = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name         $certificateName -FilePath ‘<path to pfx file>'  -Password $securepfxpwd
   ```
10. 사용자 할당 ID를 사용하여 Key Vault에 액세스 정책을 할당합니다. 이렇게 하면 응용 프로그램 게이트웨이 인스턴스가 Key Vault 비밀에 액세스할 수 있습니다.

   ```PowerShell
   Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $rgname -PermissionsToSecrets get -ObjectId $userAssignedIdentity.Properties.principalId
   ```
11. NSG(네트워크 보안 그룹)를 만들어서 새 수신기가 만들어진 포트에서 응용 프로그램 게이트웨이 서브넷에 대한 액세스를 허용합니다.

    예를 들어 기본 포트의 HTTP/HTTPS에 대해 NSG는 80, 443에 대한 인바운드 액세스를 허용하고 관리 작업을 위해 65200-65535를 허용합니다.

   ```PowerShell
   $srule01 = New-AzureRmNetworkSecurityRuleConfig -Name "listeners" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange 22,80,443 -Access Allow -Priority 100

   $srule02 = New-AzureRmNetworkSecurityRuleConfig -Name "managementPorts" -Direction Inbound -SourceAddressPrefix * -SourcePortRange * -Protocol * -DestinationAddressPrefix * -DestinationPortRange "65200-65535" -Access Allow -Priority 101

   $nsg = New-AzureRmNetworkSecurityGroup -Name $nsgName -ResourceGroupName $rgname -Location $location -SecurityRules $srule01,$srule02 -Force
   ```

12. VNet 및 서브넷 만들기:

   ```PowerShell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 
   $gwSubnetName -AddressPrefix "$AddressPrefix/24" -NetworkSecurityGroup $nsg

   $vnet = New-AzureRmvirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix "$AddressPrefix/24" -Subnet $gwSubnet -Force
   ```
13. 예약된/고정 유형의 공용 IP 주소 만들기:

   ```PowerShell
   $publicip = New-AzureRmPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -Sku Standard -Force
   ```

14. 응용 프로그램 게이트웨이 만들기:

   ```PowerShell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet

   $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name $fipconfig01Name -PublicIPAddress $publicip

   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

   $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort01Name -Port 443

   $fp02 = New-AzureRmApplicationGatewayFrontendPort -Name $frontendPort02Name -Port 80

   $sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -KeyVaultSecretId $secret.Id

   $listener01 = New-AzureRmApplicationGatewayHttpListener -Name $listener01Name -Protocol Https -FrontendIPConfiguration
 $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01

   $listener02 = New-AzureRmApplicationGatewayHttpListener -Name $listener02Name -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02

   $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name $poolSetting01Name -Port 80 -Protocol Http -CookieBasedAffinity Disabled

   $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule01Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

   $rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name $rule02Name -RuleType basic -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool

   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2

   $listeners = @($listener02)

   $fps = @($fp01, $fp02)

   $fipconfigs = @($fipconfig01)

   $sslCerts = @($sslCert01)

   $rules = @($rule01, $rule02)

   $listeners = @($listener01, $listener02)

   $appgw = New-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Location $location -UserAssignedIdentityId $userAssignedIdentity.ResourceId -Probes $probeHttps -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfigs -FrontendPorts $fps -HttpListeners $listeners -RequestRoutingRules $rules -Sku $sku -SslPolicy $sslPolicy -sslCertificates $sslCerts -Force
   ```

15. 생성한 응용 프로그램 게이트웨이의 공용 IP 주소 가져오기:

   ```PowerShell
   $pip = Get-AzureRmPublicIpAddress -Name $publicIpName -ResourceGroupName $rgname $pip.IpAddress
   ```

## <a name="frequently-asked-questions"></a>질문과 대답

-  미리 보기에서 응용 프로그램 게이트웨이에 대한 요금이 청구되나요?

   미리 보기 중에는 무료입니다. 응용 프로그램 게이트웨이가 아닌 리소스(예: Key Vault, 가상 머신 등)에 대한 요금은 청구됩니다.
- 미리 보기는 어떤 지역에서 사용할 수 있나요?

   미리 보기는 현재 미국 동부 2 지역에서 사용할 수 있습니다. 더 많은 지역이 곧 추가될 예정입니다.
- 미리 보기에서 포털이 지원되나요?

   아니요, 비공개 미리 보기 기간 동안 지원은 비공개 PowerShell 모듈 및 Resource Manager 템플릿으로 제한됩니다.

- 비공개 미리 보기 중에 프로덕션 워크로드가 지원되나요?

   아니요, 비공개 미리 보기 중에는 SLA나 지원이 없습니다. 미리 보기 중에는 프로덕션 워크로드를 배치하지 않는 것이 좋습니다. 지원은 미리 보기용 이메일 별칭을 사용한 제품 그룹과의 직접적인 상호 작용으로 제한됩니다.

- 문제는 어떻게 보고하나요?

   비공개 미리 보기에는 버그가 있을 수 있으며 코드를 자주 배포할 수 있습니다. 문제를 보고하거나 지원을 요청하려면 지원 별칭인 appgwxzone@microsoft.com을 사용하십시오.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항


|문제  |세부 정보  |
|---------|---------|---------|
|결제     |현재 요금이 청구되지 않음|
|진단 로그(메트릭 아님)     |성능 및 요청/응답 로그가 현재 나타나지 않음|
|포털/CLI/SDK     |포털, CLI 또는 SDK에 대한 지원이 없습니다. 미리 보기 게이트웨이에 대한 업데이트를 발급할 때 포털을 사용하지 말아야 합니다.|
|템플릿을 통한 업데이트가 가끔 실패합니다.     |KeyVault 액세스 정책의 경쟁 조건 때문입니다.|Key Vault 및 사용자 할당 ID가 만들어지면 템플릿에서 제거될 수 있으며 템플릿에는 비밀 및 ID에 대한 참조만 필요합니다.|
|자동 확장     |현재 자동 크기 조정이 지원되지 않음|
|WAF     |WAF는 현재 지원되지 않습니다.|
|사용자가 제공한 인증서 및 동적 VIP     |새로운 모델에서는 지원되지 않습니다. Key Vault를 사용하여 인증서 및 정적 VIP를 저장합니다.|
|응용 프로그램 게이트웨이의 이전 버전 및 미리 보기 버전에 대한 동일한 서브넷     |기존 응용 프로그램 게이트웨이(이전 모델)가 있는 서브넷은 비공개 미리 보기 버전에 사용할 수 없습니다.|
|HTTP/2, FIPS 모드, WebSocket, Azure Web Apps를 백 엔드로     |현재 지원되지 않음 |


## <a name="support-and-feedback"></a>지원 및 피드백

지원이 필요하거나 피드백 있으면 appgwxzone@microsoft.com에 문의하세요. 응용 프로그램 게이트웨이 제품 그룹에서는 제품 향상을 위한 여러분의 피드백을 환영하며 필요한 경우 지침을 제공합니다.

## <a name="next-steps"></a>다음 단계

다른 응용 프로그램 게이트웨이 기능에 대해 알아보기:

- [Azure Application Gateway란?](overview.md)