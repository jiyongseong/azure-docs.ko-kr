---
title: 'Azure Active Directory B2C: QQ 구성 | Microsoft Docs'
description: 소비자에게 Azure Active Directory B2C를 사용하여 보안이 유지되는 응용 프로그램에서 QQ 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 3/26/2017
ms.author: davidmu
ms.openlocfilehash: 12daed5f90e2b073a7520c061f18761baaed5c03
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: 고객에게 QQ 계정으로 등록 및 로그인 제공

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.
> 

## <a name="create-a-qq-application"></a>QQ 응용 프로그램 만들기

Azure AD(Azure Active Directory) B2C에서 QQ를 ID 공급자로 사용하려면 QQ 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. 이렇게 하려면 QQ 계정이 필요합니다. 계정이 없는 경우 [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)에서 가져올 수 있습니다.

### <a name="register-for-the-qq-developer-program"></a>QQ 개발자 프로그램 등록

1. [QQ 개발자 포털](http://open.qq.com)로 이동하고 QQ 계정 자격 증명으로 로그인합니다.
2. 로그인한 후에 [http://open.qq.com/reg](http://open.qq.com/reg)로 이동하여 개발자인 사용자를 직접 등록합니다.
3. 메뉴에서 **个人**(개별 개발자)를 선택합니다.
4. 양식에 필요한 정보를 입력하고 **下一步**(다음 단계)를 클릭합니다.
5. 전자 메일 확인 프로세스를 완료합니다.

> [!NOTE]
> 개발자로 등록한 후 승인이 되기까지 몇 일 동안 기다려야 합니다. 

### <a name="register-a-qq-application"></a>QQ 응용 프로그램 등록

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html)로 이동합니다.
2. **应用管理**(앱 관리)를 클릭합니다.
3. **创建应用**(앱 만들기)를 클릭합니다.
4. 필요한 앱 정보를 입력합니다.
5. **创建应用**(앱 만들기)를 클릭합니다.
6. 필요한 정보를 입력합니다.
7. **授权回调域**(콜백 URL) 필드에 `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`를 입력합니다. 예를 들어 `tenant_name`이 contoso.onmicrosoft.com인 경우 URL을 `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`가 되도록 설정합니다.
8. **创建应用**(앱 만들기)를 클릭합니다.
9. 확인 페이지에서 **应用管理**(앱 관리)를 클릭하여 앱 관리 페이지로 돌아갑니다.
10. 방금 만든 앱 옆에 있는 **查看**(보기)를 클릭합니다.
11. **修改**(편집)을 클릭합니다.
12. 페이지 맨 위에서 **앱 ID** 및 **앱 키**를 복사합니다.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>테넌트에서 QQ를 ID 공급자로 구성
1. 다음 단계에 따라 [Azure Portal의 B2C 기능 블레이드로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) .
2. B2C 기능 블레이드에서 **ID 공급자**를 클릭합니다.
3. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
4. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어 "QQ"를 입력합니다.
5. **ID 공급자 형식**을 클릭하고 **QQ**를 선택한 다음 **확인**을 클릭합니다.
6. **이 ID 공급자 설정**을 클릭합니다.
7. **클라이언트 ID**로 앞에서 복사한 **앱 키**를 입력합니다.
8. **클라이언트 암호**로 앞에서 복사한 **앱 암호**를 입력합니다.
9. **확인**을 클릭한 다음 **만들기**를 클릭하여 QQ 구성을 저장합니다.

