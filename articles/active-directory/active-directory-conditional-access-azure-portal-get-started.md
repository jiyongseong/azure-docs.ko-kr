---
title: Azure Active Directory에서 조건부 액세스 시작 | Microsoft Docs
description: 위치 조건을 사용하여 조건부 액세스를 테스트합니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 04a7736e85c0b149cb627411f61fc147220769ad
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Azure Active Directory에서 조건부 액세스 시작

조건부 액세스는 권한 있는 사용자가 앱에 액세스할 수 있는 조건을 정의할 수 있게 해주는 Azure Active Directory의 기능입니다. 

이 항목에서는 사용자 환경의 위치 조건에 따라 조건부 액세스를 테스트하기 위한 지침을 제공합니다.  


## <a name="scenario-description"></a>시나리오 설명

많은 조직의 일반적 요구 사항 중 하나는 회사 인트라넷에서 수행되지 않는 앱 액세스를 위해 다단계 인증만 필요하다는 것입니다. Azure Active Directory를 사용하면 위치 기반 조건부 액세스 정책을 구성하여 이 목표를 쉽게 달성할 수 있습니다. 여기서는 관련 정책 구성에 대한 자세한 지침을 제공합니다. 정책은 [신뢰할 수 있는 IP](authentication/howto-mfa-mfasettings.md#trusted-ips)를 활용하여 회사 인트라넷과 다른 모든 위치 간에 시도되는 액세스를 구별합니다.


## <a name="prerequisites"></a>필수 조건

이 항목의 시나리오에서는 [Azure Active Directory 조건부 액세스](active-directory-conditional-access-azure-portal.md)에서 설명한 개념을 잘 알고 있다고 가정합니다.

이 시나리오를 테스트하려면 다음을 수행해야 합니다.

- 테스트 사용자 만들기 

- 테스트 사용자에게 Azure AD Premium 라이선스 할당

- 관리되는 앱 구성 및 테스트 사용자 할당

- 신뢰할 수 있는 IP 구성

신뢰할 수 있는 IP에 대한 자세한 내용은 [신뢰할 수 있는 IP](authentication/howto-mfa-mfasettings.md#trusted-ips)를 참조하세요.


## <a name="policy-configuration-steps"></a>정책 구성 단계

**조건부 액세스 정책을 구성하려면 다음을 수행합니다.**

1. Azure Portal의 왼쪽 탐색 모음에서 **Azure Active Directory**를 클릭합니다. 

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. **Azure Active Directory** 블레이드의 **관리** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. **조건부 액세스** 블레이드에서 **새로 만들기** 블레이드를 열려면 위쪽의 도구 모음에서 **추가**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. **새로 만들기** 블레이드의 **이름** 텍스트 상자에서 정책 이름을 입력합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. **할당** 섹션에서 **사용자 및 그룹**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. **사용자 및 그룹** 블레이드에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. **사용자 및 그룹 선택**을 클릭합니다.

    나. **선택**을 클릭합니다.

    다. **선택** 블레이드에서 테스트 사용자를 선택한 다음 **선택**을 클릭합니다.

    d. **사용자 및 그룹** 블레이드에서 **완료**를 클릭합니다.

7. **새로 만들기** 블레이드에서 **클라우드 앱** 블레이드를 열려면 **할당** 섹션에서 **클라우드 앱**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. **클라우드 앱** 블레이드에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. **앱 선택**을 클릭합니다.

    나. **선택**을 클릭합니다.

    다. **선택** 블레이드에서 클라우드 앱을 선택한 다음 **선택**을 클릭합니다.

    d. **클라우드 앱** 블레이드에서 **완료**를 클릭합니다.

9. **새로 만들기** 블레이드에서 **조건** 블레이드를 열려면 **할당** 섹션에서 **조건**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. **조건** 블레이드에서 **위치** 블레이드를 열려면 **위치**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. **위치** 블레이드에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. **구성** 아래에서 **예**를 클릭합니다.

    나. **포함**에서 **모든 위치**를 클릭합니다.

    다. **제외**를 클릭한 다음 **모든 신뢰할 수 있는 IP**를 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. **Done**을 클릭합니다.

12. **조건** 블레이드에서 **완료**를 클릭합니다.

13. **새로 만들기** 블레이드에서 **허용** 블레이드를 열려면 **제어** 섹션에서 **허용**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. **허용** 블레이드에서 다음 단계를 수행합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. **다단계 인증 필요**를 선택합니다.

    나. **선택**을 클릭합니다.

15. **새로 만들기** 블레이드의 **정책 사용**에서 **설정**을 클릭합니다.

    ![조건부 액세스](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. **새로 만들기** 블레이드에서 **만들기**를 클릭합니다.


## <a name="testing-the-policy"></a>정책 테스트

정책을 테스트하려면 다음과 같은 장치에서 앱에 액세스해야 합니다. 

1. 구성된 신뢰할 수 있는 IP에 속한 IP 주소가 있는 장치 

1. 구성된 신뢰할 수 있는 IP에 속하지 않은 IP 주소가 있는 장치

다단계 인증은 구성된 신뢰할 수 있는 IP에 속하지 않은 장치에서 수행되는 연결 시도에서만 필요합니다. 


## <a name="next-steps"></a>다음 단계

조건부 액세스에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 참조하세요.

