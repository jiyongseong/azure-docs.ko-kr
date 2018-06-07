## <a name="configure-the-nodejs-simulated-device"></a>Node.js 시뮬레이션 장치 구성
1. 원격 모니터링 대시보드에서 **+ 장치 추가**를 클릭한 다음 *사용자 지정 장치*를 추가합니다. IoT Hub 호스트 이름, 장치 ID, 장치 키를 메모합니다. 이 자습서에서 나중에 remote_monitoring.js 장치 클라이언트 응용 프로그램을 준비할 때 해당 정보가 필요합니다.
2. Node.js 버전 0.12.x 이상이 개발 컴퓨터에 설치되었는지 확인합니다. 명령 프롬프트 또는 쉘에서 `node --version` 을(를) 실행하고 버전을 확인합니다. Linux에서 Node.js를 설치하기 위한 패키지 관리자 사용에 관한 정보는 [패키지 관리자를 통해 Node.js 설치][node-linux]를 참조하세요.
3. Node.js를 설치했으면 [azure-iot-sdk-node][lnk-github-repo] 리포지토리의 최신 버전을 개발 컴퓨터에 복제합니다. 항상 최신 라이브러리와 샘플 버전의 **마스터** 분기를 사용합니다.
4. [azure-iot-sdk-node][lnk-github-repo] 리포지토리의 로컬 사본의 node/device/samples 폴더에서 다음 두 파일을 개발 컴퓨터의 빈 폴더에 복사합니다.
   
   * packages.json
   * remote_monitoring.js
5. remote_monitoring.js 파일을 열고 다음 변수 정의를 찾습니다.
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. **[IoT Hub device connection string]** 을 장치 연결 문자열로 바꿉니다. 1단계에서 메모한 IoT Hub 호스트 이름, 장치 ID, 장치 키 값을 사용합니다. 장치 연결 문자열은 다음과 같은 형식입니다.
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    IoT Hub 호스트 이름이 **contoso**이고 장치 ID가 **mydevice**이면 연결 문자열은 다음 코드 조각과 같습니다.
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. 파일을 저장합니다. 이러한 파일이 포함된 폴더에서 쉘 또는 명령 프롬프트에 다음 명령을 입력하고 필요한 패키지를 설치하고 샘플 응용 프로그램을 실행합니다.
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>작업 중인 동적 원격 분석 관찰
대시보드는 기존 시뮬레이션 장치의 온도와 습도 원격 분석을 보여줍니다.

![기본 대시보드][image1]

이전 섹션에서 실행한 Node.js 시뮬레이션 장치를 선택했다면 온도, 습도, 외부 온도 원격 분석이 보일 것입니다.

![대시보드에 외부 온도 추가][image2]

원격 모니터링 솔루션은 추가 외부 온도 원격 분석 형식을 자동으로 인식하고 대시보드 차트에 추가합니다.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-v1-send-external-temperature/image1.png
[image2]: media/iot-suite-v1-send-external-temperature/image2.png