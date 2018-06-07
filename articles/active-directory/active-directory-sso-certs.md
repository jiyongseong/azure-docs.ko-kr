---
title: Azure AD에서 페더레이션 인증서 관리 | Microsoft Docs
description: 페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.openlocfilehash: 9924d06f149b0d234cd8f5546cab8abbab740aac
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리
이 문서에서는 Azure AD(Azure Active Directory)에서 SaaS 응용 프로그램에 페더레이션된 SSO(Single Sign-On)를 설정하기 위해 만드는 인증서와 관련된 일반적인 질문과 정보를 다룹니다. Azure AD 앱 갤러리에서 또는 비갤러리 응용 프로그램 템플릿을 사용하여 응용 프로그램을 추가하고, 페더레이션된 SSO 옵션을 사용하여 응용 프로그램을 구성합니다.

이 문서는 아래 예와 같이 SAML 페더레이션을 통해 Azure AD SSO를 사용하도록 구성된 앱에만 관련되어 있습니다.

![Azure AD Single Sign-On](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>갤러리 및 비갤러리 응용 프로그램에 대해 자동 생성된 인증서
갤러리에서 새 응용 프로그램을 추가하고 SAML 기반 로그온을 구성하면 Azure AD에서 해당 응용 프로그램에 대해 3년 동안 유효한 인증서를 생성합니다. 이 인증서는 **SAML 서명 인증서** 섹션에서 다운로드할 수 있습니다. 갤러리 응용 프로그램의 경우 이 섹션에서는 응용 프로그램의 요구 사항에 따라 인증서 또는 메타데이터를 다운로드하는 옵션을 표시할 수 있습니다.

![Azure AD Single Sign-On](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>페더레이션 인증서의 만료 날짜 사용자 지정 및 새 인증서 롤오버
기본적으로 인증서는 3년 후에 만료되도록 설정됩니다. 다음 단계를 완료하여 인증서의 다른 만료 날짜를 선택할 수 있습니다.
스크린샷에서는 예제를 위해 Salesforce를 사용하지만, 이러한 단계는 페더레이션된 모든 SaaS 앱에 적용할 수 있습니다.

1. [Azure Portal](https://aad.portal.azure.com)의 왼쪽 창에서 **엔터프라이즈 응용 프로그램**을 클릭한 다음 **개요** 페이지에서 **새 응용 프로그램**을 클릭합니다.

   ![SSO 구성 마법사 열기](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. 갤러리 응용 프로그램을 검색한 다음 추가할 응용 프로그램을 선택합니다. 필요한 응용 프로그램을 찾을 수 없으면 **비갤러리 응용 프로그램** 옵션을 사용하여 응용 프로그램을 추가합니다. 이 기능은 Azure AD Premium(P1 및 P2) SKU에서만 사용할 수 있습니다.

    ![Azure AD Single Sign-On](./media/active-directory-sso-certs/add_gallery_application.png)

3. 왼쪽 창에서 **Single Sign-On** 링크를 클릭하고 **Single Sign-On 모드**를 **SAML 기반 로그인**으로 변경합니다. 이 단계에서는 응용 프로그램에 대해 3년 유효 인증서를 생성합니다.

4. 새 인증서를 만들려면 **SAML 서명 인증서** 섹션에서 **새 인증서 만들기** 링크를 클릭합니다.

    ![새 인증서 생성](./media/active-directory-sso-certs/create_new_certficate.png)

5. **새 인증서 만들기** 링크는 달력 컨트롤을 엽니다. 날짜와 시간은 현재 날짜로부터 최대 3년까지 설정할 수 있습니다. 선택한 날짜와 시간은 새 인증서의 새로운 만료 날짜와 시간입니다. **저장**을 클릭합니다.

    ![다운로드한 다음 인증서 업로드](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. 이제 새 인증서를 다운로드할 수 있습니다. **인증서** 링크를 클릭하여 다운로드합니다. 이 시점에서 인증서는 활성 상태가 아닙니다. 이 인증서를 롤오버하려면 **Make new certificate active**(새 인증서 활성화) 확인란을 선택하고 **저장**을 클릭합니다. 이 시점부터 Azure AD에서 새 인증서를 사용하여 응답에 서명하기 시작합니다.

7.  특정 SaaS 응용 프로그램에 인증서를 업로드하는 방법을 알아보려면 **응용 프로그램 구성 자습서 보기** 링크를 클릭합니다.

## <a name="renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서 갱신
다음 갱신 단계를 수행하면 사용자에게 심각한 가동 중지가 발생하지 않습니다. 이 섹션의 스크린샷에서는 Salesforce를 예로 사용하지만, 이러한 단계는 페더레이션된 모든 SaaS 앱에 적용할 수 있습니다.

1. **Azure Active Directory** 응용 프로그램 **Single Sign-On** 페이지에서 응용 프로그램에 대한 새 인증서를 생성합니다. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기** 링크를 클릭하여 이 작업을 수행할 수 있습니다.

    ![새 인증서 생성](./media/active-directory-sso-certs/create_new_certficate.png)

2. 새 인증서에 대해 원하는 만료 날짜와 시간을 선택하고 **저장**을 클릭합니다.

3. **SAML 서명 인증서** 옵션에서 인증서를 다운로드합니다. 새 인증서를 SaaS 응용 프로그램의 Single Sign-On 구성 화면에 업로드합니다. 특정 SaaS 응용 프로그램에 대해 이 작업을 수행하는 방법을 알아보려면 **응용 프로그램 구성 자습서 보기** 링크를 클릭합니다.
   
4. Azure AD에서 새 인증서를 활성화하려면 **Make new certificate active**(새 인증서 활성화) 확인란을 선택하고 페이지 위쪽에서 **저장** 단추를 클릭합니다. 그러면 Azure AD 쪽에서 새 인증서를 롤오버합니다. 인증서의 상태가 **신규**에서 **활성**으로 변경됩니다. 이 시점부터 Azure AD에서 새 인증서를 사용하여 응답에 서명하기 시작합니다. 
   
    ![새 인증서 생성](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)
* [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)
