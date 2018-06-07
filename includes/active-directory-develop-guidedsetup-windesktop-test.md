## <a name="test-your-code"></a>코드 테스트

**F5**를 선택하여 Visual Studio에서 프로젝트를 실행합니다. 응용 프로그램 **MainWindow**는 다음과 같이 표시됩니다.

![응용 프로그램 테스트](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

처음으로 응용 프로그램을 실행하여 **Call Microsoft Graph API** 단추를 선택하는 것이라면 로그인하라는 메시지가 표시됩니다. Azure Active Directory 계정(회사 또는 학교 계정) 또는 Microsoft 계정(live.com, outlook.com)을 사용해 테스트합니다.

![응용프로그램에 로그인합니다.](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>응용 프로그램 액세스에 대한 동의 제공
응용 프로그램에 처음으로 로그인하면 다음과 같이 응용 프로그램이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지도 표시됩니다. 

![응용 프로그램 액세스에 대한 사용자 동의 제공](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>응용 프로그램 결과 보기
로그인하면, Microsoft Graph API에 대한 호출에서 반환되는 사용자 프로필 정보가 표시됩니다. 결과가 **API 호출 결과** 상자에 표시됩니다. `AcquireTokenAsync` 또는 `AcquireTokenSilentAsync`에 대한 호출을 통해 획득한 토큰에 관한 기본 정보는 **토큰 정보** 상자에 표시되어야 합니다. 결과에 다음 속성이 포함될 수 있습니다.

|자산  |형식  |설명 |
|---------|---------|---------|
|**name** |사용자의 전체 이름 |사용자의 이름과 성입니다.|
|**사용자 이름** |<span>user@domain.com</span> |사용자를 식별하는 데 사용하는 사용자 이름입니다.|
|**토큰 만료** |DateTime |토큰이 만료되는 시간입니다. MSAL은 필요에 따라 토큰을 갱신하여 만료 날짜를 연장합니다.|
|**액세스 토큰** |문자열 |*인증 헤더*가 필요한 HTTP 요청으로 전송된 토큰 문자열|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 응용 프로그램 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 *Calendars.Read* 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다. 

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
