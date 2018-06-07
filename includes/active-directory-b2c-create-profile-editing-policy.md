응용 프로그램에서 프로필 편집을 사용하도록 설정하려면 **프로필 편집** 정책을 사용합니다. 이 정책은 프로필을 편집하는 동안 고객이 경험하게 될 환경, 그리고 성공적으로 완료 시 응용 프로그램에서 수신하게 될 토큰의 콘텐츠를 설명합니다.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

설정의 정책 섹션에서 **프로필 편집 정책**을 선택하고 **+ 추가**를 선택합니다.

![프로필 편집 정책을 선택하고 추가 버튼을 클릭합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

참조할 응용 프로그램에 대한 정책 **이름**을 입력합니다. 예를 들어 `SiPe`을 입력합니다.

**ID 공급자**를 클릭하고 **로컬 계정 로그인**을 선택합니다. 또한 필요에 따라 이미 구성되어 있는 소셜 ID 공급자를 선택할 수 있습니다. **확인**을 클릭합니다.

![ID 공급자로 로컬 계정 등록을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

**프로필 특성**을 선택합니다. 소비자가 프로필에서 보고 편집할 수 있는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택합니다. **확인**을 클릭합니다.

![일부 특성을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

**응용 프로그램 클레임**을 선택합니다. 프로필 편집에 성공하면 응용 프로그램으로 다시 보낸 권한 부여 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **우편 번호**를 선택합니다.

![일부 응용 프로그램 클레임을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

**만들기**를 클릭하여 정책을 추가합니다. 정책이 **B2C_1_SiPe**로 나열됩니다. **B2C_1_** 접두사가 이름에 추가됩니다.

**B2C_1_SiPe**를 클릭하여 해당 정책을 엽니다. 표에 지정된 설정을 확인한 후 **지금 실행**을 클릭합니다.

![정책을 선택하고 실행합니다.](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| 설정      | 값  |
| ------------ | ------ |
| **응용 프로그램** | Contoso B2C 앱 |
| **회신 URL 선택** | `https://localhost:44316/` |

새 브라우저 탭이 열리고 구성된 프로필 편집 소비자 환경을 확인할 수 있습니다.

> [!NOTE]
> 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>