
## <a name="register-your-application"></a>응용 프로그램 등록
두 가지 방법 중 하나로 응용 프로그램을 등록할 수 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드
다음을 수행하여 응용 프로그램을 신속하게 등록할 수 있습니다.
1. [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure)로 이동합니다.

2. **앱 추가**를 선택합니다.

3. **응용 프로그램 이름** 상자에서 응용 프로그램의 이름을 입력합니다.

4. **단계별 설치** 확인란을 선택한 다음 **만들기**를 선택하도록 합니다.

5. 응용 프로그램 ID를 가져오는 지침에 따라 코드에 붙여넣습니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드
응용 프로그램을 등록하고 응용 프로그램 등록 정보를 솔루션에 추가하려면 다음을 수행합니다.
1. 응용 프로그램을 이미 등록한 경우 [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/portal/register-app)로 이동합니다.

2. **앱 추가**를 선택합니다.

3. **응용 프로그램 이름** 상자에서 응용 프로그램의 이름을 입력합니다. 

4. **단계별 설치** 확인란의 선택을 취소한 다음 **만들기**를 선택하도록 합니다.

5. **플랫폼 추가**를 선택하고, **네이티브 응용 프로그램**을 선택한 다음 **저장**을 선택합니다.

6. **응용 프로그램 ID** 상자에서 GUID를 복사합니다.

7. Visual Studio로 이동하고, *App.xaml.cs* 파일을 열고, `your_client_id_here`을 방금 등록하고 복사한 응용 프로그램 ID로 바꿉니다.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
