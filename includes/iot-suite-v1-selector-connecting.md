> [!div class="op_single_selector"]
> * [Windows에서 C](../articles/iot-suite/iot-suite-v1-connecting-devices.md)
> * [Linux에서 C](../articles/iot-suite/iot-suite-v1-connecting-devices-linux.md)
> * [Node.JS](../articles/iot-suite/iot-suite-v1-connecting-devices-node.md)
> 
> 

## <a name="scenario-overview"></a>시나리오 개요
이 시나리오에서는 원격 모니터링 [미리 구성된 솔루션][lnk-what-are-preconfig-solutions]에 다음과 같은 원격 분석 데이터를 보낼 장치를 만듭니다.

* 외부 온도
* 내부 온도
* 습도

간소함을 위하여 장치의 코드가 샘플 값을 생성하지만, 사용자는 자신의 장치에 실제 센서를 연결하고 실제 원격 분석 데이터를 보내어 샘플을 확장할 것을 권장합니다.

장치는 솔루션 대시보드에서 호출된 메서드 및 솔루션 대시보드에 설정된 desired 속성 값에 응답할 수도 있습니다.

이 자습서를 완료하려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

## <a name="before-you-start"></a>시작하기 전에
장치에 대한 코드를 작성하기 전에, 미리 구성된 원격 모니터링 솔루션을 프로비전하고 이 솔루션에 새로운 사용자 지정 장치를 프로비전해야 합니다.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>미리 구성된 사용자의 원격 모니터링 솔루션 프로비전
이 자습서에서 만드는 장치는 미리 구성된 [원격 모니터링][lnk-remote-monitoring] 솔루션의 인스턴스에 데이터를 전송합니다. Azure 계정에서 미리 구성된 원격 모니터링 솔루션을 미리 프로비전하지 않은 경우 다음 단계를 사용합니다.

1. <https://www.azureiotsuite.com/> 페이지에서 솔루션을 만들려면 **+**를 클릭합니다.
2. **원격 모니터링** 패널에서 **선택**을 클릭하여 솔루션을 만듭니다.
3. **원격 모니터링 솔루션 만들기** 페이지에서 선택한 **솔루션 이름**을 입력하고, 배포하려는 **지역**을 선택한 후, 사용하려는 Azure 구독을 선택합니다. 그런 다음 **솔루션 만들기**를 클릭합니다.
4. 프로비전 프로세스가 완료될 때까지 기다립니다.

> [!WARNING]
> 미리 구성된 솔루션에서는 청구 가능한 Azure 서비스를 사용합니다. 불필요한 비용을 방지하기 위해 완료된 후에는 미리 구성된 솔루션을 구독에서 제거해야 합니다. <https://www.azureiotsuite.com/> 페이지를 방문하여 미리 구성된 솔루션을 구독에서 완전히 제거할 수 있습니다.
> 
> 

원격 모니터링 솔루션의 프로비전 프로세스가 완료되면 **시작** 을 클릭하여 브라우저에서 솔루션 대시보드를 엽니다.

![솔루션 대시보드][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>원격 모니터링 솔루션에서 장치 프로비전
> [!NOTE]
> 솔루션에 장치가 이미 프로비전되어 있으면 이 단계를 건너뜁니다. 클라이언트 응용 프로그램을 만들 때 장치 자격 증명을 알아야 합니다.
> 
> 

미리 구성된 솔루션에 연결하는 장치는 유효한 자격 증명을 사용하여 IoT Hub에 자신을 식별할 수 있어야 합니다. 솔루션 대시보드에서 장치 자격 증명을 검색할 수 있습니다. 이 자습서의 뒷부분에서는 클라이언트 응용 프로그램에 있는 장치 자격 증명을 포함합니다.

원격 모니터링 솔루션에 장치를 추가하려면 솔루션 대시보드에서 다음 단계를 완료합니다.

1. 대시보드의 왼쪽 아래 모서리에서 **장치 추가**를 클릭합니다.
   
   ![장치 추가][1]
2. **사용자 지정 장치** 패널에서 **새로 추가**를 클릭합니다.
   
   ![사용자 지정 장치 추가][2]
3. **직접 나만의 장치 ID 정의**를 선택합니다. 장치 ID(예: **mydevice**)를 입력하고 **ID 확인**을 클릭하여 해당 이름이 이미 사용되고 있는지 확인한 후 **만들기**를 클릭하여 장치를 프로비전합니다.
   
   ![장치 ID 추가][3]
4. 장치 자격 증명(장치 ID, IoT Hub 호스트 이름 및 장치 키)을 적어 둡니다. 원격 모니터링 솔루션에 연결하려면 클라이언트 응용 프로그램에 이러한 값이 필요합니다. **완료**를 클릭합니다.
   
    ![장치 자격 증명 보기][4]
5. 솔루션 대시보드의 장치 목록에서 장치를 선택합니다. 그런 다음 **장치 세부 정보** 패널에서 **장치 사용**을 클릭합니다. 현재 장치 상태는 **실행 중**입니다. 이제 원격 모니터링 솔루션은 장치에서 원격 분석을 수신하고 장치에서 메서드를 호출할 수 있습니다.

[img-dashboard]: ./media/iot-suite-v1-selector-connecting/dashboard.png
[1]: ./media/iot-suite-v1-selector-connecting/suite0.png
[2]: ./media/iot-suite-v1-selector-connecting/suite1.png
[3]: ./media/iot-suite-v1-selector-connecting/suite2.png
[4]: ./media/iot-suite-v1-selector-connecting/suite3.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/