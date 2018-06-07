---
title: Azure IoT Edge C# 모듈 | Microsoft Docs
description: C# 코드가 있는 IoT Edge 모듈을 만들어 에지 장치에 배포
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 03/14/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 1caa887a13453ce2b2b07e83b74f0ed57535b026
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="develop-and-deploy-a-c-iot-edge-module-to-your-simulated-device---preview"></a>C# IoT Edge 모듈을 개발하여 시뮬레이트된 장치에 배포 - 미리 보기

비즈니스 논리를 직접 IoT Edge 장치에 구현하는 코드를 배포하려면 IoT Edge 모듈을 사용할 수 있습니다. 이 자습서에서는 센서 데이터를 필터링하는 IoT Edge 모듈을 만들고 배포하는 과정을 안내합니다. [Windows][lnk-tutorial1-win] 또는 [Linux][lnk-tutorial1-lin]의 시뮬레이트된 장치에 Azure IoT Edge 배포 자습서에서 만든 시뮬레이트된 IoT Edge 장치를 사용하게 됩니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.    

> [!div class="checklist"]
> * Visual Studio Code를 사용하여 .NET 코어 2.0을 기반으로 IoT Edge 모듈을 만듭니다.
> * Visual Studio Code 및 Docker를 사용하여 docker 이미지를 만들어 레지스트리에 게시합니다. 
> * 모듈을 IoT Edge 장치에 배포합니다.
> * 생성된 데이터 보기


이 자습서에서 만드는 IoT Edge 모듈은 장치에서 생성된 온도 데이터를 필터링합니다. 온도가 지정된 임계값을 초과하는 경우에만 메시지 업스트림을 전송합니다. 에지에서 이 유형의 분석은 클라우드로 전송되고 저장되는 데이터 양을 줄이는 데 유용합니다. 

## <a name="prerequisites"></a>필수 조건

* 빠른 시작 또는 첫 번째 자습서에서 만든 Azure IoT Edge 장치
* IoT Edge 장치에 대한 기본 키 연결 문자열입니다.  
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Visual Studio Code용 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# for Visual Studio Code(OmniSharp 제공) 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp). 
* Visual Studio Code가 있는 동일한 컴퓨터의 [Docker](https://docs.docker.com/engine/installation/). 이 자습서에서는 CE(Community Edition)로 충분합니다. 
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기
이 자습서에서는 VS Code용 Azure IoT Edge 확장을 사용하여 모듈을 빌드하고 파일에서 **컨테이너 이미지**를 만듭니다. 그런 후 이미지를 저장하고 관리하는 **레지스트리**에 이 이미지를 푸시합니다. 마지막으로 IoT Edge 장치에서 실행되도록 레지스트리의 이미지를 배포합니다.  

이 자습서에서는 Docker 호환 레지스트리를 사용할 수 있습니다. 클라우드에서 사용 가능한 두 개의 인기 있는 Docker 레지스트리 서비스는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) 및 [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)입니다. 이 자습서에서는 Azure Container Registry를 사용합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **컨테이너** > **Azure Container Registry**를 선택합니다.
2. 레지스트리에 이름을 지정하고, 구독을 선택하고, 리소스 그룹을 선택하고, SKU를 **기본**으로 설정합니다. 
3. **만들기**를 선택합니다.
4. 컨테이너 레지스트리를 만든 후에는 해당 레지스트리로 이동하고 **액세스 키**를 선택합니다. 
5. **관리 사용자**를 **사용**으로 전환합니다.
6. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 복사합니다. 레지스트리에 Docker 이미지를 게시할 때 및 에지 런타임에 레지스트리 자격 증명을 추가할 때 자습서의 뒷부분에 나오는 이러한 값을 사용합니다. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge 모듈 프로젝트 만들기
다음 단계는 Visual Studio Code 및 Azure IoT Edge 확장을 사용하여 .NET Core 2.0을 기반으로 IoT Edge 모듈을 만드는 방법을 보여 줍니다.
1. Visual Studio Code에서 **보기** > **통합 터미널**을 선택하여 VS Code 통합 터미널을 엽니다.
2. 통합 터미널에서 다음 명령을 입력하여 dotnet에 **AzureIoTEdgeModule** 템플릿을 설치(또는 갱신)합니다.

    ```cmd/sh
    dotnet new -i Microsoft.Azure.IoT.Edge.Module
    ```

