---
title: 응용 프로그램이 액세스 패널에 표시되는 방식 | Microsoft Docs
description: 응용 프로그램이 액세스 패널에 표시되는 문제 해결
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewr: japere
ms.openlocfilehash: 0d4203ea39adf027d783a482198f523e18cbc246
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="how-applications-appear-on-the-access-panel"></a>응용 프로그램이 액세스 패널에 표시되는 방식

액세스 패널은 웹 기반 포털로 Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 사용자가 Azure AD 관리자를 통해 액세스 권한을 부여 받은 클라우드 기반 응용 프로그램을 보고 시작할 수 있도록 합니다. 이러한 응용 프로그램은 Azure AD 포털에서 사용자를 대신하여 구성됩니다. 관리자는 사용자에게 직접 또는 사용자가 사용자의 액세스 패널에 표시되는 응용 프로그램에서 결과의 일부인 그룹에 응용 프로그램을 프로비전할 수 있습니다.

## <a name="general-issues-to-check-first"></a>먼저 확인해야 할 일반적인 문제

-   응용 프로그램이 사용자 또는 사용자가 구성원인 그룹에서 제거된 경우 몇 분 후에 사용자의 액세스 패널에 로그인하고 다시 로그아웃하여 응용 프로그램이 제거되었는지 확인합니다.

-   라이선스가 사용자 또는 사용자가 구성원인 그룹에서 제거된 경우 변경 사항이 만들어질 그룹의 크기 및 복잡성에 따라 시간이 오래 걸릴 수 있습니다. 액세스 패널에 로그인하기 전에 잠시 여유 시간을 둡니다.

## <a name="problems-related-to-assigning-applications-to-users"></a>사용자의 응용 프로그램 할당과 관련된 문제

사용자는 이전에 할당되었기 때문에 액세스 패널에 응용 프로그램이 표시될 수 있습니다. 다음은 확인하는 몇 가지 방법입니다.

-   [사용자가 응용 프로그램에 할당되었는지 확인](#check-if-a-user-is-assigned-to-the-application)

-   [사용자가 응용 프로그램과 관련된 라이센스가 있는지 확인](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>사용자가 응용 프로그램에 할당되었는지 확인

사용자가 응용 프로그램에 할당되었는지 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

6.  문제의 응용 프로그램 이름을 **검색**합니다.

7.  **사용자 및 그룹**을 클릭합니다.

8.  사용자가 응용 프로그램에 할당되었는지 확인합니다.

  * 응용 프로그램에서 사용자를 제거하려는 경우 사용자의 **행을 클릭**하고 **삭제**를 선택합니다.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>사용자가 응용 프로그램과 관련된 라이센스가 있는지 확인

사용자의 할당된 라이선스를 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **라이선스**를 클릭하여 사용자가 현재 할당된 라이선스를 봅니다.

   * 사용자가 Office 라이선스에 할당된 경우 사용자의 액세스 패널에 나타나도록 자사 Office 응용 프로그램을 활성화합니다.

## <a name="problems-related-to-assigning-applications-to-groups"></a>그룹에 응용 프로그램 할당과 관련된 문제

사용자는 응용 프로그램이 할당된 그룹에 속해 있으므로 액세스 패널에 응용 프로그램이 표시될 수 있습니다. 다음은 확인하는 몇 가지 방법입니다.

-   [사용자의 그룹 멤버 자격 확인](#check-a-users-group-memberships)

-   [사용자가 라이선스에 할당된 그룹의 멤버인지 확인](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>사용자의 그룹 구성원 자격 확인

그룹의 멤버 자격을 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **그룹**을 클릭합니다.

8.  사용자가 응용 프로그램에 할당된 그룹에 속하는지 확인합니다.

   * 그룹에서 사용자를 제거하려는 경우 그룹의 **행을 클릭**하고 삭제를 선택합니다.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>사용자가 라이선스에 할당된 그룹의 멤버인지 확인

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **그룹**을 클릭합니다.

8.  특정 그룹의 행을 클릭합니다.

9.  **라이선스**를 클릭하여 그룹이 할당된 라이선스를 봅니다.

  * 그룹이 Office 라이선스에 할당된 경우 사용자의 액세스 패널에 나타나도록 특정 자사 Office 응용 프로그램을 활성화할 수 있습니다.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>이러한 문제 해결 단계가 문제를 해결하지 않는 경우

사용할 수 있는 경우 다음 정보로 지원 티켓을 엽니다.

-   상관 관계 오류 ID

-   UPN(사용자 전자 메일 주소)

-   테넌트 ID

-   브라우저 종류

-   오류가 발생하는 동안 표준 시간대 및 시간/기간

-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 관리](manage-apps/what-is-application-management.md)
