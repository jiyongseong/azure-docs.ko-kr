---
title: Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화 | Microsoft Docs
description: Azure Active Directory에서 사용자가 로그인하지 않도록 엔터프라이즈 응용 프로그램을 비활성화하는 방법
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
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: be0c8ede4a99c532aecbfbb4ee0fc51657f96886
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화
Azure AD(Azure Active Directory)에서 사용자가 로그인하지 않도록 엔터프라이즈 응용 프로그램을 비활성화하는 것은 쉽습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 있어야 하고 해당 디렉터리에 대한 전역 관리자여야 합니다.

## <a name="how-do-i-disable-user-sign-ins"></a>사용자 로그인을 비활성화하려면 어떻게 합니까?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
3. **Azure Active Directory** -  ***directoryname*** 창, 즉 관리 중인 디렉터리에 대한 Azure AD 창에서 **엔터프라이즈 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 앱 열기](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. **엔터프라이즈 응용 프로그램** 창에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 창에서 앱을 선택합니다.
6. ***appname*** 창, 즉 제목에서 선택된 앱의 이름을 사용한 창에서 **속성**을 선택합니다.

    ![모든 응용 프로그램 명령 선택](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. ***appname*** - **속성** 창에서 **사용자가 로그인할 수 있습니까?** 에 대해 **아니요**를 선택합니다.
8. **저장** 명령을 선택합니다.

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](active-directory-coreapps-remove-assignment-azure-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](active-directory-coreapps-change-app-logo-user-azure-portal.md)
