## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>사용자 로그인에 MSAL(Microsoft 인증 라이브러리) 사용

1.  `app.js`라는 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 마우스 오른쪽 단추로 클릭하고 `Add` > `New Item` > `JavaScript File`을 선택합니다.
2.  `app.js` 파일에 다음 코드를 추가합니다.

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });
    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - The token type: For loginRedirect, tokenType = "id_token". For acquireTokenRedirect, tokenType:"access_token".
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>추가 정보

사용자가 *‘Call Microsoft Graph API(Microsoft Graph API 호출)’* 단추를 처음으로 클릭하면 `callGraphApi` 메서드는 사용자를 로그인하기 위해 `loginRedirect`를 호출합니다. 이 메서드를 호출한 결과 사용자가 *Microsoft Azure Active Directory v2 끝점*으로 리디렉션되고 여기서 사용자의 자격 증명을 묻고 유효성을 검사합니다. 성공적으로 로그인되면 사용자는 다시 원래 *index.html* 페이지로 리디렉션되고 토큰을 받아 `msal.js`에서 처리되며 토큰에 포함된 정보가 캐시됩니다. 이 토큰은 *ID 토큰*이라고 하며 사용자 표시 이름과 같은 사용자에 대한 기본 정보를 포함합니다. 이 토큰에서 제공하는 데이터를 어떤 용도로든 사용할 계획이면 백 엔드 서버에서 이 토큰의 유효성을 검사하여 토큰이 응용 프로그램의 유효한 사용자에게 발급되었음을 보장하는지 확인해야 합니다.

이 가이드에서 생성하는 SPA는 ID 토큰을 직접 사용하지 않고 대신 `acquireTokenSilent` 및/또는 `acquireTokenRedirect`를 호출하여 Microsoft Graph API를 쿼리하는 데 사용하는 *액세스 토큰*을 가져옵니다. ID 토큰의 유효성을 검사하는 예제가 필요한 경우 GitHub에서 [이](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 예제") 응용 프로그램 예제를 살펴보세요. 이 예제는 토큰 유효성 검사를 위해 ASP.NET Web API를 사용합니다.

#### <a name="getting-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

초기 로그인 후, 사용자는 리소스에 액세스하기 위해 토큰을 요청해야 할 때마다 다시 인증하는 것을 원하지 않으므로 토큰을 획득하는 대부분 경우 *acquireTokenSilent*를 사용해야 합니다. 하지만 사용자가 Azure Active Directory v2 끝점과 강제로 상호 작용하도록 해야 하는 다음과 같은 상황이 있습니다.
-   암호가 만료되어 사용자가 자격 증명을 다시 입력해야 할 수 있습니다.
-   응용 프로그램이 사용자 동의가 필요한 리소스에 액세스를 요청하고 있습니다.
-   2단계 인증이 필요합니다.

*acquireTokenRedirect(scope)*를 호출하면 사용자가 Azure Active Directory v2 끝점(또는 팝업 창의 *acquireTokenPopup(scope)* 결과)으로 리디렉션되며 여기에서 사용자는 자격 증명을 확인하거나 필요한 리소스에 동의하거나 2단계 인증을 완료하여 상호 작용해야 합니다.

#### <a name="getting-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기
` acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginRedirect`(또는 `loginPopup`)가 처음으로 실행된 후 일반적으로 `acquireTokenSilent` 메서드를 사용하여 후속 호출 시 보호되는 리소스에 액세스하는 데 사용되는 토큰을 가져옵니다. 즉, 토큰을 요청하거나 갱신하기 위한 호출이 자동으로 수행됩니다.
일부 경우에는 `acquireTokenSilent`가 실패할 수 있습니다(예를 들어 사용자 암호가 만료된 경우). 응용 프로그램에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1.  즉시 `acquireTokenRedirect`에 대한 호출을 수행합니다. 그러면 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 응용 프로그램에 사용자가 사용할 수 있는 인증되지 않은 콘텐츠가 없는 온라인 응용 프로그램에서 일반적으로 사용됩니다. 이 설정 안내에서 생성하는 예제는 이 패턴을 사용합니다.

2. 또한 응용 프로그램에서는 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 보여줍니다. 따라서 사용자가 로그인할 적절한 시간을 선택하거나 이후에 응용 프로그램이 `acquireTokenSilent`를 다시 시작할 수 있습니다. 이는 사용자가 중단 없이 응용 프로그램의 기능을 사용할 수 있는 경우(예: 응용 프로그램에 사용 가능한 인증되지 않은 콘텐츠가 있는 경우) 일반적으로 사용됩니다. 이 경우 사용자는 보호된 리소스에 액세스하기 위해 로그인하거나, 오래된 정보를 새로 고치는 시점을 결정할 수 있습니다.

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API 호출

`app.js` 파일에 다음 코드를 추가합니다.

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 가이드에서 생성한 응용 프로그램 예제에서는 `callWebApiWithToken()` 메서드를 사용하여 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행한 다음 콘텐츠를 호출자에게 반환합니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 가이드에서 생성한 응용 프로그램 예제의 경우 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 끝점입니다.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>사용자를 로그아웃하는 메서드 추가

`app.js` 파일에 다음 코드를 추가합니다.

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
