---
title: Azure Active Directory의 사용자 환경에서 응용 프로그램 숨기기 | Microsoft Docs
description: Azure Active Directory 액세스 패널 또는 Office 365 시작 관리자의 사용자 환경에서 응용 프로그램을 숨기는 방법입니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 564b724ab3971e0566fb2b1dc3a75c2eeac3d391
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Azure Active Directory의 사용자 환경에서 응용 프로그램 숨기기

사용자 액세스 패널 또는 Office 365 시작 관리자에 표시하지 않으려는 응용 프로그램이 있는 경우 이 앱 타일을 숨기는 옵션이 있습니다.  다음 두 가지 옵션을 사용하여 사용자의 앱 시작 관리자에서 응용 프로그램을 숨길 수 있습니다.

- 사용자 액세스 패널 및 Office 365 앱 시작 관리자에서 타사 응용 프로그램 숨기기
- 사용자 액세스 패널에서 모든 Office 365 응용 프로그램 숨기기

앱을 숨긴다면 앱 사용자가 여전히 앱에 대한 권한을 갖지만 앱은 앱 시작 관리자에 나타나지 않습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 있어야 하며 해당 디렉터리의 전역 관리자여야 합니다.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>사용자의 최종 사용자 환경에서 응용 프로그램 숨기기
상황에 따라 아래 단계를 사용하여 액세스 패널에서 응용 프로그램을 숨길 수 있습니다.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>사용자의 액세스 패널 및 O365 앱 시작 관리자로부터 타사 앱을 숨기려면 어떻게 해야 하나요?
사용자 액세스 패널 및 Office 365 앱 시작 관리자에서 응용 프로그램을 숨기려면 다음 단계를 따르세요.

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
3.  **Azure Active Directory - *directoryname*** 화면(즉, 관리 중인 디렉터리에 대한 Azure AD 화면)에서 **Enterprise 응용 프로그램**을 선택합니다.
![Enterprise 앱](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  **Enterprise 응용 프로그램** 화면에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.
5.  **Enterprise 응용 프로그램 - 모든 응용 프로그램** 화면에서 앱을 선택합니다.</br>
![Enterprise 앱](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  ***appname*** 화면(즉, 제목에서 선택된 앱의 이름을 사용한 화면)에서 속성을 선택합니다.
7.  ***appname* -속성** 화면에서, **사용자에게 보입니까?** 에 대해 **예**를 선택합니다.
![Enterprise 앱](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  **저장** 명령을 선택합니다.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>사용자 액세스 패널에서 Office 365 응용 프로그램을 숨기려면 어떻게 하나요?

액세스 패널에서 모든 Office 365 응용 프로그램을 숨기려면 다음 단계를 따르세요. Office 365 포털에는 해당 앱이 계속 표시됩니다.

1.  디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
3.  **Azure Active Directory - *directoryname*** 화면(즉, 관리 중인 디렉터리에 대한 Azure AD 화면)에서 **사용자 설정**을 선택합니다.
4.  **사용자 설정** 화면의 **엔터프라이즈 응용 프로그램** 아래에서 **사용자가 Office 365 포털에서 Office 365 앱만 볼 수 있음**에 대해 **예**를 선택합니다.

![엔터프라이즈 앱](media/active-directory-coreapps-hide-third-party-app/apps4.png)

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](active-directory-coreapps-remove-assignment-azure-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](active-directory-coreapps-change-app-logo-user-azure-portal.md)

