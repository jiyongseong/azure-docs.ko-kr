## <a name="add-the-applications-registration-information-to-your-app"></a>앱에 응용 프로그램의 등록 정보 추가

이 단계에서는 응용 프로그램 등록 정보의 리디렉션 URL을 구성하고 JavaScript SPA 응용 프로그램에 응용 프로그램 ID를 추가해야 합니다.

### <a name="configure-redirect-url"></a>리디렉션 URL 구성

웹 서버에 따라 index.html 페이지에 대한 URL로 `Redirect URL` 필드를 구성하고 *업데이트*를 클릭합니다.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>리디렉션 URL을 얻기 위한 Visual Studio 지침
> 리디렉션 URL을 가져오려면:
> 1.    *솔루션 탐색기*에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 `F4` 누르기).
> 2.    `URL`의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    값을 이 페이지 맨 위에 `Redirect URL`로 붙여 넣고 `Update`를 클릭합니다.

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Python에 대한 리디렉션 URL 설정
> Python의 경우 명령줄을 통해 웹 서버 포트를 설정할 수 있습니다. 이 단계별 설치는 참조를 위해 포트 8080을 사용하지만 다른 포트도 자유롭게 사용 가능합니다. 어떤 경우든, 다음 지침에 따라 응용 프로그램 등록 정보에 리디렉션 URL을 설정합니다.<br/>
> 이 페이지 맨 위에서 `http://localhost:8080/`을 `Redirect URL`로 설정하거나 사용자 지정 TCP 포트를 사용하는 경우 `http://localhost:[port]/`를 사용하고(여기서 *[port]*는 사용자 지정 TCP 포트 번호임) [업데이트]를 클릭합니다.

### <a name="configure-your-javascript-spa-application"></a>JavaScript SPA 응용 프로그램 구성

1.  응용 프로그램 등록 정보를 포함하는 `msalconfig.js`라는 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 마우스 오른쪽 단추로 클릭하고 `Add` > `New Item` > `JavaScript File`을 선택합니다. 이름을 `msalconfig.js`로 지정합니다.
2.  `msalconfig.js` 파일에 다음 코드를 추가합니다.

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