3. 새 모듈에 대한 프로젝트를 만듭니다. 다음 명령은 컨테이너 리포지토리와 함께 프로젝트 폴더인 **FilterModule**을 만듭니다. Azure 컨테이너 레지스트리를 사용하는 경우 두 번째 매개 변수는 `<your container registry name>.azurecr.io` 형식이어야 합니다. 현재 작업 폴더에 다음 명령을 입력합니다.

    ```cmd/sh
    dotnet new aziotedgemodule -n FilterModule -r <your container registry address>/filtermodule
    ```
 
4. **파일** > **폴더 열기**를 선택합니다.
5. **FilterModule** 폴더로 이동한 다음 **폴더 선택**을 클릭하여 VS Code에서 프로젝트를 엽니다.
6. VS Code 탐색기에서 **Program.cs**를 클릭하여 엽니다.

   ![Program.cs 열기][1]

7. **FilterModule** 네임스페이스의 맨 위에서 나중에 사용되는 유형에 `using` 문을 3개 추가합니다.

    ```csharp
    using System.Collections.Generic;     // for KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // for TwinCollection
    using Newtonsoft.Json;                // for JsonConvert
    ```

8. `temperatureThreshold` 변수를 **Program** 클래스에 추가합니다. 이 변수는 데이터가 IoT Hub로 전송되기 위해 측정된 온도가 초과해야 하는 값을 설정합니다. 

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

9. `MessageBody`, `Machine` 및 `Ambient` 클래스를 **Program** 클래스에 추가합니다. 이러한 클래스는 수신 메시지 본문의 예상 스키마를 정의합니다.

    ```csharp
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

10. **Init** 메서드에서 코드는 **DeviceClient** 개체를 만들고 구성합니다. 이 개체를 사용하면 메시지를 주고받기 위해 로컬 Azure IoT Edge 런타임에 모듈을 연결할 수 있습니다. **Init** 메서드에 사용된 연결 문자열이 IoT Edge 런타임에 의해 모듈로 제공됩니다. **DeviceClient**를 만든 후 코드는 Module Twin의 원하는 속성에서 TemperatureThreshold를 읽고 **input1** 끝점을 통해 IoT Edge 허브로부터 메시지를 수신하기 위한 콜백을 등록합니다. `SetInputMessageHandlerAsync` 메서드를 새 메서드로 바꾸로 원하는 속성 업데이트에 대한 `SetDesiredPropertyUpdateCallbackAsync` 메서드를 추가합니다. 이 변경을 수행하려면 **Init** 메서드의 마지막 줄을 다음 코드로 바꾸세요.

    ```csharp
    // Register callback to be called when a message is received by the module
    // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

    // Read TemperatureThreshold from Module Twin Desired Properties
    var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
    var moduleTwinCollection = moduleTwin.Properties.Desired;
    try {
        temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
    } catch(ArgumentOutOfRangeException e) {
        Console.WriteLine("Property TemperatureThreshold not exist");
    }

    // Attach callback for Twin desired properties updates
    await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

    // Register callback to be called when a message is received by the module
    await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
    ```

11. **Program** 클래스에 `onDesiredPropertiesUpdate` 메서드를 추가합니다. 이 메서드는 모듈 쌍에서 원하는 속성에 대한 업데이트를 수신하고, 일치하도록 **temperatureThreshold** 변수를 업데이트합니다. 모든 모듈에는 자체 모듈 쌍이 있어서 클라우드에서 직접 모듈 내에서 실행되는 코드를 구성할 수 있습니다.

    ```csharp
    static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

