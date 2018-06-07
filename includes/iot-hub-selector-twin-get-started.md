> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

장치 쌍은 장치의 상태 정보(메타데이터, 상태 및 조건)를 저장하는 JSON 문서입니다. IoT Hub는 여기에 연결하는 각 장치에 대해 하나의 장치 쌍을 유지합니다.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

장치 쌍의 용도:

* 솔루션 백 엔드의 장치 메타데이터를 저장합니다.
* 장치 앱의 사용 가능한 기능 및 상태(예: 사용된 연결 방법)와 같은 현재 상태 정보를 보고합니다.
* 장치 앱과 백 엔드 앱 간에 장기 실행 워크플로(예: 펌웨어 및 구성 업데이트)의 상태를 동기화합니다.
* 장치 메타데이터, 구성 또는 상태를 쿼리합니다.

장치 쌍은 장치 구성 및 상태를 동기화하고 쿼리하기 위해 설계되었습니다. 장치 쌍을 사용하는 경우에 대한 자세한 내용은 [장치 쌍 이해][lnk-twins]에서 찾을 수 있습니다.

장치 쌍은 IoT hub에 저장되고 다음을 포함합니다.

* *태그*, 솔루션 백 엔드만 액세스할 수 있는 장치 메타데이터.
* *desired 속성*, 솔루션 백 엔드에서 수정할 수 있고 장치 앱에서 관찰할 수 있는 JSON 개체.
* *reported 속성*, 장치 앱에서 수정할 수 있고 솔루션 백 엔드에서 읽을 수 있는 JSON 개체. 태그 및 속성은 배열을 포함할 수 없지만, 개체는 중첩될 수 있습니다.

![][img-twin]

또한 솔루션 백 엔드는 위의 모든 데이터를 기반으로 하는 장치 쌍을 쿼리할 수 있습니다.
장치 쌍에 대한 자세한 내용은 [쌍 장치 이해][lnk-twins]를 참조하고 쿼리는 [IoT Hub 쿼리 언어][lnk-query] 참조를 참고하세요.


이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* *태그*를 장치 쌍에 추가하는 백 엔드 앱과 연결 채널을 *보고된 속성*으로 장치 쌍에 보고하는 시뮬레이션된 장치 앱을 만듭니다.
* 이전에 만든 태그 및 속성에 필터를 사용하여 백 엔드 앱에서 장치를 쿼리합니다.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md