---
title: Azure Active Directory의 새로운 Enterprise Application 관리 기능 | Microsoft Docs
description: Azure Active Directory의 새로운 Enterprise Application 관리 기능에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 30b3fbd415fac1e11a77b3905cb01e5935a62839
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34350868"
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Azure Active Directory의 새로운 Enterprise Application 관리 기능 

Azure AD(Azure Active Directory)는 앱을 보다 간단하고 효율적으로 관리할 수 있도록 하는 새로운 기능으로 엔터프라이즈 응용 프로그램 관리 도구를 개선하였습니다.

[Azure Portal](https://portal.azure.com)에서 Azure AD에 대해 향상된 몇 가지 기능은 다음과 같습니다.

- 간소화된 응용 프로그램 만들기 모델 및 사용한 모든 응용 프로그램 종류에 대한 지원을 갖춘 향상된 응용 프로그램 갤러리 환경 
- 응용 프로그램의 파일럿으로 진행할 수 있는 완전히 새로운 빠른 시작 환경 
- 몇 번의 클릭만으로 셀프 서비스 정책 구성 
- 응용 프로그램 프록시, Single Sign-On 구성에 대한 개선 및 사용자 고유의 응용 프로그램 환경을 통해 그 어느 때보다 더 많은 성과 획득

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Azure Active Directory 응용 프로그램 갤러리의 향상된 기능

[응용 프로그램 갤러리](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), 클라우드로 확장하려는 사용자 지정 응용 프로그램 또는 개발 중인 새로운 응용 프로그램에서 즐겨찾는 응용 프로그램을 추가합니다.  **엔터프라이즈 응용 프로그램** 또는 **모든 응용 프로그램**에서 **추가**를 클릭하여 이 새로운 환경을 시작할 수 있습니다.
 
  ![응용 프로그램 추가](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

일단 갤러리에서 가장 중요한 위치에서 표시되는 사용자 프로비전을 지원하는 기능을 갖춘 응용 프로그램을 모두 확인할 수 있습니다. 원하는 모든 종류의 서로 다른 범주를 탐색하여 관심있는 응용 프로그램을 살펴 보거나 검색 환경을 사용하여 통합하려는 응용 프로그램을 신속히 찾을 수 있습니다.

  ![응용 프로그램 갤러리](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>한 곳에서 사용자 지정 응용 프로그램 추가

갤러리에서 사전 통합된 응용 프로그램을 추가하는 것 외에도 이제는 클래식 관리 포털에서 사용한 모든 사용자 지정 응용 프로그램 구성 환경을 새 포털에서 사용할 수 있습니다. 응용 프로그램 프록시를 사용하여 온-프레미스에서 응용 프로그램을 확장하거나, 사용자 고유의 암호 또는 페더레이션된 SSO 응용 프로그램을 통합하거나, 응용 프로그램 레지스트리를 사용하여 아주 새로운 응용 프로그램을 만드는 등 이 모든 것을 한 곳에서 수행할 수 있습니다.

  ![응용 프로그램 직접 추가](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**응용 프로그램을 직접 추가하려면**

1. 응용 프로그램 갤러리 위쪽에서 **링크 직접 추가**를 클릭합니다. 
2. 두 가지 옵션, 즉 **기존 응용 프로그램 배포** 및 **새 응용 프로그램 개발** 옵션이 있습니다. 계속 읽어가면서 두 옵션의 차이점과 사용 방법에 대해 알아봅니다.

### <a name="deploying-existing-applications"></a>기존 응용 프로그램 배포

1. 이미 실행 중인 응용 프로그램이 있고 Single Sign-On 또는 프로비전을 위해 Azure AD에 통합하려는 경우 **기존 응용 프로그램 배포** 옵션을 선택합니다. 응용 프로그램 이름을 선택하고 **추가**를 클릭합니다.
2. 이것으로 끝입니다. 응용 프로그램에 대한 모든 세부 정보를 미리 알아야 하는 대신 왼쪽 메뉴를 탐색하고 언제든지 원하는 대로 응용 프로그램을 구성하여 새 응용 프로그램의 작동 방식을 설정할 수 있습니다.

  ![한 번의 클릭으로 기존 응용 프로그램 추가](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>새 응용 프로그램 개발

1. 새 응용 프로그램을 개발하는 경우 갤러리에서 [응용 프로그램 레지스트리]로 쉽게 이동할 수 있습니다.
2. 응용 프로그램 갤러리에서 **직접 추가** 옵션을 클릭하고, **기존 응용 프로그램 개발**을 선택하면 응용 프로그램 추가 환경에 대한 빠른 링크가 표시됩니다.

  ![몇 번 클릭으로 새로 개발된 응용 프로그램 추가](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>[응용 프로그램 레지스트리]를 사용하여 응용 프로그램을 추가하면 Enterprise Application 목록에 표시되며, 여기서 Single Sign-On을 구성하고 새 응용 프로그램에 대한 액세스 정책을 관리할 수 있습니다.

  ![Enterprise Application에서 새 응용 프로그램에 대한 액세스 관리](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quickstart-get-going-with-your-new-application-right-away"></a>빠른 시작: 새 응용 프로그램 즉시 사용 

사전 통합 여부에 관계없이 응용 프로그램 또는 사용자 고유 앱을 추가한 후에는 새 응용 프로그램 환경에 신속하게 적응할 수 있도록 맞춤형 빠른 시작 환경을 만들었습니다. 체계적으로 각 옵션을 따르는 경우 UI를 통해 단계별로 안내하고 가능한 빨리 새 응용 프로그램의 파일럿을 진행하는 방법을 보여 드리겠습니다. 
 
  ![새 응용 프로그램 빠른 시작 환경](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 응용 프로그램 왼쪽 탐색 메뉴에서 **빠른 시작**을 클릭하면 언제든지 응용 프로그램에 대한 새로운 빠른 시작 환경을 방문할 수 있습니다.


## <a name="updated-application-proxy-configuration"></a>업데이트된 응용 프로그램 프록시 구성
이제 추가한 새 응용 프로그램 중 하나가 온-프레미스 환경에서 실행되며 이를 Azure AD와 통합하려고 한다고 가정해 보겠습니다.  새로운 Azure AD 포털의 새 응용 프로그램 구성 환경에 대한 새로운 멋진 기능 중 하나는 응용 프로그램 프록시 구성에서 응용 프로그램의 로그온 모드를 분리하여 응용 프로그램의 여러 인스턴스를 만들 필요 없이 회사 네트워크에서 실행 중인 암호 SSO 또는 페더레이션된 응용 프로그램을 클라우드에 직접 쉽게 공개할 수 있다는 것입니다.

네이티브 Windows 인증 환경을 지원하는 응용 프로그램을 포함하여 새로운 포털에서 직접 Azure AD 응용 프로그램 프록시와 함께 사용하기 위해 추가한 새 응용 프로그램을 구성할 수 있습니다.

  ![Windows 통합 인증 로그온 옵션을 사용하도록 응용 프로그램 구성](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

[응용 프로그램 프록시]를 사용하여 네이티브 Windows 인증 응용 프로그램을 구성하려면
1. Single Sign-On 탐색 항목을 클릭하고, 로그온 설정 블레이드에서 **Windows 통합 인증**을 선택하고, 원하는 대로 설정을 구성합니다.
2. 이러한 새 인증 모드를 지원하는 것 외에도 조직의 보안 끝점에서 실행되는 응용 프로그램을 지원하기 위해 사용자 지정 도메인에서 인증서를 업로드할 수 있습니다.  
 
   ![응용 프로그램 프록시에 사용할 인증서 업로드](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. 즐겨찾는 온-프레미스 응용 프로그램에 대한 새 인증서를 업로드하려면 왼쪽 탐색 메뉴에서 **응용 프로그램 프록시** 옵션을 클릭하고 **인증서** 선택기를 클릭한 다음 클라우드 끝점에서 응용 프로그램으로의 요청을 암호화하는 데 사용할 수 있는 인증서 파일을 업로드합니다.

## <a name="advanced-federated-single-sign-on-configuration"></a>페더레이션된 Single Sign-On 고급 구성

현재 페더레이션된 응용 프로그램을 사용하는 사용자를 위해 SAML 기반 로그온 구성에는 새로운 기능이 많이 있습니다. 이제 SAML 토큰에서 클레임으로 발급된 기존 사용자 속성을 완전하게 사용자 지정, 추가, 제거 및 매핑할 수 있습니다.
 
  ![페더레이션된 응용 프로그램에 전달된 SAML 토큰 사용자 특성 사용자 지정](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


새로운 페더레이션된 SSO 구성을 확인하려면
1. 왼쪽 탐색 메뉴에서 페더레이션된 응용 프로그램의 **Single Sign-On**을 열고 '*SAML 기반 로그온** 모드가 선택되어 있는지 확인합니다. 
2. 일단 여기서 **사용자 특성** 제목 아래의 확인란을 선택하면 해당 응용 프로그램에 전달된 SAML 토큰에서 포함한 모든 특성을 수정할 수 있습니다.

또한 페더레이션된 Single Sign-On에 사용되는 인증서를 만들고, 롤오버하고, 관리할 뿐만 아니라 인증서가 만료될 때 알림을 받는 사람을 편집할 수도 있습니다. 동일한 Single Sign-On 창의 **인증서** 제목 아래에는 이러한 새 옵션이 표시됩니다.
 
  ![새 인증서 만들기, 만료 알림 전자 메일 및 인증서 서명 옵션 사용자 지정](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-parameter"></a>릴레이 상태 매개 변수
마지막으로 **릴레이 상태 매개 변수**를 포함하도록 지원하는 SAML URL 매개 변수 집합도 확장했습니다. 이 매개 변수는 로그인이 완료되면 사용자가 페더레이션된 응용 프로그램 내부에 도달하게 되는 페이지입니다. 사용자가 빠르게 진행할 수 있도록 응용 프로그램 내의 특정 위치로 사용자를 보내려는 경우 이를 구성하는 데 유용한 설정입니다.

  ![SAML 릴레이 상태 매개 변수 설정](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**릴레이 상태 매개 변수를 설정하려면**

1. Single Sign-On 구성 창의 **도메인 및 URL** 제목 아래에 있는 **고급 URL 설정 표시** 확인란을 선택합니다. 
2. 이 매개 변수 및 기타 SAML URL 설정을 지정할 수 있는 일련의 새로운 URL 입력 상자가 표시됩니다.

## <a name="bring-your-own-password-sso-applications"></a>암호 SSO 응용 프로그램 직접 가져오기

모든 응용 프로그램에서 즉시 사용할 수 있는 페더레이션을 지원하는 것은 아님을 알고 있습니다. 예를 들어 추가한 새 응용 프로그램 중 하나에 사용자가 사용자 이름과 암호를 사용하여 로그인하는 사용자 지정 로그인 화면이 있을 수 있습니다. **응용 프로그램 직접 가져오기** 기능을 사용하여 Azure AD와 이러한 종류의 응용 프로그램을 통합할 수 있으며, 이제 이 기능을 사용하여 새 포털에서 구성할 수 있습니다.
 
  ![사용자 지정 암호 자격 증명 모음 응용 프로그램과 Azure AD의 통합](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**'응용 프로그램 직접 가져오기' 기능을 확인하려면**

1. **암호 기반 로그온**에 추가한 새 사용자 지정 응용 프로그램에 대해 Single Sign-On 모드를 설정한 후 응용 프로그램에서 해당 로그인 화면을 렌더링하는 URL을 입력하고 
2. **저장**을 클릭합니다.  
2. 이렇게 하면 사용자 이름과 암호 입력 상자에 해당 URL을 자동으로 가져오고, Azure AD를 통해 액세스 패널 브라우저 확장을 사용하여 해당 응용 프로그램에 암호를 안전하게 전송할 수 있습니다.

## <a name="configure-self-service-application-access"></a>셀프 서비스 응용 프로그램 액세스 구성

새 응용 프로그램을 많이 추가한 후에는 관리자로 신경을 쓰지 않고도 사용자가 자신의 액세스 패널에서 해당 응용 프로그램을 찾아보고 추가할 수 있습니다. 이제 이 최신 릴리스를 사용하여 새 포털에서 셀프 서비스 응용 프로그램 액세스를 직접 구성하고 관리할 수 있습니다.

  ![암호 SSO 응용 프로그램에 대한 셀프 서비스 응용 프로그램 액세스 구성](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**셀프 서비스 응용 프로그램 액세스를 구성하고 관리하려면**

1. 시작하려면 응용 프로그램의 왼쪽 탐색 메뉴에서 **셀프 서비스** 옵션을 선택하고 **사용자가 이 응용 프로그램에 대한 액세스를 요청하도록 허용하시겠습니까?** 옵션을 ‘**예**’로 설정합니다. 
2. 이렇게 하면 해당 응용 프로그램에 대한 액세스를 승인할 수 있는 사용자 및 셀프 서비스 사용자를 추가할 그룹을 구성할 수 있습니다. 또한 응용 프로그램을 암호 Single Sign-On(SSO)으로 구성한 경우 승인자가 응용 프로그램에 할당된 암호를 선택적으로 관리할 수 있도록 허용하는 또 다른 옵션이 표시됩니다.

## <a name="feedback"></a>사용자 의견

향상된 Azure AD 환경 사용이 사용자의 마음에 들기를 바랍니다. 사용자 의견을 계속 보내주세요! [피드백 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)의 **관리자 포털Admin Portal** 섹션에서 개선을 위한 의견과 아이디어를 게시합니다.  매일 멋진 새로운 기능을 구축하는 방법을 기대하며, 사용자의 지침에 따라 다음에 구축할 기능을 구체화하고 정의하겠습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Active Directory로 응용 프로그램 관리](manage-apps/what-is-application-management.md)를 참조하세요.



