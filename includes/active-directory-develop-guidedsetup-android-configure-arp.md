
## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 응용 프로그램의 등록 정보 추가

이 단계에서는 프로젝트에 클라이언트 ID를 추가해야 합니다.

1.  (`app` > `java` > *`{host}.{namespace}`*에서) `MainActivity`를 엽니다.
2.  `final static String CLIENT_ID`로 시작하는 줄을 다음으로 바꿉니다.
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. 다음을 엽니다. `app` > `manifests` > `AndroidManifest.xml`
4. 다음 작업을 `manifest\application` 노드에 추가합니다. 이렇게 하면 OS에서 인증 완료 후 응용 프로그램을 다시 시작할 수 있도록 `BrowserTabActivity`가 등록됩니다.

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
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

