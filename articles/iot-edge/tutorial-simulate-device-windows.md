---
title: Windows에서 Azure IoT Edge 시뮬레이트 | Microsoft Docs
description: Windows에서 시뮬레이트된 장치에 Azure IoT Edge 런타임을 설치하고 첫 번째 모듈 배포
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 213a0e7cebda6a8b89ef460799cbec477b487a64
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166117"
---
# <a name="deploy-azure-iot-edge-on-a-simulated-device-in-windows----preview"></a>Windows에서 시뮬레이트된 장치에 Azure IoT Edge 배포 - 미리 보기

Azure IoT Edge에서는 클라우드로 모든 데이터를 푸시하는 대신, 사용자 장치에서 분석 및 데이터 처리를 수행할 수 있습니다. IoT Edge 자습서에는 Azure 서비스 또는 사용자 지정 코드에서 작성된 다양한 유형의 모듈을 배포하는 방법이 설명되어 있으나 먼저 테스트할 장치가 필요합니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

1. IoT Hub 만들기
2. IoT Edge 장치 등록
3. IoT Edge 런타임 시작
4. 모듈 배포

![자습서 아키텍처][2]

이 자습서에서 만드는 시뮬레이트된 장치는 온도, 습도 및 압력 데이터를 생성하는 풍력 터빈의 모니터입니다. 터빈은 날씨 조건에 따라 다양한 효율성 수준에서 수행되므로 이 데이터에 관심이 있을 것입니다. 다른 Azure IoT Edge 자습서에서는 비즈니스 정보를 위해 데이터를 분석하는 모듈을 배포하는 과정을 설명하므로 여기에서 수행하는 작업을 토대로 진행됩니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 사용자가 Windows를 실행하는 컴퓨터 또는 가상 머신을 사용하여 사물 인터넷을 시뮬레이션한다고 가정합니다. 

>[!TIP]
>가상 머신에서 Windows를 실행하는 경우 [중첩된 가상화][lnk-nested]를 사용하도록 설정하고 최소 2GB 메모리를 할당하세요. 

1. 지원되는 Windows 버전을 사용하고 있는지 확인합니다.
   * 윈도우 10 
   * Windows Server
2. [Windows용 Docker][lnk-docker]를 설치하고, 지금 실행 중인지 확인합니다.
3. [Windows에 Python][lnk-python]을 설치하고 pip 명령을 사용할 수 있는지 확인합니다. 이 자습서는 Python 버전 2.7.9 이상 및 3.5.4 이상에서 테스트되었습니다.  
4. 다음 명령을 실행하여 IoT Edge 제어 스크립트를 다운로드합니다.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge는 Windows 컨테이너 또는 Linux 컨테이너를 실행할 수 있습니다. 다음 Windows 버전 중 하나를 실행하는 경우 Windows 컨테이너를 사용할 수 있습니다.
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709(빌드 16299)
>    * x64 기반 장치에서 Windows IoT Core(빌드 16299)
>
> Windows IoT Core의 경우 [Windows IoT Core에 IoT Edge 런타임 설치][lnk-install-iotcore]의 지침을 따릅니다. 그렇지 않은 경우 간단히 [Windows 컨테이너를 사용하도록 Docker를 구성][lnk-docker-containers]합니다. 다음 명령을 사용하여 필수 구성 요소가 유효한지 확인합니다.
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## <a name="create-an-iot-hub"></a>IoT Hub 만들기

IoT Hub를 만들어 이 자습서를 시작합니다.
![IoT Hub 만들기][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>IoT Edge 장치 등록

새로 만든 IoT Hub에 IoT Edge 장치를 등록합니다.
![장치 등록][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>IoT Edge 런타임 구성

장치에 Azure IoT Edge 런타임을 설치하고 시작합니다. 
![장치 등록][5]

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 두 개의 모듈로 구성됩니다. **IoT Edge 에이전트**는 IoT Edge 장치에서 모듈의 배포 및 모니터링을 지원합니다. **IoT Edge 허브**는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 새 장치에서 런타임을 구성하면 처음에 IoT Edge 에이전트만 시작됩니다. IoT Edge 허브는 나중에 모듈을 배포할 때 제공됩니다. 


이전 섹션의 IoT Edge 장치 연결 문자열로 런타임을 구성합니다.

```cmd
iotedgectl setup --connection-string "{device connection string}" --nopass
```

런타임을 시작합니다.

```cmd
iotedgectl start
```

Docker를 확인하여 IoT Edge 에이전트가 모듈로 실행되고 있는지 알아봅니다.

```cmd
docker ps
```

![Docker의 edgeAgent 보기](./media/tutorial-simulate-device-windows/docker-ps.png)

## <a name="deploy-a-module"></a>모듈 배포

클라우드에서 Azure IoT Edge 장치를 관리하여 원격 분석 데이터를 IoT Hub로 전송하는 모듈을 배포합니다.
![장치 등록][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## <a name="view-generated-data"></a>생성된 데이터 보기

이 자습서에서는 새 IoT Edge 장치를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음 장치 자체를 변경하지 않고도 장치에서 실행할 IoT Edge 모듈을 푸시할 수 있도록 Azure Portal을 사용했습니다. 이 경우 푸시한 모듈에서는 자습서에 대해 사용할 수 있는 환경 데이터를 만듭니다. 

시뮬레이션된 장치를 실행 중인 컴퓨터에서 명령 프롬프트를 다시 엽니다. 클라우드에서 배포된 모듈을 IoT Edge 장치에서 실행 중인지 확인합니다. 

```cmd
docker ps
```

![장치에서 세 가지 모듈 보기](./media/tutorial-simulate-device-windows/docker-ps2.png)

tempSensor 모듈에서 클라우드로 전송되는 메시지를 봅니다. 

```cmd
docker logs -f tempSensor
```

![모듈의 데이터 보기](./media/tutorial-simulate-device-windows/docker-logs.png)

[IoT Hub 탐색기 도구][lnk-iothub-explorer]를 사용하여 장치에서 보내는 원격 분석을 볼 수도 있습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 새 IoT Edge 장치를 만들고 Azure IoT Edge 클라우드 인터페이스를 사용하여 장치에 코드를 배포했습니다. 이제 해당 환경에 대한 원시 데이터를 생성하는 시뮬레이트된 장치가 준비되었습니다. 

이 자습서는 다른 모든 IoT Edge 자습서를 진행하기 전에 반드시 먼저 진행해야 합니다. 다른 자습서를 계속 진행하면서 Azure IoT Edge가 이러한 데이터를 통해 비즈니스 통찰력을 얻는 데 어떻게 도움을 줄 수 있는지 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [C# 코드를 모듈로 개발 및 배포](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md