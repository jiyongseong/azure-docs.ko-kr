---
title: 'Azure Active Directory B2C: Amazon 구성 | Microsoft Docs'
description: 소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 Amazon 계정으로 등록 및 로그인을 제공합니다.
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
ms.openlocfilehash: a2989baa61e7b69534fe5703b2501d62a4f8aa94
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: 고객에게 Amazon 계정으로 등록 및 로그인 제공
## <a name="create-an-amazon-application"></a>Amazon 응용 프로그램 만들기
Azure Active Directory(Azure AD) B2C에서 Amazon을 ID 공급자로 사용하려면 먼저 Amazon 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이 작업을 수행하려면 Amazon 계정이 필요합니다. 계정이 없는 경우 [http://www.amazon.com/](http://www.amazon.com/)에서 가져올 수 있습니다.

1. [Amazon 개발자 센터](https://login.amazon.com/) 로 이동하여 Amazon 계정 자격 증명으로 로그인합니다.
2. 이미 수행한 경우 **등록**을 클릭하고 개발자 등록 단계를 수행하며 정책에 동의합니다.
3. **새 응용 프로그램 등록**을 클릭합니다.
   
    ![Amazon 웹 사이트에서 새 응용 프로그램 등록](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. 응용 프로그램 정보(**이름**, **설명** 및 **개인 정보 알림 URL**)를 제공하고 **저장**을 클릭합니다.
   
    ![Amazon에 새 응용 프로그램을 등록하기 위한 응용 프로그램 정보 제공](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. **웹 설정** 섹션에서 **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. (이 정보를 보려면 **비밀 표시** 단추를 클릭해야 합니다.) 테넌트에서 Amazon을 ID 공급자로 구성하려면 둘 다 필요합니다. 섹션 아래쪽에서 **편집**을 클릭합니다. **클라이언트 암호** 는 중요한 보안 자격 증명입니다.
   
    ![Amazon에 새 응용 프로그램에 대한 클라이언트 ID 및 클라이언트 암호 제공](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. **허용되는 JavaScript 원본** 필드에 `https://login.microsoftonline.com`을 입력하고 **허용되는 반환 URL** 필드에 `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`을 입력합니다. **{tenant}** 를 사용자의 테넌트 이름(예: contoso.onmicrosoft.com)으로 바꿉니다. **저장**을 클릭합니다. **{tenant}** 값은 대/소문자를 구분합니다.
   
    ![Amazon에 새 응용 프로그램에 대한 JavaScript 원본 및 반환 URL 제공](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Amazon을 ID 공급자로 구성
1. 다음 단계에 따라 [Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "Amzn"을 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **Amazon**을 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭하고 이전에 만든 Amazon 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 입력합니다.
7. **확인**, **만들기**를 차례로 클릭하여 Amazon 구성을 저장합니다.

