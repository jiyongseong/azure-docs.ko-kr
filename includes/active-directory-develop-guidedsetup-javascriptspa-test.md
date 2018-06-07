## <a name="test-your-code"></a>코드 테스트

### <a name="test-with-visual-studio"></a>Visual Studio를 사용한 테스트
Visual Studio를 사용하는 경우 **F5** 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 http://<span></span>localhost: {port} 위치로 이동합니다. 이 페이지에는 **Call Microsoft Graph API** 단추가 나타납니다.

<p/><!-- --> 

### <a name="test-with-python-or-other-web-server"></a>Python 또는 다른 웹 서버를 사용하여 테스트
Visual Studio를 사용하지 않는 경우 웹 서버가 시작되었는지 확인합니다. **index.html** 파일의 위치를 기반으로 하는 TCP 포트에서 수신 대기하도록 서버를 구성합니다. Python의 경우 해당 응용프로그램 폴더에서 명령 프롬프트 터미널을 실행하여 포트에서 수신 대기를 시작합니다.
 
```bash
python -m http.server 8080
```
브라우저를 열고 http://<span></span>localhost:8080 또는 http://<span></span>localhost:{port}를 입력합니다. 여기서 **port**는 웹 서버가 수신 대기 중인 포트입니다. **Call Microsoft Graph API** 단추를 사용하여 index.html 파일의 내용을 확인하는 것이 좋습니다.

## <a name="test-your-application"></a>응용 프로그램 테스트

브라우저에서 index.html이 로드되면 **Call Microsoft Graph API**를 선택합니다. 응용프로그램을 처음 실행하는 것이라면 브라우저는 로그인하라는 메시지를 표시하는 Microsoft Azure Active Directory(Azure AD) v2.0 끝점으로 리디렉션됩니다.
 
![JavaScript SPA 계정에 로그인](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>응용 프로그램 액세스에 대한 동의 제공

응용 프로그램에 처음으로 로그인하면 응용 프로그램이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다.

![응용 프로그램 액세스에 대한 사용자 동의 제공](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>응용 프로그램 결과 보기
로그인한 후에 **Graph API Call Response** 상자에서 사용자 프로필 정보가 표시되는지 확인해야 합니다.
 
![Microsoft Graph API 호출로부터 예상된 결과](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

**액세스 토큰** 및 **ID 토큰 클레임** 상자에서 가져온 토큰에 대한 기본 정보도 볼 수 있어야 합니다.

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 **user.read** 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 등록 포털에서 등록된 모든 응용 프로그램에서 기본적으로 자동 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. Microsoft Graph API는 **Calendars.Read** 범위가 있어야만 사용자 일정을 나열할 수 있습니다.

응용 프로그램의 컨텍스트에서 사용자 일정에 액세스하려면 응용 프로그램 등록 정보에 **Calendars.Read** 위임 권한을 추가합니다. 그런 다음 **acquireTokenSilent** 호출에 **Calendars.Read** 범위를 추가합니다. 

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API가 범위를 필요로 하지 않으면(권장하지 않음), **acquireTokenSilent** 및 **acquireTokenRedirect** 호출에서 **clientId**를 범위로 사용할 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
