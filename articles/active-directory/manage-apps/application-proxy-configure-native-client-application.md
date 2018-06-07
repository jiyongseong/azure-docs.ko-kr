---
title: 네이티브 클라이언트 앱 게시 - Azure AD | Microsoft Docs
description: Azure AD 응용 프로그램 프록시 커넥터와 통신하는 네이티브 클라이언트 앱을 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다.
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
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5c231ce09add63c6e46dee0c76bbe64c438ff820
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>네이티브 클라이언트 앱을 사용하여 프록시 응용 프로그램과 상호 작용하는 방법

웹 응용 프로그램뿐만 아니라 ADAL(Azure AD Authentication Library)로 구성된 네이티브 클라이언트 앱을 게시하는 데 Azure Active Directory 응용 프로그램 프록시를 사용할 수도 있습니다. 네이티브 클라이언트 앱은 장치에 설치되는 반면 웹앱은 브라우저를 통해 액세스되므로 웹앱과 다릅니다. 

응용 프로그램 프록시는 헤더에 전송된 Azure AD가 발급한 토큰을 수락하여 네이티브 클라이언트 앱을 지원합니다. 응용 프로그램 프록시 서비스는 사용자를 대신해서 인증을 수행합니다. 이 솔루션은 인증에 대해 응용 프로그램 토큰을 사용하지 않습니다. 

![최종 사용자, Azure Active Directory 및 게시된 응용 프로그램 간의 관계](./media/application-proxy-configure-native-client-application/richclientflow.png)

인증을 처리하고 다양한 클라이언트 환경을 지원하는 Azure AD 인증 라이브러리를 사용하여 네이티브 응용 프로그램을 게시합니다. 응용 프로그램 프록시는 [Web API 시나리오에 대한 네이티브 응용 프로그램](../develop/active-directory-authentication-scenarios.md#native-application-to-web-api)에 맞습니다. 

이 문서에서는 응용 프로그램 프록시 및 Azure AD 인증 라이브러리를 사용하여 네이티브 응용 프로그램을 게시하는 네 가지 단계를 안내합니다. 

## <a name="step-1-publish-your-application"></a>1단계: 응용 프로그램 게시
다른 응용 프로그램과 마찬가지로 프록시 응용 프로그램을 게시하고 응용 프로그램에 액세스하도록 사용자를 할당합니다. 자세한 내용은 [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](application-proxy-publish-azure-portal.md)를 참조하세요.

## <a name="step-2-configure-your-application"></a>2단계: 응용 프로그램 구성
네이티브 응용 프로그램을 다음과 같이 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **앱 등록**으로 이동합니다.
3. **새 응용 프로그램 등록**을 선택합니다.
4. 응용 프로그램에 대한 이름을 지정하고 응용 프로그램 형식으로 **네이티브**를 선택하고 응용 프로그램에 대한 리디렉션 URI를 제공합니다. 

   ![새 앱 등록 만들기](./media/application-proxy-configure-native-client-application/create.png)
5. **만들기**를 선택합니다.

새 앱 등록 만들기에 대한 자세한 정보는 [Azure Active Directory와 응용 프로그램 통합](./../develop/active-directory-integrating-applications.md)을 참조하세요.


## <a name="step-3-grant-access-to-other-applications"></a>3단계: 다른 응용 프로그램에 액세스 허용
디렉터리에서 다른 응용 프로그램에 노출될 네이티브 응용 프로그램을 사용하도록 설정합니다.

1. 여전히 **앱 등록**에서 방금 만든 새 네이티브 응용 프로그램을 선택합니다.
2. **필요한 권한**을 선택합니다.
3. **추가**를 선택합니다.
4. 첫 번째 단계를 열고 **API를 선택합니다**.
5. 검색 표시줄을 사용하여 첫 번째 섹션에 게시한 응용 프로그램 프록시 앱을 찾습니다. 해당 앱을 선택한 다음 **선택**을 클릭합니다. 

   ![프록시 앱 검색](./media/application-proxy-configure-native-client-application/select_api.png)
6. 두 번째 단계를 열고 **권한을 선택합니다**.
7. 확인란을 사용하여 프록시 응용 프로그램에 대한 네이티브 응용 프로그램 액세스 권한을 부여한 다음 **선택**을 클릭합니다.

   ![프록시 앱에 대한 액세스 권한 부여](./media/application-proxy-configure-native-client-application/select_perms.png)
8. **완료**를 선택합니다.


## <a name="step-4-edit-the-active-directory-authentication-library"></a>4단계: Active Directory 인증 라이브러리 편집
Active Directory 인증 라이브러리(ADAL)의 인증 컨텍스트에서 네이티브 응용 프로그램 코드를 편집하여 다음 텍스트를 포함합니다.

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

샘플 코드의 변수는 다음과 같이 대체되어야 합니다.

* **테넌트 ID**는 Azure Portal에서 찾을 수 있습니다. **Azure Active Directory** > **속성**으로 이동하고 디렉터리 ID를 복사합니다. 
* **외부 URL**은 프록시 응용 프로그램에 입력한 프런트 엔드 URL입니다. 이 값을 찾으려면 프록시 앱의 **응용 프로그램 프록시** 섹션으로 이동합니다.
* 네이티브 앱의 **앱 ID**는 네이티브 응용 프로그램의 **속성** 페이지에서 찾을 수 있습니다.
* **네이티브 앱의 리디렉션 URI**는 네이티브 응용 프로그램의 **리디렉션 URI** 페이지에서 찾을 수 있습니다.

ADAL을 이러한 매개 변수로 편집하면 사용자가 회사 네트워크 외부에 있을 경우 네이티브 클라이언트 앱에 인증할 수 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

네이티브 응용 프로그램 흐름에 대한 자세한 내용은 [Web API에 대한 네이티브 응용 프로그램](../develop/active-directory-authentication-scenarios.md#native-application-to-web-api)을 참조하세요.

[응용 프로그램 프록시에 대한 Single Sign-On](application-proxy-single-sign-on.md) 설정에 대한 자세히 알아보세요.
