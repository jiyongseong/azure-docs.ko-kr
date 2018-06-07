
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL을 사용하여 Microsoft Graph API에 대한 토큰 가져오기

이 섹션에서는 MSAL을 사용하여 Microsoft Graph API에 대한 토큰을 가져옵니다.

1.  *MainWindow.xaml.cs* 파일에서 MSAL에 대한 참조를 클래스에 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    ```
<!-- Workaround for Docs conversion bug -->

2. `MainWindow` 클래스 코드를 다음으로 바꿉니다.

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string _graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] _scopes = new string[] { "user.read" };

        public MainWindow()
        {
            InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;

            try
            {
                authResult = await App.PublicClientApp.AcquireTokenSilentAsync(_scopes, App.PublicClientApp.Users.FirstOrDefault());
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
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
                ResultText.Text = await GetHttpContentWithToken(_graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>자세한 정보
#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기
`AcquireTokenAsync` 메서드를 호출하면 사용자에게 로그인하라는 창이 표시됩니다. 처음으로 보호된 리소스에 액세스할 때 응용 프로그램에서는 사용자가 일반적으로 대화형으로 로그인하도록 요청합니다. 토큰을 획득하는 자동 작업에 실패한 경우(예: 사용자의 암호가 만료된 경우) 로그인해야 할 수도 있습니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기
`AcquireTokenSilentAsync` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. 요청에 대한 호출 또는 토큰 갱신이 자동으로 수행되기 때문에 `AcquireTokenAsync`가 처음으로 실행된 후에 `AcquireTokenSilentAsync`는 후속 호출에서 보호되는 리소스에 액세스하는 토큰을 가져오는 데 일반적으로 사용되는 메서드입니다.

결국 `AcquireTokenSilentAsync` 메서드가 실패합니다. 사용자가 로그아웃했거나 다른 장치에서 해당 암호를 변경하면 실패할 수 있습니다. MSAL이 대화형 작업을 요구해 이 문제를 해결할 수 있다고 감지하면 `MsalUiRequiredException` 예외를 발생합니다. 응용 프로그램에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

* 즉시 `AcquireTokenAsync`에 대해 호출할 수 있습니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 응용 프로그램에서 일반적으로 사용됩니다. 이 단계별 설치에 따라 생성된 샘플은 이 패턴을 사용합니다. 이 패턴은 샘플을 처음 실행할 때 작동되는 항목을 확인할 수 있습니다. 
    * 이 응용 프로그램을 사용한 사용자가 없기 때문에 `PublicClientApp.Users.FirstOrDefault()`에는 null 값이 포함되며 `MsalUiRequiredException` 예외가 throw됩니다. 
    * 샘플의 코드는 `AcquireTokenAsync`를 호출하여 예외를 처리합니다. 그러면 사용자에게 로그인하라는 메시지가 표시됩니다.

* 로그인에 적절한 시기를 선택할 수 있도록 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 표시할 수 있습니다. 또는 응용 프로그램이 나중에 `AcquireTokenSilentAsync`를 다시 시도할 수 있습니다. 이 패턴은 사용자가 중지하지 않고 다른 응용 프로그램 기능을 사용할 수 있는 경우에 자주 사용됩니다(예: 오프라인 콘텐츠를 응용 프로그램에서 사용할 수 있는 경우). 이 경우에 사용자는 보호된 리소스에 액세스하거나, 오래된 정보를 새로 고치기 위해 로그인하는 시점을 결정할 수 있습니다. 또는 네트워크가 일시적으로 사용할 수 없게 된 후에 복원된 경우 응용 프로그램이 `AcquireTokenSilentAsync`를 다시 시도하도록 결정할 수 있습니다.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API를 호출합니다.

`MainWindow.xaml.cs`에 다음과 같은 새 메서드를 추가합니다. 이 메서드는 인증 헤더를 사용하여 Graph API에 대한 `GET` 요청을 수행하는 데 사용됩니다.

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
        //Add the token in Authorization header
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
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 응용 프로그램 예제에서는 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행한 다음 호출자에게 콘텐츠를 반환하는 데 `GetHttpContentWithToken` 메서드를 사용합니다. 이 메서드는 HTTP 인증 헤더에서 획득된 토큰을 추가합니다. 이 샘플에서 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 끝점입니다.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>사용자를 로그아웃하는 메서드 추가

사용자를 로그아웃하려면 `MainWindow.xaml.cs` 파일에 다음 메서드를 추가합니다.

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
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>사용자 로그아웃에 대한 자세한 내용

`SignOutButton_Click` 메서드는 MSAL 사용자 캐시에서 사용자를 제거합니다. 그러면 대화식으로 수행되는 경우에만 토큰을 획득하는 이후 요청에 성공하도록 MSAL에서 현재 사용자를 효율적으로 삭제하게 됩니다.

이 샘플의 응용 프로그램이 단일 사용자를 지원하더라도 MSAL은 동시에 여러 계정에 로그인할 수 있는 시나리오를 지원합니다. 사용자 한 명이 여러 계정을 가질 수 있는 메일 응용 프로그램을 예로 듭니다.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>기본 토큰 정보 표시

토큰에 대한 기본 정보를 표시하려면 *MainWindow.xaml.cs* 파일에 다음 메서드를 추가합니다.

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
<!--start-collapse-->
### <a name="more-information"></a>자세한 정보

또한 MSAL에서는 사용자가 로그인한 후에 Microsoft Graph API를 호출하는 데 사용되는 액세스 토큰을 외에도 ID 토큰을 가져옵니다. 이 토큰에는 사용자에게 관련된 정보의 작은 하위 집합이 포함됩니다. `DisplayBasicTokenInfo` 메서드는 토큰에 포함된 기본 정보를 표시합니다. 예를 들어, 토큰 만료 날짜 및 액세스 토큰 자체를 나타내는 문자열뿐만 아니라 사용자 표시 이름 및 ID을 표시합니다. *Microsoft Graph API 호출* (Call Microsoft Graph API) 단추를 여러 번 선택하면 동일한 토큰이 후속 요청에 다시 사용된 것을 볼 수 있습니다. MSAL이 토큰 갱신 시점이라고 판단한 경우에는 만료 날짜가 연장된 것도 확인할 수 있습니다.
<!--end-collapse-->

