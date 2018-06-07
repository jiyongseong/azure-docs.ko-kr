---
title: 응용 프로그램에 대한 Azure AD SSO 구성 | Microsoft Docs
description: 셀프 서비스가 SAML 및 암호 기반 SSO로 Azure Active Directory에 앱을 연결하는 방법을 알아봅니다.
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: 0d42eb0c-6d3f-4557-9030-e88e86709a19
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/20/2018
ms.author: asmalser
ms.reviewer: luleon
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5316cca9d3c944735355d7912a0f1e044c585001
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34339811"
---
# <a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성
이 문서에서는 관리자가 Azure Active Directory 앱 갤러리에 없는 응용 프로그램에 Single Sign-On을 *코드 작성 없이*구성할 수 있도록 설정하는 기능에 대해 설명합니다. 이 기능은 2015년 11월 18일 기술 미리 보기에서 발표되었으며 [Azure Active Directory Premium](active-directory-whatis.md)에 포함되어 있습니다. 이 문서 대신 코드를 통해 사용자 지정 앱을 Azure AD와 통합하는 방법에 대한 개발자 참고 자료를 찾는다면 [Azure AD의 인증 시나리오](active-directory-authentication-scenarios.md)를 참조하세요.

Azure Active Directory 응용 프로그램 갤러리는 [이 문서](manage-apps/what-is-single-sign-on.md)에 설명한 대로 Azure Active Directory와의 Single Sign-On 형식을 지원하는 것으로 알려진 응용 프로그램 목록을 제공합니다. (조직의 IT 전문가 또는 시스템 통합자인 경우)연결할 응용 프로그램을 찾으면 Azure Portal에 나와 있는 단계별 지침에 따라 Single Sign-On을 사용하도록 설정할 수 있습니다.

[Azure Active Directory Premium](active-directory-whatis.md) 라이선스가 있는 고객에게는 다음과 같은 추가 기능이 제공됩니다.

* SAML 2.0 ID 공급자를 지원하는 응용 프로그램의 셀프 서비스 통합(SP에서 시작 또는 IdP에서 시작)
* [암호 기반 SSO](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on)
* 사용자 프로비전에 SCIM 프로토콜을 사용하는 응용 프로그램의 셀프 서비스 연결([여기에 설명됨](active-directory-scim-provisioning.md))
* [Office 365 앱 시작 관리자](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)에서 응용 프로그램에 대한 링크를 추가하는 기능

사용하지만 아직 Azure AD 응용 프로그램 갤러리에 등록되지 않은 SaaS 응용 프로그램 뿐만 아니라 조직이 클라우드 또는 온-프레미스에서 제어하는 서버에 배포된 타사 웹 응용 프로그램을 포함할 수 있습니다.

*앱 통합 템플릿*이라고도 하는 이러한 기능은 SAML, SCIM 또는 폼 기반 인증을 지원하는 앱에 대한 표준 기반 연결점을 제공하며, 다양한 응용 프로그램과의 호환성을 위해 유연한 옵션과 설정을 포함하고 있습니다. 

## <a name="adding-an-unlisted-application"></a>목록에 없는 응용 프로그램 추가
앱 통합 템플릿을 사용하여 응용 프로그램을 연결하려면 Azure Active Directory 관리자 계정을 사용하여 Azure Portal에 로그인하고 **Active Directory > 엔터프라이즈 응용 프로그램 > 새 응용 프로그램 > 비갤러리 응용 프로그램** 섹션으로 이동하며 **추가** 및 **갤러리에서 응용 프로그램 추가**를 차례로 선택합니다.

  ![][1]

앱 갤러리에서 원하는 앱을 찾을 수 없는 경우 검색 결과에 표시된 **비갤러리 응용 프로그램** 타일을 선택하여 목록에 없는 앱을 추가할 수 있습니다. 응용 프로그램에 이름을 입력한 후에 Single Sign On 옵션 및 동작을 구성할 수 있습니다. 

**빠른 팁**: 모범 사례로 응용 프로그램 갤러리에 응용 프로그램이 이미 존재하는지 확인하려면 검색 기능을 사용합니다. 앱이 검색되었고 설명에 "Single Sign-On"이 포함되어 있는 경우에는 페더레이션된 Single Sign-On이 응용 프로그램에 이미 지원됩니다.

  ![][2]

이런 방식으로 응용 프로그램을 추가하면 사전 통합된 응용 프로그램에 제공되는 환경과 매우 비슷한 환경을 구성할 수 있습니다. 시작하려면 **Single Sign-On 구성**을 선택합니다. 다음 화면에는 다음 섹션에 설명되어 있는 Single Sign-On 구성을 위한 다음과 같은 세 가지 옵션이 표시됩니다.

  ![][3]

