---
title: "펌웨어 업데이트를 지원하도록 Node.js를 사용하여 Azure IoT Suite에 Raspberry Pi 연결 | Microsoft Docs"
description: "Raspberry Pi 3 및 Azure IoT Suite에 Microsoft Azure IoT 스타터 키트를 사용합니다. Node.js를 사용하여 Raspberry Pi를 원격 모니터링 솔루션에 연결하고, 센서의 원격 분석을 클라우드로 전송하고, 원격 펌웨어 업데이트를 수행합니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 31bbeff8049c6005671b991f965fae7316e3adf6
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-enable-remote-firmware-updates-using-nodejs"></a>Raspberry Pi 3를 원격 모니터링 솔루션에 연결하고 Node.js를 사용하여 원격 펌웨어 업데이트를 사용하도록 설정

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-selector](../../includes/iot-suite-v1-raspberry-pi-kit-selector.md)]

이 자습서에서는 Raspberry Pi 3에 대해 Microsoft Azure IoT 스타터 키트를 사용하여 다음을 수행하는 방법을 보여 줍니다.

* 클라우드와 통신할 수 있는 온도 및 습도 판독기를 개발합니다.
* 원격 펌웨어 업데이트를 설정하고 수행하여 Raspberry Pi에서 클라이언트 응용 프로그램을 업데이트합니다.

이 자습서에서는 다음을 사용하여 작업을 수행합니다.

- Raspbian OS, Node.js 프로그래밍 언어 및 Node.js용 Microsoft Azure IoT SDK를 사용하여 샘플 장치를 구현합니다.
- 클라우드 기반 백 엔드로 미리 구성된 IoT Suite 원격 모니터링 솔루션

## <a name="overview"></a>개요

이 자습서에서는 다음 단계를 완료합니다.

