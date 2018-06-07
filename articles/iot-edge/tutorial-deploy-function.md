---
title: Azure IoT Edge를 사용하여 Azure 함수 배포 | Microsoft Docs
description: Azure 함수를 모듈로 Edge 장치에 배포
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: f1c6b5cd07752c6b29234a365b3298d76b639b3a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="deploy-azure-function-as-an-iot-edge-module---preview"></a>Azure 함수를 IoT Edge 모듈로 배포 - 미리 보기
비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 Azure Functions를 사용할 수 있습니다. 이 자습서에서는 “[Windows][lnk-tutorial1-win] 또는 [Linux][lnk-tutorial1-lin]에서 시뮬레이트된 장치에 Azure IoT Edge 배포” 자습서에서 만든 IoT Edge 장치에서 센서 데이터를 필터링하는 Azure 함수를 만들고 배포하는 과정을 안내합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.     

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 Azure 함수 만들기
> * VS Code 및 Docker를 사용하여 Docker 이미지를 만들어 레지스트리에 게시합니다. 
> * 모듈을 IoT Edge 장치에 배포합니다.
> * 생성된 데이터 보기


이 자습서에서 만든 Azure 함수는 장치에서 생성된 온도 데이터를 필터링하고, 온도가 지정된 임계값보다 높은 경우에만 Azure IoT Hub로 메시지 업스트림을 보냅니다. 

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 또는 이전 자습서에서 만든 Azure IoT Edge 장치
* [Visual Studio Code](https://code.visualstudio.com/) 
* [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker](https://docs.docker.com/engine/installation/). 이 자습서에서는 플랫폼용 CE(Community Edition)로 충분합니다. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
이 자습서에서는 VS Code용 Azure IoT Edge 확장을 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

이 자습서에서는 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.
2. 레지스트리에 이름을 지정하고, 구독을 선택하고, 리소스 그룹을 선택하고, SKU를 **기본**으로 설정합니다. 
3. **만들기**를 선택합니다.
4. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리로 이동하고 **액세스 키**를 선택합니다. 
5. **관리 사용자**를 **사용**으로 전환합니다.
6. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 자습서의 뒷부분에서 이러한 값을 사용합니다. 

## <a name="create-a-function-project"></a>함수 프로젝트 만들기
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 IoT Edge 함수를 만드는 방법을 보여 줍니다.
1. Visual Studio Code를 엽니다.
2. VS Code 통합 터미널을 열려면 **보기** > **통합 터미널**을 선택합니다.
3. dotnet에서 **AzureIoTEdgeFunction** 템플릿을 설치(또는 업데이트)하려면 통합 터미널에서 다음 명령을 실행합니다.

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Function
    ```
2. 새 모듈에 대한 프로젝트를 만듭니다. 다음 명령은 컨테이너 리포지토리와 함께 프로젝트 폴더인 **FilterFunction**을 만듭니다. Azure 컨테이너 레지스트리를 사용하는 경우 두 번째 매개 변수는 `<your container registry name>.azurecr.io` 형식이어야 합니다. 현재 작업 폴더에 다음 명령을 입력합니다.

    ```cmd/sh
    dotnet new aziotedgefunction -n FilterFunction -r <your container registry address>/filterfunction
    ```

3. elect **파일** > **폴더 열기**를 선택한 후 **FilterFunction** 폴더로 이동하고 VS Code에서 프로젝트를 엽니다.
4. VS Code 탐색기에서 **EdgeHubTrigger-Csharp** 폴더를 확장한 다음 **run.csx** 파일을 엽니다.
5. 파일 내용을 다음 코드로 바꿉니다.

   ```csharp
   #r "Microsoft.Azure.Devices.Client"
   #r "Newtonsoft.Json"

   using System.IO;
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;

   // Filter messages based on the temperature value in the body of the message and the temperature threshold value.
   public static async Task Run(Message messageReceived, IAsyncCollector<Message> output, TraceWriter log)
   {
        const int temperatureThreshold = 25;
        byte[] messageBytes = messageReceived.GetBytes();
        var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

        if (!string.IsNullOrEmpty(messageString))
        {
            // Get the body of the message and deserialize it
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                // Send the message to the output as the temperature value is greater than the threashold
                var filteredMessage = new Message(messageBytes);
                // Copy the properties of the original message into the new Message object
                foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);                }
                // Add a new property to the message to indicate it is an alert
                filteredMessage.Properties.Add("MessageType", "Alert");
                // Send the message        
                await output.AddAsync(filteredMessage);
                log.Info("Received and transferred a message with temperature above the threshold");
            }
        }
    }

    //Define the expected schema for the body of incoming messages
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
       public double temperature {get; set;}
       public double pressure {get; set;}         
    }
    class Ambient
    {
       public double temperature {get; set;}
       public int humidity {get; set;}         
    }
   ```

11. 파일을 저장합니다.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker 이미지를 만들어 레지스트리에 게시

1. VS Code 통합 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   이 명령에서 사용할 사용자 이름, 암호 및 로그인 서버를 찾으려면 [Azure Portal](https://portal.azure.com))로 이동합니다. **모든 리소스**에서 Azure Container Registry에 대한 타일을 클릭하여 속성을 연 다음 **액세스 키**를 클릭합니다. **사용자 이름**, **암호** 및 **로그인 서버** 필드의 값을 복사합니다. 

2. **module.json**을 엽니다. 선택적으로 `"version"`을 eg **"1.0"**으로 업데이트할 수 있습니다. 또한 `dotnet new aziotedgefunction`의 `-r` 매개 변수에 입력한 리포지토리의 이름이 표시됩니다.

3. **module.json** 파일을 저장합니다.

4. VS Code 탐색기에서 **module.json** 파일을 마우스 오른쪽 단추로 클릭하고 **Build and Push IoT Edge module Docker image**(IoT Edge 모듈 Docker 이미지 빌드 및 푸시)를 클릭합니다. VS Code 창의 맨 위에 있는 팝업 드롭다운 상자에서 컨테이너 플랫폼을 선택합니다. Linux 컨테이너의 경우 **amd64**, Windows 컨테이너의 경우 **windows-amd64**를 선택합니다. 그러면 VS Code가 함수 코드를 컨테이너화한 후 사용자가 지정한 컨테이너 레지스트리로 푸시합니다.

5. VS Code 통합 터미널에 태그와 함께 전체 컨테이너 이미지 주소를 가져올 수 있습니다. 빌드 및 푸시 정의에 대한 자세한 내용은 `module.json` 파일을 참조하세요.

## <a name="add-registry-credentials-to-your-edge-device"></a>Edge 장치에 레지스트리 자격 증명 추가
Edge 장치를 실행 중인 컴퓨터의 Edge 런타임에 레지스트리의 자격 증명을 추가합니다. 이렇게 하면 컨테이너를 끌어오기 위한 런타임 액세스 권한이 제공됩니다. 

- Windows의 경우 다음 명령을 실행합니다.
    
    ```cmd/sh
    iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

