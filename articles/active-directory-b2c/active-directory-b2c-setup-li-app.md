---
title: 'Azure Active Directory B2C: LinkedIn 구성 | Microsoft Docs'
description: 소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 LinkedIn 계정으로 등록 및 로그인 제공
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
ms.openlocfilehash: 91970b3ec41c645cd1cd3e203cc96ad655dafd7e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C: 고객에게 LinkedIn 계정으로 등록 및 로그인 제공
## <a name="create-a-linkedin-application"></a>LinkedIn 응용 프로그램 만들기
Azure Active Directory(Azure AD) B2C에서 LinkedIn을 ID 공급자로 사용하려면 LinkedIn 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이 작업을 수행하려면 LinkedIn 계정이 필요합니다. 계정이 없는 경우 [https://www.linkedin.com/](https://www.linkedin.com/)에서 가져올 수 있습니다.

1. [LinkedIn 개발자 웹 사이트](https://www.developer.linkedin.com/) 로 이동한 다음 LinkedIn 계정 자격 증명으로 로그인합니다.
2. 맨 위의 메뉴 모음에서 **내 앱**, **응용 프로그램 만들기**를 차례로 클릭합니다.
   
    ![LinkedIn - 새 앱](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)
3. **새 응용 프로그램 만들기** 양식에서 관련 정보(**회사 이름**, **이름**, **설명**, **응용 프로그램 로고 URL**, **응용 프로그램 사용**, **웹 사이트 URL**, **비즈니스 전자 메일** 및 **회사 전화**)를 입력합니다.
4. **LinkedIn API 사용 조건**에 동의하고 **제출**을 클릭합니다.
   
    ![LinkedIn - 앱 등록](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)
5. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. (**인증 키** 아래에서 찾을 수 있습니다.) 테넌트에서 LinkedIn을 ID 공급자로 구성하려면 둘 다 필요합니다.
   
   > [!NOTE]
   > **클라이언트 암호** 는 중요한 보안 자격 증명입니다.
   > 
   > 
6. **권한이 부여된 리디렉션 URL** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`를 입력합니다(**OAuth 2.0** 아래). **{tenant}** 를 사용자의 테넌트 이름(예: contoso.onmicrosoft.com)으로 바꿉니다. **추가**를 클릭한 후 **업데이트**를 클릭합니다. **{tenant}** 값은 대/소문자를 구분합니다.
   
    ![LinkedIn - 앱 설정](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>테넌트에서 LinkedIn을 ID 공급자로 구성
1. 다음 단계에 따라 [Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "LI"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **LinkedIn**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 LinkedIn 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 입력합니다.
7. **확인**, **만들기**를 차례로 클릭하여 LinkedIn 구성을 저장합니다.

