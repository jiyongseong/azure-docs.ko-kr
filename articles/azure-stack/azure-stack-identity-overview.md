---
title: Azure 스택 대 한 id의 개요 | Microsoft Docs
description: Azure 스택 함께 사용할 수 있습니다 id 시스템에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/22/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 607c7938a789b3504a425057645b291bd4c8235b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="overview-of-identity-for-azure-stack"></a>Azure 스택 대 한 id의 개요

Azure 스택 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS) Active Directory를 id 공급자로 뒷받침 되며 필요 합니다. 공급자의 선택은 Azure 스택을 처음 배포할 때 구성 하는 일회성 의사 결정 합니다. 이 문서의 개념 정보와 권한 부여 id 공급자 중에서 선택할 수 있습니다. 

Azure AD 또는 AD FS의 선택한 Azure 스택 배포 하는 모드에 의해 결정: 
- 온라인된 모드에서 배포할 때 Azure AD 또는 AD FS 사용할 수 있습니다. 
- 인터넷에 연결 하지 않고 연결이 끊어진된 모드에서 배포할 AD FS에만 사용할 수 있습니다.

Azure 스택 환경에 따라 옵션에 대 한 자세한 내용은 다음 문서를 참조 합니다.
- Azure 스택 배포 키트: [Identity 고려 사항](azure-stack-datacenter-integration.md#identity-considerations)합니다.
- Azure 스택 시스템 통합: [배포 계획에 대 한 Azure 스택 결정 통합 시스템](azure-stack-deployment-decisions.md)합니다.

 
## <a name="common-concepts-for-identity"></a>Id에 대 한 일반적인 개념
다음 섹션에서는 id 공급자와 Azure 스택에서 사용에 대 한 일반적인 개념을 설명 합니다.

![Id 공급자에 대 한 용어](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>디렉터리 테 넌 트 및 조직
디렉터리에 대 한 정보를 보유 하는 컨테이너는 *사용자*, *응용 프로그램*, *그룹*, 및 *서비스 보안 주체*합니다.
 
디렉터리 테 넌는 *조직*Microsoft 또는 회사 같은 합니다. 
- Azure AD에서는 여러 테 넌 트를 지원 하 고 자체 디렉터리에 각각 여러 조직 지원할 수 있습니다. Azure AD를 사용 하 고 여러 테 넌 트가 응용 프로그램 및 한 테 넌 트 액세스의 사용자가 동일한 해당 디렉터리의 다른 테 넌 트에 부여할 수 있습니다.
- AD FS 지원만 단일 테 넌 그리고이 단일 조직만 합니다. 

### <a name="users-and-groups"></a>개요
사용자 계정 (identities)는 사용자 ID와 암호를 사용 하 여 개별 사용자를 인증 하는 표준 계정. 그룹은 사용자 또는 다른 그룹에 포함할 수 있습니다. 

만들고 사용자 및 그룹 관리 사용 하면 id 솔루션에 따라 다릅니다. 

사용자 계정 Azure 스택: 
- 만든는 *username@domain* 형식입니다. AD FS 사용 하는 AD FS 사용자 계정에 매핑되지만 Active Directory 인스턴스를 지원 하지 않습니다는  *\<도메인 >\<별칭 >* 형식입니다. 
- 다단계 인증을 사용 하도록를 설정할 수 있습니다. 
- 처음 등록할 수 있는, 디렉터리인 해당 조직의 디렉터리에 제한 됩니다.
- 온-프레미스 디렉터리에서 가져올 수 있습니다. 자세한 내용은 참조 [Azure Active Directory와 온-프레미스 디렉터리 통합](/azure/active-directory/connect/active-directory-aadconnect)합니다. 

조직의 테 넌 트 포털에 로그인 할 때 사용 된 *https://portal.local.azurestack.external* URL입니다. 

### <a name="guest-users"></a>게스트 사용자
게스트 사용자는 디렉터리에 대 한 액세스 권한이 부여 된 다른 디렉터리 테 넌 트의 사용자 계정입니다. 게스트 사용자를 지원 하기 위해 Azure AD를 사용 하 고 다중 테 넌 트에 대 한 지원을 사용 하도록 합니다. 지원이 활성화 된 디렉터리 테 넌 트의 협력 외부 조직에 매핑함으로써 리소스에 액세스할 수 있는 게스트 사용자를 초대할 수 있습니다. 

게스트 사용자를 초대 하기 위해 클라우드 운영자 및 사용자 צ ְ ײ [Azure AD B2B 공동 작업](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)합니다. 초대 된 사용자 디렉터리에서 문서, 리소스 및 응용 프로그램에 대 한 액세스를 가져오고, 사용자 고유의 리소스 및 데이터에 대 한 제어를 유지 관리 합니다. 

게스트 사용자로 다른 조직의 디렉터리 테 넌 트에 로그인 수 있습니다. 이렇게 하려면 포털에 해당 조직의 디렉터리 이름을 추가 URL입니다. 예를 들어 Contoso 조직에 속해 Fabrikam 디렉터리에 로그인 하려고 하는 경우 사용 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com합니다.

### <a name="applications"></a>응용 프로그램
응용 프로그램을 Azure AD 또는 AD FS 등록 하 고 조직의 사용자에 게 응용 프로그램을 제공 합니다. 

응용 프로그램은 다음과 같습니다.
- **웹 응용 프로그램**: 예로 Azure 포털 및 Azure 리소스 관리자입니다. Web API 호출을 지원. 
- **네이티브 클라이언트**: Azure PowerShell, Visual Studio 및 Azure CLI 예로 합니다.

응용 프로그램에는 두 가지 유형의 테 넌 트 지원할 수 있습니다. 
- **단일 테 넌 트**: 사용자 및 서비스 응용 프로그램 등록 되어 있는 디렉터리에서만 지원 합니다. 

  > [!NOTE]    
  > AD FS는 단일 디렉터리에만 지원 하므로 AD FS 토폴로지에서 만드는 응용 프로그램은 기본적으로 단일 테 넌 트 응용 프로그램.

- **다중 테 넌 트**: 사용자 및 서비스 응용 프로그램 등록 되어 있는 디렉터리와 추가 테 넌 트 디렉터리의 사용을 지원 합니다. 다중 테 넌 트 응용 프로그램과 함께 다른 테 넌 트 디렉터리 (다른 Azure AD 테 넌) 수의 사용자가 응용 프로그램에 로그인합니다. 

  다중 테 넌 트에 대 한 자세한 내용은 참조 [다중 테 넌 트를 사용 하도록 설정](azure-stack-enable-multitenancy.md)합니다. 

  다중 테 넌 트 응용 프로그램을 개발 하는 방법에 대 한 자세한 내용은 참조 [다중 테 넌 트 앱](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)합니다.

응용 프로그램을 등록 하면 두 개체를 만듭니다.

- **응용 프로그램 개체**: 모든 테 넌 트 간에 응용 프로그램의 전역 표현 합니다. 이 관계 일대일 소프트웨어 응용 프로그램과 함께 사용 되며 응용 프로그램이 처음 등록 될 디렉터리에만 존재 합니다.

- **서비스 사용자 개체**: 응용 프로그램이 처음 등록 될 디렉터리의 응용 프로그램에 대해 생성 되는 자격 증명입니다. 서비스 사용자는 해당 응용 프로그램 사용 되는 각 추가 테 넌 트의 디렉터리에 만들어집니다. 이 관계는 일대다 소프트웨어 응용 프로그램과 함께 될 수 있습니다. 

응용 프로그램 및 서비스 사용자 개체에 대 한 자세한 참조 [응용 프로그램 및 Azure Active Directory에서 서비스 보안 주체 개체](/azure/active-directory/develop/active-directory-application-objects)합니다. 

### <a name="service-principals"></a>서비스 원칙 
서비스 사용자의 집합이 *자격 증명* 스택에서 Azure 리소스에 대 한 액세스 권한을 부여 하는 서비스 또는 응용 프로그램입니다. 서비스 사용자의 사용 응용 프로그램의 응용 프로그램 권한을 사용자의 사용 권한에서 분리합니다.

서비스 사용자는 각 테 넌 트 응용 프로그램 사용 되는 위치에 생성 됩니다. 서비스 사용자는 로그인 및 해당 테 넌 트에 의해 보안 되는 리소스 (예: 사용자)에 대 한 액세스에 대 한 id를 설정 합니다. 

- 단일 테 넌 트 응용 프로그램 하나만 서비스 사용자를 처음 만들어질 디렉터리에 있습니다. 이 서비스 사용자가 만들어지고 동의 응용 프로그램을 등록 하는 동안 사용 되 고 있습니다. 
- 다중 테 넌 트 웹 응용 프로그램이 나 API에는 각 테 넌 트에서 생성 된 서비스 사용자는 해당 테 넌 트의 사용자는 응용 프로그램의 사용에 동의 하는 위치 있습니다. 

서비스 사용자에 대 한 자격 증명으로 Azure 포털을 통해 생성 되는 키 또는 인증서 수 있습니다. 인증서를 사용 하는 인증서 키 보다 더 안전한 것으로 간주 되므로 자동화 적합 합니다. 


> [!NOTE]    
> Azure 스택을 AD FS를 사용 하면 관리자만 서비스 사용자를 만들 수 있습니다. AD FS와 함께 서비스 사용자는 인증서가 필요 하 고 권한 있는 끝점 (PEP)을 통해 생성 됩니다. 자세한 내용은 참조 [Azure 스택에 응용 프로그램 액세스 권한을 제공](azure-stack-create-service-principals.md)합니다.

Azure 스택에 대 한 서비스 사용자에 대 한 자세한 내용은 [서비스 사용자를 만들](azure-stack-create-service-principals.md)합니다.


### <a name="services"></a>Services
Azure 스택에 id 공급자와 상호 작용 하는 서비스는 id 공급자와 응용 프로그램으로 등록 됩니다. 응용 프로그램과 마찬가지로 등록 id 시스템을 인증 하는 서비스를 활성화 합니다. 

모든 Azure 서비스를 사용 하 여 [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 프로토콜 및 [JSON 웹 토큰](/azure/active-directory/develop/active-directory-token-and-claims) 자신의 id를 설정 합니다. 때문에 Azure AD와 AD FS 프로토콜을 지속적으로 사용, 사용할 수 있습니다 [Azure Active Directory 인증 라이브러리](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 온-프레미스 인증을 또는 연결 된 경우) (에 Azure입니다. ADAL을 간 클라우드 및 온-프레미스 리소스 관리에 대 한 Azure PowerShell 및 Azure CLI와 같은 도구를도 사용할 수 있습니다. 


### <a name="identities-and-your-identity-system"></a>Id 및 id 시스템 
Azure 스택에 대 한 id는 사용자 계정, 그룹 및 서비스 사용자를 포함 합니다. 

Azure 스택, 여러 기본 제공 응용 프로그램 및 서비스를 자동으로 설치 하는 경우에 디렉터리 테 넌 트에서 id 공급자와 등록 합니다. 등록 하는 일부 서비스 관리에 사용 됩니다. 다른 서비스는 사용자가 사용할 수 있습니다. 기본 등록 서로 및 나중에 추가 하는 id와 상호 작용할 수 있는 핵심 서비스 id를 제공 합니다.

다중 테 넌 트와 Azure AD를 설정 하면 일부 응용 프로그램을 새 디렉터리에 전파 합니다. 

## <a name="authentication-and-authorization"></a>인증 및 권한 부여
 

### <a name="authentication-by-applications-and-users"></a>응용 프로그램 및 사용자 인증
  
![Azure 스택 계층 사이 identity](media/azure-stack-identity-overview/identity-layers.png)

응용 프로그램 및 사용자에 대 한 Azure 스택 아키텍처 4 단계 계층으로 설명 합니다. 이러한 각 계층 간의 상호 작용 유형의 인증을 사용할 수 있습니다.


|계층    |레이어 간의 인증  |
|---------|---------|
|도구 및 관리 포털 등의 클라이언트     | 도구에 액세스 하거나 스택에서 Azure 리소스를 수정 하 고 클라이언트가 사용 하는 [JSON 웹 토큰](/azure/active-directory/develop/active-directory-token-and-claims) Azure 리소스 관리자에 전화를 합니다. <br>Azure 리소스 관리자 JSON 웹 토큰의 유효성을 검사 하 고 관찰 된 *클레임* 수준의 권한 부여를 예측 하려면 발급된 된 토큰에서 해당 사용자 또는 서비스 사용자가 Azure 스택의 합니다. |
|Azure 리소스 관리자와 핵심 서비스     |Azure 리소스 관리자는 사용자의 통신을 전송 하는 리소스 공급자와 통신 합니다. <br> 사용 하 여 전송 *직접 명령적* 호출 또는 *선언적* 통해 호출 [Azure 리소스 관리자 템플릿을](/azure/azure-stack/user/azure-stack-arm-templates.md)합니다.|
|리소스 공급자     |인증서 기반 인증으로 보안 리소스 공급자에 전달 되는 통화입니다. <br>Azure 리소스 관리자 및 리소스 공급자는 다음 API 통해 통신에 남아 있습니다. Azure 리소스 관리자에서 받은 모든 호출에 대 한 리소스 공급자는 해당 인증서를 사용 하 여 호출을 확인 합니다.|
|인프라 및 비즈니스 논리     |리소스 공급자가 선택한 인증 모드를 사용 하 여 비즈니스 논리 및 인프라와 통신 합니다. Azure 스택와 함께 제공 되는 기본 리소스 공급자 보안이 통신을 위해 Windows 인증을 사용 합니다.|

![인증에 필요한 정보](media/azure-stack-identity-overview/authentication.png)


### <a name="authenticate-to-azure-resource-manager"></a>Azure 리소스 관리자 인증
Id 공급자를 사용 하 여 인증 하 고 JSON 웹 토큰을 수신 하는 다음 정보가 있어야 합니다. 
1.  **Id 시스템 (인증 기관)에 대 한 URL**: id 공급자를 연결할 수 있는 URL입니다. 예를 들어 *https://login.windows.net*입니다. 
2.  **Azure 리소스 관리자에 대 한 앱 ID URI**:에 대 한 Azure 리소스 관리자에 등록 된 id 공급자의 고유 식별자입니다. 각 Azure 스택 설치에 고유한 이기도합니다.
3.  **자격 증명**: id 공급자와 인증을 사용 하는 자격 증명입니다. 
4.  **Azure 리소스 관리자에 대 한 URL**: URL이 Azure 리소스 관리자 서비스의 위치입니다. 예를 들어 *https://management.azure.com* 또는 *https://management.local.azurestack.external*합니다.

보안 주체 (클라이언트, 응용 프로그램 또는 사용자)는 리소스에 액세스 하려면 인증 요청, 요청 포함 되어야 합니다.
- 사용자의 자격 증명입니다.
- 앱의 보안 주체에서 액세스 하려는 리소스 ID URI입니다. 

자격 증명 id 공급자에 의해 유효성이 검사 됩니다. Id 공급자에는 또한 앱 ID URI에 대 한 등록된 된 응용 프로그램 및 보안 주체에 해당 리소스에 대 한 토큰을 가져올 수 있는 올바른 권한이 있는지 확인 합니다. 요청이 유효한 경우 JSON 웹 토큰 부여 됩니다. 

토큰 다음 통과 해야는 요청의 헤더에서 Azure 리소스 관리자에 합니다. Azure 리소스 관리자 순서에 관계 없이 다음 작업을 수행 합니다.
- 유효성을 검사는 *발급자* (iss) 클레임에 올바른 id 공급자 로부터 토큰 있는지 확인 합니다. 
- 유효성을 검사는 *audience* (aud) 클레임 토큰이 Azure 리소스 관리자에 발급 되었는지 확인 합니다. 
- 유효성을 검사 JSON 웹 토큰은 OpenID를 통해 구성 된 인증서로 서명 된 Azure 리소스 관리자에 알려져 있습니다. 
- 검토는 *에서 발급 된* (iat) 및 *만료* (exp) 토큰 활성 상태 인지 확인 하 고 허용 될 수 있습니다. 

Azure 리소스 관리자를 사용 하는 모든 유효성 검사 완료 되 면는 *할 때* (oid) 및 *그룹* 주장 하는 보안 주체가 액세스할 수 있는 리소스의 목록을 만듭니다. 

![토큰 교환 프로토콜의 다이어그램](media/azure-stack-identity-overview/token-exchange.png)


### <a name="use-role-based-access-control"></a>역할 기반 Access Control 사용  
역할 기반 액세스 제어 (RBAC) Azure 스택의 Microsoft Azure의 구현은 일치 합니다. 사용자, 그룹 및 응용 프로그램에서 RBAC에 적절 한 역할을 할당 하 여 리소스에 대 한 액세스를 관리할 수 있습니다. Azure 스택 함께 RBAC를 사용 하는 방법에 대 한 내용은 다음 문서를 참조 합니다.
- [Azure Portal에서 역할 기반 액세스 제어로 시작합니다](/azure/role-based-access-control/overview).
- [역할 기반 액세스 제어를 사용 하 여 Azure 구독 리소스에 대 한 액세스를 관리 하려면](/azure/role-based-access-control/role-assignments-portal)합니다.
- [신속히 알아봅니다 액세스 제어에 대 한 사용자 지정 역할 만들기](/azure/role-based-access-control/custom-roles)합니다.
- [역할 기반 액세스 제어 관리](azure-stack-manage-permissions.md) Azure 스택의 합니다.


### <a name="authenticate-with-azure-powershell"></a>Azure PowerShell을 사용 하 여 인증  
Azure 스택 인증에 Azure PowerShell을 사용 하는 방법에 대 한 세부 정보에서 찾을 수 있습니다 [Azure 스택 사용자의 PowerShell 환경을 구성](azure-stack-powershell-configure-user.md)합니다.

### <a name="authenticate-with-azure-cli"></a>Azure CLI를 사용 하 여 인증
Azure 스택 인증에 Azure PowerShell을 사용 하는 방법에 대 한 정보를 참조 하십시오. [설치 Azure 스택에 사용할 Azure CLI 구성 및](/azure/azure-stack/user/azure-stack-connect-cli.md)합니다.

## <a name="next-steps"></a>다음 단계
- [Id 아키텍처](azure-stack-identity-architecture.md)   
- [데이터 센터 통합-identity](azure-stack-integrate-identity.md)




