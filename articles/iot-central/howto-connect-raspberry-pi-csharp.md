---
title: Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결(C#) | Microsoft Docs
description: 장치 개발자로서 C#을 사용하여 Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아봅니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: d09d3de04f8c846eadc7367ca4d4559eb55f995b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200472"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Azure IoT Central 응용 프로그램에 Raspberry Pi 연결(C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

이 문서에서는 장치 개발자로서 C# 프로그래밍 언어를 사용하여 Raspberry Pi를 Microsoft Azure IoT Central 응용 프로그램에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음이 필요합니다.

* 개발 컴퓨터에 [.NET Core 2](https://www.microsoft.com/net)가 설치되어 있어야 합니다. 또한 [Visual Studio Code](https://code.visualstudio.com/) 같은 적절한 코드 편집기가 있어야 합니다.
* **샘플 Devkits** 응용 프로그램 템플릿으로 만든 Azure IoT Central 응용 프로그램. 자세한 내용은 [Azure IoT Central 응용 프로그램 만들기](howto-create-application.md)를 참조하세요.
* Raspbian 운영 체제를 실행하는 Raspberry Pi 장치.

**샘플 Devkits** 응용 프로그램 템플릿으로 만든 응용 프로그램에는 다음과 같은 특징을 가진 **Raspberry Pi** 장치가 포함됩니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

| 필드 이름     | Units  | 최소 | 최대 | 소수 자릿수 |
| -------------- | ------ | ------- | ------- | -------------- |
| humidity       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>설정

숫자 설정

| 표시 이름 | 필드 이름 | Units | 소수 자릿수 | 최소 | 최대 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 전압      | setVoltage | 볼트 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 암페어  | 0              | 0       | 100     | 0       |
| 팬 속도    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

설정 전환

| 표시 이름 | 필드 이름 | 온 텍스트 | 오프 텍스트 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 켜기      | 끄기      | 꺼짐     |

### <a name="properties"></a>properties

| type            | 표시 이름 | 필드 이름 | 데이터 형식 |
| --------------- | ------------ | ---------- | --------- |
| 장치 속성 | 다이 번호   | dieNumber  | number    |
| 텍스트            | 위치     | location   | 해당 없음       |

### <a name="add-a-real-device"></a>실제 장치 추가

Azure IoT Central 응용 프로그램에서 **Raspberry Pi** 장치 템플릿으로 실제 장치를 추가하고 장치 연결 문자열을 기록해 둡니다. 자세한 내용은 [Azure IoT Central 응용 프로그램에 실제 장치 추가](tutorial-add-device.md)를 참조하세요.

## <a name="create-your-net-application"></a>.NET 응용 프로그램 만들기

데스크톱 컴퓨터에서 장치 응용 프로그램을 만들고 테스트합니다.

Visual Studio Code를 사용하여 다음 단계를 완료할 수 있습니다. 자세한 내용은 [C# 사용](https://code.visualstudio.com/docs/languages/csharp)을 참조하세요.

> [!NOTE]
> 원한다면 다른 코드 편집기를 사용하여 다음 단계를 완료해도 됩니다.

1. .NET 프로젝트를 초기화하고 필수 NuGet 패키지를 추가하려면 다음 명령을 실행합니다.

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Visual Studio Code에서 `pisample` 폴더를 엽니다. **pisample.csproj** 프로젝트 파일을 엽니다. 다음 코드 조각에 표시된 `<RuntimeIdentifiers>` 태그를 추가합니다.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > 표시된 버전보다 **Microsoft.Azure.Devices.Client** 패키지 버전 번호가 더 높을 수 있습니다.

1. **pisample.csproj** 파일을 저장합니다. Visual Studio Code에서 복원 명령을 실행하라는 메시지를 표시하면 **복원**을 선택합니다.

1. **Program.cs**를 열고 내용을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
          {
            Random rand = new Random();

            while (true)
            {
              double currentTemperature = baseTemperature + rand.NextDouble() * 20;
              double currentPressure = basePressure + rand.NextDouble() * 100;
              double currentHumidity = baseHumidity + rand.NextDouble() * 20;

              var telemetryDataPoint = new
              {
                humidity = currentHumidity,
                pressure = currentPressure,
                temp = currentTemperature
              };
              var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
              var message = new Message(Encoding.ASCII.GetBytes(messageString));

              token.ThrowIfCancellationRequested();
              await Client.SendEventAsync(message);

              Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

              await Task.Delay(1000);
            }
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

    > [!NOTE]
    > 그 다음 단계에서 `{your device connection string}` 자리 표시자를 업데이트합니다.

## <a name="run-your-net-application"></a>.NET 응용 프로그램 실행

장치가 Azure IoT Central에 인증할 장치 관련 연결 문자열을 코드에 추가합니다. Azure IoT Central 응용 프로그램에 실제 장치를 추가할 때 이 연결 문자열을 기록해 두었습니다.

1. **Program.cs** 파일의 `{your device connection string}`을 앞에서 기록해 둔 연결 문자열로 바꿉니다.

1. 명령줄 환경에서 다음 명령을 실행합니다.

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` 폴더를 Raspberry Pi 장치에 복사합니다. 다음과 같이 **scp** 명령을 사용하여 파일을 추가합니다.

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    자세한 내용은 [Raspberry Pi 원격 액세스](https://www.raspberrypi.org/documentation/remote-access/)를 참조하세요.

1. Raspberry Pi 장치에 로그인하여 셸에서 다음 명령을 실행합니다.

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Raspberry Pi에서 다음 명령을 실행합니다.

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![프로그램 시작](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Azure IoT Central 응용 프로그램에서, Raspberry Pi에서 실행되는 코드가 응용 프로그램과 상호 작용하는 방식을 살펴볼 수 있습니다.

    * 실제 장치의 **측정값** 페이지에서 원격 분석 데이터를 볼 수 있습니다.
    * **속성** 페이지에서, 보고된 **다이 번호** 속성 값을 볼 수 있습니다.
    * **설정** 페이지에서, 전압, 팬 속도 등 Raspberry Pi의 다양한 설정을 변경할 수 있습니다.

    다음 스크린샷은 설정 변경 내용을 수신하는 Raspberry Pi를 보여줍니다.

    ![설정 변경 내용을 수신하는 Raspberry Pi](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="next-steps"></a>다음 단계

Raspberry Pi를 Azure IoT Central 응용 프로그램에 연결하는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

* [Azure IoT Central에 일반 Node.js 클라이언트 응용 프로그램 연결](howto-connect-nodejs.md)