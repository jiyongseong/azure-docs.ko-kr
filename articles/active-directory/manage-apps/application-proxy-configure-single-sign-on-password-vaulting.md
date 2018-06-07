---
title: Azure AD 응용 프로그램 프록시를 사용하여 앱에 SSO(Single sign-on) | Microsoft Docs
description: Azure Portal에서 Azure AD 응용 프로그램을 사용하여 게시된 온-프레미스 응용 프로그램에 대한 SSO(Single sign-on)를 켭니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: fef163e258f797fc57d391a353b1acc148a3ef49
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>응용 프로그램 프록시를 사용하여 Single Sign-On에 대한 암호 자격 증명 모음 설정

Azure Active Directory 응용 프로그램 프록시는 원격 직원들이 안전하게 액세스할 수 있도록 온-프레미스 응용 프로그램을 게시하여 생산성을 개선하는 데 도움을 줍니다. Azure Portal에서는 이러한 앱에 대한 SSO(Single Sign-On)을 설정할 수도 있습니다. 사용자는 Azure AD만으로 인증해야 하며, 다시 로그인할 필요 없이 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다.

응용프로그램 프록시에서는 여러 [Single Sign-On 모드](application-proxy-single-sign-on.md)를 지원합니다. 암호 기반 로그인은 인증에 사용자 이름/암호 조합을 사용하는 응용 프로그램을 위한 것입니다. 응용 프로그램에 대한 암호 기반 로그인을 구성하는 경우 사용자는 온-프레미스 응용 프로그램에 한 번 로그인해야 합니다. 그런 다음 Azure Active Directory에서 로그인 정보를 저장하고 사용자가 원격으로 액세스할 때 응용 프로그램에 자동으로 제공합니다. 

이미 응용 프로그램 프록시를 사용하여 앱을 게시하고 테스트했을 것입니다. 그렇지 않은 경우 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](application-proxy-publish-azure-portal.md)의 단계를 수행한 다음 여기로 다시 돌아옵니다. 

## <a name="set-up-password-vaulting-for-your-application"></a>응용 프로그램에 대한 암호 보관 설정

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.
3. 목록에서 SSO로 설정할 앱을 선택합니다.  
4. **Single Sign-On**을 선택합니다.

   ![Single Sign-On 선택](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. SSO 모드에 대해 **암호 기반 로그온**을 선택합니다.
6. 로그온 URL에 대해 사용자가 회사 네트워크 외부에서 앱에 로그인하기 위해 사용자 이름과 암호를 입력하는 페이지에 대한 URL을 입력합니다. 이 URL은 응용 프로그램 프록시를 통해 앱을 게시할 때 만든 외부 URL일 수 있습니다. 

   ![암호 기반 로그온 선택 및 URL 입력](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. **저장**을 선택합니다.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>앱 테스트

응용 프로그램에 원격으로 액세스하도록 구성된 외부 URL로 이동합니다. 해당 앱에 대한 자격 증명(또는 액세스 권한으로 설정한 테스트 계정에 대한 자격 증명)으로 로그인합니다. 로그인한 후에는 자격 증명을 다시 입력하지 않아도 앱에서 나갔다가 다시 돌아올 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 프록시를 사용하는 Single Sign-On](application-proxy-single-sign-on.md)을 구현하는 다른 방법에 대해 읽기
- [Azure AD 응용 프로그램 프록시를 사용하여 앱에 원격으로 액세스하는 경우 보안 고려 사항](application-proxy-security.md)을 알아봅니다