---
title: Azure AD v2 UWP 시작 | Microsoft Docs
description: 유니버설 Windows 플랫폼(XAML) 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 액세스 토큰을 필요로 하는 API를 호출하는 방식
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/20/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 390559922b3b8fb293d1c8b38f36dfd0a1df9ebd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763376"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>유니버설 Windows 플랫폼(UWP) 응용 프로그램에서 Microsoft Graph API 호출

이 가이드에서는 네이티브 유니버설 Windows 플랫폼(XAML) 응용 프로그램이 액세스 토큰을 얻은 다음, 이 액세스 토큰을 사용하여 Azure Active Directory v2 엔드포인트에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법에 대해 설명합니다.

이 가이드를 모두 살펴보면 응용 프로그램에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)을 사용해 보호되는 API를 호출할 수 있습니다.  

> 이 가이드는 유니버설 Windows 플랫폼 개발을 설치한 Visual Studio 2017을 필요로 합니다. 유니버설 Windows 플랫폼 앱을 개발하기 위해 Visual Studio를 다운로드하고 구성하는 방법에 대한 지침은 이 [문서](https://docs.microsoft.com/windows/uwp/get-started/get-set-up "UWP에 대한 Visual Studio 설정")을 확인합니다.

### <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 가이드의 작동 방식](media/active-directory-mobileanddesktopapp-windowsuniversalplatform-introduction/uwp-intro.png)

이 가이드에서 만든 샘플 응용 프로그램은 UWP 앱이 Azure Active Directory v2 끝점에서 토큰을 수락하는 Microsoft Graph API 또는 Web API를 쿼리하도록 합니다. 이 시나리오에서는 토큰은 권한 부여 헤더를 통해 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.

### <a name="nuget-packages"></a>NuGet 패키지

이 가이드에서는 다음 NuGet 패키지를 사용합니다.

|라이브러리|설명|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|MSAL(Microsoft 인증 라이브러리)|


## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 Microsoft Graph API 같은 토큰이 필요한 Web API를 쿼리할 수 있도록 Windows Desktop .NET 응용 프로그램(XAML)을 *Microsoft에 로그인*과 통합하는 방법에 대한 단계별 지침을 제공합니다.

이 가이드에 따라 만든 응용 프로그램은 Graph API, 로그 아웃 단추 및 호출의 결과를 표시하는 텍스트 상자를 쿼리하는 데 사용되는 단추를 표시합니다.

> 이 샘플의 Visual Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)하고, 실행하기 전에 코드 샘플을 구성하려면 [응용 프로그램 등록](#register-your-application "응용 프로그램 등록 단계")의 단계로 건너뜁니다.


### <a name="create-your-application"></a>응용 프로그램 만들기
1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택<br/>
2. *템플릿*에서 **Visual C#** 을 선택
3. **비어 있는 앱(유니버설 Windows)** 을 선택
4. 이름을 지정하고 ‘확인’을 클릭합니다.
5. 메시지가 표시되면 *대상* 및 *최소* 버전에 대한 모든 버전을 자유롭게 선택하고 '확인'을 클릭합니다.<br/><br/>![최소 및 대상 버전](media/active-directory-uwp-v2.md/vs-minimum-target.png)

## <a name="add-the-microsoft-authentication-library-msal-to-your-project"></a>프로젝트에 MSAL(Microsoft 인증 라이브러리) 추가
1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭
2. 패키지 관리자 콘솔 창에서 다음 명령을 복사/붙여넣기합니다.

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

> [!NOTE]
> 위의 패키지는 [MSAL(Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)을 설치합니다. MSAL은 Azure Active Directory v2로 보호되는 API에 액세스하는 데 사용되는 사용자 토큰의 획득, 캐싱 및 새로 고침을 처리합니다.

## <a name="initialize-msal"></a>MSAL 초기화
이 단계를 수행하면 MSAL 라이브러리와의 상호 작용을 처리하는 클래스를 만들 수 있습니다(예: 토큰 처리).

1. **App.xaml.cs** 파일을 열고 MSAL 라이브러리에 대한 참조를 클래스에 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. 앱의 클래스에 다음 두 줄을 추가합니다(<code>sealed partial class App : Application</code> 블록 내).

    ```csharp
    // Below is the clientId of your app registration. 
    // You have to replace the below with the Application Id for your app registration
    private static string ClientId = "your_client_id_here";
    
    public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
    ```

## <a name="create-your-applications-ui"></a>응용 프로그램 UI 만들기

**MainPage.xaml** 파일은 프로젝트 템플릿의 일부로 자동으로 생성되어야 합니다. 이 파일을 연 다음, 지침을 따릅니다.

1.  응용 프로그램의 **<Grid>** 노드를 다음으로 바꿉니다.

    ```xml
    <Grid>
        <StackPanel Background="Azure">
            <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
                <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
                <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
            </StackPanel>
            <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
            <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
            <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
        </StackPanel>
    </Grid>
    ```
    
## <a name="use-the-microsoft-authentication-library-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL(Microsoft 인증 라이브러리)를 사용하여 Microsoft Graph API에 대한 토큰 가져오기

이 섹션에서는 MSAL을 사용하여 Microsoft Graph API에 대한 토큰을 가져오는 방법을 보여 줍니다.

1.  **MainPage.xaml.cs**에서 클래스에 MSAL 라이브러리에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    ```
2. <code>MainPage</code> 클래스의 코드를 다음으로 바꿉니다.

    ```csharp
    public sealed partial class MainPage : Page
    {
        // Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";
    
        // Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };
    
        public MainPage()
        {
            this.InitializeComponent();
        }
    
        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;
    
            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
    
                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }
    
            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

### <a name="more-information"></a>추가 정보
#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기
`AcquireTokenAsync` 메서드를 호출하면 사용자에게 로그인하라는 창이 표시됩니다. 처음으로 보호된 리소스에 액세스할 때 응용 프로그램에서는 사용자가 일반적으로 대화형으로 로그인하도록 요청합니다. 토큰을 획득하는 자동 작업에 실패한 경우(예: 사용자의 암호가 만료된 경우) 로그인해야 할 수도 있습니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기
`AcquireTokenSilentAsync` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. 요청에 대한 호출 또는 토큰 갱신이 자동으로 수행되기 때문에 `AcquireTokenAsync`가 처음으로 실행된 후에 `AcquireTokenSilentAsync`는 후속 호출에서 보호되는 리소스에 액세스하는 토큰을 가져오는 데 일반적으로 사용되는 메서드입니다.

결국 `AcquireTokenSilentAsync` 메서드가 실패합니다. 사용자가 로그아웃했거나 다른 장치에서 해당 암호를 변경하면 실패할 수 있습니다. MSAL이 대화형 작업을 요구해 이 문제를 해결할 수 있다고 감지하면 `MsalUiRequiredException` 예외를 발생합니다. 응용 프로그램에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

* 즉시 `AcquireTokenAsync`에 대해 호출할 수 있습니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 응용 프로그램에서 일반적으로 사용됩니다. 이 단계별 설치에 따라 생성된 샘플은 이 패턴을 사용합니다. 이 패턴은 샘플을 처음 실행할 때 작동되는 항목을 확인할 수 있습니다. 
    * 이 응용 프로그램을 사용한 사용자가 없기 때문에 `PublicClientApp.Users.FirstOrDefault()`에는 null 값이 포함되며 `MsalUiRequiredException` 예외가 throw됩니다. 
    * 샘플의 코드는 `AcquireTokenAsync`를 호출하여 예외를 처리합니다. 그러면 사용자에게 로그인하라는 메시지가 표시됩니다.

* 로그인에 적절한 시기를 선택할 수 있도록 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 표시할 수 있습니다. 또는 응용 프로그램이 나중에 `AcquireTokenSilentAsync`를 다시 시도할 수 있습니다. 이 패턴은 사용자가 중지하지 않고 다른 응용 프로그램 기능을 사용할 수 있는 경우에 자주 사용됩니다(예: 오프라인 콘텐츠를 응용 프로그램에서 사용할 수 있는 경우). 이 경우에 사용자는 보호된 리소스에 액세스하거나, 오래된 정보를 새로 고치기 위해 로그인하는 시점을 결정할 수 있습니다. 또는 네트워크가 일시적으로 사용할 수 없게 된 후에 복원된 경우 응용 프로그램이 `AcquireTokenSilentAsync`를 다시 시도하도록 결정할 수 있습니다.

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API 호출

1. **MainPage.xaml.cs**에 다음과 같은 새 메서드를 추가합니다. 이 메서드는 인증 헤더를 사용하여 Graph API에 대한 `GET` 요청을 수행하는 데 사용됩니다.

    ```csharp
    /// <summary>
    /// Perform an HTTP GET request to a URL using an HTTP Authorization header
    /// </summary>
    /// <param name="url">The URL</param>
    /// <param name="token">The token</param>
    /// <returns>String containing the results of the GET operation</returns>
    public async Task<string> GetHttpContentWithToken(string url, string token)
    {
        var httpClient = new System.Net.Http.HttpClient();
        System.Net.Http.HttpResponseMessage response;
        try
        {
            var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
            // Add the token in Authorization header
            request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
            response = await httpClient.SendAsync(request);
            var content = await response.Content.ReadAsStringAsync();
            return content;
        }
        catch (Exception ex)
        {
            return ex.ToString();
        }
    }
    ```

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 샘플 응용 프로그램에서 `GetHttpContentWithToken` 메서드는 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행한 다음 호출자에게 콘텐츠를 반환하는 데 사용됩니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 샘플에서 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 끝점입니다.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>사용자를 로그아웃하는 메서드 추가

1. 사용자를 로그아웃하려면 **MainPage.xaml.cs**에 다음 메서드를 추가합니다.

    ```csharp
    /// <summary>
    /// Sign out the current user
    /// </summary>
    private void SignOutButton_Click(object sender, RoutedEventArgs e)
    {
        if (App.PublicClientApp.Users.Any())
        {
            try
            {
                App.PublicClientApp.Remove(App.PublicClientApp.Users.FirstOrDefault());
                this.ResultText.Text = "User has signed-out";
                this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            }
            catch (MsalException ex)
            {
                ResultText.Text = $"Error signing-out user: {ex.Message}";
            }
        }
    }
    ```

### <a name="more-info-on-sign-out"></a>로그아웃에 대한 자세한 정보

`SignOutButton_Click` 메서드는 MSAL 사용자 캐시에서 사용자를 제거하여 MSAL에 현재 사용자를 잊으라고 효율적으로 전달합니다. 따라서 대화식으로 수행되는 경우에만 토큰 획득을 위한 이후 요청에 성공하게 됩니다.
이 샘플의 응용 프로그램이 단일 사용자를 지원하더라도 MSAL은 동시에 여러 계정에 로그인할 수 있는 시나리오를 지원합니다(예: 사용자 한 명이 여러 계정을 가질 수 있는 메일 응용 프로그램).

## <a name="display-basic-token-information"></a>기본 토큰 정보 표시

1. 토큰에 대한 기본 정보를 표시하려면 **MainPage.xaml.cs** 파일에 다음 메서드를 추가합니다.

    ```csharp
    /// <summary>
    /// Display basic information contained in the token
    /// </summary>
    private void DisplayBasicTokenInfo(AuthenticationResult authResult)
    {
        TokenInfoText.Text = "";
        if (authResult != null)
        {
            TokenInfoText.Text += $"Name: {authResult.User.Name}" + Environment.NewLine;
            TokenInfoText.Text += $"Username: {authResult.User.DisplayableId}" + Environment.NewLine;
            TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
            TokenInfoText.Text += $"Access Token: {authResult.AccessToken}" + Environment.NewLine;
        }
    }
    ```

### <a name="more-information"></a>추가 정보

*OpenID Connect*를 통해 획득된 ID 토큰에는 사용자와 관련된 정보의 일부가 포함됩니다. `DisplayBasicTokenInfo`는 토큰에 포함된 기본 정보를 표시합니다(예: 사용자 표시 이름 및 ID, 토큰 만료 날짜 및 액세스 토큰 자체를 나타내는 문자열). 사용자가 볼 수 있도록 이러한 정보가 표시됩니다. **Microsoft Graph API 호출** (Call Microsoft Graph API) 단추를 여러 번 누르면 동일한 토큰이 후속 요청에 다시 사용된 것을 볼 수 있습니다. MSAL이 토큰 갱신 시점이라고 판단한 경우에는 만료 날짜가 연장된 것도 확인할 수 있습니다.

## <a name="register-your-application"></a>응용 프로그램 등록

이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동해 응용 프로그램을 등록합니다.
2. 응용 프로그램의 이름을 입력합니다. 
3. 안내식 설정 옵션이 선택 취소되어 있는지 확인합니다.
4. **플랫폼 추가**를 클릭한 다음, **네이티브 응용 프로그램**을 선택하고 저장 키를 누름
5. 응용 프로그램 ID의 GUID를 복사하고, Visual Studio로 이동한 다음, **App.xaml.cs**를 열어 `your_client_id_here`를 방금 등록한 응용 프로그램 ID로 바꿉니다.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>페더레이션된 도메인에서 통합된 인증을 사용하도록 설정(선택 사항)

페더레이션된 Azure Active Directory 도메인과 함께 사용할 경우 Windows 통합 인증을 사용하려면 응용 프로그램 매니페스트가 추가 기능을 사용하도록 설정해야 합니다.

1. **Package.appxmanifest** 두 번 클릭
2. **기능** 탭을 선택하고 다음 설정이 사용하도록 설정되어 있는지 확인합니다.

    - 엔터프라이즈 인증
    - 개인 네트워크(클라이언트 및 서버)
    - 공유 사용자 인증서 

3. 그런 다음, **App.xaml.cs**를 열고 앱 생성자에 다음 줄을 추가합니다.

    ```csharp
    App.PublicClientApp.UseCorporateNetwork = true;
    ```

> [!IMPORTANT]
> Windows 통합 인증은 기본적으로 이 샘플에 대해 구성되지 않습니다. *엔터프라이즈 인증* 또는 *공유 사용자 인증서* 기능을 요청 응용 프로그램은 Windows 스토어에 의한 더 높은 수준의 확인 작업을 요구하고 일부 개발자가 더 높은 수준의 확인 작업을 수행하려 하기 때문입니다. 페더레이션된 Azure Active Directory 도메인을 사용한 Windows 통합 인증이 필요한 경우에만 이 설정을 사용하도록 설정하세요.


## <a name="test-your-code"></a>코드 테스트

응용 프로그램을 테스트하려면 `F5` 키를 눌러 Visual Studio에서 프로젝트를 실행합니다. 아래와 같이 주 창이 표시됩니다.

![응용 프로그램의 사용자 인터페이스](media/active-directory-uwp-v2.md/testapp-ui.png)

테스트할 준비가 되면 *Microsoft Graph API 호출*을 클릭하고 Microsoft Azure Active Directory(조직 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용해 로그인합니다. 처음인 경우 로그인하라는 창이 표시됩니다.

![로그인 페이지](media/active-directory-uwp-v2.md/sign-in-page.png)

### <a name="consent"></a>동의
응용 프로그램에 처음으로 로그인하면 다음과 유사한 동의 화면이 표시됩니다. 여기서 명백히 동의해야 합니다.

![동의 화면](media/active-directory-uwp-v2.md/consentscreen.png)
### <a name="expected-results"></a>예상 결과
API 호출 결과 화면에 Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시됩니다.

![결과 화면](media/active-directory-uwp-v2.md/uwp-results-screen.PNG)

[토큰 정보] 상자에 `AcquireTokenAsync` 또는 `AcquireTokenSilentAsync`를 통해 획득한 토큰에 대한 기본 정보가 표시됩니다.

|자산  |형식  |설명 |
|---------|---------|---------|
|**Name** |사용자의 전체 이름 |사용자의 이름과 성입니다.|
|**사용자 이름** |<span>user@domain.com</span> |사용자를 식별하는 데 사용하는 사용자 이름입니다.|
|**토큰 만료** |Datetime |토큰이 만료되는 시간입니다. MSAL은 필요에 따라 토큰을 갱신하여 만료 날짜를 연장합니다.|
|**액세스 토큰** |문자열 |*인증 헤더*가 필요한 HTTP 요청으로 전송된 토큰 문자열|

#### <a name="see-what-is-in-the-access-token-optional"></a>액세스 토큰 내용 확인(선택 사항)
필요에 따라 디코딩하고 클레임 목록을 참조하려면 '액세스 토큰'에서 값을 복사하고 https://jwt.ms에 붙여넣기할 수 있습니다.

### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 응용 프로그램 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 *Calendars.Read* 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다. 

> [!NOTE]
> 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="issue-1"></a>문제 1:
페더레이션된 Azure Active Directory 도메인에서 응용 프로그램에 로그인한 경우 다음 오류 중 하나가 나타날 수 있습니다.
 - 요청에서 유효한 클라이언트 인증서를 찾을 수 없습니다.
 - 사용자의 인증서 저장소에서 유효한 인증서를 찾을 수 없습니다.
 - 다른 인증 방법을 다시 선택하십시오.

**원인:** 엔터프라이즈 및 인증서 기능을 사용할 수 없음

**솔루션:** [페더레이션된 도메인에서 통합 인증](#enable-integrated-authentication-on-federated-domains-optional)의 단계를 따름

### <a name="issue-2"></a>문제 2:
[페더레이션된 도메인에서 통합 인증](#enable-integrated-authentication-on-federated-domains-optional)을 사용하도록 설정하고 Windows 10 컴퓨터에서 Windows Hello를 사용하여 다중-팩터-인증이 구성된 환경에 로그인하면 인증서 목록이 표시됩니다. 그러나 사용자의 PIN을 사용하도록 선택하는 경우 PIN 창은 절대 표시되지 않습니다.

**원인:** Windows 10 데스크톱에서 실행되는 UWP 응용 프로그램에서 웹 인증 브로커에 대해 알려진 제한 사항(Windows 10 Mobile에서 제대로 작동)

**해결 방법:** 사용자는 다른 옵션을 사용하여 로그인을 선택한 다음, *사용자 이름 및 암호를 사용하여 로그인*을 선택하여 암호를 제공한 다음, 전화 인증을 거칩니다.

