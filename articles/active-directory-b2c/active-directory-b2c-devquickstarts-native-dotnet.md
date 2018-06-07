---
title: .NET Azure Active Directory B2C 인증, 등록, 편집 프로필 | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 로그인, 등록 및 프로필 관리를 포함하는 Windows 데스크톱 응용 프로그램을 빌드하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: .net
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/07/2017
ms.author: davidmu
ms.openlocfilehash: ec679115259611eb05021a68a552d25777a73356
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-build-a-windows-desktop-app"></a>Azure AD B2C: Windows 데스크톱 앱 빌드
Azure AD(Azure Active Directory) B2C를 사용하여 몇 가지 간단한 단계에서 강력한 셀프 서비스 ID 관리 기능을 데스크톱 앱에 추가할 수 있습니다. 이 문서에서는 사용자 등록, 로그인 및 프로필 관리를 포함하는 .NET WPF(Windows Presentation Foundation) "할 일 모음" 앱을 만드는 방법을 보여 줍니다. 이 앱에서는 사용자 이름 또는 전자 메일을 사용하여 등록 및 로그인할 수 있습니다. 또한 Facebook 및 Google과 같은 소셜 계정을 사용하여 등록 및 로그인하는 기능도 지원합니다.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 가져오기
Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다.  디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 아직 없는 경우 [B2C 디렉터리를 만든](active-directory-b2c-get-started.md) 후에 이 가이드를 계속 진행합니다.

## <a name="create-an-application"></a>응용 프로그램 만들기
다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다.  다음을 수행해야 합니다.

* 응용 프로그램에 **네이티브 클라이언트** 를 포함합니다.
* **리디렉션 URI** `urn:ietf:wg:oauth:2.0:oob`를 복사합니다. 이 코드 샘플에 대한 기본 URL입니다.
* 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 시간은 나중에 필요합니다.

## <a name="create-your-policies"></a>정책 만들기
Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 코드 샘플은 등록, 로그인 및 프로필 편집 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)에서 설명한 대로 각 형식에 대한 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

* ID 공급자 블레이드에서 **사용자 ID 등록** 또는 **메일 등록** 중 하나를 선택합니다.
* 등록 정책에서 **표시 이름** 및 다른 등록 특성을 선택합니다.
* 모든 정책에 대한 응용 프로그램 클레임으로 **표시 이름** 및 **개체 ID** 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
* 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다.  이러한 정책 이름이 나중에 필요합니다.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 성공적으로 만들면 앱을 빌드할 준비가 되었습니다.

