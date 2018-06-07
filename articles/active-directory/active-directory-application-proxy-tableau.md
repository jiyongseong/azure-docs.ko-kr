---
title: Azure Active Directory 응용 프로그램 프록시 및 Tableau | Microsoft Docs
description: Azure AD(Azure Active Directory) 응용 프로그램 프록시를 사용하여 Tableau 배포에 대해 원격 액세스를 제공하는 방법을 알아봅니다.  에서도 확인할 수 있습니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7fa8654e413ac337994d35afdce7bd3478127f64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34070875"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory 응용 프로그램 프록시 및 Tableau 

Azure Active Directory 응용 프로그램 프록시 및 Tableau를 사용하여 Tableau 배포에 대한 원격 액세스를 제공하기 위해 응용 프로그램 프록시를 쉽게 사용할 수 있도록 합니다. 이 아티클에서는 이 시나리오를 구성하는 방법에 대해 설명합니다.  

## <a name="prerequisites"></a>필수 조건 

이 아티클의 시나리오에서는 다음을 가정합니다.

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure)가 구성됨 

- [응용 프로그램 프록시 커넥터](manage-apps/application-proxy-enable.md)가 설치됨 

 

## <a name="enabling-application-proxy-for-tableau"></a>Tableau에 대해 응용 프로그램 프록시 사용 

Tableau에 대해 응용 프로그램 프록시를 사용하려는 경우 [aadapfeedback@microsoft.com](mailto:aadapfeedback@microsoft.com)으로 이메일을 보내 이 시나리오를 사용하도록 설정해야 합니다.
이메일에서:

-   Tableau에 대해 응용 프로그램 프록시를 사용하도록 설정을 제목으로 사용
-   본문에 테넌트 ID 포함    

응용 프로그램을 사용할 준비가 되었을 경우 확인 메시지가 표시됩니다. 일반적으로 약 1주가 걸립니다. 그러나 확인 메시지를 기다리는 동안 구성을 마칠 수 있습니다.


 

## <a name="publish-your-applications-in-azure"></a>Azure에 응용 프로그램 게시 

Tableau를 게시하려면 Azure Portal에서 응용 프로그램을 게시해야 합니다.

관련 작업:

- 1~8단계의 자세한 지침은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)를 참조하세요. 
- 앱 프록시 필드의 Tableau 값을 확인하는 방법에 대한 정보는 Tableau 설명서를 참조하세요.  

**앱을 게시하려면**: 


1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. **Azure Active Directory > 엔터프라이즈 응용 프로그램**을 선택합니다. 

3. 블레이드의 위쪽에서 **추가**를 선택합니다. 

4. **온-프레미스 응용 프로그램**을 선택합니다. 

5. 새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다. 

    - **내부 URL**: 이 응용 프로그램에는 자체 Tableau URL인 내부 URL이 있어야 합니다. 예: `https://adventure-works.tableau.com` 

    - **사전 인증 방법**: Azure Active Directory(권장되지만 필수 아님)입니다. 

6. 블레이드의 위쪽에서 **추가**를 선택합니다. 응용 프로그램이 추가되고 빠른 시작 메뉴가 열립니다. 

7. 빠른 시작 메뉴에서 **테스트할 사용자 지정**을 선택하고 응용 프로그램에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 응용 프로그램에 대한 액세스 권한이 있는지 확인합니다. 

8. **할당**을 선택하여 테스트 사용자 할당을 저장합니다. 

9. (선택 사항) 앱 관리 페이지에서 **Single Sign-On**을 선택합니다. 드롭다운 메뉴에서 **Windows 통합 인증**을 선택하고 Tableau 구성에 따라 필수 필드를 입력합니다. **저장**을 선택합니다. 

 

## <a name="testing"></a>테스트 

응용 프로그램을 테스트할 준비가 되었습니다. Tableau를 게시하는 데 사용되는 외부 URL에 액세스하고 두 응용 프로그램에 할당된 사용자 권한으로 로그인합니다.



## <a name="next-steps"></a>다음 단계

Azure AD 응용 프로그램 프록시에 대한 자세한 정보는 [온-프레미스 응용 프로그램에 보안 원격 액세스를 제공하는 방법](manage-apps/application-proxy.md)을 참조하세요.

