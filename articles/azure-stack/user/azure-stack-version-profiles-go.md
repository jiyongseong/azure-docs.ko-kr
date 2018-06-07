---
title: API 버전 프로필을 사용 하 여 Azure 스택의 통용 | Microsoft Docs
description: 이동 중에 Azure 스택 API 버전 프로필을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd2d0c46c0829a73d32c96b506b9f2111eda3c84
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Azure 스택에서 통용 API 버전 프로필을 사용 하 여

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="go-and-version-profiles"></a>이동 및 버전 프로필

프로필은 서로 다른 서비스에서 다른 버전으로 여러 리소스 종류의 조합입니다. 프로필을 사용 하 여 혼합 하 고 다른 리소스 형식 간에 일치 하는 데 도움이 됩니다. 프로필을 제공할 수 있습니다.

 - 특정 API 버전에 잠금을 설정 하면 응용 프로그램에 대 한 안정성 합니다.
 - Azure 스택 및 지역 Azure 데이터 센터와 응용 프로그램에 대 한 호환 됩니다.

이동 SDK 프로필은 프로필에서 사용할 수 있는 / 경로에서 버전에는 **YYYY-월-일** 형식입니다. 지금 바로 최신 Azure 스택이 프로필 버전은 **2017-03-09**합니다. 프로필에서 지정된 된 서비스를 가져오려면 프로필에서 해당 해당 모듈을 가져와야 합니다. 예를 들어, 가져오려는 **계산** 서비스 **2017-03-09** 프로필:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>이동에 대 한 Azure SDK를 설치 합니다.

  1. Git를 설치 합니다. 자세한 내용은 [시작-Git 설치](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)합니다.
  2. 설치는 [프로그래밍 언어를 이동](https://golang.org/dl)합니다.  
  Azure에 대 한 API 프로필 Go 1.9 이상 버전이 필요 합니다.
  3. 다음 bash 명령을 실행 하 여 이동 하려면 Azure SDK 및 해당 종속성을 설치 합니다.
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>이동 SDK

Azure로 이동 SDK에 대 한 자세한 정보를 얻을 수 있습니다.
- Azure SDK에서 이동 [Go에 대 한 Azure SDK 설치](https://docs.microsoft.com/go/azure/azure-sdk-go-install)합니다.
- Azure로 이동 SDK에는 GitHub에서 공개적으로 사용할 수는 [go에 대 한 azure sdk](https://github.com/Azure/azure-sdk-for-go)합니다.

### <a name="go-autorest-dependencies"></a>Go AutoRest 종속성

이동 SDK는 Azure 리소스 관리자 끝점에 REST 요청을 보낼 Azure Go AutoRest 모듈에 따라 달라 집니다. Azure Go AutoRest 모듈 종속성을 가져와야 합니다 [GitHub의 Azure Go AutoRest](https://github.com/Azure/go-autorest)합니다. 설치 bash 명령을 찾을 수 있습니다는 **설치** 섹션.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Azure 스택에서 이동 SDK 프로필을 사용 하는 방법

실행 하려면 샘플 Go 코드 Azure 스택:
  1. 이동 및 해당 종속성에 대 한 Azure SDK를 설치 합니다. 명령에 대 한 이전 섹션을 참조 [Go에 대 한 Azure SDK 설치](#install-azure-sdk-for-go)합니다.
  2. 리소스 관리자 끝점에서 메타 데이터 정보를 가져옵니다. 끝점 Go 코드를 실행 하는 데 필요한 정보로 JSON 파일을 반환 합니다.

  > [!Note]  
  > **ResourceManagerUrl** Azure 스택 개발 키트 (ASDK)에: `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** 통합된 시스템의은: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  샘플 JSON 파일:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. 사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만드는 방법에 대 한 정보를 참조 하십시오. [Azure 스택에서 제공 하는 구독을 만들](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)합니다. 
  4. "구독" 범위를 가진 서비스 사용자 만들기 및 **소유자** 역할입니다. ID 및 암호에는 서비스 사용자를 저장 합니다. Azure 스택에 대 한 서비스 사용자를 만드는 방법에 대 한 정보를 참조 하십시오. [서비스 보안 주체를 만들](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad)합니다. Azure 스택 환경 설정 되어 있습니다.
  5. 코드에서 이동 SDK 프로필에서 서비스 모듈을 가져옵니다. Azure 스택 프로필의 현재 버전은 **2017-03-09**합니다. 예를 들어에서 네트워크 모듈을 가져오려면 **2017-03-09** 프로필 유형: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. 함수를 만들고 사용 하 여 클라이언트를 인증 한 **새로** 클라이언트 함수 호출 합니다. 가상 네트워크 클라이언트를 만들려면 다음 코드를 사용할 수 있습니다.  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  설정 `<baseURI>` 에 **ResourceManagerUrl** 2 단계에 사용 되는 값입니다.
  설정 `<subscriptionID>` 에 **SubscriptionID** 3 단계에서 저장 된 값입니다.
  참조 인증 토큰을 만들려면 아래 섹션.  

  7. 이전 단계에서 만든 클라이언트를 사용 하 여 API 메서드를 호출 합니다. 예를 들어, 이전 단계에서 클라이언트를 사용 하 여 가상 네트워크를 만들려면: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  이동 SDK 프로필을 사용 하 여 Azure 스택에 가상 네트워크를 만드는 자세한 예제를 참조 하십시오. [예제](#example)합니다.

## <a name="authentication"></a>인증

이동 SDK를 사용 하 여 Azure Active Directory의 권한 부여자 속성을 가져오려면 Go AutoRest 모듈을 설치 합니다. 이러한 모듈은 이미 설치 된 "이동 SDK" 설치; 그렇지 않은 경우 설치는 [GitHub의 인증 패키지](https://github.com/Azure/go-autorest/tree/master/autorest/adal)합니다.

권한 부여 자의 리소스 클라이언트에 대 한 권한 부여 자의로 설정 되어야 합니다. 권한 부여자; 얻으려고 하는 여러 방법을 에 대 한 전체 목록은 참조에 여기 해당 합니다.

이 섹션에서는 클라이언트 자격 증명을 사용 하 여 Azure 스택에 권한 부여자 토큰을 가져오기 위해 일반적으로 제공 합니다.

  1. 소유자 역할 구독에 서비스 사용자를 사용할 수 있는 경우이 단계를 건너뜁니다. 그렇지 않으면 서비스 사용자를 만들 [지침]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) 구독으로 범위가 지정 된 "소유자" 역할을 할당 하 고 [지침]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)합니다. 서비스 응용 프로그램 사용자 ID 및 암호를 저장 합니다. 

  2. 가져오기 **adal** Go AutoRest에서 코드에 패키지 합니다. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. NewOAuthConfig 메서드를 사용 하 여 한 oauthConfig 만들기 **adal** 모듈입니다. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  설정 `<activeDirectoryEndpoint>` "loginEndpoint"의 값에 속성이 ResourceManagerUrl 메타 데이터에서이 문서의 이전 섹션에서 검색 합니다.
  설정 `<tenantID>` Azure 스택 테 넌 트 id 값 

  4. 마지막으로, adal 모듈에서 NewServicePrincipalToken 방법을 사용 하 여 서비스 보안 주체 토큰을 만듭니다. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  설정 `<activeDirectoryResourceID>` "audience"에 있는 값 중 하나에이 문서의 이전 섹션에 맞춰져 있으며 ResourceManagerUrl 메타 데이터에서 목록을 검색 합니다.  
  설정 `<clientID>` 서비스 주 응용 프로그램 ID 서비스 사용자가이 문서의 이전 섹션에 맞춰져 있으며 생성 때을 저장 합니다.  
  설정 `<clientSecret>` 서비스 주 응용 프로그램에 보안 저장 서비스 사용자가이 문서의 이전 섹션에서 생성 하는 경우.  

## <a name="example"></a>예

이 섹션에서는 샘플 가상 네트워크를 만드는 Azure 스택에 Go 코드를 보여 줍니다. 이동 SDK의 전체 예제를 보려면 참조 [Azure 이동 SDk 샘플 리포지토리](https://github.com/Azure-Samples/azure-sdk-for-go-samples)합니다. Azure 스택 샘플은 하이브리드에서 사용할 수 있는 / 내 저장소의 서비스 폴더 경로입니다.

> [!Note]  
> 이 예제의 코드를 실행 하는 데 사용 하는 구독에 있는지 확인 하십시오 **네트워크** 로 나열 하는 리소스 공급자 **등록 된**합니다. 를 확인 하려면 Azure 스택 포털에서 구독에 대 한 확인을 클릭할 **리소스 공급자입니다.**

1. 코드에서 필요한 패키지를 가져옵니다. 네트워크 모듈을 가져오려면 Azure 스택에 최신 사용할 수 있는 프로필을 사용 해야 합니다. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. 환경 변수를 정의 합니다. 가상 네트워크를 만들려면 리소스 그룹이 필요 합니다. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. 환경 변수를 정의한을 사용 하 여 인증 토큰을 만드는 메서드를 추가할 **adal** 패키지 합니다. 이전 섹션에서 인증에 대 한 정보를 참조 하십시오.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Main 메서드를 추가 합니다. Main 메서드는 먼저 이전 단계에서 정의 된 메서드를 사용 하 여 토큰을 가져옵니다. 그런 다음 프로필에서 네트워크 모듈을 사용 하 여 클라이언트를 만듭니다. 마지막으로, 가상 네트워크를 만듭니다. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>다음 단계
* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
