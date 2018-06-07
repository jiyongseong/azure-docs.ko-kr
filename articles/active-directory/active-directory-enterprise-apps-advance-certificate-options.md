---
title: Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션 | Microsoft Docs
description: Azure Active Directory에서 사전 통합된 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션을 사용하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: cec377ad92e1491413f9d3ebc953abb154b83f01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory의 갤러리 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션
현재 Azure AD(Azure Active Directory)는 Azure Active Directory 앱 갤러리에서 수천 개의 사전 통합 응용 프로그램을 지원합니다. 여기에는 SAML 2.0 프로토콜을 통해 Single Sign-On을 지원하는 500여 개의 응용 프로그램이 포함됩니다. 사용자가 SAML을 사용하여 Azure AD를 통해 응용 프로그램을 인증하면 Azure AD는 응용 프로그램에 토큰을 보냅니다(HTTP POST를 통해). 그런 다음 응용 프로그램이 토큰을 유효성 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰은 Azure AD 및 특정 표준 알고리즘에서 생성된 고유한 인증서로 서명됩니다.

Azure AD는 갤러리 응용 프로그램의 기본 설정 중 일부를 사용합니다. 응용 프로그램 요구 사항에 따라 기본값이 설정됩니다.

Azure AD는 고급 인증서 서명 설정을 지원합니다. 이 옵션을 선택하려면 먼저 **SAML 고급 인증서 서명 설정 표시** 확인란을 선택합니다.

![고급 인증서 서명 설정 표시](./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png)

이 확인란을 선택하면 인증서 서명 옵션과 인증서 서명 알고리즘을 설정할 수 있습니다.

## <a name="certificate-signing-options"></a>인증서 서명 옵션

Azure AD는 다음과 같이 세 가지 인증서 서명 옵션을 지원합니다.

* **SAML 어설션 서명**. 대부분의 갤러리 응용 프로그램에 이 기본 옵션이 설정됩니다. 이 옵션을 선택하면 IdP인 Azure AD에서 응용 프로그램의 X509 인증서로 SAML 어설션 및 인증서에 서명합니다. 또한 **서명 알고리즘** 드롭다운 목록에서 선택되는 서명 알고리즘을 사용합니다.

* **SAML 응답 서명**. 이 옵션을 선택하면 IdP인 Azure AD에서 응용 프로그램의 X509 인증서로 SAML 응답에 서명합니다. 또한 **서명 알고리즘** 드롭다운 목록에서 선택되는 서명 알고리즘을 사용합니다.

* **SAML 응답 및 어설션 서명**. 이 옵션을 선택하면 IdP인 Azure AD에서 응용 프로그램의 X509 인증서로 전체 SAML 토큰에 서명합니다. 또한 **서명 알고리즘** 드롭다운 목록에서 선택되는 서명 알고리즘을 사용합니다.

    ![인증서 서명 옵션](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>인증서 서명 알고리즘

Azure AD는 SAML 응답에 서명하기 위해 두 가지 서명 알고리즘을 지원합니다.

* **SHA-256**. Azure AD가 SAML 응답에 서명하기 위해 이 기본 알고리즘을 사용합니다. 최신 알고리즘이며, SHA-1보다 더 안전한 것으로 간주됩니다. 대부분의 응용 프로그램에서 SHA-256 알고리즘을 지원합니다. 응용 프로그램이 서명 알고리즘으로 SHA-1만 지원하는 경우 이를 변경할 수 있습니다. 그렇지 않으면 SAML 응답에 서명하는 데 SHA-256 알고리즘을 사용하는 것이 좋습니다.

    ![SHA-256 인증서 서명 알고리즘](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png)

* **SHA-1**. 이전 알고리즘이며, SHA-256보다 덜 안전하다고 여겨집니다. 응용 프로그램에서 이 서명 알고리즘만 지원하는 경우 **서명 알고리즘** 드롭다운 목록에서 이 옵션을 선택할 수 있습니다. 그러면 Azure AD에서 SHA-1 알고리즘으로 SAML 응답에 서명합니다.

    ![SHA-1 인증서 서명 알고리즘](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory 앱 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [SAML 기반 Single Sign-On 문제 해결](develop/active-directory-saml-debugging.md)


