---
title: API 버전 프로필을 사용 하 여 Azure 스택의 Ruby와 | Microsoft Docs
description: Azure 스택의 Ruby API 버전 프로필을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd8130ac12f9c7c2095f9329dc4ce8a34187cf62
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Azure 스택에서 Ruby API 버전 프로필 사용

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

## <a name="ruby-and-api-version-profiles"></a>Ruby 및 API 버전 프로필

Azure 스택 리소스 관리자에 대 한 Ruby SDK 빌드 및 인프라를 관리 하는 데 유용한 도구를 제공 합니다. SDK에 대 한 리소스 공급자에는 계산, 가상 네트워크 및 저장소 Ruby 언어와 함께 포함 됩니다. Ruby SDK의 API 프로필 글로벌 Azure 리소스 및 Azure 스택에 리소스 간에 전환할 수 있도록 하 여 하이브리드 클라우드 개발을 사용 합니다.

API 프로필은 리소스 공급자와 서비스 버전의 조합입니다. 여러 리소스 종류를 결합 하는 API 프로필을 사용할 수 있습니다.

 - 하려면 모든 서비스의 최신 버전을 사용 하 여 **최신** Azure SDK 롤업 보석의 프로필입니다.
 - Azure 스택와 호환 되는 서비스를 사용 하려면 사용 된 **V2017_03_09** Azure SDK 롤업 보석의 프로필입니다.
 - 서비스의 최신 api 버전을 사용 하려면 사용 된 **최신** 특정 보석의 프로필입니다. 예를 들어 단독 계산 서비스의 최신 api 버전을 사용 하려는 경우 사용 하 여는 **최신** 의 프로필은 **계산** 보석 합니다.
 - 서비스에 대 한 특정 api 버전을 사용 하려면 보석 내에 정의 된 특정 API 버전을 사용 합니다.

> [!Note]   
> 동일한 응용 프로그램에서 옵션을 모두를 결합할 수 있습니다.

