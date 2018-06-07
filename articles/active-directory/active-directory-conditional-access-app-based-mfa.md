---
title: 빠른 시작 - Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA(Multi-Factor Authentication) 요구 | Microsoft Docs
description: 이 빠른 시작에서는 Azure AD(Azure Active Directory) 조건부 액세스를 사용하여 액세스되는 클라우드 앱 유형에 인증 요구 사항을 연결하는 방법을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.topic: article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 475b0229b9e627a56b02d2299ee2e5400aa0ede1
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156112"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>빠른 시작: Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구 

사용자의 로그인 환경을 간소화하기 위해 사용자가 사용자 이름 및 암호를 사용하여 클라우드 앱에 로그인하도록 허용할 수 있습니다. 그러나 대부분의 환경에는 MFA(Multi-Factor Authentication) 같은 강력한 형태의 계정 확인을 요구해야 하는 앱이 적어도 몇 개씩 있습니다. 조직의 이메일 시스템 또는 HR 앱에 대한 액세스를 예로 들 수 있습니다. Azure AD(Azure Active Directory)에서는 조건부 액세스 정책을 사용하여 이 목표를 달성할 수 있습니다.    

이 빠른 시작에서는 해당 환경에서 선택한 클라우드 앱에 Multi-Factor Authentication을 요구하는 [Azure AD 조건부 액세스 정책](active-directory-conditional-access-azure-portal.md)을 구성하는 방법을 보여줍니다.

![정책 만들기](./media/active-directory-conditional-access-app-based-mfa/32.png)


Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.



## <a name="prerequisites"></a>필수 조건 

이 빠른 시작의 시나리오를 완료하려면 다음이 필요합니다.

- **Azure AD Premium Edition에 대한 액세스 권한** - Azure AD 조건부 액세스는 Azure AD Premium 기능입니다. 

- **Isabella Simonsen이라고 하는 테스트 계정** - 테스트 계정을 만드는 방법을 모르는 경우 [클라우드 기반 사용자 추가](add-users-azure-active-directory.md#add-cloud-based-users)를 참조하세요.



## <a name="create-your-conditional-access-policy"></a>조건부 액세스 정책 만들기 

이 섹션에서는 필요한 조건부 액세스 정책을 만드는 방법을 보여줍니다. 이 빠른 시작의 시나리오에서는 다음을 사용합니다.

- MFA를 요구하는 클라우드 앱의 자리 표시자로 사용할 Azure Portal. 
- 조건부 액세스 정책을 테스트할 샘플 사용자.  

정책에서 다음을 설정합니다.

|설정 |값|
|---     | --- |
|개요 | Isabella Simonsen |
|클라우드 앱 | Microsoft Azure 관리 |
|액세스 권한 부여 | Multi-Factor Authentication 필요 |
 

![정책 만들기](./media/active-directory-conditional-access-app-based-mfa/31.png)

 


**조건부 액세스 정책을 구성하려면:**

1. 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure Portal의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다. 

    ![Azure Active Directory](./media/active-directory-conditional-access-app-based-mfa/02.png)

3. **Azure Active Directory** 페이지의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-app-based-mfa/03.png)
 
4. **조건부 액세스** 페이지에서 위쪽의 도구 모음에서 **추가**를 클릭합니다.

    ![추가](./media/active-directory-conditional-access-app-based-mfa/04.png)

5. **새로 만들기** 페이지의 **이름** 텍스트 상자에 **Azure Portal에 액세스하려면 MFA 필요**를 입력합니다.

    ![Name](./media/active-directory-conditional-access-app-based-mfa/05.png)

6. **할당** 섹션에서 **사용자 및 그룹**을 클릭합니다.

    ![개요](./media/active-directory-conditional-access-app-based-mfa/06.png)

7. **사용자 및 그룹** 페이지에서 다음 단계를 수행합니다.

    ![개요](./media/active-directory-conditional-access-app-based-mfa/24.png)

    a. **사용자 및 그룹 선택**을 클릭한 다음, **사용자 및 그룹**을 선택합니다.

    나. **선택**을 클릭합니다.

    다. **선택** 페이지에서 **Isabella Simonsen**을 선택한 다음, **선택**을 클릭합니다.

    d. **사용자 및 그룹** 페이지에서 **완료**를 클릭합니다.

8. **클라우드 앱**을 클릭합니다.

    ![클라우드 앱](./media/active-directory-conditional-access-app-based-mfa/08.png)

