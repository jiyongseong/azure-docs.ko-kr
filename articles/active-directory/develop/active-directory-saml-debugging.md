---
title: Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법 | Microsoft Docs
description: 'Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법을 알아봅니다. '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory에서 SAML 기반 Single Sign-On을 응용 프로그램에 디버그하는 방법

SAML 기반 응용 프로그램 통합을 디버그할 때 종종 [Fiddler](http://www.telerik.com/fiddler)와 같은 도구를 사용하여 응용 프로그램에 발급된 SAML 토큰을 포함하는 SAML 요청 및 SAML 응답을 확인하는 것이 유용합니다. 

![Fiddler][1]

자주 발생하는 문제는 Azure Active Directory 또는 응용 프로그램의 잘못된 구성과 관련이 있습니다. 오류를 확인하는 위치에 따라 SAML 요청 또는 응답을 검토해야 합니다.

- 내 회사 로그인 페이지에서 오류가 표시됩니다. [섹션에 연결]
- 로그인한 후 응용 프로그램의 페이지에 오류가 표시됩니다. [섹션에 연결]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>내 회사 로그인 페이지에 오류가 표시됩니다.

[페더레이션된 Single Sign-On에 대해 구성된 갤러리 응용 프로그램에 로그인하는 문제](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML)에서 오류 코드 및 문제 해결 단계 간 일대일 매핑을 찾을 수 있습니다.

회사 로그인 페이지에 오류가 표시되는 경우에는 문제를 디버깅하려면 오류 메시지와 SAML 요청이 필요합니다.

### <a name="how-can-i-get-the-error--message"></a>오류 메시지는 어떻게 가져옵니까?

오류 메시지를 가져오려면 페이지의 오른쪽 아래 모서리를 살펴봅니다. 다음을 포함하는 오류를 확인할 수 있습니다.

- CorrelationID
- 타임스탬프
- 메시지

![오류][2] 

 
상관 관계 ID 및 타임스탬프는 로그인 실패와 관련된 백 엔드 로그를 찾는 데 사용할 수 있는 고유한 식별자를 만듭니다. 이러한 값은 엔지니어가 문제에 대한 해결책을 더 신속히 제공하는 데 유용하므로 Microsoft를 통해 지원 사례를 만들 때 중요합니다.

메시지는 로그인 문제의 근본 원인입니다. 


### <a name="how-can-i-review-the-saml-request"></a>SAML 요청을 검토하려면 어떻게 해야 하나요?

응용 프로그램에서 Azure Active Directory로 보낸 SAML 요청은 일반적으로 [https://login.microsoftonline.com](https://login.microsoftonline.com)의 마지막 HTTP 200 응답입니다.
 
Fiddler를 사용하면 이 줄을 선택한 다음, 오른쪽 패널에서 **검사기 > WebForms** 탭을 선택하여 SAML 요청을 볼 수 있습니다. **SAMLRequest** 값을 마우스 오른쪽 단추로 클릭한 다음, **TextWizard로 보내기**를 선택합니다. 그런 다음 **Transform** 메뉴에서 **From Base64**를 선택하여 토큰을 디코딩하고 해당 콘텐츠를 확인합니다. 

또한 SAMLrequest의 값을 복사하여 다른 Base64 디코더를 사용할 수 있습니다.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

디코딩된 SAML 요청을 사용하여 다음을 검토합니다.

1. SAML 요청의 **대상**은 Azure Active Directory에서 가져온 SAML Single Sign-On 서비스 URL에 해당합니다.
 
2. SAML 요청의 **발급자**는 Azure Active Directory의 응용 프로그램에 대해 구성한 것과 동일한 **식별자**입니다. Azure AD는 사용자 디렉터리에서 응용 프로그램을 찾는 데 발급자를 사용합니다.

3. **AssertionConsumerServiceURL**은 응용 프로그램이 Azure Active Directory에서 SAML 토큰을 수신해야 하는 위치입니다. Azure Active Directory에서 이 값을 구성할 수 있지만 SAML 요청에 포함된 경우에는 필수적이지 않습니다.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>로그인한 후 응용 프로그램의 페이지에 오류가 표시됩니다.

이 시나리오에서 응용 프로그램은 Azure AD에서 발행된 응답을 수락하지 않습니다. 누락되거나 잘못된 사항을 알 수 있도록 응용 프로그램 공급업체를 통해 SAML 토큰을 포함하는 Azure AD의 응답을 확인하는 것이 중요합니다. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>SAML 응답을 받아 검토하려면 어떻게 해야 하나요?

SAML 토큰이 포함된 Azure AD의 응답은 일반적으로 https://login.microsoftonline.com에서 HTTP 302가 리디렉션되고 응용 프로그램의 구성된 **회신 URL**로 전송된 후에 발생하는 응답입니다. 

이 줄을 선택한 다음 오른쪽 패널에서 **Inspectors > WebForms** 탭을 선택하여 SAML 토큰을 볼 수 있습니다. 여기에서 **SAMLResponse** 값을 마우스 오른쪽 단추로 클릭하고 **Send to TextWizard**를 선택합니다. 그런 다음, **변환** 메뉴에서 **Base64에서**를 선택하여 토큰을 디코딩하고 해당 콘텐츠가 다른 Base64 디코더를 사용하는 것을 확인합니다. 

또한 **SAMLrequest**의 값을 복사하여 다른 Base64 디코더를 사용할 수 있습니다.

SAML 반응에서 누락되거나 잘못된 항목에 대한 더 자세한 정보는 [로그인 후 응용 프로그램의 페이지에 표시된 오류](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) 문제 해결 지침을 방문해 보세요.

SAML 응답을 검토하는 방법에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response)을 방문해 보세요.


## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)
* [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [사전 통합된 앱에 대해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
