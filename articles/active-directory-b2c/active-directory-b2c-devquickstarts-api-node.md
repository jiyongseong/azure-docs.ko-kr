---
title: 'Azure AD B2C: Node.js를 사용하여 Web API 보안 유지 | Microsoft Docs'
description: B2C 테넌트에서 토큰을 수락하는 Node.js Web API를 빌드하는 방법
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: 3ce0e43beec41599c842cd273cb7230660bdf8d8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-secure-a-web-api-by-using-nodejs"></a>Azure AD B2C: Node.js를 사용하여 Web API 보안 유지
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD(Azure Active Directory) B2C로 OAuth 2.0 액세스 토큰을 사용하여 Web API를 보호할 수 있습니다. 이 토큰을 통해 클라이언트 앱이 Azure AD B2C를 사용하여 API에 인증할 수 있습니다. 이 문서에서는 사용자가 태스크를 추가하고 나열할 수 있는 "할 일 모음" API를 만드는 방법을 보여 줍니다. Web API는 Azure AD B2C를 사용하여 보호되며 사용자가 해당 할 일 목록을 관리하도록 인증할 수 있습니다.

> [!NOTE]
> 이 샘플은 [iOS B2C 샘플 응용 프로그램](active-directory-b2c-devquickstarts-ios.md)을 사용하여 연결되도록 작성되었습니다. 현재 연습을 먼저 수행한 다음 해당 샘플도 함께 수행하세요.
>
>

**Passport** 는 Node.js에 대한 인증 미들웨어입니다. 유연한 모듈식 Passport는 어떤 Express 기반 또는 Restify 웹 응용 프로그램에도 원활하게 설치할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Azure AD(Azure Active Directory)에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Azure AD `passport-azure-ad` 플러그 인에 추가합니다.

이 샘플을 수행하려면 다음을 수행해야 합니다.

1. Azure AD에 응용 프로그램을 등록합니다.
2. Passport의 `passport-azure-ad` 플러그 인을 사용하도록 응용 프로그램을 설정합니다.
3. "to-do list" Web API를 호출하도록 클라이언트 응용 프로그램을 구성합니다.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 가져오기
Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다.  디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다.  디렉터리가 없는 경우 계속하기 전에 [B2C 디렉터리를 만듭니다](active-directory-b2c-get-started.md) .

## <a name="create-an-application"></a>응용 프로그램 만들기
다음으로 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 클라이언트 앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

* 응용 프로그램에서 **웹앱/웹 API** 포함
* **회신 URL**로 `http://localhost/TodoListService`을 입력합니다. 이 코드 샘플에 대한 기본 URL입니다.
* 응용 프로그램에 **응용 프로그램 암호** 를 만들고 복사합니다. 이 데이터가 나중에 필요합니다. 참고로 이 값은 사용하기 전에 [XML 이스케이프](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) 되어야 합니다.
* 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 데이터가 나중에 필요합니다.

## <a name="create-your-policies"></a>정책 만들기
Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 등록 및 로그인이라는 두 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다.  세 가지 정책을 만들 때 다음을 확인합니다.

* 등록 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
* 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다.  물론 다른 클레임을 선택할 수 있습니다.
* 각 정책을 만든 후에 **이름** 을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다.  이러한 정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작합니다.

