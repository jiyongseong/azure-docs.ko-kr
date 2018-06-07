---
title: Azure Active Directory를 사용하여 Batch Management 솔루션 인증 | Microsoft Docs
description: Azure Resource Manager로 구축된 응용 프로그램과 Batch 리소스 공급자는 Azure AD로 인증합니다.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: danlep
ms.openlocfilehash: a16d60dd48efeced7735562884acd26640d36483
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Active Directory를 사용하여 Batch Management 솔루션 인증

Azure Batch Management 서비스를 호출하는 응용 프로그램은 [Azure Active Directory][aad_about](Azure AD)로 인증합니다. Azure AD는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure에서는 해당 고객, 서비스 관리자 및 조직 사용자의 인증을 위해 Azure AD를 자체적으로 사용합니다.

Batch 관리 .NET 라이브러리는 배치 계정, 계정 키, 응용 프로그램 및 응용 프로그램 패키지를 사용하기 위한 종류를 표시합니다. Batch 관리 .NET 라이브러리는 Azure 리소스 공급자 클라이언트이며 [Azure Resource Manager][resman_overview]와 함께 프로그래밍 방식으로 해당 리소스를 관리하는 데 사용됩니다. Azure AD는 Batch 관리 .NET 라이브러리를 비롯한 Azure 리소스 공급자 클라이언트 및 [Azure Resource Manager][resman_overview]를 통해 만들어지는 요청을 인증하는 데 필요합니다.

이 문서에서는 Batch Management .NET 라이브러리를 사용하는 응용 프로그램에서 Azure AD를 사용하여 인증하는 방법을 살펴봅니다. Azure AD를 사용하여 통합 인증을 통해 구독 관리자 또는 공동 관리자를 인증하는 방법을 보여 줍니다. GitHub에서 사용할 수 있는 [AccountManagment][acct_mgmt_sample] 샘플 프로젝트에서 Batch Management .NET 라이브러리와 함께 Azure AD를 사용하도록 단계별로 안내합니다.

Batch 관리 .NET 라이브러리 및 AccountManagement 샘플을 사용하는 방법에 대한 자세한 내용은 [.NET용 Batch 관리 클라이언트 라이브러리를 사용하여 배치 계정 및 할당량 관리](batch-management-dotnet.md)를 참조하세요.

## <a name="register-your-application-with-azure-ad"></a>Azure AD에 응용 프로그램 등록

Azure [Active Directory 인증 라이브러리][aad_adal](ADAL)는 응용 프로그램 내에서 사용하기 위해 Azure AD에 프로그래밍 방식 인터페이스를 제공합니다. 응용 프로그램에서 ADAL을 호출하려면 Azure AD 테넌트에 응용 프로그램을 등록해야 합니다. 응용 프로그램을 등록할 때 Azure AD 테넌트 내에서 이름을 포함하여 응용 프로그램에 대한 Azure AD 정보를 제공합니다. 그런 다음 Azure AD는 런타임 시 응용 프로그램을 Azure AD와 연결하는 데 사용하는 응용 프로그램 ID를 제공합니다. 응용 프로그램 ID에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 및 서비스 주체 개체](../active-directory/develop/active-directory-application-objects.md)를 참조하세요.

