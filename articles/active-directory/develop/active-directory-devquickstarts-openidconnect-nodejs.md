---
title: Azure AD Node.js 웹앱 시작 | Microsoft Docs
description: 로그인을 위해 Azure AD와 통합되는 Node.js Express MVC 웹앱을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: nodejs
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 81deecec-dbe2-4e75-8bc0-cf3788645f99
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 04/20/2018
ms.author: celested
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8068e193ac267ef23bdaab1fc23bc7bf9b0d330b
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="azure-ad-nodejs-web-app-getting-started"></a>Azure AD Node.js 웹앱 시작
여기서는 Passport를 통해 다음 작업을 수행합니다.

* 사용자는 Azure AD(Azure Active Directory)로 앱에 로그인합니다.
* 사용자에 대한 정보를 표시합니다.
* 앱에서 사용자를 로그아웃합니다.

Passport는 Node.js에 대한 인증 미들웨어입니다. 유연한 모듈식 Passport는 어떤 Express 기반 또는 Resitify 웹 응용 프로그램에도 원활하게 추가할 수 있습니다. 포괄적인 전략 모음이 사용자 이름 및 암호, Facebook, Twitter 등을 사용하는 인증을 지원합니다. Microsoft는 Microsoft Azure Active Directory에 대한 전략을 개발했습니다. 여기서는 이 모듈을 설치하고 Microsoft Azure Active Directory `passport-azure-ad` 플러그 인을 추가합니다.

이렇게 하려면 다음 단계를 따르세요.

1. 앱을 등록합니다.
2. 앱을 설정하여 `passport-azure-ad` 전략을 사용합니다.
3. Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청을 실행합니다.
4. 사용자에 대한 데이터를 인쇄합니다.

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS)에서 유지 관리됩니다. 자습서에 따라 [.zip 파일로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)하거나 구조를 복제할 수 있습니다.

```git clone --branch skeleton https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

전체 응용 프로그램은 이 자습서 마지막 부분에서도 제공됩니다.

## <a name="step-1-register-an-app"></a>1단계: 앱 등록
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 페이지 위쪽에 있는 메뉴에서 계정을 선택합니다. **디렉터리** 목록에서 응용 프로그램을 등록할 Active Directory 테넌트를 선택합니다.

3. 화면 왼쪽에 있는 메뉴에서 **모든 서비스**를 선택한 다음, **Azure Active Directory**를 선택합니다.

4. **앱 등록**을 선택하고 **추가**를 선택합니다.

5. 프롬프트에 따라 새 **웹 응용 프로그램** 및/또는 **WebAPI**를 만듭니다.
  * 응용 프로그램의 **이름**은 사용자에게 응용 프로그램을 설명합니다.

  * **로그온 URL** 은 앱의 기본 URL입니다. 기본값은 `http://localhost:3000/auth/openid/return`입니다.

6. 등록 후에는 Azure AD가 사용자 앱에 고유한 응용 프로그램 ID를 할당합니다. 이 값은 다음 섹션에서 필요하므로 응용 프로그램 페이지에서 복사해 둡니다.
7. 응용 프로그램에 대한 **설정** -> **속성** 페이지에서 앱 ID URI를 업데이트합니다. **앱 ID URI** 는 응용 프로그램의 고유 식별자입니다. 규칙은 `https://<tenant-domain>/<app-name>` 형식(예: `https://contoso.onmicrosoft.com/my-first-aad-app`)을 사용하는 것입니다.

## <a name="step-2-add-prerequisites-to-your-directory"></a>2단계: 필수 구성 요소를 디렉터리에 추가
1. 명령줄에서 루트 폴더가 없는 경우 디렉터리를 루트 폴더로 변경하고 다음 명령을 실행합니다.

    * `npm install express`
    * `npm install ejs`
    * `npm install ejs-locals`
    * `npm install restify`
    * `npm install mongoose`
    * `npm install bunyan`
    * `npm install assert-plus`
    * `npm install passport`

2. 또한 동일한 `passport-azure-ad`가 필요합니다.
    * `npm install passport-azure-ad`