## <a name="download-the-code"></a>코드 다운로드
이 자습서에 대한 코드는 [GitHub에서 유지 관리](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS)됩니다. 진행하면서 샘플을 빌드하기 위해 [골격 프로젝트를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/skeleton.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS.git
```

완성된 앱도 [.zip 파일로 가능](https://github.com/AzureADQuickStarts/B2C-WebAPI-NodeJS/archive/complete.zip)하거나 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

## <a name="download-nodejs-for-your-platform"></a>사용자 플랫폼을 위한 Node.js 다운로드
이 샘플을 사용하려면 작동하는 Node.js 설치가 필요합니다.

[nodejs.org](http://nodejs.org)에서 Node.js를 설치합니다.

## <a name="install-mongodb-for-your-platform"></a>사용자 플랫폼을 위한 MongoDB 설치
이 샘플을 사용하려면 작동하는 MongoDB 설치가 필요합니다. MongoDB를 사용하여 REST API가 서버 인스턴스 간에 지속되도록 할 것입니다.

[mongodb.org](http://www.mongodb.org)에서 MongoDB를 설치합니다.

> [!NOTE]
> 이 연습에서는 연습 과정을 작성할 때의 MongoDB에 대한 기본 설치 및 서버 끝점인 `mongodb://localhost`을(를) 사용한다고 가정합니다.
>
>

## <a name="install-the-restify-modules-in-your-web-api"></a>Web API에 Restify 모듈 설치
REST API를 빌드하는 데 Restify를 사용합니다. Restify는 Express에서 파생된 유연한 최소 Node.js 응용 프로그램 프레임워크입니다. Connect 위에 REST API를 구축하기 위한 강력한 기능 집합을 포함합니다.

### <a name="install-restify"></a>Restify 설치
명령줄에서 해당 디렉터리를 `azuread`로 변경합니다. `azuread` 디렉터리가 존재하지 않는 경우 만듭니다.

`cd azuread` 또는 `mkdir azuread;`

다음 명령을 입력합니다.

`npm install restify`

이 명령은 Restify를 설치합니다.

#### <a name="did-you-get-an-error"></a>오류가 발생했나요?
일부 운영 체제에서 `npm`을 사용하는 경우 오류 `Error: EPERM, chmod '/usr/local/bin/..'` 및 관리자로 계정을 실행하는 요청을 수신할 수 있습니다. 이 문제가 발생하면 `sudo` 명령을 사용하여 더 높은 권한 수준으로 `npm`을 실행하세요.

#### <a name="did-you-get-a-dtrace-error"></a>DTrace 오류가 발생했나요?
Restify를 설치할 때 다음 텍스트와 같은 내용이 표시될 수 있습니다.

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify는 DTrace를 사용하여 REST 호출을 추적하는 강력한 메커니즘을 제공합니다. 그러나 대부분의 운영 체제에서는 DTrace를 사용할 수 없습니다. 이러한 오류는 무시해도 됩니다.

이 명령의 출력은 다음 텍스트와 유사하게 표시됩니다.

    restify@2.6.1 node_modules/restify
    ├── assert-plus@0.1.4
    ├── once@1.3.0
    ├── deep-equal@0.0.0
    ├── escape-regexp-component@1.0.2
    ├── qs@0.6.5
    ├── tunnel-agent@0.3.0
    ├── keep-alive-agent@0.0.1
    ├── lru-cache@2.3.1
    ├── node-uuid@1.4.0
    ├── negotiator@0.3.0
    ├── mime@1.2.11
    ├── semver@2.2.1
    ├── spdy@1.14.12
    ├── backoff@2.3.0
    ├── formidable@1.0.14
    ├── verror@1.3.6 (extsprintf@1.0.2)
    ├── csv@0.3.6
    ├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
    └── bunyan@0.22.0 (mv@0.0.5)

## <a name="install-passport-in-your-web-api"></a>Web API에 Passport 설치
아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

다음 명령을 사용하여 Passport를 설치합니다.

`npm install passport`

이 명령의 출력은 다음 텍스트와 유사합니다.

    passport@0.1.17 node_modules\passport
    ├── pause@0.0.1
    └── pkginfo@0.2.3

## <a name="add-passport-azuread-to-your-web-api"></a>Web API에 passport-azuread 추가
다음으로 Azure AD를 Passport와 함께 연결하는 전략 제품군인 `passport-azuread`를 사용하여 OAuth 전략을 추가합니다. REST API 샘플에서는 이 전략을 전달자 토큰으로 사용합니다.

> [!NOTE]
> OAuth2는 알려진 모든 토큰 유형이 발급될 수 있는 프레임워크를 제공하지만 특정 토큰 유형만 일반적으로 사용할 수 있습니다. 끝점을 보호하기 위한 토큰이 전달자 토큰입니다. 전달자 토큰 형식은 OAuth2에서 가장 널리 발급됩니다. 많은 구현에서 발급되는 유일한 토큰 유형이 전달자 토큰이라고 가정합니다.
>
>

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

다음 명령을 사용하여 Passport `passport-azure-ad` 모듈을 설치합니다.

`npm install passport-azure-ad`

이 명령의 출력은 다음 텍스트와 유사합니다.

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## <a name="add-mongodb-modules-to-your-web-api"></a>Web API에 MongoDB 모듈 추가
이 샘플을 MongoDB를 데이터 저장소로 사용합니다. Mongoose 설치는 모델 및 스키마를 관리하는 데 널리 사용되는 플러그 인입니다.

* `npm install mongoose`

## <a name="install-additional-modules"></a>추가 모듈 설치
다음에는 나머지 필수 모듈을 설치합니다.

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

`node_modules` 디렉터리에 모듈을 설치합니다.

* `npm install assert-plus`
* `npm install ejs`
* `npm install ejs-locals`
* `npm install express`
* `npm install bunyan`

## <a name="create-a-serverjs-file-with-your-dependencies"></a>종속성을 적용하여 server.js 파일 만들기
`server.js` 파일은 Web API 서버에 대한 대부분의 기능을 제공합니다.

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

편집기에서 `server.js` 파일을 만듭니다. 다음 정보를 추가합니다.

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

파일을 저장합니다. 나중에 이 파일로 다시 돌아갈 것입니다.

## <a name="create-a-configjs-file-to-store-your-azure-ad-settings"></a>Azure AD 설정을 저장하기 위한 config.js 파일 만들기
이 코드 파일은 Azure AD 포털의 구성 매개 변수를 `Passport.js` 파일에 전달합니다. 이 연습의 첫 번째 부분에서 포털에 Web API를 추가했을 때 이러한 구성 값을 만들었습니다. 코드를 복사한 후에 이러한 매개 변수 값에 추가할 항목에 대해 설명할 것입니다.

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

편집기에서 `config.js` 파일을 만듭니다. 다음 정보를 추가합니다.

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
clientID: <your client ID for this Web API you created in the portal>
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>', // the Client ID of the application that is calling your API, usually a web API or native client
identityMetadata: 'https://login.microsoftonline.com/<tenant name>/.well-known/openid-configuration', // Make sure you add the B2C tenant name in the <tenant name> area
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>' // This is the policy you'll want to validate against in B2C. Usually this is your Sign-in policy (as users sign in to this API)
passReqToCallback: false // This is a node.js construct that lets you pass the req all the way back to any upstream caller. We turn this off as there is no upstream caller.
};

