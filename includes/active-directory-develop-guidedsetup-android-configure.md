
## <a name="register-your-application"></a>응용 프로그램 등록
다음 두 섹션에 설명된 대로 두 가지 방법 중 하나로 응용 프로그램을 등록할 수 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드
다음을 수행하여 응용 프로그램을 신속하게 등록할 수 있습니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)로 이동합니다.
2.  **응용 프로그램 이름** 상자에서 응용 프로그램의 이름을 입력합니다.

3. **단계별 설치** 확인란을 선택한 다음 **만들기**를 선택하도록 합니다.

4. 응용 프로그램 ID를 가져오는 지침에 따라 코드에 붙여넣습니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드
응용 프로그램을 등록하고 응용 프로그램 등록 정보를 솔루션에 추가하려면 다음을 수행합니다.
1. 응용 프로그램을 이미 등록한 경우 [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.
2. **응용 프로그램 이름** 상자에서 응용 프로그램의 이름을 입력합니다. 

3. **단계별 설치** 확인란의 선택을 취소한 다음 **만들기**를 선택하도록 합니다.

4. **플랫폼 추가**를 선택하고, **네이티브 응용 프로그램**을 선택한 다음 **저장**을 선택합니다.

5. **앱** > **java** > **{host}.{namespace}** 아래에서 `MainActivity`를 엽니다. 

6.  다음 줄에서 *[여기에 응용 프로그램 ID 입력]*을 방금 등록한 응용 프로그램 ID로 바꿉니다.

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. **앱** > **매니페스트** 아래에서 *AndroidManifest.xml* 파일을 엽니다.

8. `manifest\application` 노드에서 다음 작업을 추가합니다. 이렇게 하면 인증을 완료한 후 OS가 응용 프로그램을 다시 시작할 수 있도록 하는 `BrowserTabActivity` 작업이 등록됩니다.

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            
            <!--Add in your scheme/host from registered redirect URI-->
            <!--By default, the scheme should be similar to 'msal[appId]' -->
            <data android:scheme="msal[Enter the application Id here]"
                android:host="auth" />
        </intent-filter>
    </activity>
    ```
<!-- Workaround for Docs conversion bug -->
9. `BrowserTabActivity` 노드에서 `[Enter the application Id here]`을 응용 프로그램 ID로 바꿉니다.
