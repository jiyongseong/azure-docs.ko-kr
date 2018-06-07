---
title: Azure AD의 권한 | Microsoft Docs
description: Azure Active Directory의 범위와 권한 및 이들을 사용하는 방법 알아보기
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="permissions-in-azure-ad"></a>Azure AD의 권한
Azure AD(Azure Active Directory)는 OAuth 및 OpenID Connect(OIDC) 흐름을 위해 권한을 폭 넓게 사용합니다. 앱이 Azure AD에서 액세스 토큰을 받는 경우, 해당 토큰은 앱이 특정 리소스에 대해 가지는 권한(일명 범위)을 설명하는 클레임을 포함합니다. 따라서 토큰이 호출하는 API에 적절한 권한만 포함하는지 확인하기만 하면 되므로 리소스 인증이 쉬워집니다. 

## <a name="types-of-permissions"></a>권한 형식
Azure AD는 두 종류의 권한을 정의합니다. 
* **위임된 권한** - 로그인한 사용자가 현재 있는 앱에서 사용합니다. 이 앱에 대해 사용자 또는 관리자는 앱이 요청하는 권한 및 앱이 API를 호출할 때 로그인한 사용자로 행동하도록 위임된 권한이라는 데 동의합니다. API에 따라 사용자는 API에 직접 동의할 수 없고 대신에 관리자에게 ["관리자 동의"](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)를 제공할 것을 요구할 수 있습니다.
* **응용 프로그램 권한** - 로그인한 사용자가 없이 실행한 앱(예: 백그라운드 서비스 또는 디먼으로 실행한 앱)에서 사용합니다. 응용 프로그램 권한은 일반적으로 아주 강력하고 사용자 경계 간의 데이터 또는 다른 방법이라면 관리자로 제한될 수 있는 데이터에 액세스할 수 있도록 하므로 [관리자만이 동의](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant)할 수 있습니다. 

효과적인 권한은 앱이 API를 요청할 때 갖게 될 권한입니다. 

* 위임된 권한의 경우 앱의 효과적인 권한은 (동의를 통해) 앱에 부여한 위임된 권한과 현재 로그인한 사용자의 권한의 최소 권한 교집합입니다. 앱은 로그인한 사용자보다 더 많은 권한을 가질 수 없습니다. 조직 내에서 로그인한 사용자의 권한은 정책 또는 관리자 역할 하나 이상의 멤버 자격에 의해 결정될 수 있습니다. 관리자 역할에 대한 자세한 내용은 [Azure AD에서 관리자 역할 할당](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md)을 참조하세요.
    예를 들어 사용자의 앱에 Microsoft Graph의 `User.ReadWrite.All` 위임된 권한을 부여한 것으로 가정합니다. 이 권한은 일반적으로 조직에 있는 모든 사용자의 프로필을 읽고 업데이트하는 앱 권한을 부여합니다. 로그인한 사용자가 전역 관리자인 경우 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있게 됩니다. 그러나 로그인한 사용자가 관리자 역할이 아니면 앱은 로그인한 사용자의 프로필만 업데이트할 수 있게 됩니다. 즉, 대신 행동할 권한을 가진 사용자가 조직에 있는 다른 사용자의 프로필에 대한 권한을 가지고 있지 않으므로 해당 다른 사용자의 프로필을 업데이트할 수 없습니다.
* 응용 프로그램 권한의 경우 앱의 효과적인 권한은 권한이 암시하는 권한의 전체 수준입니다. 예를 들어 `User.ReadWrite.All` 응용 프로그램 권한을 가진 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있습니다. 

## <a name="permission-attributes"></a>권한 특성
Azure AD의 권한은 사용자, 관리자 또는 앱 개발자가 액세스할 권한을 부여한 것이 무엇인지에 관하여 현명한 결정을 내리는 데 도움이 되는 여러 속성을 포함합니다. 

> [!NOTE]
> Azure AD 응용 프로그램 또는 서비스 주체가 Azure Portal 또는 PowerShell을 사용하여 표시하는 권한을 볼 수 있습니다. 이 스크립트를 실행하여 Microsoft Graph가 표시하는 권한을 확인해 보세요.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| 속성 이름 | 설명 | 예 | 
| --- | --- | --- |
| ID | 이 권한을 고유하게 식별하는 GUID 값입니다. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | 이 범위가 사용할 수 있는지 여부를 나타냅니다. | true | 
| type | 이 권한이 사용자 동의 또는 관리자 동의를 요구하는지 여부를 나타냅니다. | 사용자 | 
| AdminConsentDescription | 관리자 동의 환경에 있는 동안 관리자에게 표시되는 설명입니다. | 앱에서 사용자 사서함의 이메일을 읽을 수 있도록 허용합니다. | 
| AdminConsentDisplayName | 관리자 동의 환경에 있는 동안 관리자에게 표시되는 친숙한 이름입니다. | 사용자 메일 읽기 | 
| UserConsentDescription | 사용자 동의 환경에 있는 동안 사용자에게 표시되는 설명입니다. |  앱에서 사서함의 이메일을 읽을 수 있도록 허용합니다. | 
| UserConsentDisplayName | 사용자 동의 환경에 있는 동안 사용자에게 표시되는 친숙한 이름입니다. | 메일 읽기 | 
| 값 | OAuth 2.0 권한 흐름 중에 권한을 식별하는 데 사용되는 문자열입니다. 이 문자열을 앱 ID URI 문자열과 조합하여 정규화된 권한 이름을 형성할 수도 있습니다. | `Mail.Read` | 