이는 `passport-azure-ad`가 의존하는 라이브러리를 설치합니다.

## <a name="step-3-set-up-your-app-to-use-the-passport-node-js-strategy"></a>3단계: passport-node-js 전략을 사용하도록 앱 설정
여기서는 OpenID Connect 인증 프로토콜을 사용하도록 Express를 구성합니다. Passport는 로그인 및 로그아웃 요청을 실행하고, 사용자의 세션을 관리하고, 사용자에 대한 정보를 가져오는 데 사용됩니다.

1. 시작하려면 프로젝트의 루트에서 `config.js` 파일을 열고 `exports.creds` 섹션에 앱의 구성 값을 입력합니다.

  * `clientID`는 등록 포털에서 앱에 할당된 **응용 프로그램 ID**입니다.

  * `returnURL`은 포털에서 입력한 **리디렉션 URI**입니다.

  * `clientSecret` 는 포털에서 생성한 비밀입니다.

2. 다음으로 프로젝트의 루트에서 `app.js` 파일을 엽니다. 다음 호출을 추가하여 `passport-azure-ad`과 함께 제공되는 `OIDCStrategy` 전략을 호출합니다.

    ```JavaScript
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // add a logger

    var log = bunyan.createLogger({
        name: 'Microsoft OIDC Example Web Application'
    });
    ```

3. 그런 후에 방금 참조한 전략을 사용하여 로그인 요청을 처리합니다.

    ```JavaScript
    // Use the OIDCStrategy within Passport. (Section 2)
    //
    //   Strategies in passport require a `validate` function that accepts
    //   credentials (in this case, an OpenID identifier), and invokes a callback
    //   with a user object.
    passport.use(new OIDCStrategy({
        callbackURL: config.creds.returnURL,
        realm: config.creds.realm,
        clientID: config.creds.clientID,
        clientSecret: config.creds.clientSecret,
        oidcIssuer: config.creds.issuer,
        identityMetadata: config.creds.identityMetadata,
        skipUserProfile: config.creds.skipUserProfile,
        responseType: config.creds.responseType,
        responseMode: config.creds.responseMode
    },
    function(iss, sub, profile, accessToken, refreshToken, done) {
        if (!profile.email) {
        return done(new Error("No email found"), null);
        }
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
Passport는 모든 전략 작성자가 준수하는 유사한 패턴을 모든 전략(Twitter, Facebook 등)에 대해 사용합니다. 전략을 보면 토큰 및 완료가 매개 변수로 포함된 함수에 전달되는 것을 확인할 수 있습니다. 전략은 작업을 완료한 후에 사용자에게 돌아갑니다. 그러면 사용자를 저장하고 토큰을 다시 요청할 필요가 없도록 보관하려 합니다.

> [!IMPORTANT]
이전 코드는 서버에 인증하는 모든 사용자를 사용합니다. 이를 자동 등록이라고 합니다. 사용자가 결정한 프로세스를 통해 등록하지 않은 프로덕션 서버에는 누구도 인증하지 않는 것이 좋습니다. 이것은 Facebook에 등록할 수 있도록 허용하지만 추가 정보를 입력하도록 요구하는 소비자 앱에서 주로 나타나는 패턴입니다. 응용 프로그램 예제가 아니라면 반환된 토큰 개체에서 사용자의 전자 메일 주소를 추출하고 사용자에게 추가 정보를 입력하도록 요구할 수 있습니다. 테스트 서버이므로 메모리 내 데이터베이스에 추가합니다.


4. 다음에는 Passport에서 요구하는 대로 로그인한 사용자를 추적할 수 있는 메서드를 추가하겠습니다. 이러한 메서드에는 사용자 정보의 직렬화 및 역직렬화가 포함됩니다.

    ```JavaScript

            // Passport session setup. (Section 2)

            //   To support persistent sign-in sessions, Passport needs to be able to
            //   serialize users into the session and deserialize them out of the session. Typically,
            //   this is done simply by storing the user ID when serializing and finding
            //   the user by ID when deserializing.
            passport.serializeUser(function(user, done) {
                done(null, user.email);
            });

            passport.deserializeUser(function(id, done) {
                findByEmail(id, function (err, user) {
                    done(err, user);
                });
            });

            // array to hold signed-in users
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