## <a name="download-the-code"></a>코드 다운로드
이 자습서에 대한 코드는 [GitHub에서 유지 관리됩니다](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). 진행하면서 샘플을 빌드하기 위해 [골격 프로젝트를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

완성된 앱도 [.zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)하거나 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

샘플 코드를 다운로드한 후 Visual Studio .sln 파일을 열어 시작합니다. `TaskClient` 프로젝트는 사용자와 상호 작용하는 WPF 데스크톱 응용 프로그램입니다. 이 자습서에서는 Azure에 호스트되는 각 사용자의 할 일 목록을 저장하는 백 엔드 작업 웹 API를 호출합니다.  웹 API를 빌드할 필요가 없습니다. 이미 실행 중입니다.

Web API가 Azure AD B2C를 사용하여 요청을 안전하게 인증하는 방법을 알아보려면 [Web API 시작 문서](active-directory-b2c-devquickstarts-api-dotnet.md)를 확인하세요.

## <a name="execute-policies"></a>정책 실행
앱은 인증 메시지를 전송하여 Azure AD B2C와 통신하며 이는 HTTP 요청의 일부로 실행하고자 하는 정책을 지정합니다. .NET 데스크톱 응용 프로그램의 경우 MSAL(미리 보기 Microsoft 인증 라이브러리)을 사용하여 OAuth 2.0 인증 메시지를 보내고 정책을 실행하고 Web API를 호출하는 토큰을 가져올 수 있습니다.

### <a name="install-msal"></a>MSAL 설치
Visual Studio 패키지 관리자 콘솔을 사용하여 MSAL을 `TaskClient` 프로젝트에 추가합니다.

```
PM> Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="enter-your-b2c-details"></a>B2C 세부 정보 입력
`Globals.cs` 파일을 열고 각각의 속성 값을 사용자 고유의 값으로 바꿉니다. 이 클래스는 일반적으로 사용되는 값을 참조하기 위해 `TaskClient` 전반에 사용됩니다.

```csharp
public static class Globals
{
    ...

    // TODO: Replace these five default with your own configuration values
    public static string tenant = "fabrikamb2c.onmicrosoft.com";
    public static string clientId = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
    public static string signInPolicy = "b2c_1_sign_in";
    public static string signUpPolicy = "b2c_1_sign_up";
    public static string editProfilePolicy = "b2c_1_edit_profile";

    ...
}
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### <a name="create-the-publicclientapplication"></a>PublicClientApplication 만들기
MSAL의 기본 클래스는 `PublicClientApplication`입니다. 이 클래스는 Azure AD B2C 시스템에 있는 응용 프로그램을 나타냅니다. 앱이 시작되면 `MainWindow.xaml.cs`에서 `PublicClientApplication` 인스턴스를 만듭니다. 이를 창 전체에서 사용할 수 있습니다.

```csharp
protected async override void OnInitialized(EventArgs e)
{
    base.OnInitialized(e);

    pca = new PublicClientApplication(Globals.clientId)
    {
        // MSAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
        // we've extended the MSAL TokenCache and created a simple FileCache in this app.
        UserTokenCache = new FileCache(),
    };

    ...
```

### <a name="initiate-a-sign-up-flow"></a>등록 흐름 시작
사용자가 등록하려는 경우 이전에 만든 등록 정책을 사용하는 등록 흐름을 시작할 수 있습니다. MSAL을 사용하여 `pca.AcquireTokenAsync(...)`를 호출하면 됩니다. `AcquireTokenAsync(...)` 에 전달하는 매개 변수는 받을 토큰의 종류, 인증 요청에서 사용되는 정책 등을 결정합니다.

```csharp
private async void SignUp(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        // Use the app's clientId here as the scope parameter, indicating that
        // you want a token to the your app's backend web API (represented by
        // the cloud hosted task API).  Use the UiOptions.ForceLogin flag to
        // indicate to MSAL that it should show a sign-up UI no matter what.
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                Globals.signUpPolicy);

        // Upon success, indicate in the app that the user is signed in.
        SignInButton.Visibility = Visibility.Collapsed;
        SignUpButton.Visibility = Visibility.Collapsed;
        EditProfileButton.Visibility = Visibility.Visible;
        SignOutButton.Visibility = Visibility.Visible;

        // When the request completes successfully, you can get user
        // information from the AuthenticationResult
        UsernameLabel.Content = result.User.Name;

        // After the sign up successfully completes, display the user's To-Do List
        GetTodoList();
    }

    // Handle any exeptions that occurred during execution of the policy.
    catch (MsalException ex)
    {
        if (ex.ErrorCode != "authentication_canceled")
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }

            MessageBox.Show(message);
        }

        return;
    }
}
```

### <a name="initiate-a-sign-in-flow"></a>로그인 흐름 시작
등록 흐름을 시작하는 것과 동일한 방식으로 로그인 흐름을 시작할 수 있습니다. 사용자가 로그인하면 로그인 정책을 사용하여 동일한 방식으로 MSAL을 호출합니다.

```csharp
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.signInPolicy);
        ...
```

### <a name="initiate-an-edit-profile-flow"></a>프로필 편집 흐름 시작
다시 동일한 방식으로 프로필 편집 정책을 실행할 수 있습니다.

```csharp
private async void EditProfile(object sender, RoutedEventArgs e)
{
    AuthenticationResult result = null;
    try
    {
        result = await pca.AcquireTokenAsync(new string[] { Globals.clientId },
                    string.Empty, UiOptions.ForceLogin, null, null, Globals.authority,
                    Globals.editProfilePolicy);
```

이 모든 경우에 MSAL은 `AuthenticationResult` 에서 토큰을 반환하거나 예외를 throw합니다. MSAL에서 토큰을 가져올 때마다 `AuthenticationResult.User` 개체를 사용하여 UI와 같은 앱의 사용자 데이터를 업데이트합니다. 또한 ADAL은 응용 프로그램의 다른 부분에 사용하기 위해 토큰을 캐시합니다.

### <a name="check-for-tokens-on-app-start"></a>앱 시작에서 토큰 확인
또한 MSAL를 사용하여 사용자의 로그인 상태를 추적할 수 있습니다.  이 앱에서 사용자가 앱을 닫았다가 다시 연 후에도 로그인된 상태를 유지하게 하려 합니다.  `OnInitialized` 재정의 내부에서 MSAL의 `AcquireTokenSilent` 메서드를 사용하여 캐시된 토큰을 확인합니다.

```csharp
AuthenticationResult result = null;
try
{
    // If the user has has a token cached with any policy, we'll display them as signed-in.
    TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
    string existingPolicy = tci == null ? null : tci.Policy;
    result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    SignInButton.Visibility = Visibility.Collapsed;
    SignUpButton.Visibility = Visibility.Collapsed;
    EditProfileButton.Visibility = Visibility.Visible;
    SignOutButton.Visibility = Visibility.Visible;
    UsernameLabel.Content = result.User.Name;
    GetTodoList();
}
catch (MsalException ex)
{
    if (ex.ErrorCode == "failed_to_acquire_token_silently")
    {
        // There are no tokens in the cache.  Proceed without calling the To Do list service.
    }
    else
    {
        // An unexpected error occurred.
        string message = ex.Message;
        if (ex.InnerException != null)
        {
            message += "Inner Exception : " + ex.InnerException.Message;
        }
        MessageBox.Show(message);
    }
    return;
}
```

## <a name="call-the-task-api"></a>태스크 API 호출
MSAL을 사용하여 정책을 실행하고 토큰을 가져왔습니다.  이러한 토큰 중 하나를 사용하여 태스크 API를 호출하려는 경우 MSAL의 `AcquireTokenSilent` 메서드를 다시 사용하여 캐쉬된 토큰을 확인할 수 있습니다.

```csharp
private async void GetTodoList()
{
    AuthenticationResult result = null;
    try
    {
        // Here we want to check for a cached token, independent of whatever policy was used to acquire it.
        TokenCacheItem tci = pca.UserTokenCache.ReadItems(Globals.clientId).Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
        string existingPolicy = tci == null ? null : tci.Policy;

        // Use AcquireTokenSilent to indicate that MSAL should throw an exception if a token cannot be acquired
        result = await pca.AcquireTokenSilentAsync(new string[] { Globals.clientId }, string.Empty, Globals.authority, existingPolicy, false);

    }
    // If a token could not be acquired silently, we'll catch the exception and show the user a message.
    catch (MsalException ex)
    {
        // There is no access token in the cache, so prompt the user to sign-in.
        if (ex.ErrorCode == "failed_to_acquire_token_silently")
        {
            MessageBox.Show("Please sign up or sign in first");
            SignInButton.Visibility = Visibility.Visible;
            SignUpButton.Visibility = Visibility.Visible;
            EditProfileButton.Visibility = Visibility.Collapsed;
            SignOutButton.Visibility = Visibility.Collapsed;
            UsernameLabel.Content = string.Empty;
        }
        else
        {
            // An unexpected error occurred.
            string message = ex.Message;
            if (ex.InnerException != null)
            {
                message += "Inner Exception : " + ex.InnerException.Message;
            }
            MessageBox.Show(message);
        }

        return;
    }
    ...
```

`AcquireTokenSilentAsync(...)`에 대한 호출이 성공하고 캐시에 토큰이 발견되면 HTTP 요청의 `Authorization` 헤더에 토큰을 추가할 수 있습니다. 태스크 웹 API는 이 헤더를 사용하여 사용자 할 일 목록에 대한 읽기 요청을 인증합니다.

```csharp
    ...
    // Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

    // Call the To Do list service.
    HttpResponseMessage response = await httpClient.GetAsync(Globals.taskServiceUrl + "/api/tasks");
    ...
```

## <a name="sign-the-user-out"></a>사용자를 로그아웃
마지막으로 사용자가 **로그아웃**을 선택한 경우 MSAL을 사용하여 앱에서 사용자의 세션을 종료할 수 있습니다.  MSAL을 사용하는 경우 토큰 캐시에서 모든 토큰을 삭제하는 방식으로 이 작업이 수행됩니다.

```csharp
private void SignOut(object sender, RoutedEventArgs e)
{
    // Clear any remnants of the user's session.
    pca.UserTokenCache.Clear(Globals.clientId);

    // This is a helper method that clears browser cookies in the browser control that MSAL uses, it is not part of MSAL.
    ClearCookies();

    // Update the UI to show the user as signed out.
    TaskList.ItemsSource = string.Empty;
    SignInButton.Visibility = Visibility.Visible;
    SignUpButton.Visibility = Visibility.Visible;
    EditProfileButton.Visibility = Visibility.Collapsed;
    SignOutButton.Visibility = Visibility.Collapsed;
    return;
}
```

## <a name="run-the-sample-app"></a>샘플 앱 실행
마지막으로 샘플을 빌드하하고 실행합니다.  메일 주소 또는 사용자 이름을 사용하여 앱에 등록합니다. 로그아웃했다가 동일한 사용자로 다시 로그인합니다. 해당 사용자의 프로필을 편집합니다. 로그아웃했다가 다른 사용자로 등록합니다.

## <a name="add-social-idps"></a>소셜 IDP 추가
현재, 앱은 **로컬 계정**을 사용하는 사용자 등록 및 로그인만 지원합니다. 이들은 사용자 이름 및 암호를 사용하는 B2C 디렉터리에 저장된 계정입니다. Azure AD B2C를 사용하면 코드를 변경하지 않고도 다른 IDP(ID 공급자)에 대한 지원을 추가할 수 있습니다.

소셜 IDP를 앱에 추가하려면 이 문서 중에서 상세한 지침을 수행하여 시작합니다. 지원하려는 각 IDP의 경우 해당 시스템에서 응용 프로그램을 등록하고 클라이언트 ID를 얻어야 합니다.

* [Facebook을 IDP로 설정](active-directory-b2c-setup-fb-app.md)
* [Google을 IDP로 설정](active-directory-b2c-setup-goog-app.md)
* [Amazon을 IDP로 설정](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn을 IDP로 설정](active-directory-b2c-setup-li-app.md)

B2C 디렉터리에 ID 공급자를 추가한 후 [정책 참조 문서](active-directory-b2c-reference-policies.md)에서 설명한 대로 새 IDP를 포함하도록 세 가지 정책을 각각 편집해야 합니다. 정책을 저장한 후 앱을 다시 실행합니다. ID 환경 각각에서 로그인 및 등록으로 추가된 새 IDP가 표시되어야 합니다.

정책을 실험하고 샘플 앱에서 영향을 확인할 수 있습니다. IDP를 추가 또는 제거하거나 응용 프로그램 클레임을 조작하거나 등록 특성을 변경합니다. 어떻게 정책, 인증 요청 및 MSAL을 모두 함께 연결하는지 확인할 수 있을 때까지 실험해 보세요.

참조를 위해 완료된 샘플은 [.zip 파일로 제공](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip)됩니다. 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```
