---
title: Node.js 웹앱에 로그인 추가 - Azure Active Directory B2C
description: Azure Active Directory B2C를 사용하여 사용자가 로그인하는 Node.js 웹앱을 작성하는 방법입니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 03/10/2017
ms.author: davidmu
ms.openlocfilehash: b10b2338bfbcd449075e612ae00cc23802dce3a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-add-sign-in-to-a-nodejs-web-app"></a>Azure AD B2C: Node.js 웹앱에 로그인 추가

**Passport** 는 Node.js에 대한 인증 미들웨어입니다. 유연한 모듈식 Passport는 어떤 Express 기반 또는 Restify 웹 응용 프로그램에도 원활하게 설치할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다.

Azure AD(Azure Active Directory)의 경우 이 모듈을 설치한 다음 Azure AD `passport-azure-ad` 플러그 인을 추가할 수 있습니다.

그러려면 다음 작업을 수행해야 합니다.

1. Azure AD를 사용하여 응용 프로그램을 등록합니다.
2. 앱을 설정하여 `passport-azure-ad` 플러그 인을 사용합니다.
3. Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청을 실행합니다.
4. 사용자 데이터를 인쇄합니다.

이 자습서에 대한 코드는 [GitHub에서 유지 관리됩니다](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). 자습서에 따라 [.zip 파일로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/master.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

전체 응용 프로그램은 이 자습서 마지막 부분에서 제공됩니다.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다.  디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 [B2C 디렉터리를 만든](active-directory-b2c-get-started.md) 후에 이 가이드를 계속 진행합니다.

## <a name="create-an-application"></a>응용 프로그램 만들기

다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 하나의 논리 앱을 구성하기 때문에 클라이언트 앱과 웹 API는 모두 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

- 응용 프로그램에 **웹앱**/**웹 API**를 포함합니다.
- **회신 URL**로 `http://localhost:3000/auth/openid/return`을 입력합니다. 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호** 를 만들고 복사합니다. 이 시간은 나중에 필요합니다. 참고로 이 값은 사용하기 전에 [XML 이스케이프](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) 되어야 합니다.
- 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 나중에도 필요합니다.

## <a name="create-your-policies"></a>정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 등록, 로그인 및 Facebook을 사용하여 로그인 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)에서 설명한 대로 각 형식에 이 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- 등록 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다.  이러한 정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)를 시작합니다.

## <a name="add-prerequisites-to-your-directory"></a>필수 구성 요소를 디렉터리에 추가합니다.

아직 수행하지 않은 경우 명령줄에서 디렉터리를 루트 폴더로 변경합니다. 다음 명령을 실행합니다.

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

또한 빠른 시작의 골격에 있는 미리 보기에 `passport-azure-ad`를 사용했습니다.

- `npm install passport-azure-ad`

이는 `passport-azure-ad`가 의존하는 라이브러리를 설치합니다.

## <a name="set-up-your-app-to-use-the-passport-nodejs-strategy"></a>Passport-Node.js 전략을 사용하도록 앱을 설정합니다.
OpenID Connect 인증 프로토콜을 사용하도록 빠른 미들웨어를 구성합니다. Passport는 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하며, 사용자에 대한 정보를 가져오는 데 사용됩니다.

프로젝트 루트에 있는 `config.js` 파일을 열고 `exports.creds` 섹션에 앱의 구성 값을 입력합니다.
- `clientID`: 등록 포털에서 앱에 할당된 **응용 프로그램 ID**입니다.
- `returnURL`: 포털에서 입력한 **리디렉션 URI**입니다.
- `tenantName`: 앱의 테넌트 이름(예: **contoso.onmicrosoft.com**)입니다.

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

프로젝트의 루트에서 `app.js` 파일을 엽니다. 다음 호출을 추가하여 `passport-azure-ad`와 함께 제공되는 `OIDCStrategy` 전략을 호출합니다.


```javascript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

로그인 요청을 처리하도록 참조한 전략을 사용합니다.

```javascript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport는 Twitter, Facebook을 포함한 모든 전략에 비슷한 패턴을 사용합니다. 모든 전략 작성자는 이 패턴을 따릅니다. 전략을 보면 매개 변수로 토큰을 가진 `function()` 및 `done`을 전달하는 것을 볼 수 있습니다. 전략은 모든 작업을 완료한 후에 사용자에게 돌아갑니다. 사용자를 저장하고 다시 요청하지 않아도 되도록 토큰을 저장합니다.

> [!IMPORTANT]
앞의 코드는 서버에서 인증한 모든 사용자를 사용합니다. 이는 자동 등록입니다. 프로덕션 서버를 사용하는 경우 설정한 등록 프로세스를 통해 마치지 않으면 사용자를 통과시키지 않으려 합니다. 소비자 앱에서 이 패턴을 종종 볼 수 있습니다. Facebook을 사용하여 등록할 수 있지만 추가 정보를 입력하도록 요구합니다. 응용 프로그램이 샘플이 아니라면 반환된 토큰 개체에서 메일을 추출하고 사용자에게 추가 정보를 입력하도록 요구할 수 있습니다. 테스트 서버이므로 메모리 내 데이터베이스에 사용자를 추가합니다.

Passport의 필요에 따라 로그인한 사용자를 추적할 수 있도록 하는 메서드를 추가합니다. 여기에는 사용자 정보의 직렬화 및 역직렬화가 포함됩니다.

```javascript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
    log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};

```

빠른 엔진을 로드하는 코드를 추가합니다. 다음 예제에서는 빠른 설치가 제공하는 기본값 `/views` 및 `/routes` 패턴의 사용을 확인할 수 있습니다.

```javascript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

실제 로그인 요청을 `passport-azure-ad` 엔진에 전달하는 `POST` 경로를 추가합니다.

```javascript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## <a name="use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행

이제 앱이 OpenID Connect 인증 프로토콜을 사용하여 v2.0 끝점과 통신하도록 올바르게 구성되었습니다. `passport-azure-ad`가 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 세부 과정을 처리했습니다. 이제 사용자에게 로그인 및 로그아웃하는 방법을 알려주고 로그인한 사용자에 대한 추가 정보를 수집하기만 하면 됩니다.

우선 기본값, 로그인, 계정 및 로그아웃 메서드를 `app.js` 파일에 추가합니다.

```javascript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

이러한 메서드를 자세히 검토하려면:
- `/` 경로는 요청에서 사용자를 전달하여(있는 경우) `index.ejs` 뷰로 리디렉션됩니다.
- `/account` 경로는 우선 사용자가 인증되었는지 확인합니다(이에 대한 구현은 아래에 있음). 그런 다음 사용자에 대한 추가 정보를 얻을 수 있도록 요청에 사용자를 전달합니다.
- `/login` 경로는 `passport-azure-ad`에서 `azuread-openidconnect` 인증자를 호출합니다. 성공하지 못하면 경로는 사용자를 다시 `/login`으로 리디렉션합니다.
- `/logout`은 단순히 `logout.ejs`(및 해당 경로)를 호출합니다. 쿠키를 비운 다음 사용자를 다시 `index.ejs`로 반환합니다.


`app.js`의 마지막 부분에 `/account` 경로에 사용되는 `EnsureAuthenticated` 메서드를 추가합니다.

```javascript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

마지막으로, `app.js`에서 서버 자체를 만듭니다.

```javascript

app.listen(3000);

```


## <a name="create-the-views-and-routes-in-express-to-call-your-policies"></a>빠른 실행에서 뷰와 경로를 만들어 정책 호출

이제 `app.js`가 완료되었습니다. 로그인 및 등록 정책을 호출할 수 있도록 하는 경로 및 뷰를 추가해야 합니다. 또한 만든 `/logout` 및 `/login` 경로를 처리합니다.

루트 디렉터리 아래에 `/routes/index.js` 경로를 만듭니다.

```javascript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

루트 디렉터리 아래에 `/routes/user.js` 경로를 만듭니다.

```javascript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

이러한 간단한 경로는 뷰에 요청을 전달합니다. 사용자가 있는 경우 포함됩니다.

루트 디렉터리 아래에 `/views/index.ejs` 뷰를 만듭니다. 로그인 및 로그아웃에 대한 정책을 호출하는 단순한 페이지입니다. 또한 이를 사용하여 계정 정보를 얻을 수 있습니다. 사용자가 로그인한 증명을 제공하는 요청을 통해 전달되기 때문에 조건부 `if (!user)`를 사용할 수 있습니다.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login/?p=your facebook policy">Sign in with Facebook</a>
    <a href="/login/?p=your email sign-in policy">Sign in with email</a>
    <a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
    <h2>Hello, <%= user.displayName %>.</h2>
    <a href="/account">Account info</a></br>
    <a href="/logout">Log out</a>
<% } %>
```

`passport-azure-ad`가 사용자 요청에 포함한 추가 정보를 볼 수 있도록 루트 디렉터리에 `/views/account.ejs` 뷰를 만듭니다.

```javascript
<% if (!user) { %>
    <h2>Welcome! Please sign in.</h2>
    <a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

이제 앱을 작성하고 실행합니다.

`node app.js`를 실행하고 `http://localhost:3000`으로 이동합니다.


전자 메일 또는 Facebook을 사용하여 앱에 등록 또는 로그인합니다. 로그아웃했다가 다른 사용자로 다시 로그인합니다.

## <a name="next-steps"></a>다음 단계

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [.zip 파일로 제공](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip)됩니다. 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음을 시도해 볼 수 있습니다.

[Node.js에서 B2C 모델을 사용하여 웹 API 보안](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->
