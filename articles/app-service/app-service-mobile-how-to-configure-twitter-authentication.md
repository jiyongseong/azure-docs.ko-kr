---
title: App Services 응용 프로그램에 대해 Twitter 인증을 구성하는 방법
description: App Services 응용 프로그램에 대해 Twitter 인증을 구성하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.openlocfilehash: f6449f99fda9c1a612ed9f9134751ff76b25904c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Twitter 로그인을 사용하도록 App Service 응용 프로그램을 구성하는 방법
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

이 항목에서는 Twitter를 인증 공급자로 사용하도록 Azure App Service를 구성하는 방법을 보여 줍니다.

이 항목의 절차를 완료하려면 검증된 전자 메일 주소 및 전화 번호가 포함된 Twitter 계정이 있어야 합니다. 새 Twitter 계정을 만들려면 <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>으로 이동합니다.

## <a name="register"> </a>Twitter를 사용하여 응용 프로그램 등록
1. [Azure 포털]에 로그인한 다음 응용 프로그램으로 이동합니다. **URL**을 복사합니다. Twitter 앱을 구성하는 데 사용합니다.
2. [Twitter 개발자] 웹 사이트로 이동하고 Twitter 계정 자격 증명을 사용하여 로그인한 다음 **새 앱 만들기**를 클릭합니다.
3. 새 앱에 대한 **이름** 및 **설명**을 입력합니다. **Website** 값으로 응용 프로그램의 **URL**을 붙여 넣습니다. 그런 다음에 **Callback URL**에 앞서 복사한 **Callback URL**을 붙여 넣습니다. 이는 */.auth/login/twitter/callback*경로를 사용하여 추가된 모바일 앱 게이트웨이입니다. 예: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. HTTPS 체계를 사용 중인지 확인합니다.
4. 페이지 맨 아래에서 사용 약관을 읽고 동의합니다. 그런 다음 **Create your Twitter application**을 클릭합니다. 앱이 등록되고 응용 프로그램 세부 정보가 표시됩니다.
5. **Settings** 탭을 클릭하고 **Allow this application to be used to sign in with Twitter**를 선택한 다음 **Update Settings**를 클릭합니다.
6. **Keys and Access Tokens** 탭을 선택합니다. **Consumer Key (API Key)** 및 **Consumer secret (API Secret)** 의 값을 적어 둡니다.
   
   > [!NOTE]
   > 소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.
   > 
   > 

## <a name="secrets"> </a>응용 프로그램에 Twitter 정보 추가
1. [Azure 포털]로 돌아가서 응용 프로그램으로 이동합니다. **Settings**를 클릭한 다음 **Authentication / Authorization**을 클릭합니다.
2. 인증/권한 부여 기능이 사용하도록 설정되지 않은 경우 스위치를 **On**으로 전환합니다.
3. **Twitter**를 클릭합니다. 앞에서 얻은 App ID 및 App Secret 값을 붙여넣습니다. 그런 후 **OK**를 클릭합니다.
   
   ![][1]
   
   기본적으로 App Service는 인증을 제공하지만 사이트 콘텐츠 및 API에 액세스하는 권한을 제한하지는 않습니다. 앱 코드에서 사용자 권한을 부여해야 합니다.
4. (선택 사항) Twitter에서 인증된 사용자만 사이트에 액세스하도록 제한하려면 **Twitter**에 **요청이 인증되지 않으면 수행할 동작**을 설정합니다. 이렇게 하려면 모든 요청이 인증되어야 하며 모든 인증되지 않은 요청은 인증을 위해 Twitter에 리디렉션되어야 합니다.
5. **저장**을 클릭합니다.

이제 앱에서 Twitter를 인증에 사용할 준비가 되었습니다.

## <a name="related-content"> </a>관련 콘텐츠
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 개발자]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure 포털]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
