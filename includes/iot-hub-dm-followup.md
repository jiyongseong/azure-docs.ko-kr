## <a name="customize-and-extend-the-device-management-actions"></a>장치 관리 작업 사용자 지정 및 확장

IoT 솔루션에서 정의된 장치 관리 패턴 집합을 확장하거나 장치 쌍 및 클라우드-장치 메서드 기본 형식을 사용하여 사용자 지정 패턴을 활성화하도록 설정할 수 있습니다. 장치 관리 작업의 다른 예로 공장 재설정, 펌웨어 업데이트, 소프트웨어 업데이트, 전원 관리, 네트워크 및 연결 관리, 데이터 암호화가 있습니다.

## <a name="device-maintenance-windows"></a>장치 유지 관리 기간

일반적으로 서비스 중단 및 가동 중지 시간을 최소화하면서 작업을 수행하도록 장치를 구성합니다. 장치 유지 관리 기간은 장치에서 해당 구성을 업데이트해야 할 경우 시간을 정의하는 데 널리 사용되는 패턴입니다. 백 엔드 솔루션에서는 장치 쌍의 desired 속성을 사용하여 유지 관리 기간을 사용하는 장치에 대한 정책을 정의하고 활성화할 수 있습니다. 장치에서 유지 관리 기간 정책을 수신하면 장치 쌍의 reported 속성을 사용하여 정책의 상태를 보고할 수 있습니다. 그런 다음 백 엔드 앱은 장치 쌍 쿼리를 사용하여 장치 및 각 정책의 규정 준수를 입증합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 장치에서 원격 다시 시작을 트리거하는 데 직접 메서드를 사용했습니다. 보고된 속성을 사용하여 장치에서 마지막으로 다시 시작한 시간을 보고하고, 장치 쌍을 쿼리하여 장치가 클라우드에서 마지막으로 다시 시작한 시간을 확인했습니다.

IoT Hub 및 장치 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 다음을 참조하세요

[자습서: 펌웨어 업데이트를 수행하는 방법][lnk-fwupdate]

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

계속해서 IoT Hub를 시작하려면 [IoT Edge 시작][lnk-iot-edge]을 참조하세요.

[lnk-fwupdate]: ../articles/iot-hub/iot-hub-node-node-firmware-update.md
[lnk-tutorial-jobs]: ../articles/iot-hub/iot-hub-node-node-schedule-jobs.md
[lnk-iot-edge]: ../articles/iot-edge/tutorial-simulate-device-linux.md