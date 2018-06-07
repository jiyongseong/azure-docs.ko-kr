---
title: 'Azure Active Directory B2C: Google+ 구성 | Microsoft Docs'
description: 소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Google+ 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: db3044754e0c1316b49eda0600d41e3009c1ba16
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: 고객에게 Google+ 계정으로 등록 및 로그인 제공
## <a name="create-a-google-application"></a>Google+ 응용 프로그램 만들기
Azure AD(Azure Active Directory) B2C에서 Google+를 ID 공급자로 사용하려면 Google+ 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 Google+ 계정이 필요합니다. 계정이 없는 경우 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)에서 가져올 수 있습니다.

1. [Google 개발자 콘솔](https://console.developers.google.com/) 로 이동하고 Google + 계정 자격 증명으로 로그인합니다.
2. **프로젝트 만들기**를 클릭하고 **프로젝트 이름**을 입력한 다음 **만들기**를 클릭합니다.
   
    ![Google+ - 시작](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+ - 새 프로젝트](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. **API 관리자**를 클릭한 다음 왼쪽 탐색에서 **자격 증명**을 클릭합니다.
4. **OAuth 동의 화면**을 위쪽 탭에서 클릭합니다.
   
    ![Google+ - 자격 증명](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. 유효한 **메일 주소**를 선택하거나 지정하고 **제품 이름**을 제공한 후 **저장**을 클릭합니다.
   
    ![Google+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. **새 자격 증명**을 클릭한 다음 **OAuth 클라이언트 ID**를 선택합니다.
   
    ![Google+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. **응용 프로그램 형식**에서 **웹 응용 프로그램**을 선택합니다.
   
    ![Google+ - OAuth 동의 화면](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. 응용 프로그램에 대한 **이름**을 제공하고 **권한이 부여된 JavaScript 원본** 필드에 `https://login.microsoftonline.com`을 입력하고 **권한이 부여된 리디렉션 URI** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다. **{tenant}** 값은 대/소문자를 구분합니다. **만들기**를 클릭합니다.
   
    ![Google+ - 클라이언트 ID 만들기](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에서 Google+를 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.
   
    ![Google+ - 클라이언트 암호](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Google+를 ID 공급자로 구성
1. 다음 단계에 따라 [Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "G+"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Google**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Google+ 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 입력합니다.
7. **확인**을 클릭한 다음 **만들기**를 클릭하여 Google+ 구성을 저장합니다.

