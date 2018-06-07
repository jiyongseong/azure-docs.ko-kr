IoT Hub와 통신할 수 있도록, 시뮬레이트된 장치의 장치 ID를 만듭니다. IoT Edge 장치는 일반적인 IoT 장치와 다르게 작동하며 다른 방식으로 관리될 수 있으므로, 처음부터 IoT Edge 장치로 선언합니다. 

1. Azure Portal에서 IoT Hub로 이동합니다.
1. **IoT Edge(미리 보기)**를 선택한 다음 **IoT Edge 장치 추가**를 선택합니다.

   ![IoT Edge 장치 추가](./media/iot-edge-register-device/add-device.png)

1. 시뮬레이트된 장치에 고유한 장치 ID를 제공합니다.
1. **저장**을 선택하여 장치를 추가합니다.
1. 장치 목록에서 새 장치를 선택합니다.
1. **연결 문자열—기본 키**의 값을 복사하여 저장합니다. 다음 섹션에서 이 값을 사용하여 IoT Edge 런타임을 구성할 것입니다. 

