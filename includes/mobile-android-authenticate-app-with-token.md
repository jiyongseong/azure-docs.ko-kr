
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 백 엔드 Azure 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 메서드는 비효율적이며 많은 고객이 동시에 앱을 시작하려고 하는 경우 사용 관련 문제가 발생할 수 있습니다. 더 나은 접근 방법은 Azure 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

> [!NOTE]
> 클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 백 엔드 Azure 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.
>
>

1. ToDoActivity.java 파일을 열고 다음 import 문을 추가합니다.

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. 다음 멤버를 `ToDoActivity` 클래스에 추가합니다.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. ToDoActivity.java 파일에 `cacheUserToken` 메서드에 대한 다음 정의를 추가합니다.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    이 메서드는 비공개로 표시된 기본 설정 파일에 사용자 ID와 토큰을 저장합니다. 이렇게 하면 장치의 다른 앱이 토큰에 액세스할 수 없도록 캐시 액세스가 보호됩니다. 기본 설정이 앱에 대해 샌드박스됩니다. 그러나 다른 사람이 장치에 액세스하게 되면 다른 수단을 통해 토큰 캐시에 액세스할 수도 있습니다.

   > [!NOTE]
   > 데이터에 대한 토큰 액세스가 매우 중요하며 다른 사람이 장치에 액세스할 수 있는 경우 암호화를 사용하여 토큰을 추가로 보호할 수 있습니다. 그러나 완전히 안전한 솔루션은 이 자습서의 범위를 벗어나며 보안 요구 사항에 따라 달라집니다.
   >
   >

4. ToDoActivity.java 파일에 `loadUserTokenCache` 메서드에 대한 다음 정의를 추가합니다.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. *ToDoActivity.java* 파일에서 `authenticate` 및 `onActivityResult` 메서드를 토큰 캐시를 사용하는 다음 메서드로 바꿉니다. Google이 아닌 다른 계정을 사용하려는 경우 로그인 공급자를 변경합니다.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, login and create a token cache
        else
        {
            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. 앱을 작성하고 유효한 계정으로 인증을 테스트합니다. 앱을 두 번 이상 실행합니다. 첫 번째 실행 중 로그인하고 토큰 캐시를 만들라는 프롬프트가 표시되어야 합니다. 그런 다음 각 실행은 인증에 대한 토큰 캐시를 로드하려고 시도합니다. 로그인할 필요가 없습니다.
