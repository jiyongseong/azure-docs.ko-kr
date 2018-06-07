## <a name="test-querying-the-microsoft-graph-api-from-your-ios-application"></a>iOS 응용 프로그램에서 Microsoft Graph API 쿼리 테스트

**Command** + **R**을 눌러 시뮬레이터에서 코드를 실행합니다.

![시뮬레이터에서 응용 프로그램을 테스트합니다.](media/active-directory-develop-guidedsetup-ios-test/iostestscreenshot.png)

테스트할 준비가 되면 **Call Microsoft Graph API**를 선택합니다. 메시지가 표시되면 사용자 이름 및 암호를 입력합니다.

### <a name="provide-consent-for-application-access"></a>응용 프로그램 액세스에 대한 동의 제공
응용 프로그램에 처음으로 로그인하면 응용 프로그램이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다.

![응용 프로그램 액세스에 대한 사용자 동의 제공](media/active-directory-develop-guidedsetup-ios-test/iosconsentscreen.png)

### <a name="view-application-results"></a>응용 프로그램 결과 보기
로그인한 후에 **로깅** 섹션에 Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시되는지 확인해야 합니다. 

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 **user.read** 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 **Calendars.Read** 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 **Calendars.Read** 위임 권한을 추가합니다. 그런 다음 **acquireTokenSilent** 호출에 **Calendars.Read** 범위를 추가합니다. 

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
