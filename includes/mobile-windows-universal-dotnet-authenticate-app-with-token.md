
1. MainPage.xaml.cs 프로젝트 파일에서 다음 **using** 문을 추가합니다.
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. **AuthenticateAsync** 메서드를 다음 코드로 바꿉니다.
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: http://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Login with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    이 버전의 **AuthenticateAsync**에서는 앱이 **PasswordVault**에 저장된 자격 증명을 사용하여 서비스에 액세스합니다. 일반 로그인은 저장된 자격 증명이 없는 경우에도 수행됩니다.
   
   > [!NOTE]
   > 캐시된 토큰은 만료될 수 있으며, 토큰 만료는 인증 후 앱을 사용할 때 발생할 수도 있습니다. 토큰이 만료되었는지 확인하는 방법을 알아보려면 [만료된 인증 토큰 확인](http://aka.ms/jww5vp)을 참조하세요. 토큰 만료와 관련된 권한 부여 오류를 처리하는 방법은 [Azure Mobile Services 관리 SDK에서 만료된 토큰 캐시 및 처리](http://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx)게시물을 참조하세요. 
   > 
   > 
3. 앱을 두 번 다시 시작합니다.
   
    첫 번째 시작에서는 공급자를 사용한 로그인이 다시 필요합니다. 그러나 두 번째 다시 시작에서는 캐시된 자격 증명이 사용되고 로그인이 무시됩니다. 

