응용 프로그램에서 세분화된 암호 재설정을 사용하려면 **암호 재설정** 정책을 사용합니다. [여기](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)에 테넌트 범위의 암호 재설정 옵션이 지정되어 있습니다. 이 정책은 암호를 재설정하는 동안 고객이 경험하게 될 환경, 그리고 성공적으로 완료 시 응용 프로그램에서 수신하게 될 토큰의 콘텐츠를 설명합니다.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

설정의 정책 섹션에서 **암호 재설정 정책**을 선택하고 **+ 추가**를 선택합니다.

![등록 또는 로그인 정책을 선택하고 추가 단추를 클릭합니다.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-policy.png)

참조할 응용 프로그램에 대한 정책 **이름**을 입력합니다. 예를 들어 `SSPR`을 입력합니다.

**ID 공급자**를 클릭하고 **Reset password using email address**(전자 메일 주소를 사용하여 암호 재설정)를 선택합니다. **확인**을 클릭합니다.

![ID 공급자로 전자 메일 주소를 사용하여 암호 재설정을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

**응용 프로그램 클레임**을 선택합니다. 암호 재설정에 성공하면 응용 프로그램으로 다시 보낸 권한 부여 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID**를 선택합니다.

![일부 응용 프로그램 클레임을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

**만들기**를 클릭하여 정책을 추가합니다. 정책이 **B2C_1_SSPR**로 나열됩니다. **B2C_1_** 접두사가 이름에 추가됩니다.

**B2C_1_SSPR**을 선택하여 해당 정책을 엽니다. 표에 지정된 설정을 확인한 후 **지금 실행**을 클릭합니다.

![정책을 선택하고 실행합니다.](media/active-directory-b2c-create-password-reset-policy/run-b2c-password-reset-policy.png)

| 설정      | 값  |
| ------------ | ------ |
| **응용 프로그램** | Contoso B2C 앱 |
| **회신 URL 선택** | `https://localhost:44316/` |

새 브라우저 탭이 열리고 응용 프로그램에서 암호 재설정 사용자 환경을 확인할 수 있습니다.

> [!NOTE]
> 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>
