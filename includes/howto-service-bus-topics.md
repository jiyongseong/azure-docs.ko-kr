## <a name="what-are-service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독 정의
Service Bus 토픽 및 구독은 *게시/구독* 메시징 통신 모델을 지원합니다. 토픽 및 구독을 사용하는 경우 분산 응용 프로그램의 구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 토픽을 통해 메시지를 교환합니다.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

각 메시지가 단일 소비자에 의해 처리되는 Service Bus 큐와는 반대로, 토픽과 구독은 게시/구독 패턴을 사용하여 “일 대 다” 형태의 통신을 제공합니다. 하나의 토픽에 여러 구독을 등록할 수 있습니다. 토픽에 메시지를 전송하면 각 구독에서 독립적으로 처리할 수 있습니다.

토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 필요에 따라 구독 단위로 토픽에 대한 필터 규칙을 등록할 수 있습니다. 필터 규칙을 사용하면 토픽 구독에 따라 토픽에 수신되는 메시지를 필터링 또는 제한할 수 있습니다.

Service Bus 토픽 및 구독을 사용하면 다수의 사용자와 응용 프로그램에 대해 많은 메시지를 확장하고 처리할 수 있습니다.

## <a name="create-a-namespace"></a>네임스페이스 만들기
Azure에서 Service Bus 토픽 및 구독 사용을 시작하려면 먼저 *서비스 네임스페이스*를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 Service Bus 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

네임스페이스를 만들려면

1. [Azure Portal][Azure portal]에 로그인합니다.
2. 포털의 왼쪽 탐색 창에서 **리소스 만들기**, **엔터프라이즈 통합** 및 **Service Bus**를 차례로 클릭합니다.
3. **네임스페이스 만들기** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.
4. 네임스페이스 이름을 사용할 수 있게 설정한 후 가격 책정 계층(기본, 표준 또는 프리미엄)을 선택합니다.
5. **구독** 필드에서 네임스페이스를 만들 Azure 구독을 선택합니다.
6. **리소스 그룹** 필드에서 네임스페이스가 있는 기존 리소스 그룹을 선택하거나 새로 만듭니다.      
7. **위치**에서 네임스페이스가 호스트되어야 하는 국가 또는 지역을 선택합니다.
   
    ![네임스페이스 만들기][create-namespace]
8. **만들기** 단추를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

### <a name="obtain-the-credentials"></a>자격 증명 얻기
1. 네임스페이스 목록에서 새로 만든 네임스페이스 이름을 클릭합니다.
2. **Service Bus 네임스페이스** 창에서 **공유 액세스 정책**을 클릭합니다.
3. **공유 액세스 정책** 창에서 **RootManageSharedAccessKey**를 클릭합니다.
   
    ![connection-info][connection-info]
4. **정책: RootManageSharedAccessKey** 창에서 **연결 문자열–기본 키** 옆의 복사 단추를 클릭하여 나중에 사용하기 위해 연결 문자열을 클립보드에 복사합니다.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


