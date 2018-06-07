---
title: Azure Active Directory의 명명된 위치 구성 | Microsoft Docs
description: 명명된 위치를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3b7bd6f4bea111815f647af09ebaa868696b25bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="configure-named-locations-in-azure-active-directory"></a>Azure Active Directory의 명명된 위치 구성

명명된 위치를 사용하여 조직에 신뢰할 수 있는 IP 주소 범위 레이블을 지정할 수 있습니다. Microsoft Azure Active Directory는 다음 상황에서 명명된 위치를 사용합니다.

- 보고되는 거짓 긍정의 수를 줄이기 위한 [위험 이벤트](active-directory-reporting-risk-events.md) 검색.  

- [위치 기반 조건부 액세스](active-directory-conditional-access-locations.md).


이 문서에서는 명명된 위치를 환경에 구성할 수 있는 방법을 설명합니다.


## <a name="entry-points"></a>진입점

다음을 클릭하여 Microsoft Azure Active Directory 페이지 **보안** 섹션의 명명된 위치 구성 페이지에 액세스할 수 있습니다.

![진입점](./media/active-directory-named-locations/34.png)

- **조건부 액세스:**

    - **관리** 섹션에서 **명명된 위치**를 클릭합니다.
    
        ![명명된 위치 명령](./media/active-directory-named-locations/06.png)

- **위험한 로그인**

    - 위쪽 도구 모음에서 **알려진 IP 주소 범위 추가**를 클릭합니다.

       ![명명된 위치 명령](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>구성 예

**명명된 위치를 구성하려면:**

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 창에서 **Azure Active Directory**를 클릭합니다.

    ![왼쪽 창의 Azure Active Directory 링크](./media/active-directory-named-locations/01.png)

3. **Azure Active Directory** 페이지의 **보안** 섹션에서 **조건부 액세스**를 클릭합니다.

    ![조건부 액세스 명령](./media/active-directory-named-locations/05.png)


4. **조건부 액세스** 페이지의 **관리** 섹션에서 **명명된 위치**를 클릭합니다.

    ![명명된 위치 명령](./media/active-directory-named-locations/06.png)


5. **명명된 위치** 페이지에서 **새 위치**를 클릭합니다.

    ![새 위치 명령](./media/active-directory-named-locations/07.png)


6. **새로 만들기** 페이지에서 다음을 확인할 수 있습니다.

    ![새로 만들기 블레이드](./media/active-directory-named-locations/56.png)

    a. **이름** 상자에 명명된 위치의 이름을 입력합니다.

    나. **IP 범위** 상자에 IP 범위를 입력합니다. IP 범위는 CIDR(*Classless Inter-Domain Routing*) 형식이어야 합니다.  

    다. **만들기**를 클릭합니다.



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md).

- [Azure Active Directory 조건부 액세스의 위치 조건](active-directory-conditional-access-locations.md)

- [Azure Active Directory 위험 이벤트](active-directory-reporting-risk-events.md).

- [Azure Active Directory 포털의 위험한 로그인 보고서](active-directory-reporting-security-risky-sign-ins.md).  
