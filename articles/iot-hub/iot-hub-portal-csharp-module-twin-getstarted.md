---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(포털 및 .NET) | Microsoft Docs
description: 포털 및 .NET을 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
services: iot-hub
documentationcenter: .net
author: chrissie926
manager: timlt
editor: ''
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30a53620a640b0122286a6ac69c0f98cd0bbde40
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361691"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>포털 및 .NET 장치를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 장치 ID 및 장치 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 응용 프로그램에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 장치 또는 펌웨어 장치와 같이 여러 구성 요소가 있는 가능한 장치에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.

이 자습서에서는 다음에 대해 알아봅니다. 
1. 포털에서 모듈 ID를 만드는 방법 
2. .NET 장치 SDK를 사용하여 장치에서 모듈을 업데이트하는 방법.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 내용은 [Azure IoT SDK][lnk-hub-sdks]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. (계정이 없는 경우 몇 분 만에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>포털에서 장치 ID 만들기

이제 IoT Hub가 있습니다. [포털](https://portal.azure.com)을 열고 IoT Hub로 이동합니다. IoT 장치를 클릭하고 추가를 클릭하여 장치 ID를 만듭니다. 이름을 **MyFirstDevice**로 지정합니다. 

![장치 ID 만들기][8]

저장하면 장치 ID 목록에서 MyFirstDevice ID가 성공적으로 만들어진 것을 볼 수 있습니다.

![장치 ID가 만들어짐][11]

이제 해당 행을 클릭합니다. 장치 세부 정보가 표시됩니다.

![장치 세부 정보][10]

## <a name="create-a-module-identity-in-the-portal"></a>포털에서 모듈 ID 만들기

하나의 장치 ID 내에 최대 20개의 모듈 ID를 만들 수 있습니다. 위쪽의 **모듈 ID 추가** 단추를 클릭하여 **myFirstModule**이라는 첫 번째 모듈 ID를 만듭니다. 

![장치 세부 정보][9]

방금 만든 모듈 ID를 저장하고 클릭합니다. 모듈 ID 세부 정보를 볼 수 있습니다. 연결 문자열 - 기본 키를 저장합니다. 장치에서 모듈을 설정한 경우 다음 섹션에서 사용됩니다.

![장치 세부 정보][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET 장치 SDK를 사용하여 모듈 쌍 업데이트

IoT Hub에서 모듈 ID를 성공적으로 만들었습니다. 시뮬레이트된 장치에서 클라우드와 통신해 보겠습니다. 모듈 ID가 만들어지면 모듈 쌍이 IoT Hub에서 암시적으로 생성됩니다. 이 섹션에서는 시뮬레이션된 장치에 모듈 쌍 reported 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

1. **Visual Studio 프로젝트 만들기** - Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 기존 솔루션에 추가합니다. .NET Framework 버전이 4.6.1 이상인지 확인합니다. 프로젝트 이름을 **UpdateModuleTwinReportedProperties**로 지정합니다.

    ![Visual Studio 프로젝트 만들기][13]

2. **Azure IoT Hub .NET 장치 SDK V1.16.0-preview-005 설치** - 모듈 ID 및 모듈 쌍은 공개 미리 보기로 있습니다. IoT Hub 시험판 장치 SDK에서만 사용할 수 있습니다. Visual Studio에서 도구 > Nuget 패키지 관리자 > 솔루션용 Nuget 패키지 관리를 차례로 엽니다. Microsoft.Azure.Devices.Client를 검색합니다. 시험판 포함 확인란이 선택되었는지 확인합니다. V1.16.0-preview-005 버전을 선택하고 설치합니다. 이제 모든 모듈 기능에 액세스할 수 있습니다. 

    ![Azure IoT Hub .NET 서비스 SDK V1.16.0-preview-005 설치][14]

3. **모듈 연결 문자열 가져오기** - 이제 [Azure Portal][lnk-portal]에 로그인합니다. IoT Hub로 이동하고 IoT 장치를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

    ![Azure Portal 모듈 세부 정보][15]

4. **UpdateModuleTwinReportedProperties 콘솔 앱 만들기** - **Program.cs** 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 모듈 연결 문자열로 바꿉니다.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static ModuleClient Client = null;
    ```

    **Program** 클래스에 **OnDesiredPropertyChanged** 메서드를 추가합니다.

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Client.CloseAsync().Wait();
    }
    ```

    이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다. 공개 미리 보기에서는 모듈 쌍 작업에 대해서만 AMQP를 지원합니다.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