```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="required-values"></a>필요한 값
`clientID`: 웹 API 응용 프로그램의 클라이언트 ID입니다.

`IdentityMetadata`: `passport-azure-ad`가 ID 공급자에 대한 구성 데이터를 찾는 위치입니다. JSON 웹 토큰의 유효성을 검사하는 키도 찾습니다.

`audience`: 응용 프로그램 호출을 식별하는 포털의 URI(Uniform Resource Identifier)입니다.

`tenantName`: 해당 테넌트 이름입니다(예: **contoso.onmicrosoft.com**).

`policyName`: 서버로 들어오는 토큰의 유효성을 검사하려는 정책입니다. 이 정책은 클라이언트 응용 프로그램에서 로그인에 사용하는 것과 동일한 정책이어야 합니다.

> [!NOTE]
> 이제 클라이언트와 서버 설정 모두에서 동일한 정책을 사용합니다. 이미 연습 단계를 완료하고 이러한 정책을 만든 경우에는 다시 작업을 수행하지 않아도 됩니다. 연습 단계를 완료했으므로 사이트에서 클라이언트 연습에 대해 새 정책을 설정할 필요가 없습니다.
>
>

## <a name="add-configuration-to-your-serverjs-file"></a>server.js 파일에 구성 추가
만든 `config.js` 파일에서 값을 참고하려면 응용 프로그램에 필수 리소스로 `.config` 파일을 추가하고 전역 변수를 `config.js` 문서의 변수로 설정합니다.

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

편집기에서 `server.js` 파일을 엽니다. 다음 정보를 추가합니다.

```Javascript
var config = require('./config');
```
다음 코드를 포함하는 `server.js`에 새 섹션을 추가합니다.

```Javascript
// We pass these options in to the ODICBearerStrategy.