## <a name="saml-based-sign-on"></a>SAML 기반 로그온
이 옵션을 선택하여 응용 프로그램에 대한 SAML 기반 인증을 구성합니다. 응용 프로그램이 SAML 2.0를 지원해야 하고 계속하기 전에 응용 프로그램의 SAML 기능을 사용하는 방법에 대한 정보를 수집해야 합니다. **다음**을 선택한 후에 응용 프로그램에 대한 SAML 끝점에 해당하는 세 개의 URL을 입력하라는 메시지가 표시됩니다.

  ![][4]

다음과 같습니다.

* **로그온 URL(SP에서 시작된 경우에만 해당)** – 사용자가 이 응용 프로그램에 로그인하는 위치입니다. 서비스 공급자 시작 Single Sign-On을 수행하도록 응용 프로그램이 구성된 경우 사용자가 이 URL로 이동하면 서비스 공급자는 사용자를 인증하고 로그온하기 위해 Azure AD로 필요한 리디렉션을 수행합니다. 이 필드가 채워지면 Azure AD는 이 URL을 사용하여 Office 365 및 Azure AD 액세스 패널에서 응용 프로그램을 시작합니다. 이 필드가 생략되면 해당 앱이 Office 365, Azure AD 액세스 패널 또는 Azure AD Single Sign-On URL(대시보드 탭에서 복사 가능)에서 시작할 경우 Azure AD는 ID 공급자에서 시작된 로그인을 대신 수행합니다.
* **발급자 URL** - 발급자 URL은 Single Sign-On이 구성되는 응용 프로그램을 고유하게 식별해야 합니다. 이는 Azure AD가 SAML 토큰의 **대상** 매개 변수로서 응용 프로그램에 다시 전송하는 값이며, 응용 프로그램의 유효성을 검사하게 됩니다. 또한 이 값은 응용 프로그램에서 제공하는 모든 SAML 메타데이터 내에서 **엔터티 ID** 로 표시됩니다. 엔터티 ID 또는 대상 값에 대한 자세한 내용은 응용 프로그램의 SAML 설명서를 확인합니다. 다음은 응용 프로그램에 반환되는 SAML 토큰에서 대상 URL이 표시되는 방법의 예입니다.
  
  ```
  <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
    ```

* **회신 URL** - 회신 URL은 응용 프로그램이 SAML 토큰을 수신해야 하는 위치입니다. 이 URL은 **ACS(Assertion Consumer Service) URL**이라고도 합니다. SAML 토큰 회신 URL 또는 ACS URL에 대한 자세한 내용은 응용 프로그램의 SAML 설명서를 확인합니다.
  이러한 내용을 입력한 후에 **다음** 을 클릭하여 다음 화면으로 진행합니다. 이 화면은 응용 프로그램쪽에서 구성되어야 하는 사항에 대한 정보를 제공하여 Azure AD에서 SAML 토큰을 수락하도록 설정합니다.

  * **Configure Litware**를 클릭합니다.
  
    ![][5]

어떤 값이 필요한지는 응용 프로그램에 따라 달라지므로 세부 정보에 대한 응용 프로그램의 SAML 설명서를 확인합니다. **로그온** 및 **로그아웃** 서비스 URL은 모두 동일한 끝점으로 해결하는데, 이는 Azure AD의 인스턴스에 대한 SAML 요청 처리 끝점입니다. 발급자 URL은 응용 프로그램에 발급된 SAML 토큰 내에서 "발급자"로 나타나는 값입니다. 

응용 프로그램을 구성한 후에 **다음** 단추 및 **완료**를 차례로 클릭하여 대화 상자를 닫습니다. 

## <a name="assigning-users-and-groups-to-your-saml-application"></a>SAML 응용 프로그램에 사용자 및 그룹 할당
응용 프로그램이 Azure AD를 SAML 기반 ID 공급자로 사용하도록 구성되면 테스트할 준비가 다 됩니다. 보안 컨트롤인 Azure AD는 Azure AD를 사용하여 액세스를 허용한 경우가 아니면 응용 프로그램에 로그인하도록 허용하는 토큰을 발급하지 않습니다. 사용자는 직접적으로 또는 멤버인 그룹을 통해 액세스를 부여받을 수 있습니다. 

응용 프로그램에 사용자 또는 그룹을 할당하려면 **사용자 할당** 단추를 클릭합니다. 할당하려는 사용자 또는 그룹을 선택한 다음 **할당** 단추를 선택합니다.

  ![][6]

사용자를 할당하면 Azure AD가 사용자의 액세스 패널에 표시할 이 응용 프로그램의 타일을 발생시킬 뿐만 아니라 사용자에 토큰을 발급하도록 합니다. 또한 사용자가 Office 365를 사용하는 경우 응용 프로그램 타일은 Office 365 응용 프로그램 시작 관리자에 표시됩니다. 

