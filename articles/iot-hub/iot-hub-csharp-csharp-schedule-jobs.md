---
title: Azure IoT Hub(.NET/.NET)를 사용하여 작업 예약 | Microsoft Docs
description: 여러 장치에서 직접 메서드를 호출하여 Azure IoT Hub 작업을 예약하는 방법입니다. .NET용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱 및 작업을 실행하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 012/16/2018
ms.author: v-masebo;dobett
ms.openlocfilehash: 76c8d3739b2af3c010cd80585c93c097fc9eb466
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>작업 예약 및 브로드캐스트(.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub를 사용하여 수백만 대의 장치를 업데이트하는 작업을 예약하고 추적합니다. 작업을 사용하여 다음을 수행합니다.

* desired 속성 업데이트
* tags 업데이트
* 직접 메서드 호출

작업(job)은 이러한 작업(action) 중 하나를 래핑하고 장치 쌍 쿼리로 정의된 장치 집합에 대한 실행을 추적합니다. 예를 들어 백 엔드 앱은 작업(job)을 사용하여 10,000대 장치에 대해 장치를 재부팅하는 직접 메서드를 호출할 수 있습니다. 장치 쌍 쿼리로 장치 집합을 지정하고 향후 실행될 작업(job)을 예약합니다. 작업(job)은 해당하는 각 장치에서 재부팅 직접 메서드를 수신 및 실행할 때 진행 상태를 추적합니다.

이러한 각 기능에 대한 자세한 내용은 다음을 참조하세요.

* 장치 쌍 및 속성: [장치 쌍 시작][lnk-get-started-twin] 및 [자습서: 장치 쌍 속성을 사용하는 방법][lnk-twin-props]
* 직접 메서드: [IoT Hub 개발자 가이드 - 직접 메서드][lnk-dev-methods] 및 [자습서: 직접 메서드 사용][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 백 엔드 앱에 의해 호출될 수 있는 **LockDoor**라는 직접 메서드를 구현하는 장치 앱을 만듭니다.
* 여러 장치에 대해 **LockDoor** 직접 메서드를 호출하는 작업을 만드는 백 엔드 앱을 만듭니다. 다른 작업이 여러 장치로 원하는 속성 업데이트를 보냅니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET(C#) 콘솔 앱이 생깁니다.

**SimulateDeviceMethods**: IoT 허브에 연결하고 **LockDoor** 직접 메서드를 구현합니다.

**ScheduleJob**: 작업을 사용하여 **LockDoor** 직접 메서드를 호출하고 여러 장치에서 장치 쌍의 원하는 속성을 업데이트합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 내에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 솔루션 백 엔드에서 호출한 직접 메서드에 응답하는 .NET 콘솔 앱을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **SimulateDeviceMethods**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 장치 앱][img-createdeviceapp]
    
1. 솔루션 탐색기에서 **SimulateDeviceMethods** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices.client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 절차에서는 [Azure IoT 장치 SDK][lnk-nuget-client-sdk] NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창 클라이언트 앱][img-clientnuget]

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 메모한 장치 연결 문자열로 대체합니다.

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;

1. Add the following to implement the direct method on the device:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }

1. Add the following to implement the device twins listener on the device:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. 작업을 저장하고 솔루션을 빌드합니다.         

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. [일시적인 오류 처리][lnk-transient-faults](영문) MSDN 문서에서 제시한 대로 프로덕션 코드에서 다시 시도 정책(예: 연결 다시 시도)을 구현해야 합니다.
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>직접 메서드를 호출하고 장치 쌍의 업데이트를 전송하기 위한 작업 예약

이 섹션에서는 작업을 사용하여 **LockDoor** 직접 메서드를 호출하고 원하는 속성 업데이트를 여러 장치에 전송하는 .NET 콘솔 앱(C# 사용)을 만듭니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **ScheduleJob**으로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]

1. 솔루션 탐색기에서 **ScheduleJob** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 단계에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창][img-servicenuget]

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. 다음 `using` 문이 기본 문에 아직 없으면 추가합니다.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자를 이전 섹션에서 만든 허브 및 장치 이름의 IoT Hub 연결 문자열로 바꿉니다.

    ```csharp
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. **Program** 클래스에 다른 메서드를 추가합니다.

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > 쿼리 구문에 대한 자세한 내용은 [IoT Hub 쿼리 언어][ lnk-query]를 참조하세요.
    > 

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. 작업을 저장하고 솔루션을 빌드합니다. 


## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다.

1. Visual Studio 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음, **빌드**를 클릭합니다. **여러 개의 시작 프로젝트**. `SimulateDeviceMethods`가 목록 맨 위에 표시되고 그 다음으로 `ScheduleJob`이 표시되는지 확인합니다. 두 작업을 모두 **시작**으로 설렁하고 **확인**을 클릭합니다.

1. **시작**을 클릭하거나 **디버그** 메뉴로 이동하여 **디버깅 시작**을 클릭하여 프로젝트를 실행합니다.

1. 장치 및 백 엔드 앱 모두에서 출력이 표시됩니다.

    ![작업을 예약하는 앱 실행][img-schedulejobs]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 장치에 대한 직접 메서드를 예약하고 장치 쌍의 속성을 업데이트하는 데 작업을 사용했습니다.

IoT Hub 및 장치 관리 패턴(예: 원격 무선 펌웨어 업데이트)을 계속 시작하려면 [자습서: 펌웨어 업데이트를 수행하는 방법][lnk-fwupdate]을 참조하세요

Azure IoT Edge로 AI를 에지 장치에 배포하는 방법을 자세히 알아보려면 [IoT Edge 시작][lnk-iot-edge]을 참조하세요.

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
