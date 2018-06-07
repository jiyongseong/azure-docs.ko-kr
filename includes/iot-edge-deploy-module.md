Azure IoT Edge의 주요 기능 중 하나는 클라우드에서 IoT Edge 장치에 모듈을 배포할 수 있다는 것입니다. IoT Edge 모듈은 컨테이너로 구현되는 실행 가능한 패키지입니다. 이 섹션에서는 시뮬레이트된 장치에 대한 원격 분석을 생성하는 모듈을 배포합니다. 

1. Azure Portal에서 IoT Hub로 이동합니다.
1. **IoT Edge(미리 보기)** 로 이동하여 IoT Edge 장치를 선택합니다.
1. **모듈 설정**을 선택합니다.
1. **IoT Edge 모듈 추가**를 선택합니다.
1. **이름** 필드에 `tempSensor`를 입력합니다. 
1. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다. 
1. 다른 설정은 변경하지 않고 **저장**을 선택합니다.

   ![이름 및 이미지 URI 입력 후 IoT Edge 모듈 저장](./media/iot-edge-deploy-module/name-image.png)

1. **모듈 추가** 단계로 돌아가서 **다음**을 선택합니다.
1. **경로 지정** 단계에서 **다음**을 선택합니다.
1. **템플릿 검토** 단계에서 **제출**을 선택합니다.
1. 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 선택합니다. IoT Edge 런타임을 따라 실행되는 새 tempSensor 모듈이 표시됩니다. 

   ![배포된 모듈 목록에서 tempSensor 보기][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png