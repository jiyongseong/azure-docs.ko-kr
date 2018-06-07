---
title: Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소 | Microsoft Docs
description: Azure AD Reporting API에 액세스하기 위한 필수 구성 요소에 대해 알아보기
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: ab05907f1f23c3856b41a941c1b95992ed5a79a4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929985"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API에 액세스하기 위한 필수 구성 요소

[Azure AD(Azure Active Directory) Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

Reporting API는 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) 를 사용하여 Web API에 대한 액세스 권한을 부여합니다.

reporting API에 액세스하도록 준비하려면 다음을 수행해야 합니다.

1. 역할 할당
2. 응용 프로그램 등록
3. 권한 부여
4. 구성 설정 수집



## <a name="assign-roles"></a>역할 할당

API를 통해 보고 데이터에 액세스하려면 다음 역할 중 하나를 할당받아야 합니다.

- 보안 판독기

- 보안 관리자

- 전역 관리자




## <a name="register-an-application"></a>응용 프로그램 등록

스크립트를 사용하여 reporting API에 액세스하는 경우에도 앱을 등록해야 합니다. 이렇게 하면 인증 호출에 필요한 **응용 프로그램 ID**가 제공되고, 이를 통해 코드에서 토큰을 받을 수 있습니다.

Azure AD 보고 API에 액세스하도록 디렉터리를 구성하려면 Azure AD 테넌트에서 **전역 관리자** 디렉터리 역할의 멤버이기도 한 Azure 관리자 계정을 사용하여 Azure Portal에 로그인해야 합니다.

> [!IMPORTANT]
> 이 같은 "admin" 권한이 있는 자격 증명 하에서 실행 중인 응용 프로그램은 매우 강력할 수 있으므로, 응용 프로그램의 ID/암호 자격 증명을 안전하게 보관해야 합니다.
> 


**Azure Active Directory 응용 프로그램을 등록하려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **Azure Active Directory** 페이지에서 **앱 등록**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/02.png) 

3. **앱 등록** 페이지 위쪽의 도구 모음에서 **새 응용 프로그램 등록**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/03.png)

4. **만들기** 페이지에서 다음 단계를 수행합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/04.png)

    a. **이름** 텍스트 상자에서 `Reporting API application`을 입력합니다.

    나. **응용 프로그램 형식**으로 **웹앱/API**를 선택합니다.

    다. **로그온 URL** 텍스트 상자에 `https://localhost`을(를) 입력합니다.

    d. **만들기**를 클릭합니다. 


## <a name="grant-permissions"></a>권한 부여 

액세스하려는 API에 따라 앱에 다음 권한을 부여해야 합니다.  

| API | 사용 권한 |
| --- | --- |
| Windows Azure Active Directory | 디렉터리 데이터 읽기 |
| Microsoft Graph | 모든 감사 로그 데이터 읽기 |


![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/36.png)


다음 섹션에는 두 API에 대한 단계가 나열됩니다. API 중 하나에 액세스하지 않으려는 경우 관련 단계를 건너뛰면 됩니다.
 

**API를 사용하도록 응용 프로그램에 권한을 부여하려면:**

1. **앱 등록** 페이지의 앱 목록에서 **Reporting API 응용 프로그램**을 클릭합니다.

2. **Reporting API 응용 프로그램** 페이지 위쪽의 도구 모음에서 **설정**을 클릭합니다. 

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

3. **설정** 페이지에서 **필요한 권한**을 클릭합니다. 

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/06.png)

4. **필요한 권한** 페이지의 **API** 목록에서 **Windows Azure Active Directory**를 클릭합니다. 

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/07.png)

5. **액세스 사용** 페이지에서 **디렉터리 데이터 읽기**를 선택하고 **로그인 및 사용자 프로필 읽기**를 선택 취소합니다. 

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/08.png)

6. 위쪽의 도구 모음에서 **저장**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/15.png)

7. **필요한 권한** 페이지의 위쪽 도구 모음에서 **추가**를 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/32.png)

8. **API 액세스 추가** 페이지에서 **API 선택**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/31.png)

9. **API 선택** 페이지에서 **Microsoft Graph**를 클릭한 다음, **선택**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/33.png)

10. **액세스 사용** 페이지에서 **Read all audit log data**(모든 감사 로그 데이터 읽기)를 선택한 다음, **선택**을 클릭합니다.  

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/34.png)


11. **API 액세스 추가** 페이지에서 **완료**를 클릭합니다.  

12. **필요한 권한** 페이지의 위쪽 도구 모음에서 **권한 부여**를 클릭한 다음, **예**를 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/17.png)


## <a name="gather-configuration-settings"></a>구성 설정 수집 

이 섹션에서는 디렉터리에서 다음 설정을 가져오는 방법을 보여 줍니다.

- 도메인 이름
- 클라이언트 ID
- 클라이언트 암호

Reporting API에 대한 호출을 구성하는 경우 이 값이 필요합니다. 

### <a name="get-your-domain-name"></a>도메인 이름 가져오기

**도메인 이름을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **Azure Active Directory** 페이지에서 **사용자 지정 도메인 이름**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/09.png) 

3. 도메인 목록에서 도메인 이름을 복사합니다.


### <a name="get-your-applications-client-id"></a>응용 프로그램의 클라이언트 ID 가져오기

**응용 프로그램의 클라이언트 ID를 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **앱 등록** 페이지의 앱 목록에서 **Reporting API 응용 프로그램**을 클릭합니다.

3. **Reporting API 응용 프로그램** 페이지의 **응용 프로그램 ID**에서 **복사하려면 클릭**을 클릭합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/11.png) 



### <a name="get-your-applications-client-secret"></a>응용 프로그램의 클라이언트 비밀 가져오기
응용 프로그램의 클라이언트 암호를 가져오려면 새 키를 만들고 새 키를 저장할 때 해당 값을 저장해야 합니다. 나중에 이 값을 검색하는 것이 불가능하기 때문입니다.

**응용 프로그램의 클라이언트 비밀을 가져오려면**

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 패널에서 **Azure Active Directory**를 클릭합니다.
   
    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/01.png) 

2. **앱 등록** 페이지의 앱 목록에서 **Reporting API 응용 프로그램**을 클릭합니다.


3. **Reporting API 응용 프로그램** 페이지 위쪽의 도구 모음에서 **설정**을 클릭합니다. 

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/05.png)

4. **설정** 페이지의 **APIR 액세스** 섹션에서 **키**를 클릭합니다. 

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/12.png)


5. **키** 페이지에서 다음 단계를 수행합니다.

    ![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites-azure-portal/14.png)

    a. **설명** 텍스트 상자에서 `Reporting API`를 입력합니다.

    나. **만료**로 **In 2 years**(2년)를 선택합니다.

    다. **저장**을 클릭합니다.

    d. 키 값을 복사합니다.


## <a name="next-steps"></a>다음 단계

- [인증서와 함께 Azure Active Directory reporting API를 사용하여 데이터 가져오기](active-directory-reporting-api-with-certificates.md).

- [reporting API의 첫 인상 살펴보기](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [고유의 솔루션 만들기](active-directory-reporting-api-getting-started-azure-portal.md#customize)