## <a name="install-the-azure-ruby-sdk"></a>Ruby Azure SDK 설치

 - 공식 지침에 따라 설치 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)합니다.
 - 공식 지침에 따라 설치 [Ruby](https://www.ruby-lang.org/en/documentation/installation/)합니다.
    - 설치 하는 동안 선택 **PATH 변수에 Ruby 추가**
    - 메시지가 표시 되 면 Ruby 설치 하는 동안 개발 키트를 설치 합니다.
    - 다음으로, 다음 명령을 사용 하 여 번들러 설치:  
      `Gem install bundler`
 - 사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만드는 지침은 [여기](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)합니다. 
 - 서비스 사용자를 만들고 해당 ID와 암호를 저장 합니다. Azure 스택에 대 한 서비스 사용자를 만드는 지침은 [여기](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)합니다. 
 - 구독에 대해 서비스를 사용자에 게 참가자/소유자 역할이 있는지 확인 합니다. 서비스 사용자 역할을 할당 하는 방법에 지침은 [여기](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)합니다.

## <a name="install-the-rubygem-packages"></a>Rubygem 패키지 설치

Azure rubygem 패키지를 직접 설치할 수 있습니다.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

업그레이드할 Azure 리소스 관리자 Ruby SDK 미리 보기에는 이후 릴리스에서 주요 인터페이스 변경 할 가능성이 많습니다. 부 버전 번호를 증가 된 주요 변경 사항을 나타낼 수 있습니다.

## <a name="usage-of-the-azuresdk-gem"></a>Azure_sdk 보석의 사용

Azure_sdk, 보석은 Ruby SDK에서 지원 되는 모든 gems 롤업 한 것입니다. 이 보석 이루어져는 **최신** 프로필을 지 원하는 모든 서비스의 최신 버전입니다. 버전이 지정 된 프로필 소개 **V2017_03_09** 프로필 Azure 스택에 대 한 기본 제공 됩니다.

다음 명령을 사용 하 여 azure_sdk 롤업 보석을 설치할 수 있습니다.  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>필수 요소

Azure 스택 Ruby Azure SDK를 사용 하려면 다음 값을 제공 하 고 환경 변수를 사용 하 여 값을 설정 합니다. 환경 변수 설정에 운영 체제에 대 한 표 다음 지침을 참조 하십시오. 

| 값 | 환경 변수 | 설명 | 
| --- | --- | --- | --- |
| 테넌트 ID | AZURE_TENANT_ID | Azure 스택 값 [테 넌 트 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview)합니다. |
| 클라이언트 ID | AZURE_CLIENT_ID | 이 문서의 이전 섹션에서 서비스 사용자가 생성 될 때 주 응용 프로그램 ID 저장 하는 서비스입니다.  |
| 구독 ID | AZURE_SUBSCRIPTION_ID | [구독 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) 제안에 액세스 하는 방법을 Azure 스택의 합니다. |
| 클라이언트 암호 | AZURE_CLIENT_SECRET | 서비스 사용자 응용 프로그램 서비스 사용자를 만들 때 암호 저장 합니다. |
| 리소스 관리자 끝점 | ARM_ENDPOINT | 참조 [Azure 스택 리소스 관리자 endpoin](#The-azure-stack-resource-manager-endpoint)합니다.  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure 스택 리소스 관리자 끝점

Microsoft Azure 리소스 관리자는 관리자가 배포, 관리 및 Azure 리소스를 모니터링할 수 있도록 관리 합니다. Azure 리소스 관리자는 그룹을 구성원으로, 단일 작업에서 이러한 작업을 처리할 수 있습니다.

리소스 관리자 끝점에서 메타 데이터 정보를 가져올 수 있습니다. 끝점 사용자 코드를 실행 하는 데 필요한 정보로 JSON 파일을 반환 합니다.

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

### <a name="set-environmental-variables"></a>환경 변수 설정

**Microsoft Windows**  
Windows 명령 프롬프트에서 환경 변수를 설정, 다음 형식을 사용 합니다.  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux 및 Unix 기반 시스템**  
Unix 기반 시스템에서와 같은 명령을 사용할 수 있습니다.  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>기존 API 프로필

Azure_sdk 롤업 보석 다음 두 프로필에 있습니다.

1. **V2017_03_09**  
  Azure 스택 용으로 작성 된 프로필입니다. Azure 스택 가장 호환 되도록 서비스에 대 한이 프로필을 사용 합니다.
2. **최신**  
  프로필의 모든 서비스의 최신 버전으로 구성 됩니다. 모든 서비스의 최신 버전을 사용 합니다.

Azure 스택 및 API 프로필에 대 한 자세한 내용은 참조는 [API 요약 프로필](azure-stack-version-profiles.md#summary-of-api-profiles)합니다.

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure SDK API Ruby 프로필 사용

다음 줄은 프로필 클라이언트를 인스턴스화하는 것 같습니다. 이 매개 변수는만 Azure 스택 또는 다른 사설 클라우드에 필요 합니다. 전역 Azure 기본적으로 이러한 설정은 이미 있습니다.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

계산, 저장소 및 네트워크와 같은 개별 리소스 공급자에 액세스 하 프로필 클라이언트를 사용할 수 있습니다.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>AzureStack 환경 설정 함수를 정의 합니다.

Azure 스택 환경에 서비스 사용자를 인증 하려면 사용 하 여 끝점 정의 **get_active_directory_settings()** 합니다. 이 메서드는 사용 된 **ARM_Endpoint** 환경 변수를 설정할 때 설정 하는 환경 변수입니다.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>프로필 API를 사용 하 여 예제

사용할 수는 다음 샘플에서는 Ruby 및 스택 API Azure 프로필 솔루션 만들기를 참조로 GitHub repositoreis에서 찾을 수:

 - [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Ruby를 사용 하 여 가상 컴퓨터 관리](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>예제 리소스 관리자 및 그룹

이 샘플을 실행 하려면 Ruby를 설치 했는지 확인 합니다. Visual Studio 코드를 사용 하는 경우에 확장으로 Ruby SDK를 다운로드 합니다. 

> [!Note]  
> 에 있는 샘플에 대 한 저장소를 얻을 수 있습니다 "[관리 Azure 리소스와 Ruby 있는 리소스 그룹을](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)"입니다.

1. 리포지토리를 복제 합니다.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. 번들을 사용 하 여 종속성을 설치 합니다.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. PowerShell을 사용 하 여 Azure 서비스 사용자를 만들고 필요한 값을 검색 합니다. 

  서비스 사용자를 만드는 방법에 지침은 [인증서를 사용 하 여 서비스 사용자를 만들려면 Azure PowerShell을 사용 하 여](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)합니다.

  필요한 값은 같습니다.
  - 테넌트 ID
  - 클라이언트 ID
  - 클라이언트 암호
  - 구독 ID
  - 리소스 관리자 끝점

  만든 서비스 사용자에서 검색 된 정보를 사용 하 여 다음과 같은 환경 변수를 설정 합니다.

  - AZURE_TENANT_ID 내보내기 = {테 넌 트 id}
  - AZURE_CLIENT_ID 내보내기 = {클라이언트 id}
  - AZURE_CLIENT_SECRET 내보내기 = {클라이언트 암호}
  - AZURE_SUBSCRIPTION_ID 내보내기 = {구독 id}
  - ARM_ENDPOINT 내보내기 = {AzureStack 리소스 관리자 url을 (를)

  > [!Note]  
  > Windows에서 내보내기 하는 대신 집합을 사용 합니다.

4. 위치 변수 AzureStack 위치로 설정 되었는지 확인 합니다. 예를 들어 로컬 = "local"

5. 코드의 다음 줄에 추가 하는 경우 오른쪽 active directory 끝점을 대상으로 Azure 스택 또는 다른 사설 클라우드를 사용 합니다.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. 옵션 변수 내에서 active directory 설정 및 Azure 스택 작업할 기준 URL을 추가 합니다. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Azure 스택 프로필을 대상으로 하는 프로필 클라이언트를 만듭니다.

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Azure 스택 인 서비스 사용자를 인증 하려면 끝점을 정의 해야를 사용 하 여 **get_active_directory_settings()** 합니다. 이 메서드는 사용 된 **ARM_Endpoint** 환경 변수를 설정할 때 설정 하는 환경 변수입니다.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. 샘플을 실행합니다.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure 스택 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