12. `PipeMessage` 메서드를 `FilterMessages` 메서드로 바꿉니다. 이 메서드는 모듈이 IoT Edge Hub에서 메시지를 수신할 때마다 호출됩니다. 모듈 쌍을 통해 설정된 온도 임계값 아래의 온도를 보고하는 메시지를 필터링합니다. 또한 값이 **Alert**로 설정된 **MessageType** 속성을 메시지에 추가합니다. 

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);

        try {
            DeviceClient deviceClient = (DeviceClient)userContext;

            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get message body
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                var filteredMessage = new Message(messageBytes);
                foreach (KeyValuePair<string, string> prop in message.Properties)
                {
                    filteredMessage.Properties.Add(prop.Key, prop.Value);
                }

                filteredMessage.Properties.Add("MessageType", "Alert");
                await deviceClient.SendEventAsync("output1", filteredMessage);
            }

            // Indicate that the message treatment is completed
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed
            var deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed
            DeviceClient deviceClient = (DeviceClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

13. 이 파일을 저장합니다.

## <a name="create-a-docker-image-and-publish-it-to-your-registry"></a>Docker 이미지를 만들어 레지스트리에 게시

1. VS Code 통합 터미널에 다음 명령을 입력하여 Docker에 로그인합니다. 
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   이 명령에서 사용할 사용자 이름, 암호 및 로그인 서버를 찾으려면 [Azure Portal](https://portal.azure.com))로 이동합니다. **모든 리소스**에서 Azure Container Registry에 대한 타일을 클릭하여 속성을 연 다음 **액세스 키**를 클릭합니다. **사용자 이름**, **암호** 및 **로그인 서버** 필드의 값을 복사합니다. 

2. VS Code 탐색기에서 **module.json** 파일을 마우스 오른쪽 단추로 클릭하고 **Build and Push IoT Edge module Docker image**(IoT Edge 모듈 Docker 이미지 빌드 및 푸시)를 클릭합니다. VS Code 창의 맨 위에 있는 팝업 드롭다운 상자에서 컨테이너 플랫폼을 선택합니다. Linux 컨테이너의 경우 **amd64**, Windows 컨테이너의 경우 **windows-amd64**를 선택합니다. 그러면 VS Code에서 코드가 작성되고 `FilterModule.dll`이 컨테이너화되어 지정한 컨테이너 레지스트리로 푸시됩니다.


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
4. **tempSensor** 모듈이 자동으로 채워지는지 확인합니다. 그렇지 않은 경우 다음 단계를 사용하여 추가합니다.
    1. **IoT Edge 모듈 추가**를 선택합니다.
    2. **이름** 필드에 `tempSensor`를 입력합니다.
    3. **이미지 URI** 필드에 `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`를 입력합니다.
    4. 다른 설정은 변경하지 않고 **저장**을 클릭합니다.
5. 이전 섹션에서 만든 **filterModule** 모듈을 추가합니다. 
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
6. **다음**을 클릭합니다.
7. **경로 지정** 단계에서 다음 JSON을 텍스트 상자에 복사합니다. 모듈은 모든 메시지를 Edge 런타임에 게시합니다. 런타임의 선언적 규칙은 메시지가 어디로 흐르는지를 정의합니다. 이 자습서에서는 두 개의 경로가 필요합니다. 첫 번째 경로는 **FilterMessages** 핸들러로 구성한 엔드포인트인 “input1” 엔드포인트를 통해 온도 센서에서 필터 모듈로 메시지를 전송합니다. 두 번째 경로는 필터 모듈에서 IoT Hub로 메시지를 전송합니다. 이 경로에서 `upstream`은 메시지를 IoT Hub로 보내라고 Edge Hub에 알리는 특수 대상입니다. 

    ```json
    {
       "routes":{
          "sensorToFilter":"FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filterModule/inputs/input1\")",
          "filterToIoTHub":"FROM /messages/modules/filterModule/outputs/output1 INTO $upstream"
       }
    }
    ```

8. **다음**을 클릭합니다.
9. **템플릿 검토** 단계에서 **제출**을 클릭합니다. 
10. IoT Edge 장치 세부 정보 페이지로 돌아가서 **새로 고침**을 클릭합니다. **tempSensor** 모듈 및 **IoT Edge runtime**과 함께 실행되는 새로운 **filtermodule**이 표시됩니다. 

## <a name="view-generated-data"></a>생성된 데이터 보기

IoT Edge 장치에서 IoT Hub로 보낸 장치-클라우드 메시지를 모니터하려면 다음을 수행하세요.
1. IoT Hub용 연결 문자열로 Azure IoT Toolkit 확장을 구성합니다. 
    1. **보기** > **탐색기**를 선택하여 VS Code 탐색기를 엽니다. 
    2. 탐색기에서 **IOT HUB 장치**를 클릭하고 **...** 을 클릭합니다. **IoT Hub 연결 문자열 설정**을 클릭하고, 팝업 창에서 IoT Edge 장치를 연결할 IoT Hub용 연결 문자열을 입력합니다. 

        연결 문자열을 찾으려면 Azure Portal에서 IoT Hub의 타일을 클릭한 다음 **공유 액세스 정책**을 클릭합니다. **공유 액세스 정책**에서 **iothubowner** 정책을 클릭한 다음 **iothubowner** 창에 있는 IoT Hub 연결 문자열을 복사합니다.   

2. IoT Hub에 도착하는 데이터를 모니터링하려면 **보기** > **명령 팔레트**를 선택하고 **IoT: D2C 메시지 모니터링 시작** 메뉴 명령을 검색합니다. 
3. 데이터 모니터링을 중지하려면 **IoT: D2C 메시지 모니터링 중지** 메뉴 명령을 사용합니다. 

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