## <a name="types-of-consent"></a>동의 형식
Azure AD의 응용 프로그램이 필요한 리소스 또는 API에 액세스하려면 동의가 필요합니다. 앱이 성공하기 위해 알아야 할 수 있는 여러 종류의 동의가 있습니다. 권한을 정의하는 경우 사용자가 앱 또는 API에 액세스 권한을 얻는 방법도 이해해야 합니다.

* **고정적인 사용자 동의** - 앱이 상호작용하려는 리소스를 지정하는 [OAuth 2.0 인증 흐름](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) 중에 자동으로 이루어집니다. 고정적 사용자 동의 시나리오에서 앱은 Azure Portal의 앱 구성에 필요한 모든 권한을 이미 지정했어야 합니다. 사용자(또는 해당하는 경우 관리자)가 이 앱에 대한 동의를 받지 못한 경우, Azure AD는 사용자에게 지금 동의를 제공하라는 메시지를 표시합니다. 

    고정적 API 집합에 액세스를 요청하는 Azure AD 앱 등록에 대해 자세히 알아봅니다.
* **동적 사용자 동의** - v2 Azure AD 앱 모델의 기능입니다. 이 시나리오에서 앱은 [v2 앱에 대한 OAuth 2.0 권한 흐름](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)에서 필요한 범위 집합을 요청합니다. 사용자가 아직 동의하지 않은 경우 지금 동의하라는 메시지가 표시됩니다. [동적 동의에 대해 자세히 알아봅니다](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > 동적 동의는 편리할 수 있지만 권한에 대해 관리자 동의가 필요한데 관리자 동의 환경은 동의 시간에 해당 권한에 대해 인식하지 못한다는 큰 문제가 있습니다. 관리자 권한 범위가 필요한 경우 앱이 Azure Portal에 해당 범위를 등록해야 합니다.
  
* **관리자 동의** - 앱이 높은 권한의 특정 사용 권한에 액세스해야 하는 경우 필요합니다. 이 동의는 관리자가 몇몇 추가 제어를 포함해야 앱 또는 사용자가 조직의 높은 권한 데이터에 액세스하는 것을 인증합니다. [관리자 동의를 부여하는 방법에 대해 자세히 알아봅니다](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>모범 사례

### <a name="resource-best-practices"></a>리소스 모범 사례
API를 표시하는 리소스는 보호하는 데이터 또는 작업에 매우 특정적인 권한을 정의해야 합니다. 이렇게 하면 고객이 필요하지 않은 데이터에 액세스하는 권한을 갖지 않도록 하고 사용자가 동의하는 데이터에 관한 알림도 받도록 하는 데 도움이 됩니다.

리소스는 `Read` 및 `ReadWrite` 권한을 명시적으로 따로 정의해야 합니다. 

리소스는 `Admin` 권한 같은 사용자 경계 간의 데이터에 액세스를 허용하는 권한을 표시해야 합니다. 

리소스는 `Subject`가 사용할 수 있는 데이터 형식에 해당하고 `Permission`이 사용자가 해당 데이터에 대해 실행하는 작업에 해당하고 `Modifier`가 다른 권한의 명세를 설명하기 위해 선택적으로 사용되는 다음 명명 패턴 `Subject.Permission[.Modifier]`를 따릅니다. 예:  
* Mail.Read - 사용자가 메일을 읽도록 허용합니다. 
* Mail.ReadWrite - 사용자가 메일을 읽거나 쓰도록 허용합니다.
* Mail.ReadWrite.All - 관리자 또는 사용자가 조직의 모든 메일에 액세스하도록 허용합니다.

### <a name="client-best-practices"></a>클라이언트 모범 사례
앱에 필요한 범위에 대한 권한만 요청합니다. 너무 많은 권한을 가진 앱은 손상될 경우 사용자 데이터를 노출할 위험이 있습니다.

고객은 동일한 앱에서 응용 프로그램 권한과 위임된 권한을 함께 요청하지 않아야 합니다. 그러한 요청은 권한 상승을 초래하여 사용자가 자신의 권한에서 허용하지 않는 데이터에 액세스하게 될 수 있습니다. 




