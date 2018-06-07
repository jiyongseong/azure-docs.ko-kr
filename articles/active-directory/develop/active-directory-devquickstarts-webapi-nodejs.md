---
title: Azure AD Node.js Web API 시작 | Microsoft Docs
description: 인증을 위해 Azure AD와 통합되는 Node.js REST Web API를 빌드하는 방법.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
ms.assetid: 7654ab4c-4489-4ea5-aba9-d7cdc256e42a
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 24591c46858970724fbd1fe36336f7f2b0b2fefd
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-api-getting-started"></a>Azure AD Node.js Web API 시작

이 문서에서는 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) 모듈을 사용하여 AAD(Azure Active Directory)와의 통신을 처리함으로써 [Passport](http://passportjs.org/)로 [Restify](http://restify.com/) API 끝점의 보안을 유지하는 방법을 보여 줍니다. 

이 자습서에서는 API 끝점 보안 유지와 관련된 내용을 소개합니다. 로그인 및 인증 토큰 유지는 여기에서 구현되지 않으며 클라이언트 응용 프로그램에서 수행합니다. 클라이언트 구현에 대한 자세한 내용은 [Azure AD에서 Node.js 웹앱 로그인 및 로그아웃](active-directory-devquickstarts-openidconnect-nodejs.md)을 참조하세요.

이 문서와 연결된 전체 코드 샘플은 [GitHub](https://github.com/Azure-Samples/active-directory-node-webapi-basic)에서 사용할 수 있습니다.

## <a name="create-the-sample-project"></a>샘플 프로젝트 만들기
이 서버 응용 프로그램에는 AAD 전달되는 계정 정보 뿐만 아니라 Restify 및 Passport를 지원하기 위한 몇 가지 패키지 종속성이 필요합니다.

먼저 `package.json` 파일에 다음 코드를 추가합니다.

```Shell
{
  "name": "node-aad-demo",
  "version": "0.0.1",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "passport": "0.4.0",
    "passport-azure-ad": "3.0.8",
    "restify": "6.0.1",
    "restify-plugins": "1.6.0"
  }
}
```

`package.json`이 만들어지면 명령 프롬프트에서 `npm install`을 실행하여 패키지 종속성을 설치합니다. 

### <a name="configure-the-project-to-use-active-directory"></a>Active Directory를 사용하도록 프로젝트 구성

응용 프로그램 구성을 시작하기 위해 Azure CLI에서 가져올 수는 몇 가지 계정 관련 값 있습니다. CLI을 시작하는 가장 쉬운 방법은 Azure Cloud Shell을 사용하는 것입니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Cloud Shell에서 다음 명령을 입력합니다. 

```azurecli-interactive
az ad app create --display-name node-aad-demo --homepage http://localhost --identifier-uris http://node-aad-demo
```

`create` 명령의 [인수](/cli/azure/ad/app?view=azure-cli-latest#az_ad_app_create)에는 다음이 포함됩니다.

| 인수  | 설명 |
|---------|---------|
|`display-name` | 등록의 이름 |
|`homepage` | 사용자가 로그인하여 응용 프로그램을 사용할 수 있는 URL |
|`identifier-uris` | Azure AD가 이 응용 프로그램에 사용할 수 있는 공백으로 구분된 고유 URI |

Azure Active Directory에 연결하려면 다음 정보가 필요합니다.

| Name  | 설명 | 구성 파일의 변수 이름 |
| ------------- | ------------- | ------------- |
| 테넌트 이름  | 인증에 사용할 [테넌트 이름](active-directory-howto-tenant.md) | `tenantName`  |
| 클라이언트 ID  | 클라이언트 ID는 AAD _응용 프로그램 ID_에 사용되는 OAuth 용어입니다. |  `clientID`  |

Azure Cloud Shell의 등록 응답에서 `appId` 값을 복사하고 `config.js`라는 새 파일을 만듭니다. 그런 후에는 다음 코드를 추가하고 사용자의 값을 대괄호로 묶은 토큰으로 바꿉니다.

```JavaScript
const tenantName    = //<YOUR_TENANT_NAME>;
const clientID      = //<YOUR_APP_ID_FROM_CLOUD_SHELL>;
const serverPort    = 3000;

module.exports.serverPort = serverPort;

module.exports.credentials = {
  identityMetadata: `https://login.microsoftonline.com/${tenantName}.onmicrosoft.com/.well-known/openid-configuration`, 
  clientID: clientID
};
```
개별 구성 설정에 대한 자세한 내용은 [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) 모듈 설명서를 검토하세요.

## <a name="implement-the-server"></a>서버 구현
[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad#5-usage) 모듈은 [OIDC](https://github.com/AzureAD/passport-azure-ad#51-oidcstrategy) 및 [전달자](https://github.com/AzureAD/passport-azure-ad#52-bearerstrategy)의 두 가지 인증 전략을 제공합니다. 이 문서에 구현된 서버는 전달자 전략을 사용하여 API 끝점을 보호합니다.

### <a name="step-1-import-dependencies"></a>1단계: 종속성 가져오기
`app.js`라는 새 파일을 만들고 다음 텍스트를 붙여 넣습니다.

```JavaScript
const
      restify = require('restify')
    , restifyPlugins = require('restify-plugins')
    , passport = require('passport')
    , BearerStrategy = require('passport-azure-ad').BearerStrategy
    , config = require('./config')
    , authenticatedUserTokens = []
    , serverPort = process.env.PORT || config.serverPort
;
```

코드의 섹션 내용:

- `restify` 및 `restify-plugins` 모듈은 Restify 서버를 설정하기 위해 참조됩니다.

- `passport` 및 `passport-azure-ad` 모듈은 AAD와의 통신을 담당합니다.

- `config` 변수는 이전 단계에서 만든 `config.js` 파일의 값으로 초기화됩니다.

- 사용자 토큰이 보안 끝점으로 전달될 때 `authenticatedUserTokens`에서 사용자 토큰을 저장하기 위한 배열이 만들어집니다.

- `serverPort`는 프로세스 환경 포트 또는 구성 파일에서 정의됩니다.

### <a name="step-2-instantiate-an-authentication-strategy"></a>2단계: 인증 전략 인스턴스화
끝점을 보호할 때는 현재 요청에서 인증된 사용자로부터 시작되었는지 여부를 확인하는 전략을 제공해야 합니다. 여기서 `authenticatonStrategy` 변수는 `passport-azure-ad` `BearerStrategy` 클래스의 인스턴스입니다. `require` 문 뒤에 다음 코드를 추가합니다.

```JavaScript
const authenticationStrategy = new BearerStrategy(config.credentials, (token, done) => {
    let currentUser = null;

    let userToken = authenticatedUserTokens.find((user) => {
        currentUser = user;
        user.sub === token.sub;
    });

    if(!userToken) {
        authenticatedUserTokens.push(token);
    }

    return done(null, currentUser, token);
});
```
이 구현은 `authenticatedUserTokens` 배열에 인증 토큰이 아직 없는 경우 추가하여 자동 등록을 사용합니다.

이 전략의 새 인스턴스가 만들어지면 `use` 메서드를 통해 Passport로 전달해야 합니다. `app.js`에 다음 코드를 추가하여 Passport에서 해당 전략을 사용합니다.

```JavaScript
passport.use(authenticationStrategy);
```

### <a name="step-3-server-configuration"></a>3단계: 서버 구성
인증 전략이 정의되었으므로 이제 몇 가지 기본적인 설정을 사용하여 Restify 서버를 설정하고 보안을 위해 Passport를 사용하도록 설정할 수 있습니다.

```JavaScript
const server = restify.createServer({ name: 'Azure Active Directroy with Node.js Demo' });
server.use(restifyPlugins.authorizationParser());
server.use(passport.initialize());
server.use(passport.session());
```
이 서버는 초기화되고 인증 헤더를 구문 분석하도록 구성된 후 Passport를 사용하도록 설정됩니다.


### <a name="step-4-define-routes"></a>4단계: 경로 정의
이제 경로를 정의하고 AAD를 사용하여 보안을 유지할 항목을 결정할 수 있습니다. 이 프로젝트에는 루트 수준이 열려 있고 `/api` 경로가 인증을 요구하도록 설정된 2개의 경로가 포함되어 있습니다.

`app.js`에서 루트 수준 경로에 대해 다음 코드를 추가합니다.

```JavaScript
server.get('/', (req, res, next) => {
    res.send(200, 'Try: curl -isS -X GET http://127.0.0.1:3000/api');
    next();
});
```

루트 경로는 모든 요청이 경로를 통과하도록 허용하고, `/api` 경로를 테스트하기 위한 명령을 포함하는 메시지를 반환합니다. 반면 `/api` 경로는 [`passport.authenticate`](http://passportjs.org/docs/authenticate)를 사용하여 잠겨집니다. 루트 경로 후에 다음 코드를 추가합니다.

```JavaScript
server.get('/api', passport.authenticate('oauth-bearer', { session: false }), (req, res, next) => {
    res.json({ message: 'response from API endpoint' });
    return next();
});
```

이 구성은 `/api`에 대한 전달자 토큰 액세스를 포함하는 인증된 요청만 허용합니다. 옵션 `session: false`는 세션이 API에 대한 각 요청 시 토큰이 전달되도록 요구하지 못하도록 하는 데 사용됩니다.

마지막으로 `listen` 메서드를 호출하여 서버가 구성된 포트에서 수신 대기하도록 설정합니다.

```JavaScript
server.listen(serverPort);
```

## <a name="run-the-sample"></a>샘플 실행

서버가 구현되었으므로 명령 프롬프트를 열고 다음을 입력하여 서버를 시작할 수 있습니다.

```Shell
npm start
```

서버가 실행되는 동안 서버에 요청을 제출하여 결과를 테스트할 수 있습니다. 루트 경로의 응답을 보여 주기 위해 bash 셸을 열고 다음 코드를 입력합니다.

```Shell 
curl -isS -X GET http://127.0.0.1:3000/
```

서버를 올바르게 구성한 경우 응답은 다음과 비슷해야 합니다.

```Shell
HTTP/1.1 200 OK
Server: Azure Active Directroy with Node.js Demo
Content-Type: application/json
Content-Length: 49
Date: Tue, 10 Oct 2017 18:35:13 GMT
Connection: keep-alive

Try: curl -isS -X GET http://127.0.0.1:3000/api
```

다음으로 bash 셸에 다음 명령을 입력하여 인증을 요구하는 경로를 테스트할 수 있습니다.

```Shell 
curl -isS -X GET http://127.0.0.1:3000/api
```

서버를 올바르게 구성한 경우 서버가 `Unauthorized` 상태로 응답해야 합니다.

```Shell
HTTP/1.1 401 Unauthorized
Server: Azure Active Directroy with Node.js Demo
WWW-Authenticate: token is not found
Date: Tue, 10 Oct 2017 16:22:03 GMT
Connection: keep-alive
Content-Length: 12

Unauthorized
```
보안 API를 만들었으므로 API에 인증 토큰을 전달할 수 있는 클라이언트를 구현할 수 있습니다.

## <a name="next-steps"></a>다음 단계
소개에서 언급한 것처럼 로그인, 로그아웃 및 토큰 관리를 처리하는 서버에 연결하려면 해당 클라이언트를 구현해야 합니다. 코드 기반 예제를 보려면 [iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios) 및 [Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)에서 클라이언트 응용 프로그램을 참조하세요. 단계별 자습서를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Azure AD에서 Node.js 웹앱 로그인 및 로그아웃](active-directory-devquickstarts-openidconnect-nodejs.md)