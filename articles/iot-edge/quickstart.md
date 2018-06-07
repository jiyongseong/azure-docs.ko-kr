---
title: Azure IoT Edge + Windows 빠른 시작 | Microsoft Docs
description: 시뮬레이트된 에지 장치에서 분석을 실행하여 Azure IoT Edge를 시도합니다.
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/03/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 888f74d215956f4ad38605ca247f681da700a787
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166219"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>빠른 시작: Azure Portal에서 Windows 장치(미리 보기)로 첫 번째 IoT Edge 모듈을 배포합니다.

이 빠른 시작에서 Azure IoT Edge 클라우드 인터페이스를 사용하여 사전 빌드된 코드를 IoT Edge 장치에 원격으로 배포합니다. 이 작업을 수행하려면 먼저 Windows 장치를 사용하여 IoT Edge 장치를 시뮬레이션한 다음 모듈을 배포할 수 있습니다.

활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][lnk-account]을 만드세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 사용자가 Windows를 실행하는 컴퓨터 또는 가상 머신을 사용하여 사물 인터넷을 시뮬레이션한다고 가정합니다. 가상 머신에서 Windows를 실행하는 경우 [중첩된 가상화][lnk-nested]를 사용하도록 설정하고 최소 2GB 메모리를 할당하세요. 

1. 지원되는 Windows 버전을 사용하고 있는지 확인합니다.
   * 윈도우 10 
   * Windows Server
2. [Windows용 Docker][lnk-docker]를 설치하고, 지금 실행 중인지 확인합니다.
3. [Windows에 Python][lnk-python]을 설치하고 pip 명령을 사용할 수 있는지 확인합니다. 이 빠른 시작은 Python 버전 2.7.9 이상 및 3.5.4 이상에서 테스트되었습니다.  
4. 다음 명령을 실행하여 IoT Edge 제어 스크립트를 다운로드합니다.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge는 Windows 컨테이너 또는 Linux 컨테이너를 실행할 수 있습니다. Windows 컨테이너를 사용하려면 다음을 실행해야 합니다.
>    * Windows 10 Fall Creators Update 또는
>    * Windows Server 1709(빌드 16299) 또는
>    * x64 기반 장치에서 Windows IoT Core(빌드 16299)
>
> Windows IoT Core의 경우 [Windows IoT Core에 IoT Edge 런타임 설치][lnk-install-iotcore]의 지침을 따릅니다. 아니면 [Windows 컨테이너를 사용하도록 Docker를 구성][lnk-docker-containers]하고, 선택적으로 다음과 같은 powershell 명령을 사용하여 필수 조건을 검증합니다.
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```

## <a name="create-an-iot-hub-with-azure-cli"></a>Azure CLI를 사용하여 IoT Hub 만들기

Azure 구독에서 IoT Hub를 만듭니다. 이 빠른 시작에는 무료 수준의 IoT Hub가 작동합니다. 전에 IoT Hub를 사용했으며 이미 무료 허브를 만든 경우, 이 섹션을 건너뛰고 [IoT Edge 장치 등록][anchor-register]으로 이동할 수 있습니다. 구독마다 하나의 무료 IoT Hub만 가질 수 있습니다. 

1. [Azure Portal][lnk-portal]에 로그인합니다. 
1. **Cloud Shell** 단추를 선택합니다. 

   ![Cloud Shell 단추][1]

1. 리소스 그룹을 만듭니다. 다음 코드는 **미국 서부** 지역에 **IoTEdge**라는 리소스 그룹을 만듭니다.

   ```azurecli
   az group create --name IoTEdge --location westus
   ```

1. 새 리소스 그룹에 IoT Hub를 만듭니다. 다음 코드는 리소스 그룹 **IoTEdge**에 **MyIotHub**라는 무료 **F1** 허브를 만듭니다.

   ```azurecli
   az iot hub create --resource-group IoTEdge --name MyIotHub --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>IoT Edge 장치 등록

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="configure-the-iot-edge-runtime"></a>IoT Edge 런타임 구성

IoT Edge 런타임은 모든 IoT Edge 장치에 배포되며, 두 개의 모듈로 구성됩니다. 첫째, IoT Edge 에이전트는 IoT Edge 장치에서 모듈의 배포 및 모니터링을 지원합니다. 둘째, IoT Edge 허브는 IoT Edge 장치의 모듈 간 통신과 장치와 IoT Hub 간의 통신을 관리합니다. 

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

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>생성된 데이터 보기

이 빠른 시작에서는 새 IoT Edge 장치를 만들고 여기에 IoT Edge 런타임을 설치했습니다. 그런 다음 장치 자체를 변경하지 않고도 장치에서 실행할 IoT Edge 모듈을 푸시할 수 있도록 Azure Portal을 사용했습니다. 이 경우 푸시한 모듈에서는 자습서에 대해 사용할 수 있는 환경 데이터를 만듭니다. 

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
## <a name="clean-up-resources"></a>리소스 정리

생성한 시뮬레이트된 장치를 각 모듈에 대해 시작된 Docker 컨테이너와 함께 제거하려면, 아래 명령을 사용합니다. 

```cmd
iotedgectl uninstall
```

생성한 IoT Hub가 더 이상 필요하지 않은 경우 [az iot hub delete][lnk-delete] 명령을 사용하여 리소스 및 리소스와 연결된 모든 장치를 제거할 수 있습니다.

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

## <a name="next-steps"></a>다음 단계

지금까지 IoT Edge 모듈을 IoT Edge 장치에 배포하는 방법을 배웠습니다. 이제 에지에서 데이터를 분석할 수 있도록 서로 다른 유형의 Azure 서비스를 모듈로 배포해보세요. 

* [Azure Functions를 모듈로 배포](tutorial-deploy-function.md)
* [Azure Stream Analytics를 모듈로 배포](tutorial-deploy-stream-analytics.md)
* [자신의 고유한 코드를 모듈로 배포](tutorial-csharp-module.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png

<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-python]: https://www.python.org/downloads/
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete
[lnk-install-iotcore]: how-to-install-iot-core.md

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
