---
title: Azure AD 앱 프록시 링크 및 URL 변환 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시 커넥터에 대한 기본 사항을 제공합니다.
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
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 8c3cd66a376c1526f50201b7fd662c4faf650df5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161606"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대해 하드 코드된 링크 리디렉션

Azure AD 응용 프로그램 프록시는 원격 사용자가 자신의 장치에서 온-프레미스 앱을 사용할 수 있도록 합니다. 그러나 일부 앱은 HTML에 포함된 로컬 링크와 함께 개발되었습니다. 이러한 링크는 앱이 원격으로 사용되는 경우 제대로 작동하지 않습니다. 서로 가리키도록 하는 여러 온-프레미스 응용 프로그램이 있는 경우 사용자는 사무실에 있지 않은 경우 작업을 계속하기 위한 링크를 예상합니다. 

링크가 회사 네트워크의 내부와 외부에서 동일하게 작동하는지 확인하는 가장 좋은 방법은 앱의 외부 URL을 내부 URL과 동일하도록 구성하는 것입니다. [사용자 지정 도메인](application-proxy-configure-custom-domain.md)을 사용하여 외부 URL이 기본 응용 프로그램 프록시 도메인 대신 회사 도메인 이름을 갖도록 구성합니다.


테넌트에서 사용자 지정 도메인을 사용할 수 없는 경우 이 기능을 제공하기 위한 몇 가지 다른 옵션이 있습니다. 이러한 모든 기능은 사용자 지정 도메인 및 서로 간에 호환이 가능하므로 필요한 경우 다른 솔루션 및 사용자 지정을 구성할 수 있습니다. 

**옵션 1: 관리되는 브라우저 사용** – 이 솔루션은 계획이 사용자가 Intune 관리되는 브라우저를 통해 응용 프로그램에 액세스하도록 요구하거나 권장하는 경우 적용될 수 있습니다. 모든 게시된 URL을 처리합니다. 

**옵션 2: MyApps 확장 사용** – 이 솔루션은 사용자가 클라이언트 브라우저 확장을 설치할 것을 요구하지만 게시된 모든 URL 및 작업을 가장 유명한 브라우저로 처리합니다. 

**옵션 3: 링크 변환 설정 사용** – 이것은 사용자가 볼 수 없는 관리자 쪽 설정입니다. 그러나 HTML 및 CSS의 URL만 처리합니다. Javascript를 통해 생성된 하드 코딩된 내부 URL(예)은 작동하지 않습니다.  

이 세 가지 기능은 사용자가 어디에 있든 관계 없이 링크를 계속 작동시킵니다. 내부 끝점이나 포트를 직접 가리키는 앱을 사용하는 경우 이러한 내부 URL을 게시된 외부 응용 프로그램 프록시 URL에 매핑할 수 있습니다. 

 
> [!NOTE]
> 마지막 옵션은 어떤 이유로든 사용자 정의 도메인을 사용하여 자신의 앱에 대해 내부 및 외부 URL을 동일하게 사용할 수 없는 테넌트에만 해당됩니다. 이 기능을 사용하도록 설정하기 전에 [Azure AD 응용 프로그램 프록시의 사용자 지정 도메인](application-proxy-configure-custom-domain.md)이 작동하는지 확인하세요. 