5. 다음에는 Express 엔진을 로드하는 코드를 추가하겠습니다. 여기서 Express가 제공하는 기본 /views 및 /routes 패턴이 사용되는 것을 확인할 수 있습니다.

    ```JavaScript

        // configure Express (section 2)

            var app = express();
            app.configure(function() {
          app.set('views', __dirname + '/views');
          app.set('view engine', 'ejs');
          app.use(express.logger());
          app.use(express.methodOverride());
          app.use(cookieParser());
          app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
          app.use(bodyParser.urlencoded({ extended : true }));
          // Initialize Passport!  Also use passport.session() middleware, to support
          // persistent login sessions (recommended).
          app.use(passport.initialize());
          app.use(passport.session());
          app.use(app.router);
          app.use(express.static(__dirname + '/../../public'));
        });

    ```

6. 마지막으로 실제 로그인 요청을 `passport-azure-ad` 엔진에 전달하는 경로를 추가합니다.

    ```JavaScript

        // Our Auth routes (section 3)

        // GET /auth/openid
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. The first step in OpenID authentication involves redirecting
        //   the user to their OpenID provider. After authenticating, the OpenID
        //   provider redirects the user back to this application at
        //   /auth/openid/return.
        app.get('/auth/openid',
        passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
        function(req, res) {
            log.info('Authentication was called in the Sample');
            res.redirect('/');
        });

        // GET /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.get('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });

        // POST /auth/openid/return
        //   Use passport.authenticate() as route middleware to authenticate the
        //   request. If authentication fails, the user is redirected back to the
        //   sign-in page. Otherwise, the primary route function is called,
        //   which, in this example, redirects the user to the home page.
        app.post('/auth/openid/return',
          passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
          function(req, res) {
            log.info('We received a return from AzureAD.');
            res.redirect('/');
          });
     ```


## <a name="step-4-use-passport-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>4단계: Passport를 사용하여 Azure AD에 로그인 및 로그아웃 요청 실행
이제 앱이 OpenID Connect 인증 프로토콜을 사용하여 끝점과 통신하도록 올바르게 구성되었습니다. `passport-azure-ad`가 인증 메시지를 작성하고, Azure AD에서 토큰의 유효성을 검사하고, 사용자 세션을 유지 관리하는 모든 세부 과정을 처리했습니다. 이제 사용자에게 로그인 및 로그아웃하는 방법을 알려주고 로그인한 사용자에 대한 추가 정보를 수집하기만 하면 됩니다.

1. 우선 기본값, 로그인, 계정 및 로그아웃 메서드를 `app.js` 파일에 추가해 보겠습니다.

    ```JavaScript

        //Routes (section 4)

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

2. 자세히 살펴보겠습니다.

  * `/` 경로는 요청에 사용자를 전달하여(있는 경우) index.ejs 뷰로 리디렉션됩니다.
  * `/account` 경로는 먼저 *인증되었는지 확인*하고(다음 예제에서 구현) 사용자에 대한 추가 정보를 얻을 수 있도록 요청에 사용자를 전달합니다.
  * `/login` 경로는 `passport-azuread`에서 azuread-openidconnect 인증자를 호출합니다. 성공하지 못하면 경로는 사용자를 다시 /login으로 리디렉션합니다.
  * `/logout` 경로는 쿠키를 지우는 logout.ejs(및 경로)를 호출하고 사용자를 다시 index.ejs로 돌려보냅니다.

3. `app.js`의 마지막 부분을 위해 이전에 나와 있는 것처럼 `/account`에서 사용된 **EnsureAuthenticated** 메서드를 추가하겠습니다.

    ```JavaScript

        // Simple route middleware to ensure user is authenticated. (section 4)

        //   Use this route middleware on any resource that needs to be protected. If
        //   the request is authenticated (typically via a persistent sign-in session),
        //   the request proceeds. Otherwise, the user is redirected to the
        //   sign-in page.
        function ensureAuthenticated(req, res, next) {
          if (req.isAuthenticated()) { return next(); }
          res.redirect('/login')
        }
    ```

4. 마지막으로, `app.js`에서 서버 자체를 만들겠습니다.

```JavaScript

        app.listen(3000);

