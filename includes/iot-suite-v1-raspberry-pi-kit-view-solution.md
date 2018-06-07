## <a name="view-the-solution-dashboard"></a>솔루션 대시보드 보기

솔루션 대시보드를 사용하면 배포된 솔루션을 관리할 수 있습니다. 예를 들어 원격 분석 보기, 장치 추가 및 메서드 호출 작업을 수행할 수 있습니다.

1. 프로비전이 완료되고 미리 구성된 솔루션에 대한 타일이 **준비**를 가리키면 **시작**을 선택하여 새 탭에서 원격 모니터링 솔루션 포털을 엽니다.

    ![미리 구성된 솔루션 시작][img-launch-solution]

1. 기본적으로 솔루션 포털에 *대시보드*가 표시됩니다. 페이지 왼쪽의 메뉴를 사용하여 솔루션 포털의 다른 영역으로 이동할 수 있습니다.

    ![미리 구성된 원격 모니터링 솔루션 대시보드][img-menu]

## <a name="add-a-device"></a>장치 추가

미리 구성된 솔루션에 연결하는 장치는 유효한 자격 증명을 사용하여 IoT Hub에 자신을 식별할 수 있어야 합니다. 솔루션 대시보드에서 장치 자격 증명을 검색할 수 있습니다. 이 자습서의 뒷부분에서는 클라이언트 응용 프로그램에 있는 장치 자격 증명을 포함합니다.

원격 모니터링 솔루션에 사용자 지정 장치를 추가하지 았다면 추가합니다. 솔루션 대시보드에서 다음 단계를 완료합니다.

1. 대시보드의 왼쪽 아래 모서리에서 **장치 추가**를 클릭합니다.

   ![장치 추가][1]

1. **사용자 지정 장치** 패널에서 **새로 추가**를 클릭합니다.

   ![사용자 지정 장치 추가][2]

1. **직접 나만의 장치 ID 정의**를 선택합니다. 장치 ID(예: **rasppi**)를 입력하고 **ID 확인**을 클릭하여 솔루션에서 해당 이름이 이미 사용되고 있는지 확인한 다음 **만들기**를 클릭하여 장치를 프로비전합니다.

   ![장치 ID 추가][3]

1. 장치 자격 증명(**장치 ID**, **IoT Hub 호스트 이름** 및 **장치 키**)을 적어 둡니다. 원격 모니터링 솔루션에 연결하려면 Raspberry Pi의 클라이언트 응용 프로그램에 이러한 값이 필요합니다. **완료**를 클릭합니다.

    ![장치 자격 증명 보기][4]

1. 솔루션 대시보드의 장치 목록에서 장치를 선택합니다. 그런 다음 **장치 세부 정보** 패널에서 **장치 사용**을 클릭합니다. 현재 장치 상태는 **실행 중**입니다. 이제 원격 모니터링 솔루션은 장치에서 원격 분석을 수신하고 장치에서 메서드를 호출할 수 있습니다.

[img-launch-solution]: media/iot-suite-v1-raspberry-pi-kit-view-solution/launch.png
[img-menu]: media/iot-suite-v1-raspberry-pi-kit-view-solution/menu.png
[1]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite0.png
[2]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite1.png
[3]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite2.png
[4]: media/iot-suite-v1-raspberry-pi-kit-view-solution/suite3.png