var options = {
    // The URL of the metadata document for your app. We put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience,
    passReqToCallback: config.creds.passReqToCallback

};
```

다음으로 응용 프로그램 호출에서 수신한 사용자에 몇 가지 자리 표시자를 추가해 보겠습니다.

```Javascript
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
```

계속해서 로거를 만들어 보겠습니다.

```Javascript
// Our logger
var log = bunyan.createLogger({
    name: 'Microsoft Azure Active Directory Sample'
});
```

## <a name="add-the-mongodb-model-and-schema-information-by-using-mongoose"></a>Moongoose를 사용하여 MongoDB 모델 및 스키마 정보 추가
REST API 서비스에서 이러한 세 파일을 함께 가져옴에 따라 이전 준비가 성공합니다.

이 연습의 경우 앞에서 설명한 대로 MongoDB를 사용하여 작업을 저장합니다.

`config.js` 파일에서 데이터베이스 **tasklist**를 호출했습니다. 해당 이름은 `mongoose_auth_local` 연결 URL의 끝에도 배치했습니다. MongoDB에서 이 데이터베이스를 미리 만들 필요가 없습니다. 서버 응용 프로그램을 처음으로 실행할 경우 사용자에 대한 데이터베이스를 만듭니다.

사용하려는 MongoDB 데이터베이스를 서버에 알린 후에는 일부 추가 코드를 작성하여 서버 작업에 대한 모델 및 스키마를 만들어야 합니다.

### <a name="expand-the-model"></a>모델 확장
이 스키마 모델은 간단합니다. 필요에 따라 확장할 수 있습니다.

`owner`: 작업에 할당된 사람입니다. 이 개체는 **문자열**입니다.  

`Text`: 작업 자체입니다. 이 개체는 **문자열**입니다.

`date`: 작업이 만료되는 날짜입니다. 이 개체는 **날짜/시간**입니다.

`completed`: 작업이 완료된 경우입니다. 이 개체는 **부울**입니다.

### <a name="create-the-schema-in-the-code"></a>코드에 스키마 만들기
아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

편집기에서 `server.js` 파일을 엽니다. 구성 항목 아래에 다음 정보를 추가합니다.

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 3000; // Note we are hosting our API on port 3000
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');

// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
    owner: String,
    Text: String,
    completed: Boolean,
    date: Date
});

// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
먼저 스키마를 만든 후 **경로**를 정의할 때 코드 전체에 데이터를 저장하는 데 사용할 모델 개체를 만듭니다.

## <a name="add-routes-for-your-rest-api-task-server"></a>REST API 작업 서버에 대한 경로 추가
작업할 데이터베이스 모델이 준비되었으므로 REST API 서버에 사용할 경로를 추가하겠습니다.

### <a name="about-routes-in-restify"></a>Restify의 경로 정보
경로는 Express 스택을 사용할 때 작업하는 것과 동일한 방식으로 Restify에서 작동합니다. 클라이언트 응용 프로그램이 호출할 것으로 예상하는 URI를 사용하여 경로를 정의합니다.

Restify 경로의 일반적인 패턴:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Resitfy 및 Express는 응용 프로그램 유형 정의 및 여러 다른 끝점에서 복잡한 라우팅 수행 등의 훨씬 더 수준 높은 기능을 제공합니다. 이 자습서의 목적에 따라 이러한 경로를 간단하게 유지합니다.

#### <a name="add-default-routes-to-your-server"></a>서버에 기본 경로 추가
이제 REST API에 대한 **만들기** 및 **나열**이라는 기본 CRUD 경로를 추가합니다. 다른 경로는 샘플의 `complete` 분기에서 찾을 수 있습니다.

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

편집기에서 `server.js` 파일을 엽니다. 위에서 작성한 데이터베이스 항목 아래에 다음 정보를 추가합니다.

```Javascript
/**
 *
 * APIs for our REST Task server
 */

// Create a task

function createTask(req, res, next) {

    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
    var _task = new Task();

    if (!req.params.Text) {
        req.log.warn({
            params: req.params
        }, 'createTodo: missing task');
        next(new MissingTaskError());
        return;
    }

    _task.owner = owner;
    _task.Text = req.params.Text;
    _task.date = new Date();

    _task.save(function(err) {
        if (err) {
            req.log.warn(err, 'createTask: unable to save');
            next(err);
        } else {
            res.send(201, _task);

        }
    });

    return next();

}
```

```Javascript
/// Simple returns the list of TODOs that were loaded.

