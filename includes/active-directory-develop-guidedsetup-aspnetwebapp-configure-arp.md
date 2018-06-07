
## <a name="configure-your-aspnet-web-app-with-the-applications-registration-information"></a>응용 프로그램의 등록 정보를 사용하여 ASP.NET 웹앱 구성

이 단계에서는 SSL을 사용한 다음 SSL URL을 사용하여 응용 프로그램 등록 정보를 구성하도록 프로젝트를 구성합니다. 그런 다음 *web.config*를 통해 솔루션에 응용 프로그램 등록 정보를 추가합니다.

1.  솔루션 탐색기에서 프로젝트를 선택하고 `Properties` 창을 확인합니다([속성] 창이 보이지 않으면 F4 누르기).
2.  `SSL Enabled`를 `True`로 변경
3.  위의 `SSL URL`에서 값을 복사해 이 페이지 맨 위에 있는 `Redirect URL` 필드에 붙여넣은 다음 [업데이트]를 클릭합니다.<br/><br/>![](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
4.  다음 내용을 루트 폴더에 있는 `web.config` 파일의 `configuration\appSettings` 섹션 아래에 추가합니다.

```xml
<add key="ClientId" value="[Enter the application Id here]" />
<add key="RedirectUri" value="[Enter the Redirect URL here]" />
<add key="Tenant" value="common" />
<add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
```
