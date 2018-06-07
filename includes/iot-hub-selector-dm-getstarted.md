> [!div class="op_single_selector"]
> * [장치: Node.js 서비스: Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [장치: Node.js 서비스: C#](../articles/iot-hub/iot-hub-csharp-node-device-management-get-started.md)
> * [장치: C# 서비스: C#](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [장치: Java 서비스: Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [장치: Python 서비스: Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

백 엔드 앱은 Azure IoT Hub 기본 형식, 즉 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod]를 사용하여 장치에서 장치 관리 작업을 원격으로 시작하고 모니터링할 수 있습니다. 이 자습서에서는 백 엔드 앱 및 장치 앱이 함께 작동하여 IoT Hub를 사용하여 원격 장치 다시 부팅을 시작하고 모니터링하는 방법을 보여 줍니다.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

직접 메서드를 사용하여 클라우드의 백 엔드 앱에서 장치 관리 작업(예: 재부팅, 초기화 및 펌웨어 업데이트)을 시작합니다. 장치는 다음과 같은 역할을 합니다.

* IoT Hub에서 보낸 메서드 요청 처리.
* 장치에서 해당하는 장치 특정 작업 시작.
* *reported 속성*을 통해 IoT Hub에 상태 업데이트 제공.

클라우드에서 백 엔드 앱을 사용하여 장치 쌍 쿼리를 실행하고 장치 관리 작업의 진행 상태를 보고할 수 있습니다.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
