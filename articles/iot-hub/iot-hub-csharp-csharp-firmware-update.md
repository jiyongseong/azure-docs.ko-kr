---
title: Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(.NET/.NET) | Microsoft Docs
description: 장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. .NET용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: v-jamebr;dobett
ms.openlocfilehash: 50bb2ada9cf848bdcfb4f958272ff918996bf411
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>장치 관리를 사용하여 장치 펌웨어 업데이트(.NET/.NET)를 시작합니다.
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>소개
[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 살펴보았습니다. 이 자습서에서는 동일한 IoT Hub 기본 형식을 사용하고 시뮬레이션된 종단 간 펌웨어 업데이트를 수행하는 방법을 보여 줍니다.  이 패턴은 [Raspberry Pi 장치 구현 샘플][lnk-rpi-implementation]에 대한 펌웨어 업데이트 구현에 사용됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 **firmwareUpdate** 직접 메서드를 호출하는 .NET 콘솔 앱을 만듭니다.
* **firmwareUpdate** 직접 메서드를 구현하는 시뮬레이트된 장치 앱을 만듭니다. 이 메서드는 펌웨어 이미지를 다운로드하기 위해 대기했다가 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용하는 다단계 프로세스를 시작합니다. 업데이트의 각 단계 동안, 장치는 보고된 속성을 사용하여 진행 상황을 보고합니다.

이 자습서의 끝 부분에는 다음과 같은 .NET(C#) 콘솔 장치 앱과 .NET(C#) 콘솔 백 엔드 앱이 있습니다.

* **SimulatedDeviceFwUpdate**는 앞에서 만든 장치 ID를 사용하여 IoT Hub에 연결하고, **firmwareUpdate** 직접 메서드를 수신하며, 펌웨어 업데이트를 시뮬레이션하기 위한 다단계 프로세스(이미지 다운로드 대기, 새 이미지 다운로드, 마지막으로 이미지 적용 등)를 실행합니다.

* **TriggerFWUpdate**는 서비스 SDK를 사용하여 시뮬레이션된 장치에 **firmwareUpdate** 직접 메서드를 원격으로 호출하고, 응답을 표시하고, 업데이트된 보고 속성을 주기적으로(500밀리초마다) 표시합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

IoT hub 허브를 만들고 IoT Hub 연결 문자열을 확보하려면 [장치 관리 시작](iot-hub-csharp-csharp-device-management-get-started.md) 문서의 내용을 수행하세요.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 장치에서 원격 펌웨어 업데이트를 시작하는 .NET 콘솔 앱(C# 사용)을 만듭니다. 앱은 직접 메서드를 사용하여 업데이트를 시작하고 장치 쌍 쿼리를 사용하여 정기적으로 활성 펌웨어 업데이트의 상태를 가져옵니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **TriggerFWUpdate**로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createserviceapp]

2. [솔루션 Explorer]에서 **TriggerFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창][img-servicenuget]
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. **Program** 클래스에 다음 필드를 추가합니다. 여러 자리 표시자 값을 이전 섹션에서 만든 허브에 대한 IoT Hub 연결 문자열 및 장치의 ID로 바꿉니다.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. **Program** 클래스에 다음 메서드를 추가합니다. 이 메서드는 500밀리초마다 업데이트된 상태에 대한 장치 쌍을 폴링합니다. 상태가 실제로 변경되는 경우에만 콘솔에 씁니다. 이 샘플의 경우 구독에서 추가 IoT Hub 메시지를 소비하지 않도록 방지하기 위해 장치가 **applyComplete**라는 상태 또는 오류를 보고할 경우 폴링이 중지됩니다.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다. 그러면 레지스트리 관리자를 만들어 장치 쌍을 읽고, 작업자 스레드에서 폴링 작업을 시작한 후 펌웨어 업데이트를 트리거합니다.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. 솔루션을 빌드하십시오.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 다음을 수행합니다.

* 클라우드에서 호출하는 직접 메서드에 응답하는 .NET 콘솔 앱 만들기
* 직접 메서드를 통해 백 엔드 서비스에 의해 트리거되는 펌웨어 업데이트 시뮬레이션
* reported 속성을 사용하여 장치 및 해당 장치가 마지막으로 펌웨어 업데이트를 완료한 시간을 확인하는 장치 쌍 쿼리를 사용하도록 설정

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **SimulateDeviceFWUpdate**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 장치 앱][img-createdeviceapp]
    
2. 솔루션 탐색기에서 **SimulateDeviceFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices.client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 절차에서는 [Azure IoT 장치 SDK][lnk-nuget-client-sdk] NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창 클라이언트 앱][img-clientnuget]
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 **장치 ID 만들기** 섹션에서 메모한 장치 연결 문자열로 바꿉니다.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. 다음 메서드를 추가하여 장치 쌍을 통해 다시 클라우드에 상태를 보고합니다. 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. 다음 메서드를 추가하여 펌웨어 이미지를 다운로드하도록 시뮬레이션합니다.
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. 다음 메서드를 추가하여 펌웨어 이미지를 장치에 적용하도록 시뮬레이션합니다.
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  다음 메서드를 추가하여 펌웨어 이미지를 다운로드하기 위해 대기하도록 시뮬레이션합니다. 상태를 **대기**로 업데이트하고 쌍의 다른 펌웨어 업데이트 속성의 선택을 취소합니다. 이러한 속성의 선택을 취소하여 이전 펌웨어 업데이트에서 기존 값을 제거합니다. 보고 속성이 PUT 작업(이전 값을 모두 대체하는 전체 집합 속성)이 아닌 PATCH 작업(델타)으로 전송되기 때문에 필요합니다. 일반적으로 장치는 사용 가능한 업데이트에 대한 알림을 받으며 관리자가 정의한 정책이 장치가 업데이트를 다운로드하고 적용하도록 유발합니다. 이 함수가 해당 정책을 활성화하는 논리가 실행되어야 하는 부분입니다. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. 다음 메서드를 추가하여 다운로드를 수행합니다. 장치 쌍을 통해 상태를 **다운로드 중**으로 업데이트하고, 시뮬레이션 다운로드 메서드를 호출하고, 다운로드 작업의 결과에 따라 쌍을 통해 **downloadComplete** 또는 **downloadFailed**라는 상태를 보고합니다. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. 다음 메서드를 추가하여 이미지를 적용합니다. 장치 쌍을 통해 상태를 **적용 중**으로 업데이트하고, 시뮬레이션 적용 이미지 메서드를 호출하고, 적용 작업의 결과에 따라 쌍을 통해 **applyComplete** 또는 **applyFailed**라는 상태를 업데이트합니다. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. 다음 메서드를 추가하여 장치에 이미지를 적용하여 이미지를 다운로드하기 위해 대기하는 펌웨어 업데이트 작업을 시퀀싱합니다.
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. 다음 메서드를 추가하여 클라우드에서 **updateFirmware** 직접 메서드를 처리합니다. 메시지 페이로드의 펌웨어 업데이트에 대한 URL을 추출하고 **doUpdate** 작업에 전달합니다. 이 작업은 다른 스레드 풀 스레드에서 시작합니다. 그러면 즉시 클라우드에 메서드 응답을 반환합니다.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> 이 메서드는 시뮬레이션된 업데이트를 트리거하여 **작업**으로 실행하고 메서드 호출에 즉시 응답하고 펌웨어 업데이트가 시작되었음을 서비스에 알립니다. 업데이트 상태 및 완료는 장치 쌍의 보고 속성을 통해 서비스에 전송됩니다. 다음과 같은 이유로 해당 작업이 완료된 후가 아니라 업데이트를 시작할 때에 메서드 호출에 응답합니다.
> * 실제 업데이트 프로세스가 메서드 호출 제한 시간보다 오래 걸리기 때문입니다.
> * 실제 업데이트 프로세스에 다시 부팅이 필요하기 때문입니다. 이로 인해 **MetodRequest** 개체를 사용할 수 없도록 만든 이 앱을 다시 시작합니다. (하지만 다시 부팅한 후에도 보고된 속성을 업데이트할 수 있습니다.) 

14. 마지막으로 **Main** 메서드에 다음 코드를 추가하여 IoT Hub에 대한 연결을 열고 메서드 수신기를 초기화합니다.
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. 솔루션을 빌드하십시오.       

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.


## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.
1. .NET 장치 앱 **SimulatedDeviceFWUpdate**를 실행합니다.  **SimulatedDeviceFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음 **새 인스턴스 시작**을 선택합니다. IoT Hub의 메서드 호출에 대한 수신이 시작됩니다. 

2. 장치가 연결되어 메서드 호출을 기다리게 되면 .NET **TriggerFWUpdate** 앱을 실행하여 시뮬레이션된 장치 앱에서 펌웨어 업데이트 메서드를 호출합니다. **TriggerFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음 **새 인스턴스 시작**을 선택합니다. **SimulatedDeviceFWUpdate** 콘솔에서 작성된 업데이트 시퀀스 및 **TriggerFWUpdate** 콘솔에서 장치의 보고 속성을 통해 보고된 콘솔 또는 시퀀스가 표시됩니다. 프로세스를 완료하는 데 수 초가 걸립니다. 
   
    ![서비스 및 장치 앱 실행][img-combinedrun]


## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, 보고된 속성을 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/