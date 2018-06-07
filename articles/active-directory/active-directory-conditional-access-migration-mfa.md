---
title: "Azure Portal에서 다단계 인증이 필요한 클래식 정책 마이그레이션 | Microsoft Docs"
description: "이 문서에서는 Azure Portal에서 다단계 인증이 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다."
services: active-directory
keywords: "앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 77484dc3773736ea15c39ede5f9d49b6b694d960
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2017
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Azure Portal에서 다단계 인증이 필요한 클래식 정책 마이그레이션 

이 문서에서는 클라우드 앱에 대한 **다단계 인증**이 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다. 필수 구성 요소는 아니지만 클래식 정책 마이그레이션을 시작하기 전에 [Azure Portal에서 클래식 정책 마이그레이션](active-directory-conditional-access-migration.md)을 읽어 보는 것이 좋습니다.


 
## <a name="overview"></a>개요 

이 문서의 시나리오에서는 클라우드 앱에 대한 **다단계 인증**이 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


마이그레이션 프로세스는 다음 단계로 구성됩니다.

1. [클래식 정책을 열어](#open-a-classic-policy) 구성 설정을 가져옵니다.
2. 클래식 정책을 대체할 새 Azure AD 조건부 액세스 정책을 만듭니다. 
3. 클래식 정책을 사용하지 않도록 설정합니다.



## <a name="open-a-classic-policy"></a>클래식 정책 열기

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration-mfa/02.png)

3. **관리** 섹션에서 **클래식 정책(미리 보기)**을 클릭합니다.

    ![클래식 정책](./media/active-directory-conditional-access-migration-mfa/12.png)

4. 클래식 정책 목록에서 클라우드 앱에 대한 **다단계 인증**이 필요한 정책을 클릭합니다.

    ![클래식 정책](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>새 조건부 액세스 정책 만들기


1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/02.png)



3. **조건부 액세스** 페이지에서 **새로 만들기** 페이지를 열려면 위쪽의 도구 모음에서 **추가**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/03.png)

4. **새로 만들기** 페이지의 **이름** 텍스트 상자에서 정책 이름을 입력합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/29.png)

5. **할당** 섹션에서 **사용자 및 그룹**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/05.png)

    a. 클래식 정책에서 모든 사용자를 선택한 경우 **모든 사용자**를 클릭합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-migration/35.png)

    b. 클래식 정책에서 그룹을 선택한 경우 **사용자 및 그룹 선택**을 클릭한 다음 필요한 사용자 및 그룹을 선택합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/36.png)

    c. 제외된 그룹이 있는 경우 **제외** 탭을 클릭한 다음 필요한 사용자 및 그룹을 선택합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-migration/37.png)

6. **새로 만들기** 페이지에서 **클라우드 앱** 페이지를 열려면 **할당** 섹션에서 **클라우드 앱**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. **클라우드 앱** 페이지에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/08.png)

    a. **앱 선택**을 클릭합니다.

    b. **선택**을 클릭합니다.

    c. **선택** 페이지에서 클라우드 앱을 선택한 다음 **선택**을 클릭합니다.

    d. **클라우드 앱** 페이지에서 **완료**를 클릭합니다.



9. **다단계 인증 필요**를 선택한 경우:

    ![조건부 액세스](./media/active-directory-conditional-access-migration/26.png)

    a. **액세스 제어** 섹션에서 **허용**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/27.png)

    b. **허용** 페이지에서 **액세스 허용**을 클릭하고 **다단계 인증 필요**를 클릭합니다.

    c. **선택**을 클릭합니다.


10. **설정**을 클릭하여 정책을 사용하도록 설정합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>클래식 정책을 사용하지 않도록 설정

클래식 정책을 사용하지 않도록 설정하려면 **세부 정보** 보기에서 **사용 안 함**을 클릭하세요.

![클래식 정책](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>다음 단계

- 클래식 정책 마이그레이션에 대한 자세한 내용은 [Azure Portal에서 클래식 정책 마이그레이션](active-directory-conditional-access-migration.md)을 참조하세요.


- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 