9. **클라우드 앱** 페이지에서 다음 단계를 수행합니다.

    ![클라우드 앱 선택](./media/active-directory-conditional-access-app-based-mfa/26.png)

    a. **앱 선택**을 클릭합니다.

    나. **선택**을 클릭합니다.

    다. **선택** 페이지에서 **Microsoft Azure 관리**를 선택한 다음, **선택**을 클릭합니다.

    d. **클라우드 앱** 페이지에서 **완료**를 클릭합니다.


10. **액세스 제어** 섹션에서 **허용**을 클릭합니다.

    ![액세스 제어](./media/active-directory-conditional-access-app-based-mfa/10.png)

11. **허용** 페이지에서 다음 단계를 수행합니다.

    ![허용](./media/active-directory-conditional-access-app-based-mfa/11.png)

    a. **액세스 권한 부여**를 선택합니다.

    a. **다단계 인증 필요**를 선택합니다.

    나. **선택**을 클릭합니다.

12. **정책 사용** 섹션에서 **켬**을 클릭합니다.

    ![정책 설정](./media/active-directory-conditional-access-app-based-mfa/18.png)

13. **만들기**를 클릭합니다.


## <a name="evaluate-a-simulated-sign-in"></a>시뮬레이션된 로그인 평가

조건부 액세스 정책을 구성했으니, 예상대로 작동하는지 확인해야 합니다. 첫 번째 단계로, 조건부 액세스 what if 정책 도구를 사용하여 테스트 사용자 로그인을 시뮬레이션합니다. 이 시뮬레이션은 이 로그인이 정책에 미치는 영향을 평가하고, 시뮬레이션 보고서를 생성합니다.  

what if 정책 평가 도구를 초기화하려면 다음을 설정합니다.

- 사용자로 **Isabella Simonsen** 
- 클라우드 앱으로 **Microsoft Azure 관리**

 **What If**를 클릭하면 다음 내용을 보여주는 시뮬레이션 보고서가 작성됩니다.

- **적용되는 정책** 아래에 **Azure Portal에 액세스하려면 MFA 필요** 
- **권한 부여 컨트롤**로 **Multi-Factor Authentication 필요**.

![What if 정책 도구](./media/active-directory-conditional-access-app-based-mfa/23.png)



**조건부 액세스 정책을 평가하려면:**

1. [조건부 액세스 - 정책](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) 페이지의 위쪽 메뉴에서 **What If**를 클릭합니다.  
 
    ![What If](./media/active-directory-conditional-access-app-based-mfa/14.png)

2. **사용자**를 클릭하고, **Isabella Simonsen**을 선택하고, **선택**을 클릭합니다.

    ![사용자](./media/active-directory-conditional-access-app-based-mfa/15.png)

2. 클라우드 앱을 선택하려면 다음 단계를 수행합니다.

    ![클라우드 앱](./media/active-directory-conditional-access-app-based-mfa/16.png)

    a. **클라우드 앱**을 클릭합니다.

    나. **클라우드 앱** 페이지에서 **앱 선택**을 클릭합니다.

    다. **선택**을 클릭합니다.

    d. **선택** 페이지에서 Microsoft Azure 관리**를 선택한 다음, **선택**을 클릭합니다.

    e. 클라우드 앱 페이지에서 **완료**를 클릭합니다.

3. **What If**를 클릭합니다.


## <a name="test-your-conditional-access-policy"></a>조건부 액세스 정책 테스트

이전 섹션에서는 시뮬레이션된 로그인을 평가하는 방법을 배웠습니다. 시뮬레이션 외에도, 조건부 액세스 정책이 예상대로 작동하는지 테스트해야 합니다. 

정책을 테스트하려면 **Isabella Simonsen** 테스트 계정을 사용하여 [Azure portal](https://portal.azure.com)에 로그인합니다. 계정에 추가 보안 확인을 설정하라고 요구하는 대화 상자가 표시됩니다.

![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/22.png)


## <a name="clean-up-resources"></a>리소스 정리

테스트 사용자 및 조건부 액세스 정책이 더 이상 필요 없으면 삭제합니다.

- Azure AD 사용자를 삭제하는 방법을 모르겠으면 [Azure AD에서 사용자 삭제](add-users-azure-active-directory.md#delete-users-from-azure-ad)를 참조하세요.

- 정책을 삭제하려면 정책을 선택하고, 빠른 실행 도구 모음에서 **삭제**를 클릭합니다.

    ![Multi-Factor Authentication](./media/active-directory-conditional-access-app-based-mfa/33.png)


## <a name="next-steps"></a>다음 단계

조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.