function listTasks(req, res, next) {
    // Resitify currently has a bug which doesn't allow you to set default headers
    // This headers comply with CORS and allow us to mongodbServer our response to any origin

    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "X-Requested-With");

    log.info("listTasks was called for: ", owner);

    Task.find({
        owner: owner
    }).limit(20).sort('date').exec(function(err, data) {

        if (err)
            return next(err);

        if (data.length > 0) {
            log.info(data);
        }

        if (!data.length) {
            log.warn(err, "There is no tasks in the database. Add one!");
        }

        if (!owner) {
            log.warn(err, "You did not pass an owner when listing tasks.");
        } else {

            res.json(data);

        }
    });

    return next();
}
```


#### <a name="add-error-handling-for-the-routes"></a>경로에 대한 오류 처리 추가
일부 오류 처리를 추가하여 클라이언트에 다시 발생할 수 있는 문제를 이해할 수 있는 방식으로 통신할 수 있도록 합니다.

다음 코드를 추가합니다.

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## <a name="create-your-server"></a>서버 만들기
이제 데이터베이스를 정의하고 경로를 적용했습니다. 마지막으로 할 일은 호출을 관리할 서버 인스턴스를 추가하는 것입니다.

Restify 및 Express는 REST API 서버에 대한 세부적인 사용자 지정 기능을 제공하지만 여기서는 가장 기본적인 설정을 사용합니다.

```Javascript

/**
 * Our Server
 */


var server = restify.createServer({
    name: "Microsoft Azure Active Directroy TODO Server",
    version: "2.0.1"
});

// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());

// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());

// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());

// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());

// Allow 5 requests/second by IP, and burst to 10
server.use(restify.throttle({
    burst: 10,
    rate: 5,
    ip: true,
}));

// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
    mapParams: true
})); // Allows for JSON mapping to REST
server.use(restify.authorizationParser()); // Looks for authorization headers

// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support


```
## <a name="add-the-routes-to-the-server-without-authentication"></a>서버에 경로 추가(인증 없이)
```Javascript
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), listTasks);
server.get('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.head('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), getTask);
server.post('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.post('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), createTask);
server.del('/api/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks/:owner', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeTask);
server.del('/api/tasks', passport.authenticate('oauth-bearer', {
    session: false
}), removeAll, function respond(req, res, next) {
    res.send(204);
    next();
});


// Register a default '/' handler

server.get('/', function root(req, res, next) {
    var routes = [
        'GET     /',
        'POST    /api/tasks/:owner/:task',
        'POST    /api/tasks (for JSON body)',
        'GET     /api/tasks',
        'PUT     /api/tasks/:owner',
        'GET     /api/tasks/:owner',
        'DELETE  /api/tasks/:owner/:task'
    ];
    res.send(200, routes);
    next();
});
```

```Javascript

server.listen(serverPort, function() {

    var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
    consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
    consoleMessage += '\n %s server is listening at %s';
    consoleMessage += '\n Open your browser to %s/api/tasks\n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
    consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
    consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';

    //log.info(consoleMessage, server.name, server.url, server.url, server.url);

});

```

## <a name="add-authentication-to-your-rest-api-server"></a>REST API 서버에 인증 추가
이제 실행 중인 REST API 서버가 있고 Azure AD에 대해 유용하게 만들 수 있습니다.

아직 없는 경우 명령줄에서 해당 디렉터리를 `azuread`로 변경합니다.

`cd azuread`

### <a name="use-the-oidcbearerstrategy-that-is-included-with-passport-azure-ad"></a>passport-azure-ad에 포함된 OIDCBearerStrategy 사용
> [!TIP]
> API를 작성하는 경우 항상 사용자가 스푸핑할 수 없는 토큰의 고유한 항목에 데이터를 연결해야 합니다. 이 서버는 ToDo 항목을 저장할 때 "소유자" 필드에 넣은 토큰(token.oid를 통해 호출됨)에서 사용자의 **oid** 를 기준으로 항목을 저장합니다. 이 값은 해당 사용자만 자신의 할 일 항목에 액세스할 수 있도록 합니다. API에 "owner"가 노출되지 않으므로 외부 사용자가 인증된 경우에도 다른 사용자의 ToDo 항목을 요청할 수 있습니다.
>
>

다음으로 `passport-azure-ad`와 함께 전달자 전략을 사용합니다.

```Javascript
var findById = function(id, fn) {
    for (var i = 0, len = users.length; i < len; i++) {
        var user = users[i];
        if (user.oid === id) {
            log.info('Found user: ', user);
            return fn(null, user);
        }
    }
    return fn(null, null);
};


