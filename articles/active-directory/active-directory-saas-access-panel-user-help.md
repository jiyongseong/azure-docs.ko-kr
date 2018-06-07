---
title: Azure Active Directory에서 My Apps 포털 액세스 및 사용에 대한 지원 | Microsoft Docs
description: 액세스 패널에서 로그인하고 일반적인 작업을 수행할 수 있도록 도움을 받습니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: f0604007255d829cab0502e93895ca541da3b93a
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34260512"
---
# <a name="troubleshoot-issues-with-accessing-and-using-the-my-apps-portal"></a>My Apps 포털 액세스 및 사용과 관련된 문제 해결

My Apps 포털에 로그인하거나 사용하는 데 문제가 있으면 기술 지원팀 또는 관리자에게 문의하기 전에 다음 문제 해결 팁을 사용해 보세요.

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>MyApps portal에 로그인하는 데 문제가 발생했습니다.

여기서 설명하는 일반 도움말을 참조해 보세요.

- 먼저 올바른 URL([https://myapps.microsoft.com](https://myapps.microsoft.com))을 사용하고 있는지 확인합니다.
- 브라우저의 신뢰할 수 있는 사이트에 URL을 추가해보세요.
- 암호가 올바르고 만료되지 않았는지 확인합니다. 자세한 내용은 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)을 참조하세요.
- 인증 연락처 정보가 최신이고 액세스를 차단하지 않았는지 확인합니다. 자세한 내용은 [Azure Multi-Factor Authentication은 무엇을 의미하나요?](https://docs.microsoft.com/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)를 참조하세요.
- 브라우저의 쿠키를 지우고 다시 로그인해봅니다.

로그인을 시도하는 동안 문제가 계속 발생하면 관리자에게 문의하세요.


## <a name="i-seem-to-be-having-password-issues"></a>암호 문제가 있습니다.

암호를 잊어버려 IT 담당자로부터 암호를 받지 못했거나, 계정에서 암호가 잠겨 있거나, 암호를 변경하려는 경우 [Azure AD 암호를 잊어버렸어요. 도와주세요!](active-directory-passwords-update-your-own-password.md)를 참조하세요.

## <a name="i-need-to-register-for-password-reset"></a>암호 재설정을 위해 등록해야 합니다.

SSPR(셀프 서비스 암호 재설정)을 사용하여 다른 사람에게 문의하지 않고도 암호를 재설정하거나 계정 잠금을 해제할 수 있습니다. 이 기능을 사용하려면 먼저 인증 방법을 등록하거나 관리자가 요구하는 미리 정의된 인증 방법을 확인해야 합니다. 자세한 내용은 [셀프 서비스 암호 재설정 등록](active-directory-passwords-reset-register.md)을 참조하세요.


## <a name="i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치하는 데 문제가 있습니다.

My Apps 포털에는 JavaScript를 지원하고 CSS를 사용하도록 설정된 브라우저가 필요합니다. 암호 기반 Single Sign-On 앱을 사용하는 경우 포함되는 확장도 설치해야 합니다. 이 확장은 암호 기반 Single Sign-On 앱에 구성된 응용 프로그램을 시작할 때 자동으로 다운로드됩니다.

다음 브라우저 요구 사항을 충족하는지 확인합니다.
- **Edge**: Windows 10 Anniversary Edition 이상
- **Chrome**: Windows 7 이상 및 Mac OS X 이상
- **Firefox 26.0 이상**: Windows XP SP2 이상 및 Mac OS X 10.6 이상
- **Internet Explorer 11**: Windows 7 이상(제한적 지원)

다음 사이트에서 확장을 직접 다운로드할 수도 있습니다.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

확장을 설치했지만 여전히 문제가 발생하는 경우 다음을 시도해 보세요.

- 브라우저 확장 설정을 확인하여 확장을 사용하도록 설정되어 있는지 확인합니다.
- 브라우저를 다시 시작하고 My Apps 포털에 로그인합니다.
- 브라우저의 쿠키를 지우고 My Apps 포털에 로그인합니다.
- 진단 도구에 대한 액세스 및 Internet Explorer용 확장을 구성하는 단계별 지침은 [Internet Explorer용 액세스 패널 확장 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)을 참조하세요.

## <a name="use-the-my-apps-secure-sign-in-extension"></a>My Apps 보안 로그인 확장 사용
* `https://myapps.microsoft.com` 이외의 My Apps URL을 사용하는 경우 다음을 수행하여 기본 URL을 구성합니다.
   1. 확장에 로그인되지 *않은* 상태에서 확장 아이콘을 마우스 오른쪽 단추로 클릭합니다.
   2. 메뉴에서 **My Apps URL**을 선택합니다.
   3. 기본 URL을 선택합니다.
   4. 확장 아이콘을 선택합니다.
   5. 확장에 로그인하려면 **시작하려면 로그인하세요**를 선택합니다.

* 브라우저에서 앱에 직접 로그인하려면 다음을 수행합니다.
   1. 확장이 설치되면 **시작하려면 로그인하세요**를 선택하여 확장에 로그인합니다.
   2. 로그온 URL로 앱에 로그인합니다.  
       로그온 URL은 일반적으로 로그인 양식을 표시하는 앱의 URL입니다.
      확장에서 상태를 변경하고 암호를 사용할 수 있다고 알려주어야 합니다.
   3. 로그인하려면 확장 아이콘을 선택합니다.

* 확장에서 앱을 시작하려면 다음을 수행합니다.
   1. 확장이 설치되면 **시작하려면 로그인하세요**를 선택하여 확장에 로그인합니다.
   2. 확장 아이콘을 선택하여 해당 메뉴를 엽니다.
   3. My Apps 포털에서 사용할 수 있는 앱을 검색합니다.
   4. 검색 결과 목록에서 앱을 선택합니다.  
       마지막으로 사용한 세 개의 앱이 **최근에 사용한 항목** 바로 가기 목록에 표시됩니다.

> [!NOTE]
> 이러한 옵션은 Edge, Chrome 및 Firefox에서만 사용할 수 있습니다.

## <a name="how-do-i-add-a-new-app"></a>새 앱을 추가하려면 어떻게 할까요?

1.  **앱** 페이지에서 **앱 추가**를 선택합니다.
2.  추가하려는 앱을 검색한 다음, **추가**를 선택합니다.

   > [!NOTE]
   > * 이 옵션은 관리자가 계정에 대해 사용하도록 설정한 경우에만 액세스할 수 있습니다.
   > * 앱에 권한이 필요한 경우 관리자의 승인을 기다려야 할 수 있습니다.
   > 

## <a name="how-do-i-manage-my-group-memberships"></a>그룹 멤버 자격을 관리하려면 어떻게 할까요?

**그룹** 타일을 선택하고 다음 중 하나를 수행합니다. 
* 그룹을 만들려면 **내가 소유한 그룹** 아래에서 **그룹 만들기**를 선택하고 지침을 따릅니다.
* 그룹에 조인하려면 **내가 속한 그룹**에서 **그룹 조인**을 선택하고 지침을 따릅니다.

   > [!NOTE]
   > * 이 옵션은 관리자가 계정에 대해 사용하도록 설정한 경우에만 액세스할 수 있습니다.
   > * 그룹의 멤버인 경우 세부 정보를 보고, 그룹에서 나갈 수 있습니다.
   > * 그룹의 소유자인 경우 세부 정보를 보고, 멤버를 추가하거나 제거하고, 그룹에서 나갈 수 있습니다.
   >


## <a name="next-steps"></a>다음 단계

자세한 문제 해결 내용은 [응용 프로그램 액세스 패널 웹 사이트 또는 모바일 응용 프로그램을 사용하는 문제](active-directory-application-access-panel-content-map.md)를 참조하세요.

