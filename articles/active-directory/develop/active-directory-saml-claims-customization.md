---
title: Azure Active Directory의 엔터프라이즈 응용 프로그램에 SAML 토큰에서 발급된 클레임 사용자 지정 | Microsoft Docs
description: Azure Active Directory의 엔터프라이즈 응용 프로그램에 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 485e4c52751b390ebd440718e7a55de7b5b5ffd7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Azure Active Directory의 엔터프라이즈 응용 프로그램에 SAML 토큰에서 발급된 클레임 사용자 지정
오늘날 Azure Active Directory는 사용자 지정 응용 프로그램뿐만 아니라 Azure AD 앱 갤러리에서 미리 통합된 응용 프로그램 모두를 포함하여 대부분의 엔터프라이즈 응용 프로그램에서 단일 로그인을 지원합니다. 사용자가 SAML 2.0 프로토콜을 사용하여 Azure AD를 통해 응용 프로그램을 인증하면 Azure AD는 (HTTP POST를 통해) 응용 프로그램에 토큰을 보냅니다. 그런 다음 응용 프로그램이 토큰의 유효성을 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰에는 "클레임"이라고 알려진 사용자에 대한 정보가 포함되어 있습니다.

ID에서 "클레임"은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다. [SAML 토큰 ](http://en.wikipedia.org/wiki/SAML_2.0)에서 이러한 데이터는 일반적으로 SAML 특성 문에 포함됩니다. 사용자 고유의 ID는 대개 이름 식별자라고도 하는 SAML Subject에 나타납니다.

기본적으로 Azure Active Directory는 Azure AD에 사용자의 사용자 이름(또는 사용자 계정 이름) 값과 함께 NameIdentifier 클레임이 포함된 SAML 토큰을 응용 프로그램에 발급합니다. 이 값이 사용자를 고유하게 식별할 수 있습니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰이 응용 프로그램에 발급한 클레임을 보거나 편집하려면 Azure Portal에서 응용 프로그램을 엽니다. 그런 다음 응용 프로그램의 **사용자 특성** 섹션에서 **기타 모든 사용자 특성 보기 및 편집**을 선택합니다.

![사용자 특성 섹션][1]

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.
* 응용 프로그램이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.
* Azure Active Directory에 저장된 사용자 이름(또는 사용자 계정 이름) 이외의 NameIdentifier 클레임을 요구하는 방식으로 응용 프로그램이 배포되었습니다.

원하는 기본 클레임 값을 편집할 수 있습니다. SAML 토큰 특성 테이블에서 클레임 행을 선택합니다. 그러면 **특성 편집** 섹션이 열리고 클레임 이름, 값 및 클레임과 연결된 네임스페이스를 편집할 수 있습니다.

![사용자 특성 편집][2]

**...** 아이콘을 클릭하면 열리는 상황에 맞는 메뉴를 사용하여 클레임(NameIdentifier 제외)을 제거할 수도 있습니다. **특성 추가** 단추를 사용하여 새 클레임을 추가할 수도 있습니다.

![사용자 특성 편집][3]

## <a name="editing-the-nameidentifier-claim"></a>NameIdentifier 클레임 편집
응용 프로그램이 다른 사용자 이름을 사용하여 배포된 경우 문제를 해결하려면 **사용자 특성** 섹션에서 **사용자 ID** 드롭다운을 클릭합니다. 이 작업을 수행하면 몇 가지 옵션이 있는 대화 상자가 제공됩니다.

![사용자 특성 편집][4]

드롭다운에서 **user.mail**을 선택하여 NameIdentifier 클레임을 디렉터리에서 사용자의 이메일 주소가 되도록 설정합니다. 또는 **user.onpremisessamaccountname**을 선택하여 온-프레미스 Azure AD에서 동기화된 사용자의 SAM 계정 이름으로 설정합니다.

특수한 **ExtractMailPrefix()** 함수를 사용하여 이메일 주소, SAM 계정 이름 또는 사용자 계정 이름에서 도메인 접미사를 제거할 수도 있습니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith").

![사용자 특성 편집][5]

이제 확인된 도메인에 사용자 ID 값을 결합하는 **join()** 함수가 추가되었습니다. **사용자 ID**에서 join() 함수를 선택하면 첫 번째로 이메일 주소 또는 사용자 계정 이름과 같은 사용자 ID를 선택한 다음 두 번째 드롭다운에서 확인된 도메인을 선택합니다. 확인된 도메인과 이메일 주소를 선택하면 Azure AD가 joe_smith@contoso.com에서 첫 번째 값 joe_smith를 추출해서 contoso.onmicrosoft.com에 추가합니다. 다음 예제를 참조하세요.

![사용자 특성 편집][6]

## <a name="adding-claims"></a>클레임 추가
클레임을 추가할 때 특성 이름(SAML 사양에 따라 URI 패턴을 엄격히 따를 필요는 없음)을 지정할 수 있습니다. 디렉터리에 저장된 사용자 특성 중 원하는 것으로 값을 설정합니다.

![사용자 특성 추가][7]

예를 들어 조직에서 사용자가 속한 부서를 클레임(에: 영업)으로 보내야 합니다. 응용 프로그램에 필요한 클레임 값을 입력한 다음 **user.department**를 값으로 선택합니다.

> [!NOTE]
> 지정된 사용자에 대해 선택한 특성에 대한 값이 저장되어 있지 않으면 해당 클레임이 토큰에서 발급되지 않습니다.

> [!TIP]
> **user.onpremisesecurityidentifier** 및 **user.onpremisesamaccountname**은 [Azure AD Connect 도구](../active-directory-aadconnect.md)를 사용하여 온-프레미스 Active Directory에서 사용자 데이터를 동기화할 때만 지원됩니다.

## <a name="restricted-claims"></a>제한된 클레임

SAML에는 몇 가지 제한된 클레임이 있습니다. 이러한 클레임을 추가하면 Azure AD는 이러한 클레임을 보내지 않습니다. 다음은 SAML 제한된 클레임 집합입니다.

    | 클레임 형식(URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)
* [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