AccountManagement 샘플 응용 프로그램을 등록하려면 [Azure Active Directory와 응용 프로그램 통합][aad_integrate]에서 [응용 프로그램 추가](../active-directory/develop/active-directory-integrating-applications.md#adding-an-application) 섹션의 단계를 따릅니다. 응용 프로그램 유형으로 **네이티브 클라이언트 응용 프로그램**을 지정합니다. **리디렉션 URI**의 업계 표준 OAuth 2.0 URI는 `urn:ietf:wg:oauth:2.0:oob`입니다. 그러나 실제 끝점일 필요가 없으므로 `http://myaccountmanagementsample`리디렉션 URI**에 대한 유효한 URI(예:** )를 지정할 수 있습니다.

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

등록 프로세스를 완료한 후 응용 프로그램에 대해 나열된 응용 프로그램 ID 및 개체(서비스 주체) ID가 표시됩니다.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>응용 프로그램에 대한 Azure Resource Manager API 액세스 권한 부여

다음으로, Azure Resource Manager API에 응용 프로그램에 대한 액세스 권한을 위임해야 합니다. 리소스 관리자 API에 대한 Azure AD 식별자는 **Windows Azure Service Management API**입니다.

Azure Portal에서 다음 단계를 따릅니다.

1. Azure Portal의 왼쪽 탐색 창에서 **모든 서비스**를 선택하고 **앱 등록**을 클릭한 다음, **추가**를 클릭합니다.
2. 앱 등록의 목록에서 응용 프로그램의 이름을 검색합니다.

    ![응용 프로그램 이름 검색](./media/batch-aad-auth-management/search-app-registration.png)

3. **설정** 블레이드를 표시합니다. **API 액세스** 섹션에서 **필요한 사용 권한**을 선택합니다.
4. **추가**를 클릭하여 새로운 필요한 사용 권한을 추가합니다. 
5. 1단계에서 **Windows Azure Service Management API**를 입력하고 결과 목록에서 해당 API를 선택하고 **선택** 단추를 클릭합니다.
6. 2단계에서 **조직 사용자로 Azure 클래식 배포 모델에 액세스** 옆의 확인란을 선택하고 **선택** 단추를 클릭합니다.
7. **완료** 단추를 클릭합니다.

**필요한 사용 권한** 블레이드는 이제 응용 프로그램에 대한 권한이 ADAL과 리소스 관리자 API에 부여되었음을 표시합니다. Azure AD에 앱을 처음 등록할 때 사용 권한은 기본적으로 ADAL에 부여됩니다.

![Azure Resource Manager API에 대한 권한 위임](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD 끝점

Azure AD를 사용하여 Batch Management 솔루션을 인증하려면 잘 알려진 다음 두 개의 끝점이 필요합니다.

- **Azure AD 공통 끝점**은 통합 인증의 경우와 같이 특정 테넌트를 제공하지 않을 때 일반 자격 증명 수집 인터페이스를 제공합니다.

    `https://login.microsoftonline.com/common`

- **Azure Resource Manager 끝점**은 Batch Management 서비스에 대한 요청을 인증하기 위한 토큰을 얻는 데 사용됩니다.

    `https://management.core.windows.net/`

AccountManagement 샘플 응용 프로그램은 이러한 끝점에 대한 상수를 정의합니다. 해당 상수를 변경하지 않고 그대로 둡니다.

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>응용 프로그램 ID 참조 

클라이언트 응용 프로그램은 런타임 시 Azure AD에 액세스하기 위해 응용 프로그램 ID(클라이언트 ID라고도 함)를 사용합니다. Azure Portal에서 응용 프로그램을 등록한 후 등록된 응용 프로그램에 대해 Azure AD에서 제공하는 응용 프로그램 ID를 사용하도록 코드를 업데이트합니다. AccountManagement 샘플 응용 프로그램에서 Azure Portal의 응용 프로그램 ID를 적절한 상수에 복사합니다.

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
또한 등록 프로세스 중 지정한 리디렉션 URI를 복사합니다. 코드에 지정된 리디렉션 URI는 응용 프로그램을 등록할 때 제공한 리디렉션 URI와 일치해야 합니다.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 인증 토큰 획득

Azure AD 테넌트에 AccountManagement 샘플을 등록하고 샘플 원본 코드를 값으로 업데이트하면 Azure AD를 사용하여 샘플을 인증할 준비가 됩니다. 샘플을 실행할 때 ADAL은 인증 토큰을 획득하려고 시도합니다. 이 단계에서 Microsoft 자격 증명을 묻습니다. 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

자격 증명을 제공한 후 샘플 응용 프로그램은 Batch 관리 서비스에 인증된 요청을 발급하도록 진행할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[AccountManagement 샘플 응용 프로그램][acct_mgmt_sample] 실행에 대한 자세한 내용은 [.NET용 Batch 관리 클라이언트 라이브러리를 사용하여 배치 계정 및 할당량 관리](batch-management-dotnet.md)를 참조하세요.

Azure AD에 대한 자세한 내용은 [Azure Active Directory 설명서](https://docs.microsoft.com/azure/active-directory/)를 참조하세요. ADAL을 사용하는 방법을 보여 주는 자세한 예제는 [Azure 코드 샘플](https://azure.microsoft.com/resources/samples/?service=active-directory) 라이브러리에서 사용할 수 있습니다.

Azure AD를 사용하여 Batch 서비스 응용 프로그램을 인증하려면 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)을 참조하세요. 


[aad_about]: ../active-directory/active-directory-whatis.md "Azure Active Directory란?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Azure AD의 인증 시나리오"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Azure Active Directory와 응용 프로그램 통합"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: http://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