```


## <a name="step-5-to-display-our-user-in-the-website-create-the-views-and-routes-in-express"></a>5단계: 웹 사이트에서 사용자를 표시하기 위해 Express에서 뷰와 경로 만들기
이제 `app.js`가 완료됩니다. 얻은 정보를 사용자에게 표시할 경로와 뷰를 추가하고 만든 `/logout` 및 `/login` 경로를 처리하기만 하면 됩니다.

1. 루트 디렉터리 아래에 `/routes/index.js` 경로를 만듭니다.

    ```JavaScript
                /*
                 * GET home page.
                 */

                exports.index = function(req, res){
                  res.render('index', { title: 'Express' });
                };
    ```

2. 루트 디렉터리 아래에 `/routes/user.js` 경로를 만듭니다.

    ```JavaScript
                /*
                 * GET users listing.
                 */

                exports.list = function(req, res){
                  res.send("respond with a resource");
                };
    ```

 이를 통해 사용자(있는 경우)를 포함하여 요청을 뷰에 전달합니다.

3. 루트 디렉터리 아래에 `/views/index.ejs` 뷰를 만듭니다. 이는 login 및 logout 메서드를 호출하고 계정 정보를 얻을 수 있게 해주는 간단한 페이지입니다. 요청에 전달되는 사용자가 로그인한 사용자가 있음을 입증하므로 조건부 `if (!user)`를 사용할 수 있습니다.

    ```JavaScript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
        <h2>Hello, <%= user.displayName %>.</h2>
        <a href="/account">Account Info</a></br>
        <a href="/logout">Log Out</a>
    <% } %>
    ```

4. `passport-azure-ad`가 사용자 요청에 포함한 추가 정보를 볼 수 있도록 루트 디렉터리 아래에 `/views/account.ejs` 뷰를 만듭니다.

    ```Javascript
    <% if (!user) { %>
        <h2>Welcome! Please log in.</h2>
        <a href="/login">Log In</a>
    <% } else { %>
    <p>displayName: <%= user.displayName %></p>
    <p>givenName: <%= user.name.givenName %></p>
    <p>familyName: <%= user.name.familyName %></p>
    <p>UPN: <%= user._json.upn %></p>
    <p>Profile ID: <%= user.id %></p>
  ##Next steps  <p>Full Claimes</p>
    <%- JSON.stringify(user) %>
    <p></p>
    <a href="/logout">Log Out</a>
    <% } %>
    ```

5. 레이아웃을 추가하여 모양을 개선합니다. 루트 디렉터리 아래에 '/views/layout.ejs' 뷰를 만듭니다.

    ```HTML

    <!DOCTYPE html>
    <html>
        <head>
            <title>Passport-OpenID Example</title>
        </head>
        <body>
            <% if (!user) { %>
                <p>
                <a href="/">Home</a> |
                <a href="/login">Log In</a>
                </p>
            <% } else { %>
                <p>
                <a href="/">Home</a> |
                <a href="/account">Account</a> |
                <a href="/logout">Log Out</a>
                </p>
            <% } %>
            <%- body %>
        </body>
    </html>
    ```

## <a name="next-steps"></a>다음 단계
마지막으로 앱을 빌드하고 실행합니다. `node app.js`를 실행한 후 `http://localhost:3000`으로 이동합니다.

개인 Microsoft 계정이나 회사 또는 학교 계정으로 로그인하고 /account 목록에 사용자 ID가 반영되는 방식을 확인합니다. 이제 개인 및 회사/학교 계정으로 사용자를 인증할 수 있는 업계 표준 프로토콜로 웹앱이 보안되었습니다.

참조를 위해 완성된 샘플(사용자 구성 값 제외)이 [.zip 파일로 제공](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip)됩니다. 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```

이제 좀 더 고급 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[Azure AD를 사용하여 Web API 보안 유지](active-directory-devquickstarts-webapi-nodejs.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
