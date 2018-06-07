---
title: 'Azure Active Directory B2C: Facebook 구성 | Microsoft Docs'
description: 소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Facebook 계정으로 등록 및 로그인 제공
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 8/7/2017
ms.author: davidmu
ms.openlocfilehash: 899677500b0d33b5f98807a341449199b6b3dcac
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: 고객에게 Facebook 계정으로 등록 및 로그인 제공
## <a name="create-a-facebook-application"></a>Facebook 응용 프로그램 만들기
Azure Active Directory(Azure AD) B2C에서 Facebook을 ID 공급자로 사용하려면 Facebook 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이 작업을 수행하려면 Facebook 계정이 필요합니다. 계정이 없는 경우 [https://www.facebook.com/](https://www.facebook.com/)에서 가져올 수 있습니다.

1. [Facebook 개발자 웹 사이트](https://developers.facebook.com/) 로 이동한 다음 Facebook 계정 자격 증명으로 로그인합니다.
2. 아직 등록하지 않은 경우 Facebook 개발자로 등록해야 합니다. 등록하려면 페이지의 오른쪽 위에서 **등록**을 클릭하고 Facebook의 정책에 동의하고 등록 단계를 완료합니다.
3. **내 앱**을 클릭한 후 **새 앱 추가**를 클릭합니다. 
4. 형식에서 **표시 이름** 및 유효한 **연락처 전자 메일**을 제공합니다.
5. **앱 ID 만들기**를 클릭합니다. Facebook 플랫폼 정책을 수용하고 온라인 보안 검사를 완료해야 합니다.
6. 왼쪽 열에서 **설정**을 클릭한 다음 아직 선택하지 않은 경우 **기본**을 선택합니다.
7. **범주**를 선택합니다. 
8. **+플랫폼 추가**를 클릭하고 **웹 사이트**를 선택합니다.
   
    ![Facebook - 설정](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - 설정 - 웹 사이트](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. **사이트 URL** 필드에 `https://login.microsoftonline.com/`을 입력한 다음 페이지의 맨 아래에서 **변경 내용 저장**을 클릭합니다.
   
    ![Facebook - 사이트 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. **앱 ID**의 값을 복사합니다. **표시**를 클릭하고 **앱 비밀** 값을 복사합니다. 테넌트에서 Facebook을 ID 공급자로 구성하려면 둘 다 필요합니다. **앱 암호** 는 중요한 보안 자격 증명입니다.
   
    ![Facebook - 앱 ID 및 앱 암호](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. 왼쪽 탐색 영역에서 **+ 제품 추가**를 클릭하고 **Facebook 로그인**에 대한 **설정** 단추를 클릭합니다.
   
    ![Facebook - Facebook 로그인](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. **Facebook 로그인** 아래의 오른쪽 탐색 영역에서 **설정** 클릭

    ![Facebook - Facebook 로그인 설정](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. **클라이언트 OAuth 설정** 섹션의 **유효한 OAuth 리디렉션 URI** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다. 페이지 아래쪽에 있는 **변경 내용 저장** 을 클릭합니다.
    
    ![Facebook - OAuth 리디렉션 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Facebook 응용 프로그램을 Azure AD B2C에서 사용할 수 있도록 하려면 공개적으로 사용할 수 있도록 해야 합니다. 이렇게 하려면 왼쪽 탐색 창에서 **앱 검토**를 클릭하고 페이지의 위쪽에 있는 스위치를 **예**로 설정하고 **확인**을 클릭합니다.
    
    ![Facebook - 앱 공개](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Facebook을 ID 공급자로 구성
1. 다음 단계에 따라 [Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "Facebook"을 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Facebook**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Facebook 응용 프로그램의 앱 ID 및 앱 비밀을 **클라이언트 ID** 및 **클라이언트 비밀** 필드에 각각 입력합니다.
7. **확인**, **만들기**를 차례로 클릭하여 Facebook 구성을 저장합니다.

> [!NOTE]
> 테넌트에 **ID 공급자**를 추가해도 기존 정책이 수정되지 않습니다. 방금 만든 ID 공급자를 포함하여 정책을 업데이트해야 합니다.
>