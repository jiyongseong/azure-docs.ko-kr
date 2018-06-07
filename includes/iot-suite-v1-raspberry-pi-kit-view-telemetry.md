## <a name="view-the-telemetry"></a>원격 분석 보기

이제 Raspberry Pi는 원격 분석을 원격 모니터링 솔루션으로 전송합니다. 솔루션 대시보드에서 원격 분석을 볼 수 있습니다. 솔루션 대시보드에서 Raspberry Pi로 메시지를 보낼 수 있습니다.

- 솔루션 대시보드로 이동합니다.
- **볼 장치** 드롭다운에서 장치를 선택합니다.
- Raspberry Pi의 원격 분석은 대시보드에 표시됩니다.

![Raspberry Pi의 원격 분석 표시][img-telemetry-display]

## <a name="act-on-the-device"></a>장치에 대한 작업

솔루션 대시보드에서 Raspberry Pi에 메서드를 호출할 수 있습니다. Raspberry Pi가 원격 모니터링 솔루션에 연결되는 경우 지원되는 메서드에 대한 정보를 보냅니다.

- 솔루션 대시보드에서 **장치**를 클릭하여 **장치** 페이지로 이동합니다. **장치 목록**에서 Raspberry Pi를 선택합니다. 그런 다음 **메서드**를 선택합니다.

    ![대시보드에서 장치 나열][img-list-devices]

- **메서드 호출** 페이지의 **메서드** 드롭다운에서 **LightBlink**를 선택합니다.

- **InvokeMethod**를 선택합니다. Raspberry Pi에 연결된 LED가 몇 번 깜빡입니다. Raspberry Pi의 앱은 솔루션 대시보드로 승인 메시지를 전송합니다.

    ![메서드 기록 표시][img-method-history]

- **LightStatusValue**를 사용하려면 **1**, 해제하려면 **0**으로 설정하는 **ChangeLightStatus** 메서드를 사용하여 LED 켜기 및 끄기를 전환할 수 있습니다.

> [!WARNING]
> Azure 계정에서 원격 모니터링 솔루션을 실행하는 경우 실행하는 동안 요금이 청구됩니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요. 사용을 마친 경우 Azure 계정에서 미리 구성된 솔루션을 삭제합니다.


[img-telemetry-display]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-v1-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md