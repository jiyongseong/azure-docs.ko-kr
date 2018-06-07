---
title: Azure IoT Edge Python 모듈 | Microsoft Docs
description: Python 코드로 IoT Edge 모듈을 만들어서 Edge 장치에 배포
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3c46df85f95377f5740526542ac1baf5a8fd77c0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177838"
---
# <a name="develop-and-deploy-a-python-iot-edge-module-to-your-simulated-device---preview"></a>Python IoT Edge 모듈을 개발하여 시뮬레이션된 장치에 배포 - 미리 보기

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. [Windows][lnk-tutorial1-win] 또는 [Linux][lnk-tutorial1-lin]의 시뮬레이트된 장치에 Azure IoT Edge 배포 자습서에서 만든 시뮬레이트된 IoT Edge 장치를 사용하게 됩니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 IoT Edge Python 모듈 만들기
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다. 
> * 모듈을 IoT Edge 장치에 배포합니다.
> * 생성된 데이터 보기


이 자습서에서 만드는 IoT Edge 모듈은 장치에서 생성된 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. 에지에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

> [!IMPORTANT]
> 현재 amd64 Linux 컨테이너에서만 Python 모듈을 실행할 수 있습니다. Windows 컨테이너 또는 ARM 기반 컨테이너에서 실행할 수 없습니다. 

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 또는 첫 번째 자습서에서 만든 Azure IoT Edge 장치
* IoT Edge 장치에 대한 기본 키 연결 문자열입니다.  
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Visual Studio Code용 Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python). 
* Visual Studio Code가 있는 동일한 컴퓨터의 [Docker](https://docs.docker.com/engine/installation/). 이 자습서에서는 CE(Community Edition)로 충분합니다. 
* [Python](https://www.python.org/downloads/).
* Python 패키지 설치를 위한 [Pip](https://pip.pypa.io/en/stable/installing/#installation)(일반적으로 Python 설치와 함께 포함됨)

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
이 자습서에서는 VS Code용 Azure IoT Edge 확장을 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

이 자습서에서는 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.
2. 레지스트리에 이름을 지정하고, 구독을 선택하고, 리소스 그룹을 선택하고, SKU를 **기본**으로 설정합니다. 
3. **만들기**를 선택합니다.
4. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리로 이동하고 **액세스 키**를 선택합니다. 
5. **관리 사용자**를 **사용**으로 전환합니다.
6. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 자습서의 뒷부분에서 이러한 값을 사용합니다. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge 모듈 프로젝트 만들기
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 IoT Edge Python 모듈을 만드는 방법을 보여 줍니다.
1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택하여 VS Code 통합 터미널을 엽니다.
2. 통합된 터미널에서 다음 명령을 입력하여 **cookiecutter**를 설치(또는 업데이트)합니다. 가상 환경으로 또는 아래와 같이 사용자 설치로 수행하는 것이 좋습니다.

    ```cmd/sh
    pip install --upgrade --user cookiecutter
    ```

3. 새 모듈에 대한 프로젝트를 만듭니다. 다음 명령은 컨테이너 리포지토리와 함께 프로젝트 폴더인 **FilterModule**을 만듭니다. Azure 컨테이너 레지스트리를 사용하는 경우 `image_repository` 매개 변수는 `<your container registry name>.azurecr.io/filtermodule` 형식이어야 합니다. 현재 작업 폴더에 다음 명령을 입력합니다.

    ```cmd/sh
    cookiecutter --no-input https://github.com/Azure/cookiecutter-azure-iot-edge-module module_name=FilterModule image_repository=<your container registry address>/filtermodule
    ```
 
4. **파일** > **폴더 열기**를 선택합니다.
5. **FilterModule** 폴더로 이동한 다음, **폴더 선택**을 클릭하여 VS Code에서 프로젝트를 엽니다.
6. VS Code 탐색기에서 **main.py**를 클릭하여 엽니다.
7. **FilterModule** 네임스페이스의 맨 위에서 `json` 라이브러리를 가져옵니다.

    ```python
    import json
    ```

8. 전역 카운터 아래에 `TEMPERATURE_THRESHOLD`, `RECEIVE_CALLBACKS` 및 `TWIN_CALLBACKS`를 추가합니다. 온도 임계값은 IoT Hub로 데이터를 전송하기 위해 측정된 온도가 초과해야 하는 값을 설정합니다.

    ```python
    TEMPERATURE_THRESHOLD = 25
    TWIN_CALLBACKS = RECEIVE_CALLBACKS = 0
    ```

9. `receive_message_callback` 함수를 아래 내용으로 업데이트합니다.

    ```python
    # receive_message_callback is invoked when an incoming message arrives on the specified 
    # input queue (in the case of this sample, "input1").  Because this is a filter module, 
    # we will forward this message onto the "output1" queue.
    def receive_message_callback(message, hubManager):
        global RECEIVE_CALLBACKS
        global TEMPERATURE_THRESHOLD
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        message_text = message_buffer[:size].decode('utf-8')
        print("    Data: <<<{}>>> & Size={:d}".format(message_text, size))
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print("    Properties: {}".format(key_value_pair))
        RECEIVE_CALLBACKS += 1
        print("    Total calls received: {:d}".format(RECEIVE_CALLBACKS))
        data = json.loads(message_text)
        if "machine" in data and "temperature" in data["machine"] and data["machine"]["temperature"] > TEMPERATURE_THRESHOLD:
            map_properties.add("MessageType", "Alert")
            print("Machine temperature {} exceeds threshold {}".format(data["machine"]["temperature"], TEMPERATURE_THRESHOLD))
        hubManager.forward_event_to_output("output1", message, 0)
        return IoTHubMessageDispositionResult.ACCEPTED
    ```

10. 새 함수 `device_twin_callback`을 추가합니다. 이 함수는 원하는 속성이 업데이트될 때 호출됩니다.

    ```python
    # device_twin_callback is invoked when twin's desired properties are updated.
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global TEMPERATURE_THRESHOLD
        print("\nTwin callback called with:\nupdateStatus = {}\npayload = {}\ncontext = {}".format(update_state, payload, user_context))
        data = json.loads(payload)
        if "desired" in data and "TemperatureThreshold" in data["desired"]:
            TEMPERATURE_THRESHOLD = data["desired"]["TemperatureThreshold"]
        if "TemperatureThreshold" in data:
            TEMPERATURE_THRESHOLD = data["TemperatureThreshold"]
        TWIN_CALLBACKS += 1
        print("Total calls confirmed: {:d}\n".format(TWIN_CALLBACKS))
    ```

11. `HubManager` 클래스에서 방금 추가한 `device_twin_callback` 함수를 초기화하도록 `__init__` 메서드에 새 줄을 추가합니다.

    ```python
    # sets the callback when a twin's desired properties are updated.
    self.client.set_device_twin_callback(device_twin_callback, self)
    ```


12. 이 파일을 저장합니다.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker 이미지를 만들어 레지스트리에 게시

1. VS Code 통합 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. 
     
   ```csh/sh
   docker login -u <username> -p <password> <Login server>
   ```
        
   만들 때 Azure Container Registry에서 복사한 사용자 이름, 암호 및 로그인 서버를 사용합니다.

2. VS Code 탐색기에서 **module.json** 파일을 마우스 오른쪽 단추로 클릭하고 **Build and Push IoT Edge module Docker image**(IoT Edge 모듈 Docker 이미지 빌드 및 푸시)를 클릭합니다. VS Code 창의 맨 위에 있는 팝업 드롭다운 상자에서 컨테이너 플랫폼(예: Linux 컨테이너의 경우 **amd64**)을 선택합니다. VS 코드가 `main.py` 및 필수 종속성을 컨테이너화한 다음, 지정한 컨테이너 레지스트리로 푸시합니다. 이미지를 처음 빌드하는 경우 몇 분 정도 걸릴 수 있습니다.

3. VS Code 통합 터미널에 태그와 함께 전체 컨테이너 이미지 주소를 가져올 수 있습니다. 빌드 및 푸시 정의에 대한 자세한 내용은 `module.json` 파일을 참조하세요.

## <a name="add-registry-credentials-to-edge-runtime"></a>Edge 런타임에 레지스트리 자격 증명 추가
Edge 장치를 실행 중인 컴퓨터의 Edge 런타임에 레지스트리의 자격 증명을 추가합니다. 이러한 자격 증명을 지정하면 컨테이너를 끌어오기 위한 런타임 액세스 권한이 제공됩니다. 

- Windows의 경우 다음 명령을 실행합니다.
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linux의 경우 다음 명령을 실행합니다.
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>솔루션 실행

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.
2. **IoT Edge(미리 보기)** 로 이동하여 IoT Edge 장치를 선택합니다.
3. **모듈 설정**을 선택합니다. 
2. **tempSensor** 모듈이 자동으로 채워지는지 확인합니다. 그렇지 않은 경우 다음 단계를 사용하여 추가합니다.
    1. **IoT Edge 모듈 추가**를 선택합니다.
    2. **이름** 필드에 `tempSensor`를 입력합니다.
    3. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다.
    4. 다른 설정은 변경하지 않고 **저장**을 클릭합니다.
9. 이전 섹션에서 만든 **filterModule** 모듈을 추가합니다. 
    1. **IoT Edge 모듈 추가**를 선택합니다.
    2. **이름** 필드에 `filterModule`을 입력합니다.
    3. **이미지 URI** 필드에 이미지 주소(예: `<your container registry address>/filtermodule:0.0.1-amd64`)를 입력합니다. 전체 이미지 주소는 이전 섹션에서 찾을 수 있습니다.
    4. 모듈 쌍을 편집할 수 있게 **사용** 확인란을 선택합니다. 
    5. 모듈 트윈의 텍스트 상자에 있는 JSON을 다음 JSON으로 바꿉니다. 

        ```json
        {
           "properties.desired":{
              "TemperatureThreshold":25
           }
        }
        ```
 
    6. **저장**을 클릭합니다.
10. **다음**을 클릭합니다.
11. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 모듈은 모든 메시지를 Edge 런타임에 게시합니다. 런타임의 선언적 규칙은 메시지가 어디로 흐르는지를 정의합니다. 이 자습서에서는 두 개의 경로가 필요합니다. 첫 번째 경로는 **FilterMessages** 핸들러로 구성한 엔드포인트인 “input1” 엔드포인트를 통해 온도 센서에서 필터 모듈로 메시지를 전송합니다. 두 번째 경로는 필터 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 `upstream`은 메시지를 IoT Hub로 보내라고 Edge Hub에 알리는 특수 대상입니다. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

4. **다음**을 클릭합니다.
5. **템플릿 검토** 단계에서 **제출**을 클릭합니다. 
6. IoT Edge 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 클릭합니다. **tempSensor** 모듈 및 **IoT Edge runtime**과 함께 실행되는 새로운 **filtermodule**이 표시됩니다. 

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 장치에서 IoT Hub로 보낸 장치-클라우드 메시지를 모니터하려면 다음을 수행하세요.
1. IoT Hub용 연결 문자열로 Azure IoT Toolkit 확장을 구성합니다. 
    1. **보기** > **탐색기**를 선택하여 VS Code 탐색기를 엽니다. 
    3. 탐색기에서 **IOT HUB 장치**를 클릭하고 **...** 을 클릭합니다. **IoT Hub 연결 문자열 설정**을 클릭하고, 팝업 창에서 IoT Edge 장치를 연결할 IoT Hub용 연결 문자열을 입력합니다. 

        연결 문자열을 찾으려면 Azure Portal에서 IoT Hub의 타일을 클릭한 다음 **공유 액세스 정책**을 클릭합니다. **공유 액세스 정책**에서 **iothubowner** 정책을 클릭한 다음 **iothubowner** 창에 있는 IoT Hub 연결 문자열을 복사합니다.   

1. IoT Hub에 도착하는 데이터를 모니터링하려면 **보기** > **명령 팔레트**를 선택하고 **IoT: D2C 메시지 모니터링 시작** 메뉴 명령을 검색합니다. 
2. 데이터 모니터링을 중지하려면 **IoT: D2C 메시지 모니터링 중지** 메뉴 명령을 사용합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 IoT Edge 모듈을 만들었습니다. Azure IoT Edge에서 데이터를 통해 비즈니스 통찰력을 얻는 데 도움이 되는 다른 방법을 알아보려면 다음 자습서 중 하나를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Functions를 모듈로 배포](tutorial-deploy-function.md)
> [Azure Stream Analytics를 모듈로 배포](tutorial-deploy-stream-analytics.md)


<!-- Links -->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md

<!-- Images -->
[1]: ./media/tutorial-csharp-module/programcs.png
[2]: ./media/tutorial-csharp-module/build-module.png
[3]: ./media/tutorial-csharp-module/docker-os.png
