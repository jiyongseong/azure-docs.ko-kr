---
title: Azure IoT Hub를 사용하여 장치 펌웨어 업데이트(.NET/Node) | Microsoft Docs
description: 장치 펌웨어 업데이트를 시작하려면 Azure IoT Hub에서 장치 관리를 사용하는 방법입니다. Node.js용 Azure IoT 장치 SDK를 사용하여 시뮬레이션된 장치 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 펌웨어 업데이트를 트리거하는 서비스 앱을 구현합니다.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2017
ms.author: juanpere
ms.openlocfilehash: 4f0428fc2cddee0181705cad9675cc31fc38b753
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnode"></a>장치 관리를 사용하여 장치 펌웨어 업데이트(.NET/Node)를 시작합니다.
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

[장치 관리 시작][lnk-dm-getstarted] 자습서에서 [장치 쌍][lnk-devtwin] 및 [직접 메서드][lnk-c2dmethod] 기본 형식을 사용하여 장치를 원격으로 다시 부팅하는 방법을 살펴보았습니다. 이 자습서에서는 동일한 IoT Hub 기본 형식을 사용하고 시뮬레이션된 종단 간 펌웨어 업데이트를 수행하는 방법을 보여 줍니다.  이 패턴은 [Raspberry Pi 장치 구현 샘플][lnk-rpi-implementation]에 대한 펌웨어 업데이트 구현에 사용됩니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* IoT Hub를 통해 시뮬레이션된 장치 앱에서 firmwareUpdate 직접 메서드를 호출하는 .NET 콘솔 앱을 만듭니다.
* **firmwareUpdate** 직접 메서드를 구현하는 시뮬레이트된 장치 앱을 만듭니다. 이 메서드는 펌웨어 이미지를 다운로드하기 위해 대기했다가 펌웨어 이미지를 다운로드하고, 마지막으로 펌웨어 이미지를 적용하는 다단계 프로세스를 시작합니다. 업데이트의 각 단계 동안, 장치는 보고된 속성을 사용하여 진행 상황을 보고합니다.

이 자습서의 끝 부분에는 다음과 같은 Node.js 콘솔 장치 앱과 .NET(C#) 콘솔 백 엔드 앱이 있습니다.

**dmpatterns_fwupdate_service.js** - 시뮬레이션된 장치 앱에서 직접 메서드를 호출하고 응답을 표시하며 업데이트된 reported 속성을 주기적으로(매 500밀리초) 표시합니다.

**TriggerFWUpdate** - 앞에서 만든 장치 ID를 사용하여 IoT Hub 에 연결하고, firmwareUpdate 직접 메서드를 수신하며, 펌웨어 업데이트를 시뮬레이션하기 위한 다단계 프로세스(이미지 다운로드 대기, 새 이미지 다운로드, 마지막으로 이미지 적용 등)를 실행합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* Node.js 버전 4.0.x 이상 <br/>  Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비][lnk-dev-setup]에서 설명합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

IoT hub 허브를 만들고 IoT Hub 연결 문자열을 확보하려면 [장치 관리 시작](iot-hub-csharp-node-device-management-get-started.md) 문서의 내용을 수행하세요.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트 트리거
이 섹션에서는 장치에서 원격 펌웨어 업데이트를 시작하는 .NET 콘솔 앱(C# 사용)을 만듭니다. 앱은 직접 메서드를 사용하여 업데이트를 시작하고 장치 쌍 쿼리를 사용하여 정기적으로 활성 펌웨어 업데이트의 상태를 가져옵니다.

1. Visual Studio에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 최신 솔루션에 추가합니다. 프로젝트 이름을 **TriggerFWUpdate**로 지정합니다.

    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][img-createapp]

1. 솔루션 탐색기에서 **TriggerFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...**를 클릭합니다.
1. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **microsoft.azure.devices**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 서비스 SDK][lnk-nuget-service-sdk] NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

    ![NuGet 패키지 관리자 창][img-servicenuget]
1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
1. **Program** 클래스에 다음 필드를 추가합니다. 여러 자리 표시자 값을 이전 섹션에서 만든 허브에 대한 IoT Hub 연결 문자열 및 장치의 ID로 바꿉니다.
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "{deviceIdForTargetDevice}";
        
1. **Program** 클래스에 다음 메서드를 추가합니다.
   
        public static async Task QueryTwinFWUpdateReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
1. **Program** 클래스에 다음 메서드를 추가합니다.

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

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartFirmwareUpdate().Wait();
        QueryTwinFWUpdateReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
1. 솔루션 탐색기에서 **시작 프로젝트 설정...**을 열고 **TriggerFWUpdate** 프로젝트의 **작업**이 **시작**인지 확인합니다.

1. 솔루션을 빌드하십시오.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **manageddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 다시 시작 직접 메서드에 대한 수신 대기를 시작합니다.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. Visual Studio에서 **TriggerFWUpdate** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**, **새 인스턴스 시작**을 차례로 선택합니다.

3. 콘솔에서 직접 메서드에 대한 장치 응답을 확인합니다.

    ![펌웨어가 성공적으로 업데이트됨][img-fwupdate]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 직접 메서드를 사용하여 장치에서 원격 펌웨어 업데이트를 트리거하고, 보고된 속성을 사용하여 펌웨어 업데이트 프로세스의 진행 상황을 이해했습니다.

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [jobs 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-firmware-update/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-firmware-update/createnetapp.png
[img-fwupdate]: media/iot-hub-csharp-node-firmware-update/fwupdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/