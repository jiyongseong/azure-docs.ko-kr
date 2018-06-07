Azure에서 Service Bus 메시징 엔터티 사용을 시작하려면 먼저 Azure에서 고유한 이름인 네임스페이스를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 Service Bus 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

네임스페이스를 만들려면

1. [Azure Portal][Azure portal]에 로그인합니다.
2. 포털의 왼쪽 탐색 창에서 **+ 리소스 만들기**, **엔터프라이즈 통합** 및 **Service Bus**를 차례로 클릭합니다.
3. **네임스페이스 만들기** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.
4. 네임스페이스 이름을 사용할 수 있게 설정한 후 가격 책정 계층(기본, 표준 또는 프리미엄)을 선택합니다.
5. **구독** 필드에서 네임스페이스를 만들 Azure 구독을 선택합니다.
6. **리소스 그룹** 필드에서 네임스페이스가 있는 기존 리소스 그룹을 선택하거나 새로 만듭니다.      
7. **위치**에서 네임스페이스가 호스트되어야 하는 국가 또는 지역을 선택합니다.
   
    ![네임스페이스 만들기][create-namespace]
8. **만들기**를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

### <a name="obtain-the-management-credentials"></a>관리 자격 증명 얻기
새 네임 스페이스를 만들면 네임스페이스의 모든 측면에 대한 모든 권한을 부여하는 기본 및 보조 키의 연결된 쌍을 포함한 초기 SAS(공유 액세스 서명) 규칙이 자동으로 생성됩니다. 정기적으로 보낸 사람과 받는 사람을 위해 제한된 권한을 사용하여 추가 규칙을 만드는 방법에 대한 자세한 내용은 [Service Bus 인증 및 권한 부여](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md)를 참조하세요. 초기 규칙을 복사하려면 다음 단계를 수행합니다. 

1.  **모든 리소스**를 클릭한 다음 새로 만든 네임스페이스 이름을 클릭합니다.
2. 네임스페이스 창에서 **공유 액세스 정책**을 클릭합니다.
3. **공유 액세스 정책** 화면에서 **RootManageSharedAccessKey**를 클릭합니다.
   
    ![connection-info][connection-info]
4. **정책: RootManageSharedAccessKey** 창에서 **연결 문자열–기본 키** 옆의 복사 단추를 클릭하여 나중에 사용하기 위해 연결 문자열을 클립보드에 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 붙여 넣습니다.
   
    ![connection-string][connection-string]

5. 이전 단계를 반복하여 나중에 사용할 수 있도록 **기본 키** 값을 임시 위치에 복사 및 붙여넣기합니다.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
