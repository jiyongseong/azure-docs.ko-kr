---
title: Azure IoT Hub 장치 쌍 시작(.NET/.NET) | Microsoft Docs
description: Azure IoT Hub 장치 쌍을 사용하여 태그를 추가한 다음 IoT Hub 쿼리를 사용하는 방법입니다. .NET용 Azure IoT 장치 SDK를 사용하여 시뮬레이트된 장치 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: ''
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: b337e463ac4aefd7e4644af4c2cdcad4f3c5300a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-device-twins-netnet"></a>장치 쌍(.NET/.NET) 시작
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서의 끝 부분에 다음 .NET 콘솔 앱이 제공됩니다.

* **CreateDeviceIdentity**, 장치 ID 및 관련된 보안 키를 만들어 시뮬레이트된 장치 앱에 연결하는 .NET 앱입니다.
* **AddTagsAndQuery**, 태그를 추가하고 장치 쌍을 쿼리하는 .NET 백 엔드 앱입니다.
* **ReportConnectivity**, 앞에서 만든 장치 ID와 IoT Hub를 연결하고 연결 상태를 보고하는 장치를 시뮬레이트하는 .NET 장치 앱입니다.

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 문서는 장치 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기
이 섹션에서는 **myDeviceId**와 연결된 장치 쌍에 위치 메타데이터를 추가하는 .NET 콘솔 앱(C# 사용)을 만듭니다. 그런 다음 IoT Hub에 저장된 장치 쌍을 쿼리하여 미국에 있는 장치를 선택한 다음 셀룰러 연결을 보고하는 장치를 선택합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트의 이름을 **AddTagsAndQuery**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]
1. 솔루션 탐색기에서 **AddTagsAndQuery** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색합니다. **설치**를 설치하여 **Microsoft.Azure.Devices** 패키지를 설치한 후 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창][img-servicenuget]
1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. **Program** 클래스에 다음 메서드를 추가합니다.
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager** 클래스는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드에서는 **registryManager** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍을 검색하고, 마지막으로 원하는 위치 정보로 tags를 업데이트합니다.
   
    업데이트한 후 두 개의 쿼리를 실행합니다. 첫 번째는 **Redmond43** 공장에 위치한 장치의 장치 쌍만을 선택하고, 두 번째는 또한 셀룰러 네트워크를 통해서 연결된 장치만을 선택하기 위해 쿼리를 구체화합니다.
   
    이전 코드는 **쿼리** 개체를 만들 때 반환되는 최대 문서 수를 지정한다는 점에 유의합니다. **query** 개체에는 모든 결과를 검색하기 위해 여러 번 **GetNextAsTwinAsync** 메서드를 호출하는 데 사용할 수 있는 **HasMoreResults** 부울 속성이 들어 있습니다. **GetNextAsJson**이라는 메서드는 장치 쌍이 아닌 결과(예: 집계 쿼리의 결과)에 대해 사용할 수 있습니다.
1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. 솔루션 탐색기에서 **시작 프로젝트 설정...** 을 열고 **AddTagsAndQuery** 프로젝트의 **작업**이 **시작**인지 확인합니다. 솔루션을 빌드하십시오.
1. **AddTagsAndQuery** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 후 **새 인스턴스 시작**을 선택하여 이 응용 프로그램을 실행합니다. **Redmond43**에 위치한 모든 장치를 요청하는 쿼리에 대한 결과로는 하나의 장치를 보고 셀룰러 네트워크를 사용하는 장치에 대해서는 결과를 제한하는 쿼리에 대한 결과로는 아무 장치도 볼 수 없어야 합니다.
   
    ![창에서 쿼리 결과][img-addtagapp]

다음 섹션에서는 연결 정보를 보고하고 이전 섹션의 쿼리 결과를 변경하는 장치 앱을 만듭니다.

## <a name="create-the-device-app"></a>장치 앱 만들기
이 섹션에서는 **myDeviceId**로 허브에 연결하는 .NET 콘솔 앱을 만든 다음 셀룰러 네트워크를 사용하여 연결되는 정보에 포함된 reported 속성을 업데이트합니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **ReportConnectivity**로 지정합니다.
   
    ![새 Visual C# Windows 클래식 장치 앱][img-createdeviceapp]
    
1. [솔루션 탐색기]에서 **ReportConnectivity** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리...** 를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices.client**를 검색합니다. **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치한 후 사용 약관에 동의합니다. 이 절차에서는 [Azure IoT 장치 SDK][lnk-nuget-client-sdk] NuGet 패키지 및 해당 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
   
    ![NuGet 패키지 관리자 창 클라이언트 앱][img-clientnuget]
1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 메모한 장치 연결 문자열로 대체합니다.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. **Program** 클래스에 다음 메서드를 추가합니다.

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    **Client** 개체는 서비스의 장치 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 위에 표시된 코드는 **Client** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍을 검색합니다.

1. **Program** 클래스에 다음 메서드를 추가합니다.
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   위의 코드는 **myDeviceId**의 보고된 속성을 연결 정보로 업데이트합니다.

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. [솔루션 탐색기]에서 **시작 프로젝트 설정...** 을 열고 **ReportConnectivity** 프로젝트의 **작업**이 **시작**인지 확인합니다. 솔루션을 빌드하십시오.
1. **ReportConnectivity** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 후 **새 인스턴스 시작**을 선택하여 이 응용 프로그램을 실행합니다. 쌍 정보를 가져온 다음 연결을 *보고된 속성*으로 보내는 메시지가 표시됩니다.
   
    ![장치 앱을 실행하여 연결 보고][img-rundeviceapp]
    
    
1. 장치가 연결 정보를 보고했으므로 두 쿼리 모두에 나타나야 합니다. .NET **AddTagsAndQuery** 앱을 실행하여 쿼리를 다시 실행합니다. 이번에는 **myDeviceId**가 두 쿼리 결과에 모두 나타나야 합니다.
   
    ![장치 연결이 성공적으로 보고됨][img-tagappsuccess]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 백 엔드 앱에서 tags로 장치 메타데이터를 추가하고, 장치 쌍에서 장치 연결 정보를 보고하는 시뮬레이션된 장치 앱을 작성했습니다. 또한 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 이 정보를 쿼리하는 방법도 살펴보았습니다.

아래와 같이 실행할 방법을 알아보려면 다음 리소스를 참조하세요.

* [IoT Hub 시작][lnk-iothub-getstarted] 자습서를 참조하여 장치에서 원격 분석을 보냅니다.
* [desired 속성을 사용하여 장치 구성][lnk-twin-how-to-configure] 자습서를 참조하여 장치 쌍의 desired 속성을 사용하여 장치를 구성합니다.
* [직접 메서드 사용][lnk-methods-tutorial] 자습서를 참조하여 대화형으로(예: 사용자 제어 앱에서 팬 작동) 장치를 제어합니다.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

