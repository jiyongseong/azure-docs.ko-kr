## <a name="view-device-telemetry-in-the-dashboard"></a>대시보드에서 장치 원격 분석 보기
장치가 IoT Hub에 보내는 원격 분석 데이터를 원격 모니터링 솔루션의 대시보드에서 볼 수 있습니다.

1. 브라우저에서 원격 모니터링 솔루션 대시보드로 돌아가서 왼쪽 패널의 **장치**를 클릭하여 **장치 목록**으로 이동합니다.
2. **장치 목록**에서 장치의 상태가 **실행 중**으로 표시되어야 합니다. 그렇지 않은 경우 **장치 세부 정보** 패널에서 **장치 사용**을 클릭합니다.
   
    ![장치 상태 보기][18]
3. **대시보드**를 클릭하여 대시보드로 돌아가 **볼 장치** 드롭다운에서 장치를 선택하여 원격 분석을 봅니다. 샘플 응용 프로그램의 원격 분석은 내부 온도가 50 단위이고 외부 온도가 55 단위이고 습도가 50 단위입니다.
   
    ![장치 원격 분석 보기][img-telemetry]

## <a name="invoke-a-method-on-your-device"></a>장치에서 메서드 호출
원격 모니터링 솔루션의 대시보드에서는 IoT Hub를 통해 장치에 메서드를 호출하도록 요청할 수 있습니다. 예를 들어 원격 모니터링 솔루션에서 장치 재부팅을 시뮬레이션하는 메서드를 호출할 수 있습니다.

1. 원격 모니터링 솔루션 대시보드에서 왼쪽의 **장치**를 클릭하여 **장치 목록**으로 이동합니다.
2. **장치 목록**에서 사용자 장치의 **장치 ID**를 클릭합니다.
3. **장치 세부 정보** 패널에서 **메서드**를 클릭합니다.
   
    ![장치 메서드][13]
4. **메서드** 드롭다운에서 **InitiateFirmwareUpdate**를 선택한 다음 **FWPACKAGEURI**에 더미 URL을 입력합니다. **메서드 호출**을 클릭하여 장치에서 메서드를 호출합니다.
   
    ![장치 메서드 호출][14]
   

5. 장치가 메서드를 처리하는 경우 장치 코드를 실행하는 콘솔에 메시지가 표시됩니다. 메서드의 결과가 솔루션 포털의 기록에 추가됩니다.

    ![메서드 기록 보기][img-method-history]

## <a name="next-steps"></a>다음 단계
[미리 구성된 솔루션 사용자 지정][lnk-customize] 문서에는 이 샘플을 확대하는 방법이 설명되어 있습니다. 가능한 확장에는 실제 센서 사용 및 추가적인 명령 구현이 포함됩니다.

[azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]에 대해 자세히 살펴보겠습니다.

[13]: ./media/iot-suite-v1-visualize-connecting/suite4.png
[14]: ./media/iot-suite-v1-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-v1-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-v1-visualize-connecting/telemetry.png
[img-method-history]: ./media/iot-suite-v1-visualize-connecting/history.png
[lnk-customize]: ../articles/iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-v1-permissions.md
