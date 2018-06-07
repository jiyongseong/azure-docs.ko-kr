---
title: "응용 프로그램에 대한 사용자 액세스를 제거하는 방법 | Microsoft Docs"
description: "응용 프로그램에 대한 사용자 액세스를 제거하는 방법 이해"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: aec49b108c21b97646e622c47bf69728a81d6d33
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-remove-a-users-access-to-an-application"></a>응용 프로그램에 대한 사용자 액세스를 제거하는 방법

이 문서는 응용 프로그램에 대한 사용자 액세스를 제거하는 방법을 이해하는 데 도움이 됩니다.

## <a name="i-want-to-remove-a-specific-users-or-groups-assignment-to-an-application"></a>응용 프로그램에 대한 특정 사용자 또는 그룹의 할당을 제거하려는 경우

응용 프로그램에 대한 사용자 또는 그룹 할당을 제거하려면 [Azure Active Directory의 엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) 문서에 나열된 단계를 따릅니다.

모든 사용자에 대해 응용 프로그램에 대한 모든 액세스를 비활성화하려는 경우

응용 프로그램에 대한 모든 사용자 로그인을 비활성화하려면 [Azure Active Directory의 엔터프라이즈 앱에 대한 사용자 로그인 비활성화](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) 문서에 나열된 단계를 따릅니다.

## <a name="i-want-to-delete-an-application-entirely"></a>응용 프로그램을 완전히 삭제하려는 경우

**응용 프로그램을 삭제**하려면 다음 지침을 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  삭제하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 맨 위의 응용 프로그램 **개요** 창에서 **삭제** 아이콘을 클릭합니다.

## <a name="i-want-to-disable-all-future-user-consent-operations-to-any-application"></a>모든 응용 프로그램에 대한 모든 이후 사용자 동의 작업을 비활성화하려는 경우

전체 디렉터리에 대한 사용자 동의를 비활성화하면 모든 응용 프로그램에 대한 최종 사용자 동의를 방지합니다. 관리자는 여전히 사용자의 동작에 동의할 수 있습니다. 응용 프로그램 동의 및 이 작업을 수행하거나 수행하지 않을 수 있는 이유에 대해 자세히 알아보려면 [사용자 및 관리자 동의 이해하기](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)를 참조하세요.

**전체 디렉터리에서 모든 이후 사용자 동의 작업을 비활성화**하려면 다음 지침을 따릅니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **사용자 설정**을 클릭합니다.

6.  **사용자가 앱이 데이터에 액세스하도록 허용할 수 있음** 토글을 **아니요**로 설정하고 **저장** 단추를 클릭하여 모든 이후 사용자 동의 작업을 비활성화합니다.


# <a name="next-steps"></a>다음 단계
[앱에 대한 액세스 관리](active-directory-managing-access-to-apps.md)
