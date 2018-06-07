[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

설정의 정책 섹션에서 **등록 또는 로그인 정책**을 선택하고 **+ 추가**를 클릭합니다.

![등록 또는 로그인 정책을 선택하고 추가 단추를 클릭합니다.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

참조할 응용 프로그램에 대한 정책 **이름**을 입력합니다. 예를 들어 `SiUpIn`을 입력합니다.

**ID 공급자**를 선택한 다음 **전자 메일 등록**을 선택합니다. 또한 필요에 따라 이미 구성되어 있는 소셜 ID 공급자를 선택할 수 있습니다. **확인**을 클릭합니다.

![ID 공급자로 전자 메일 등록을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

**등록 특성**을 선택합니다. 등록하는 동안 소비자로부터 수집하려는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택합니다. **확인**을 클릭합니다.

![일부 특성을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

**응용 프로그램 클레임**을 선택합니다. 성공적인 등록 또는 로그인 환경 이후에 응용 프로그램으로 다시 전송된 권한 부여 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호**, **새 사용자** 및 **사용자의 개체 ID**를 선택합니다.

![일부 응용 프로그램 클레임을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

**만들기**를 클릭하여 정책을 추가합니다. 정책이 **B2C_1_SiUpIn**으로 나열됩니다. **B2C_1_** 접두사가 이름에 추가됩니다.

**B2C_1_SiUpIn**을 클릭하여 해당 정책을 엽니다. 표에 지정된 설정을 확인한 후 **지금 실행**을 클릭합니다.

![정책을 선택하고 실행합니다.](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| 설정      | 값  |
| ------------ | ------ |
| **응용 프로그램** | Contoso B2C 앱 |
| **회신 URL 선택** | `https://localhost:44316/` |

새 브라우저 탭이 열리고 구성된 대로 등록 또는 로그인 소비자 환경을 실행할 수 있습니다.

> [!NOTE]
> 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>