- Azure 구독에서 미리 구성된 원격 모니터링 솔루션의 인스턴스를 배포합니다. 이 단계에서는 여러 Azure 서비스를 자동으로 배포하고 구성합니다.
- 사용자 컴퓨터 및 원격 모니터링 솔루션과 통신하도록 장치 및 센서를 설정합니다.
- 샘플 장치 코드를 업데이트하여 원격 모니터링 솔루션에 연결하고 솔루션 대시보드에서 볼 수 있는 원격 분석을 보냅니다.
- 샘플 장치 코드를 사용하여 클라이언트 응용 프로그램을 업데이트합니다.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prerequisites](../../includes/iot-suite-v1-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

> [!WARNING]
> 원격 모니터링 솔루션은 Azure 구독에서 Azure 서비스 집합을 프로비전합니다. 배포는 실제 엔터프라이즈 아키텍처를 반영합니다. 불필요한 Azure 사용료가 부과되지 않도록 하려면 배포가 완료된 후 azureiotsuite.com에서 미리 구성된 솔루션 인스턴스를 삭제합니다. 미리 구성된 솔루션이 다시 필요하면 쉽게 다시 만들 수 있습니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-solution](../../includes/iot-suite-v1-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-v1-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

이제 Raspberry Pi에서 원격 모니터링 클라이언트 응용 프로그램을 다운로드하고 구성할 수 있습니다.

### <a name="install-nodejs"></a>Node.js 설치

아직 그렇게 하지 않은 경우 Raspberry Pi에 Node.js를 설치합니다. Node.js용 IoT SDK에는 Node.js 0.11.5 이상 버전의 Node.js가 필요합니다. 다음 단계에서는 Raspberry Pi에 Node.js v6.10.2를 설치하는 방법을 보여 줍니다.

1. 다음 명령을 사용하여 Raspberry Pi를 업데이트합니다.

    ```sh
    sudo apt-get update
    ```

1. 다음 명령을 사용하여 Raspberry Pi에 Node.js 이진 파일을 다운로드합니다.

    ```sh
    wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz
    ```

1. 다음 명령을 사용하여 이진 파일을 설치합니다.

    ```sh
    sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz
    ```

1. 다음 명령을 사용하여 Node.js v6.10.2를 성공적으로 설치했는지 확인합니다.

    ```sh
    node --version
    ```

### <a name="clone-the-repositories"></a>리포지토리 복제

아직 해당 작업을 수행하지 않은 경우 Pi에서 다음 명령을 실행하여 필요한 리포지토리를 복제합니다.

```sh
cd ~
git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git
```

### <a name="update-the-device-connection-string"></a>장치 연결 문자열 업데이트

다음 명령을 사용하여 **nano** 편집기에서 샘플 구성 파일을 엽니다.

```sh
nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/config/deviceinfo
```

자리 표시자 값을 이 자습서를 시작할 때 만들어 저장한 장치 ID 및 IoT Hub 정보로 바꿉니다.

완료되면 deviceinfo 파일 내용이 다음과 같이 표시됩니다.

```conf
yourdeviceid
HostName=youriothubname.azure-devices.net;DeviceId=yourdeviceid;SharedAccessKey=yourdevicekey
```

변경 내용을 저장하고(**Ctrl-O**, **Enter**) 편집기를 종료합니다(**Ctrl-X**).

## <a name="run-the-sample"></a>샘플 실행

다음 명령을 실행하여 이 샘플에 대한 필수 구성 요소 패키지를 설치합니다.

```sh
cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advance/1.0
npm install
```

이제 Raspberry Pi에서 샘플 프로그램을 실행할 수 있습니다. 다음 명령을 입력합니다.

```sh
sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/advanced/1.0/remote_monitoring.js
```

다음 샘플 출력은 Raspberry Pi의 명령 프롬프트에 표시되는 출력의 예입니다.

![Raspberry Pi 앱의 출력][img-raspberry-output]

**Ctrl+C**를 눌러 언제든지 프로그램을 종료합니다.

[!INCLUDE [iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced](../../includes/iot-suite-v1-raspberry-pi-kit-view-telemetry-advanced.md)]

1. 솔루션 대시보드에서 **장치**를 클릭하여 **장치** 페이지로 이동합니다. **장치 목록**에서 Raspberry Pi를 선택합니다. 그런 다음 **메서드**를 선택합니다.

    ![대시보드에서 장치 나열][img-list-devices]

1. **메서드 호출** 페이지의 **메서드** 드롭다운에서 **InitiateFirmwareUpdate**를 선택합니다.

1. **FWPackageURI** 필드에 **https://raw.githubusercontent.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit/master/advanced/2.0/raspberry.js**를 입력합니다. 이 파일에는 펌웨어 버전 2.0 구현이 포함됩니다.

1. **InvokeMethod**를 선택합니다. Raspberry Pi의 앱은 솔루션 대시보드로 승인 메시지를 전송합니다. 그런 다음 새 버전의 펌웨어를 다운로드하여 펌웨어 업데이트 프로세스를 시작합니다.

    ![메서드 기록 표시][img-method-history]

## <a name="observe-the-firmware-update-process"></a>펌웨어 업데이트 프로세스 확인

장치에서 실행될 때, 솔루션 대시보드에서 보고된 속성을 검토하여 펌웨어 업데이트 프로세스를 확인할 수 있습니다.

1. Raspberry Pi에서 업데이트 프로세스의 진행률을 볼 수 있습니다.

    ![업데이트 진행률 표시][img-update-progress]

    > [!NOTE]
    > 업데이트가 완료되면 원격 모니터링 앱이 자동으로 다시 시작됩니다. `ps -ef` 명령을 사용하여 실행 중인지 확인합니다. 프로세스를 종료하려면 프로세스 ID와 함께 `kill` 명령을 사용합니다.

1. 장치에서 보고하는 펌웨어 업데이트의 상태를 솔루션 포털에서 볼 수 있습니다. 다음 스크린샷은 각 업데이트 프로세스 단계의 상태 및 기간과 새로운 펌웨어 버전을 보여 줍니다.

    ![작업 상태 보기][img-job-status]

    대시보드를 다시 이동하여 장치가 펌웨어 업데이트 후에도 계속 원격 분석을 보내는 것을 확인할 수 있습니다.

> [!WARNING]
> Azure 계정에서 원격 모니터링 솔루션을 실행하는 경우 실행하는 동안 요금이 청구됩니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요. 사용을 마친 경우 Azure 계정에서 미리 구성된 솔루션을 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT에 대한 추가 샘플 및 설명서를 보려면 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot/)를 방문하세요.


[img-raspberry-output]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/app-output.png
[img-update-progress]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/updateprogress.png
[img-job-status]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/jobstatus.png
[img-list-devices]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/listdevices.png
[img-method-history]: ./media/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
