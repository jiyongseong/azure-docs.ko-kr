
## <a name="register-your-application"></a>응용 프로그램 등록

여러 가지 방법으로 응용 프로그램을 만들 수 있습니다. 하나를 선택하세요.

### <a name="option-1-register-your-application-express-mode"></a>옵션 1: 응용 프로그램 등록(기본 모드)
이제 *Microsoft 응용 프로그램 등록 포털*에서 등용 프로그램을 등록해야 합니다.

1.  [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)을 통해 응용 프로그램을 등록합니다.
2.  응용 프로그램 이름과 메일을 입력합니다.
3.  *안내식 설정* 옵션이 선택되어 있는지 확인합니다.
4.  지침에 따라 응용 프로그램 ID를 가져와 코드에 붙여넣습니다.

### <a name="option-2-register-your-application-advanced-mode"></a>옵션 2: 응용 프로그램 등록(고급 모드)

1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동해 응용 프로그램을 등록합니다.
2. 응용 프로그램 이름과 메일을 입력합니다. 
3. *안내식 설정* 옵션이 선택 취소되어 있는지 확인합니다.
4.  `Add Platform`를 클릭한 다음 `Web`을 선택합니다.
5. 웹 서버에 따라 응용 프로그램의 URL에 해당하는 `Redirect URL`을 추가합니다. Visual Studio 및 Python에서 리디렉션 URL을 설정/검색하는 방법에 대한 지침은 아래 섹션을 참조하세요.
6. 페이지 맨 아래에 있는 *저장*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>리디렉션 URL을 얻기 위한 Visual Studio 지침
> 지침에 따라 리디렉션 URL을 가져옵니다.
> 1.    *솔루션 탐색기*에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 `F4` 누르기).
> 2.    `URL`의 값을 클립보드로 복사합니다.<br/> ![](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    *응용 프로그램 등록 포털*로 다시 돌아간 후 값을 `Redirect URL`로 붙여 넣고 '저장'을 클릭합니다.

<p/>

> #### <a name="setting-redirect-url-for-python"></a>Python에 대한 리디렉션 URL 설정
> Python의 경우 명령줄을 통해 웹 서버 포트를 설정할 수 있습니다. 이 단계별 설치는 참조를 위해 포트 8080을 사용하지만 다른 포트도 자유롭게 사용 가능합니다. 어떤 경우든, 아래 지침에 따라 응용 프로그램 등록 정보에 리디렉션 URL을 설정합니다.<br/>
> - *응용 프로그램 등록 포털*로 돌아가서 `http://localhost:8080/`을 `Redirect URL`로 설정하거나 사용자 지정 TCP 포트를 사용하는 경우(여기서 *[포트]*는 사용자 지정 TCP 포트 번호) `http://localhost:[port]/`를 사용하고 '저장'을 클릭합니다.


#### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

1.  응용 프로그램 등록 정보를 포함하는 `msalconfig.js`라는 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 마우스 오른쪽 단추로 클릭하고 `Add` > `New Item` > `JavaScript File`을 선택합니다. 이름을 `msalconfig.js`로 지정합니다.
2.  `msalconfig.js` 파일에 다음 코드를 추가합니다.

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
<code>Enter_the_Application_Id_here</code>를 방금 등록한 응용 프로그램 ID로 바꿉니다.
</li>
</ol>