응용 프로그램에 **구성** 탭의 **로고 업로드** 단추를 사용하여 응용 프로그램에 대한 타일 로고를 업로드할 수 있습니다. 

### <a name="customizing-the-claims-issued-in-the-saml-token"></a>SAML 토큰에 발급된 클레임 사용자 지정
사용자가 응용 프로그램에 인증할 때 Azure AD는 고유하게 식별하는 사용자에 대한 정보(또는 클레임)를 포함하는 앱에 SAML 토큰을 발급합니다. 기본적으로 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함합니다. 

**특성** 탭에서 응용 프로그램의 SAML 토큰에 전송된 클레임을 보거나 편집할 수 있습니다.

  ![][7]

SAML 토큰에서 발급된 클레임을 편집해야 할 수도 있는 두 가지 가능한 이유는 다음과 같습니다. •응용 프로그램이 다른 클레임 URI 또는 클레임 값의 집합이 필요하도록 작성되었습니다. •NameIdentifier 클레임이 Azure Active Directory에 저장된 사용자 이름(즉, 사용자 계정 이름)이 아닌 다른 항목이어야 하는 방식으로 응용 프로그램이 배포되었습니다. 

이러한 시나리오에 대한 클레임을 추가하고 편집하는 방법에 대한 정보는 [클레임 사용자 지정에 대한 문서](active-directory-saml-claims-customization.md)를 확인합니다. 

### <a name="testing-the-saml-application"></a>SAML 응용 프로그램 테스트
SAML URL 및 인증서가 Azure AD 및 응용 프로그램에서 구성되면 사용자 또는 그룹은 Azure에서 응용 프로그램에 할당되고 클레임은 필요한 경우 검토 및 편집되며 사용자는 응용 프로그램에 로그인하도록 준비됩니다. 

테스트하려면 응용 프로그램에 할당된 사용자 계정을 사용하여 https://myapps.microsoft.com에서 Azure AD 액세스 패널에 로그인한 다음, 응용 프로그램에 대한 타일을 클릭하여 Single Sign-On 프로세스를 시작합니다. 또는 여기에서 응용 프로그램 및 로그인에 대한 로그인 URL을 직접 찾아볼 수 있습니다. 

디버깅 팁은 [SAML 기반 Single Sign-On 응용 프로그램을 디버깅하는 방법에 대 한 문서](active-directory-saml-debugging.md) 

## <a name="password-single-sign-on"></a>암호 Single Sign-On
이 옵션을 선택하여 HTML 로그인 페이지를 포함하는 웹 응용 프로그램에 [암호 기반 Single Sign-On](manage-apps/what-is-single-sign-on.md) 을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 응용 프로그램에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에 유용합니다. 

**다음**을 선택한 후에 응용 프로그램의 웹 기반 로그인 페이지의 URL을 입력하라는 메시지가 표시됩니다. 사용자 이름 및 암호 입력 필드를 포함하는 페이지여야 합니다. 입력하면 Azure AD는 사용자 이름 및 암호 입력에 대한 로그인 페이지를 구문 분석하는 프로세스를 시작합니다. 프로세스가 실패한 경우 필드를 수동으로 캡처할 수 있도록 브라우저 확장을 설치하는 다른 과정을 안내합니다.(Internet Explorer, Chrome 또는 Firefox 필요)

로그인 페이지가 캡처되면 사용자 및 그룹은 할당될 수 있고 일반 [암호 SSO 앱](manage-apps/what-is-single-sign-on.md)과 마찬가지로 자격 증명 정책을 설정할 수 있습니다 .

참고: 응용 프로그램에 **구성** 탭의 **로고 업로드** 단추를 사용하여 응용 프로그램에 대한 타일 로고를 업로드할 수 있습니다. 

## <a name="existing-single-sign-on"></a>기존 Single Sign-On
이 옵션을 선택하여 조직의 Azure AD 액세스 패널 또는 Office 365 포털에 응용 프로그램에 대한 링크를 추가합니다. 인증에 대한 Azure AD 대신 현재 Azure Active Directory Federation Services(또는 다른 페더레이션 서비스)를 사용하는 사용자 지정 웹앱에 대한 링크를 추가하는 데 사용할 수 있습니다. 혹은 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 딥 링크를 추가할 수 있습니다. 

**다음**을 선택한 후에 링크할 응용 프로그램의 URL을 입력하라는 메시지가 표시됩니다. 완료되면 사용자 및 그룹은 응용 프로그램에 할당될 수 있으며 이는 응용 프로그램이 해당 사용자에 대한 [Office 365 앱 시작 관리자](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users)에 표시되도록 합니다.

참고: 응용 프로그램에 **구성** 탭의 **로고 업로드** 단추를 사용하여 응용 프로그램에 대한 타일 로고를 업로드할 수 있습니다.

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [사전 통합된 앱에 대해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법](active-directory-saml-claims-customization.md)
* [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png