- Linux의 경우 다음 명령을 실행합니다.
    
    ```cmd/sh
    sudo iotedgectl login --address <your container registry address> --username <username> --password <password> 
    ```

## <a name="run-the-solution"></a>솔루션 실행

1. **Azure Portal**에서 IoT Hub로 이동합니다.
2. **IoT Edge(미리 보기)**로 이동하여 IoT Edge 장치를 선택합니다.
1. **모듈 설정**을 선택합니다. 
2. 이 장치에 **tempSensor** 모듈을 이미 배포한 경우 자동으로 채워집니다. 그러지 않은 경우 다음 단계에 따라 추가합니다.
    1. **IoT Edge 모듈 추가**를 선택합니다.
    2. **이름** 필드에 `tempSensor`를 입력합니다.
    3. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다.
    4. 다른 설정은 변경하지 않고 **저장**을 클릭합니다.
1. **filterFunction** 모듈을 추가합니다.
    1. **IoT Edge 모듈 추가**를 다시 선택합니다.
    2. **이름** 필드에 `filterFunction`을 입력합니다.
    3. **이미지 URI** 필드에 이미지 주소(예: `<your container registry address>/filterfunction:0.0.1-amd64`)를 입력합니다. 전체 이미지 주소는 이전 섹션에서 찾을 수 있습니다.
    74. **저장**을 클릭합니다.
2. **다음**을 클릭합니다.
3. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 첫 번째 경로는 “input1” 끝점을 통해 온도 센서에서 필터 모듈로 메시지를 전송합니다. 두 번째 경로는 필터 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 `$upstream`은 메시지를 IoT Hub로 보내라고 Edge Hub에 알리는 특수 대상입니다. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterFunction/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterFunction/outputs/* INTO $upstream"
       }
    }
    ```

4. **다음**을 클릭합니다.
5. **템플릿 검토** 단계에서 **제출**을 클릭합니다. 
6. IoT Edge 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 클릭합니다. **tempSensor** 모듈 및 **IoT Edge runtime**과 함께 실행되는 새로운 **filterfunction** 모듈이 표시됩니다. 

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 장치에서 IoT Hub로 보낸 장치-클라우드 메시지를 모니터하려면 다음을 수행하세요.
1. IoT Hub용 연결 문자열로 Azure IoT Toolkit 확장을 구성합니다. 
    1. Azure Portal에서 IoT 허브로 이동하고 **공유 액세스 정책**을 선택합니다. 
    2. **iothubowner**를 선택한 후 **연결 문자열 - 기본 키** 값을 복사합니다.
    3. VS Code 탐색기에서 **IOT HUB 장치**를 클릭하고 **...**을 클릭합니다. 
    4. **Set IoT Hub Connection String**(IoT Hub 연결 문자열 설정)을 선택하고 팝업 창에 Iot Hub 연결 문자열을 입력합니다. 

2. IoT Hub에 도착하는 데이터를 모니터링하려면 **보기** > **명령 팔레트...**를 선택하고 **IoT: D2C 메시지 모니터링 시작**을 검색합니다. 
3. 데이터 모니터링을 중지하려면 명령 팔레트에서 **IoT: D2C 메시지 모니터링 중지** 명령을 사용합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Edge 장치에서 생성된 원시 데이터를 필터링하는 코드가 포함된 Azure 함수를 만들었습니다. Azure IoT Edge을 계속 탐색하려면 IoT Edge 장치를 게이트웨이로 사용하는 방법을 알아보세요. 

> [!div class="nextstepaction"]
> [IoT Edge 게이트웨이 장치 만들기](how-to-create-transparent-gateway.md)

<!--Links-->
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
