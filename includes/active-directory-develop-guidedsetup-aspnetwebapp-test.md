---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7e9518f8a90faa0566b96d58992b01e4b0a642f4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
## <a name="test-your-code"></a>코드 테스트

Visual Studio에서 응용프로그램을 테스트하려면 **F5** 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 http://<span></span>localhost:{port}로 이동합니다. 이 페이지에는 **Microsoft에 로그인** 단추가 표시됩니다. 단추를 선택하여 로그인 프로세스를 시작합니다.

테스트를 실행할 준비가 되면, Microsoft Azure Active Directory(Azure AD) 계정(회사 또는 학교 계정) 또는 개인 Microsoft 계정(<span>live.</span>com 또는 <span>outlook.</span>com)을 사용해 로그인합니다.

![Microsoft로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft 계정으로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>응용 프로그램 결과 보기
로그인한 후 사용자는 웹 사이트의 홈 페이지로 리디렉션됩니다. 홈페이지란 Microsoft 응용 프로그램 등록 포털의 응용 프로그램 등록 정보에 지정된 HTTPS URL입니다. 홈페이지는 로그아웃 링크이며 사용자의 클레임을 보기 위한 링크인 환영 메시지 *"Hello \<User>,"* 를 포함합니다. 사용자 클레임에 대한 링크는 앞에서 만든 *클레임* 컨트롤러로 이동합니다.

### <a name="browse-to-see-the-users-claims"></a>사용자 클레임 찾아보기
사용자의 클레임을 보려면 인증된 사용자만 사용할 수 있는 컨트롤러 뷰를 찾아보기할 링크를 선택합니다.

#### <a name="view-the-claims-results"></a>클레임 결과 보기
컨트롤러 뷰로 이동 후 사용자를 위한 기본 속성이 담긴 테이블을 확인해야 합니다.

|자산 |값 |설명 |
|---|---|---|
|**Name** |사용자의 전체 이름 | 사용자의 이름과 성입니다.
|**사용자 이름** |user<span>@domain.com</span> | 사용자를 식별하는 데 사용하는 사용자 이름입니다.
|**제목** |제목 |웹에서 사용자를 고유하게 식별하는 문자열입니다.|
|**테넌트 ID** |Guid | 사용자의 Azure AD 조직을 고유하게 나타내는 **guid**입니다.|

또한 인증 요청에 있는 모든 클레임 테이블을 확인해야 합니다. 자세한 내용은 [Azure AD ID 토큰에 있는 클레임 목록](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)을 참조하세요.


### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Authorize 특성이 있는 메서드 액세스 테스트(선택 사항)
`Authorize` 특성으로 보호되는 컨트롤러에 익명 사용자로 액세스 테스트를 진행하려면 다음 단계를 따릅니다.
1. 사용자 로그아웃 링크를 선택하고 로그아웃 프로세스를 완료합니다.
2. 브라우저에서 http://<span></span>localhost:{port}/claims를 입력하여 `Authorize` 특성으로 보호되는 컨트롤러에 액세스합니다.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>보호된 컨트롤러에 액세스한 후 예상된 결과
보호된 컨트롤러 뷰를 사용하기 위해 인증을 묻는 메시지가 나타납니다.

## <a name="advanced-options"></a>고급 옵션

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>전체 웹 사이트 보호
전체 웹 사이트를 보호하려면 **Global.asax** 파일에서 `Application_Start` 메서드의 `GlobalFilters` 필터에 `AuthorizeAttribute` 특성을 추가합니다.

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>응용 프로그램에 로그인할 수 있는 사용자 제한
기본적으로 이 가이드에서 만드는 모든 응용 프로그램을 빌드할 때 응용 프로그램에서는 Azure Active Directory와 통합된 모든 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)의 로그인을 수락합니다. SaaS 응용 프로그램에는 이 옵션을 권장합니다.

응용 프로그램에 대한 사용자 로그인 액세스를 제한할 수 있는 여러 가지 옵션이 있습니다.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>옵션 1: 한 조직의 Active Directory 인스턴스에 속한 사용자만 응용 프로그램에 로그인하도록 제한(단일 테넌트)

이 옵션은 *LOB 응용 프로그램*의 일반적인 시나리오입니다. 응용 프로그램에서 특정 Azure Active Directory 인스턴스에 속한 계정(해당 인스턴스의 *게스트 계정* 포함)의 로그인만 수락하도록 하려면

1. **web.config** 파일에서 `Tenant` 매개 변수의 값을 `Common`에서 조직의 테넌트 이름(예: `contoso.onmicrosoft.com`)으로 변경합니다.
2. [OWIN 스타트업 클래스](#configure-the-authentication-pipeline)에서 `ValidateIssuer` 인수를 `true`로 설정합니다.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>옵션 2: 응용 프로그램 액세스를 조직의 특정 목록에 포함된 사용자로 제한

로그인 액세스를 허용된 조직 목록에 들어 있는 Azure AD 조직에 있는 사용자 계정으로만 국한할 수 있습니다.
1. [OWIN 스타트업 클래스](#configure-the-authentication-pipeline)에서 `ValidateIssuer` 인수를 `true`로 설정합니다.
2. `ValidIssuers` 매개 변수의 값을 허용된 조직 목록으로 설정합니다.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>옵션 3: 사용자 지정 메서드를 사용하여 발급자의 유효성 검사
**IssuerValidator** 매개 변수를 사용하여 발급자의 유효성을 검사하는 사용자 지정 메서드를 구현할 수 있습니다. 이 매개 변수를 사용하는 방법에 관한 자세한 내용은 MSDN에서 [TokenValidationParameters 클래스](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx)에 관해 알아보세요.

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
