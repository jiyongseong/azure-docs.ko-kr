[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

웹 응용 프로그램을 등록하려면 표에 지정된 설정을 사용합니다.

![새 웹앱에 대한 등록 설정 예](./media/active-directory-b2c-register-web-app/b2c-new-app-settings.png)

| 설정      | 샘플 값  | 설명                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Contoso B2C 앱 | 소비자에게 응용 프로그램을 설명하는 응용 프로그램 **이름**을 입력합니다. | 
| **웹앱/웹 API 포함** | 예 | 웹 응용 프로그램에 **예**를 선택합니다. |
| **암시적 흐름 허용** | 예 | 응용 프로그램이 [OpenID Connect 로그인](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)을 사용할 경우 **예** 선택 |
| **회신 URL** | `https://localhost:44316` | 회신 URL은 Azure AD B2C에서 응용 프로그램이 요청한 토큰을 반환하는 끝점입니다. [적절한](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **회신 URL**을 입력합니다. 이 예에서는 앱이 로컬이고 포트 44316에서 수신하고 있습니다. |

**만들기**를 클릭하여 응용 프로그램을 등록합니다.

새로 등록된 응용 프로그램은 B2C 테넌트에 대한 응용 프로그램 목록에 표시됩니다. 목록에서 웹앱을 선택합니다. 웹 응용 프로그램의 속성 창이 표시 됩니다.

![웹앱 속성](./media/active-directory-b2c-register-web-app/b2c-web-app-properties.png)

전역적으로 고유한 **응용 프로그램 클라이언트 ID**를 기록합니다. 응용 프로그램 코드의 ID를 사용합니다.