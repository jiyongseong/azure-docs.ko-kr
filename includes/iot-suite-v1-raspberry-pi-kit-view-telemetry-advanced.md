## <a name="view-the-telemetry"></a>원격 분석 보기

이제 Raspberry Pi는 원격 분석을 원격 모니터링 솔루션으로 전송합니다. 솔루션 대시보드에서 원격 분석을 볼 수 있습니다. 솔루션 대시보드에서 Raspberry Pi로 메시지를 보낼 수 있습니다.

- 솔루션 대시보드로 이동합니다.
- **볼 장치** 드롭다운에서 장치를 선택합니다.
- Raspberry Pi의 원격 분석은 대시보드에 표시됩니다.

![Raspberry Pi의 원격 분석 표시][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>펌웨어 업데이트 시작

펌웨어 업데이트 프로세스는 Raspberry Pi에 업데이트된 버전의 장치 클라이언트 응용 프로그램을 다운로드하고 설치합니다. 펌웨어 업데이트 프로세스에 대한 자세한 내용은 [IoT Hub를 사용한 장치 관리 개요][lnk-update-pattern]에서 펌웨어 업데이트 패턴 설명을 참조하세요.

장치에서 메서드를 호출하여 펌웨어 업데이트 프로세스를 시작합니다. 이 메서드는 비동기적이고 업데이트 프로세스를 시작하는 즉시 반환됩니다. 장치는 보고된 속성을 사용하여 업데이트의 진행률에 대한 솔루션을 알려 줍니다.

솔루션 대시보드의 Raspberry Pi에 대한 메서드를 호출할 수 있습니다. Raspberry Pi가 먼저 원격 모니터링 솔루션에 연결되는 경우 지원되는 메서드에 대한 정보를 보냅니다. 

[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
