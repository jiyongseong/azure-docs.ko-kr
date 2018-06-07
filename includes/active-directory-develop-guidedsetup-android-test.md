## <a name="test-your-code"></a>코드 테스트

1. 장치/에뮬레이터에 코드를 배포합니다.

2. 응용 프로그램을 테스트할 준비가 되면, Azure Active Directory 계정(회사 또는 학교 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용해 로그인합니다. 

    ![응용 프로그램 테스트](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![사용자 이름 및 암호 입력](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="provide-consent-for-application-access"></a>응용 프로그램 액세스에 대한 동의 제공
응용 프로그램에 처음으로 로그인하면 다음과 같이 응용 프로그램이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지도 표시됩니다. 

![응용 프로그램 액세스에 대한 사용자 동의 제공](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)


### <a name="view-application-results"></a>응용 프로그램 결과 보기
로그인하면, Microsoft Graph API에 대한 호출에서 반환되는 결과가 표시 됩니다. Microsoft Graph API **me** 끝점을 호출하면 [사용자 프로필](https://graph.microsoft.com/v1.0/me)이 반환됩니다. 일반적인 Microsoft Graph 끝점 목록은 [Microsoft Graph API 개발자 설명서](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)를 참조하세요.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 응용 프로그램 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 *Calendars.Read* 범위가 있어야만 사용자 일정을 나열할 수 있습니다. 

응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다. 

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