var oidcStrategy = new OIDCBearerStrategy(options,
    function(token, done) {
        log.info('verifying the user');
        log.info(token, 'was the token retreived');
        findById(token.sub, function(err, user) {
            if (err) {
                return done(err);
            }
            if (!user) {
                // "Auto-registration"
                log.info('User was added automatically as they were new. Their sub is: ', token.oid);
                users.push(token);
                owner = token.oid;
                return done(null, token);
            }
            owner = token.sub;
            return done(null, user, token);
        });
    }
);

passport.use(oidcStrategy);
```

Passport는 모든 전략에 동일한 패턴을 사용합니다. 사용자는 이를 `token` 및 `done`을 매개 변수로 포함하는 `function()`에 전달합니다. 전략은 모든 작업을 완료한 후에 사용자에게 돌아갑니다. 그런 다음 사용자를 저장하고 다시 요청하지 않아도 되도록 토큰을 저장합니다.

> [!IMPORTANT]
> 위 코드는 서버에 인증하는 모든 사용자를 사용합니다. 이 프로세스를 자동 등록이라고 합니다. 프로덕션 서버에서는 등록 프로세스를 먼저 통과하지 않으면 사용자에게 API 액세스를 허용하지 않습니다. 일반적으로 이 프로세스는 Facebook을 사용하여 등록할 수 있도록 하지만 추가 정보를 입력하도록 요구하는 소비자 앱에서 나타나는 패턴입니다. 이 프로그램이 명령줄 프로그램이 아니라면 반환된 토큰 개체에서 메일을 추출하고 사용자에게 추가 정보를 입력하도록 요구할 수 있습니다. 샘플이기 때문에 메모리 내 데이터베이스에 추가합니다.
>
>

## <a name="run-your-server-application-to-verify-that-it-rejects-you"></a>서버 응용 프로그램을 실행하고 사용자를 거부하는지 확인합니다.
`curl`을 사용하여 끝점에 대해 OAuth2 보호가 적용되는지 확인합니다. 반환되는 헤더는 올바른 경로에 있는지 알려줄 만큼 충분해야 합니다.

MongoDB 인스턴스가 실행되고 있는지 확인합니다.

    $sudo mongodb

디렉터리로 변경하고 서버를 실행합니다.

    $ cd azuread
    $ node server.js

새 터미널 창에서 `curl`을 실행합니다.

기본 POST를 시도합니다.

`$ curl -isS -X POST http://127.0.0.1:3000/api/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

401 오류는 원하는 응답입니다. Passport 계층이 권한 부여 끝점으로 리디렉션을 시도함을 나타냅니다.

## <a name="you-now-have-a-rest-api-service-that-uses-oauth2"></a>이제 OAuth2를 사용하는 REST API 서비스가 있습니다.
Restify 및 OAuth를 사용하여 REST API를 구현했습니다. 이제 충분한 코드가 있으므로 계속해서 서비스를 개발하고 이 예제를 빌드할 수 있습니다. OAuth2 호환 클라이언트를 사용하지 않고 이 서버로 수행할 수 있는 작업은 모두 완료했습니다. 이를 위해 다음 단계에서는 [B2C를 포함한 iOS를 사용하여 웹 API에 연결](active-directory-b2c-devquickstarts-ios.md) 연습과 같은 추가 연습을 사용합니다.

## <a name="next-steps"></a>다음 단계
이제 다음과 같이 좀 더 고급 항목으로 이동할 수 있습니다.

[B2C로 iOS를 사용하여 Web API에 연결](active-directory-b2c-devquickstarts-ios.md)
