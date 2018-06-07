---
title: Linux에서 Azure IoT Edge 시뮬레이트 | Microsoft Docs
description: Linux에서 시뮬레이션트된 장치에 Azure IoT Edge 런타임을 설치하고 첫 번째 모듈 배포
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 01/11/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b7f0aa514cfffb8fb2ca76238a6a65a8e6443e56
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-linux-or-macos---preview"></a>Linux 또는 MacOS에서 시뮬레이션된 장치에 Azure IoT Edge 배포 - 미리 보기

Azure IoT Edge에서는 클라우드로 모든 데이터를 푸시하는 대신, 사용자 장치에서 분석 및 데이터 처리를 수행할 수 있습니다. IoT Edge 자습서에는 Azure 서비스 또는 사용자 지정 코드에서 작성된 다양한 유형의 모듈을 배포하는 방법이 설명되어 있으나 먼저 테스트할 장치가 필요합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

1. IoT Hub 만들기
2. IoT Edge 장치 등록
3. IoT Edge 런타임 시작
4. 모듈 배포

![자습서 아키텍처][2]

이 자습서에서 만드는 시뮬레이트된 장치는 온도, 습도 및 압력 데이터를 생성하는 모니터입니다. 다른 Azure IoT Edge 자습서에서는 비즈니스 정보를 위해 데이터를 분석하는 모듈을 배포하는 과정을 설명하므로 여기에서 수행하는 작업을 토대로 진행됩니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 IoT(사물 인터넷) 장치와 같은 컴퓨터 또는 가상 머신을 사용합니다. 컴퓨터를 IoT Edge 장치로 전환하려면 다음 서비스가 필요합니다.

* Python pip - IoT Edge 런타임을 설치합니다.
   * Linux: `sudo apt-get install python-pip`
     * _특정 배포(예: Raspbian)에서 특정 pip 패키지를 업그레이드하고 추가 종속성을 설치해야 할 수도 있습니다._
     ```
     sudo pip install --upgrade setuptools pip
     
     sudo apt-get install python2.7-dev libffi-dev libssl-dev
     ```
   * MacOS: `sudo easy_install pip`
* Docker - IoT Edge 모듈을 실행합니다.
   * [Linux용 Docker를 설치][lnk-docker-ubuntu]하고 실행 중인지 확인합니다. 
   * [Mac용 Docker를 설치][lnk-docker-mac]하고 실행 중인지 확인합니다. 

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

IoT Hub를 만들어 이 자습서를 시작합니다.
![IoT Hub 만들기][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge 장치 등록

새로 만든 IoT Hub에 IoT Edge 장치를 등록합니다.
![장치 등록][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>IoT Edge 런타임 설치 및 시작

장치에 Azure IoT Edge 런타임을 설치하고 시작합니다. 
![장치 등록][5]

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 두 개의 모듈로 구성됩니다. **IoT Edge 에이전트**는 IoT Edge 장치에서 모듈의 배포 및 모니터링을 지원합니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 새 장치에서 런타임을 구성하면 처음에 IoT Edge 에이전트만 시작됩니다. IoT Edge 허브는 나중에 모듈을 배포할 때 제공됩니다. 

IoT Edge 장치를 실행할 컴퓨터에서 IoT Edge 컨트롤 스크립트를 다운로드합니다.
```cmd
sudo pip install -U azure-iot-edge-runtime-ctl
```

이전 섹션의 IoT Edge 장치 연결 문자열로 런타임을 구성합니다.
```cmd
sudo iotedgectl setup --connection-string "{device connection string}" --nopass
```

런타임을 시작합니다.
```cmd
sudo iotedgectl start
```

Docker를 확인하여 IoT Edge 에이전트가 모듈로 실행되고 있는지 알아봅니다.
```cmd
sudo docker ps
```

![Docker의 edgeAgent 보기](./media/tutorial-simulate-device-linux/docker-ps.png)

## <a name="deploy-a-module"></a>모듈 배포

클라우드에서 Azure IoT Edge 장치를 관리하여 원격 분석 데이터를 IoT Hub로 전송하는 모듈을 배포합니다.
![장치 등록][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>생성된 데이터 보기

이 자습서에서는 새 IoT Edge 장치를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음 장치 자체를 변경하지 않고도 장치에서 실행할 IoT Edge 모듈을 푸시할 수 있도록 Azure Portal을 사용했습니다. 이 경우 푸시한 모듈에서는 자습서에 대해 사용할 수 있는 환경 데이터를 만듭니다. 

시뮬레이션된 장치를 실행 중인 컴퓨터에서 명령 프롬프트를 다시 엽니다. 클라우드에서 배포된 모듈을 IoT Edge 장치에서 실행 중인지 확인합니다.

```cmd
sudo docker ps
```

![장치에서 세 가지 모듈 보기](./media/tutorial-simulate-device-linux/docker-ps2.png)

tempSensor 모듈에서 클라우드로 전송되는 메시지를 봅니다.

```cmd
sudo docker logs -f tempSensor
```

![모듈의 데이터 보기](./media/tutorial-simulate-device-linux/docker-logs.png)

[IoT Hub 탐색기 도구][lnk-iothub-explorer]를 사용하여 장치에서 보내는 원격 분석을 볼 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새 IoT Edge 장치를 만들고 Azure IoT Edge 클라우드 인터페이스를 사용하여 장치에 코드를 배포했습니다. 이제 해당 환경에 대한 원시 데이터를 생성하는 시뮬레이트된 장치가 준비되었습니다. 

이 자습서는 다른 모든 IoT Edge 자습서를 진행하기 전에 반드시 먼저 진행해야 합니다. 다른 자습서를 계속 진행하면서 Azure IoT Edge가 이러한 데이터를 통해 비즈니스 통찰력을 얻는 데 어떻게 도움을 줄 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [C# 코드를 모듈로 개발 및 배포](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/tutorial-install-iot-edge/view-module.png
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-docker-ubuntu]: https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/ 
[lnk-docker-mac]: https://docs.docker.com/docker-for-mac/install/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