>또는 링크 변환으로 구성해야 하는 응용 프로그램이 SharePoint인 경우 링크 매핑에 대한 다른 방법으로 [SharePoint 2013에 대한 대체 액세스 매핑 구성](https://technet.microsoft.com/library/cc263208.aspx)을 참조하세요. 

 
### <a name="option-1-intune-managed-browser-integration"></a>옵션 1: Intune 관리 브라우저 통합 

Intune 관리 브라우저를 사용하여 응용 프로그램과 콘텐츠를 더욱 안전하게 보호할 수 있습니다. 이 솔루션을 사용하려면 Intune Managed Browser를 통해 사용자가 응용 프로그램에 액세스하도록 요구/권장해야 합니다. 응용 프로그램 프록시로 게시된 모든 내부 URL은 Managed Browser에서 인식하고 해당 외부 URL로 리디렉션됩니다. 이렇게 하면 모든 하드 코딩된 내부 URL이 작동하고 사용자가 브라우저로 이동하여 내부 URL을 직접 입력하면 사용자가 원격인 경우에도 작동합니다.  

이 옵션을 구성하는 방법을 비롯한 자세한 내용은 [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) 문서를 참조하세요.  

### <a name="option-2-myapps-browser-extension"></a>옵션 2: MyApps 브라우저 확장 

MyApps 브라우저 확장을 사용하여 응용 프로그램 프록시로 게시된 모든 내부 URL은 확장에서 인식하고 해당 외부 URL로 리디렉션됩니다. 이렇게 하면 모든 하드 코딩된 내부 URL이 작동하고 사용자가 브라우저의 주소로 이동하여 내부 URL을 직접 입력하면 사용자가 원격인 경우에도 작동합니다.  

이 기능을 사용하려면 사용자가 확장을 다운로드하고 로그인해야 합니다. 관리자 또는 사용자에게 필요한 다른 구성은 없습니다. 

 

### <a name="option-3-link-translation-setting"></a>옵션 3: 링크 변환 설정 

링크 변환을 사용하도록 설정하면 응용 프로그램 프록시 서비스에서 게시된 내부 링크에 대한 HTML 및 CSS를 검색하고 변환하므로 사용자가 중단 없이 환경을 이용할 수 있습니다. 



## <a name="how-link-translation-works"></a>링크 변환의 작동 방식

인증 후 프록시 서버가 사용자에게 응용 프로그램 데이터를 전달하면 응용 프로그램 프록시는 응용 프로그램에서 하드 코드된 링크를 검색하여 게시된 해당 외부 URL로 바꿉니다.

응용 프로그램 프록시는 응용 프로그램이 UTF-8에서 인코딩되는 것으로 가정합니다. 그렇지 않은 경우 http 응답 헤더의 인코딩 형식을 `Content-Type:text/html;charset=utf-8`과 같이 지정합니다.

### <a name="which-links-are-affected"></a>영향을 받는 링크

링크 변환 기능은 앱 본문의 코드 태그에 있는 링크만 검색합니다. 응용 프로그램 프록시에는 헤더에서 쿠키 또는 URL을 변환하는 별도의 기능이 있습니다. 

온-프레미스 응용 프로그램에는 두 가지 일반적인 유형의 내부 링크가 있습니다.

- **상대 내부 링크** - `/claims/claims.html` 같은 로컬 파일 구조에서 공유 리소스를 가리킵니다. 이러한 링크는 응용 프로그램 프록시를 통해 게시되는 앱에서 자동으로 작동하며 링크 변환과 관계없이 계속 작동합니다. 
- **하드 코드된 내부 링크** - `http://expenses` 같은 다른 온-프레미스 앱이나 `http://expenses/logo.jpg` 같은 게시된 파일에 대한 링크입니다. 링크 변환 기능은 하드 코드된 내부 링크에서 작동하며 해당 링크를 원격 사용자가 통과해야 하는 외부 URL을 가리키도록 변경합니다.

### <a name="how-do-apps-link-to-each-other"></a>앱이 서로 연결되는 방식

링크 변환은 각 응용 프로그램에 대해 사용되므로 앱별 수준에서 사용자 환경을 제어할 수 있습니다. 변환할 앱의 *대상* 링크가 아니라 해당 앱의 *원본* 링크를 원하는 경우 앱에 대해 링크 변환을 설정합니다. 

예를 들어 응용 프로그램 프록시를 통해 게시된 세 개의 응용 프로그램 Benefits, Expenses 및 Travel이 있고 모두 서로 연결되어 있다고 가정합니다. 그리고 응용 프로그램 프록시를 통해 게시되지 않은 네 번째 Feedback 앱이 있습니다.

Benefits 앱에 대해 링크 변환을 사용하도록 설정하면 Expenses 및 Travel에 대한 링크는 해당 앱에 대한 외부 URL로 리디렉션되지만 Feedback에 대한 링크는 외부 URL이 없기 때문에 리디렉션되지 않습니다. Expenses 및 Travel에서 Benefits로 다시 링크는 작동하지 않는데, 이러한 두 앱에 대해서는 링크 변환이 설정되지 않았기 때문입니다.

![링크 변환을 설정한 경우 Benefits에서 다른 앱으로 링크](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>변환되지 않는 링크

성능 및 보안 향상을 위해 일부 링크는 변환되지 않습니다.

- 코드 태그 내부에 있지 않은 링크. 
- HTML 또는 CSS에 있지 않은 링크 
- 다른 프로그램에서 연 내부 링크. 메일이나 인스턴트 메시지를 통해 전송되거나 다른 문서에 포함된 링크는 변환되지 않습니다. 사용자는 외부 URL로 이동됨을 알고 있어야 합니다.

이러한 두 시나리오 중 하나를 지원해야 하는 경우 링크 변환 대신 동일한 외부 및 외부 URL을 사용합니다.  

## <a name="enable-link-translation"></a>링크 변환을 사용하도록 설정

링크 변환 시작은 단추를 클릭하는 것만큼 간단합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **모든 응용 프로그램** > 관리할 앱 선택 > **응용 프로그램 프록시**로 이동합니다.
3. **Translate URLs in application body**(응용 프로그램 본문의 URL 변환)를 **예**로 설정합니다.

   ![[예]를 선택하여 응용 프로그램 본문의 URL 변환](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)에서도 확인할 수 있습니다.
4. **저장**을 선택하여 변경 내용을 적용합니다.

이제 사용자가 이 응용 프로그램에 액세스하면 프록시는 테넌트에서 응용 프로그램 프록시를 통해 게시된 내부 URL을 자동으로 검사합니다.

## <a name="send-feedback"></a>피드백 보내기

이 기능이 모든 앱에서 작동하도록 하는 데 도움이 되도록 피드백을 보내 주세요. HTML 및 CSS에서 30개가 넘는 태그를 검색합니다. 생성된 링크 중 변환되지 않는 예가 있으면 [응용 프로그램 프록시 피드백](mailto:aadapfeedback@microsoft.com)으로 코드 조각을 보내 주세요. 

## <a name="next-steps"></a>다음 단계
동일한 내부 및 외부 URL을 사용하도록 [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 사용](application-proxy-configure-custom-domain.md)

[SharePoint 2013에 대한 대체 액세스 매핑 구성](https://technet.microsoft.com/library/cc263208.aspx)
