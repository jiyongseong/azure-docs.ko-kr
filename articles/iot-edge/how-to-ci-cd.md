---
title: Azure IoT Edge 연속 통합 및 지속적인 배포 | Microsoft Docs
description: Azure IoT Edge 연속 통합 및 지속적인 배포 개요
services: iot-Edge
documentationcenter: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 4/30/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 2f635a4c02dd8fd2b58598e53662d1a4d82ea611
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311743"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge---preview"></a>Azure IoT Edge 연속 통합 및 지속적인 배포 - 미리 보기
이 자습서에서는 Visual Studio Team Services(VSTS) 및 Microsoft Team Foundation Server(TFS)의 연속 통합 및 지속적인 배포 기능을 사용하여 응용 프로그램을 빠르고 효율적으로 빌드, 테스트하고 Azure IoT Edge에 배포하는 방법을 설명합니다. 

이 자습서에서는 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * 단위 테스트가 포함된 샘플 IoT Edge 솔루션을 만들고 체크 인합니다.
> * VSTS에 대한 Azure IoT Edge 확장 프로그램을 설치합니다.
> * 연속 통합(CI)을 구성하여 솔루션을 빌드하고 단위 테스트를 실행합니다.
> * 지속적인 배포(CD)를 구성하여 솔루션을 배포하고 응답을 봅니다.

이 자습서를 완료하려면 30분 정도 걸립니다.

![CI 및 CD](./media/how-to-ci-cd/cd.png)

## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Visual Studio Code를 사용하여 샘플 Azure IoT Edge 솔루션 만들기

이 섹션에서는 빌드 프로세스의 일부로 실행할 수 있는 단위 테스트를 포함하는 샘플 IoT Edge 솔루션을 만듭니다. 이 섹션의 지침을 수행하기 전에 [Visual Studio Code에서 여러 모듈을 사용하여 IoT Edge 솔루션 개발](tutorial-multiple-modules-in-vscode.md)의 단계를 완료합니다.

1. VS Code 명령 팔레트에서 **Edge: New IoT Edge solution** 명령을 입력하고 실행합니다. 작업 영역 폴더를 선택하고 솔루션 이름(기본 이름은 **EdgeSolution**임)을 지정한 다음, 이 솔루션의 첫 번째 사용자 모듈로 C# 모듈(**FilterModule**)을 만듭니다. 첫 번째 모듈의 Docker 이미지 리포지토리도 지정해야 합니다. 기본 이미지 리포지토리는 로컬 Docker 레지스트리(`localhost:5000/filtermodule`)를 기반으로 합니다. 향후 연속 통합을 위해 이를 Azure Container Registry(`<your container registry address>/filtermodule`) 또는 Docker 허브로 변경해야 합니다.

    ![ACR 설정](./media/how-to-ci-cd/acr.png)

2. VS Code 창에서 IoT Edge 솔루션 작업 영역이 로드됩니다. 필요에 따라 **Edge: IoT Edge 모듈 추가**를 입력하고 실행하여 모듈을 추가할 수 있습니다. 루트 폴더에 `modules` 폴더, `.vscode` 폴더 및 배포 매니페스트 템플릿 파일이 있습니다. 모든 사용자 모듈 코드는 `modules` 폴더 아래의 하위 폴더입니다. `deployment.template.json`은 배포 매니페스트 템플릿입니다. 이 파일의 일부 매개 변수는 모든 모듈 폴더에 존재하는 `module.json`에서 구문 분석됩니다.

3. 이제 샘플 IoT Edge 솔루션이 준비되었습니다. 기본 C# 모듈은 파이프 메시지 모듈로 작동합니다. `deployment.template.json`에서 이 솔루션에 두 개의 모듈이 포함된 것을 확인할 수 있습니다. `tempSensor` 모듈에서 메시지가 생성되어 `FilterModule`을 통해 직접 파이프된 다음, IoT 허브로 전송됩니다. 전체 **Program.cs** 파일을 아래 내용으로 바꾸세요. 이 코드 조각에 대한 자세한 내용은 [IoT Edge C# 모듈 프로젝트 만들기](https://docs.microsoft.com/azure/iot-edge/tutorial-csharp-module#create-an-iot-edge-module-project)를 참조하세요.

    ```csharp
    namespace FilterModule
    {
        using System;
        using System.IO;
        using System.Runtime.InteropServices;
        using System.Runtime.Loader;
        using System.Security.Cryptography.X509Certificates;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Client.Transport.Mqtt;
        using System.Collections.Generic;     // for KeyValuePair<>
        using Microsoft.Azure.Devices.Shared; // for TwinCollection
        using Newtonsoft.Json;                // for JsonConvert

        public class MessageBody
        {
            public Machine machine { get; set; }
            public Ambient ambient { get; set; }
            public string timeCreated { get; set; }
        }
        public class Machine
        {
            public double temperature { get; set; }
            public double pressure { get; set; }
        }
        public class Ambient
        {
            public double temperature { get; set; }
            public int humidity { get; set; }
        }

        public class Program
        {
            static int counter;
            static int temperatureThreshold { get; set; } = 25;

            static void Main(string[] args)
            {
                // The Edge runtime gives us the connection string we need -- it is injected as an environment variable
                string connectionString = Environment.GetEnvironmentVariable("EdgeHubConnectionString");

                // Cert verification is not yet fully functional when using Windows OS for the container
                bool bypassCertVerification = RuntimeInformation.IsOSPlatform(OSPlatform.Windows);
                if (!bypassCertVerification) InstallCert();
                Init(connectionString, bypassCertVerification).Wait();

                // Wait until the app unloads or is cancelled
                var cts = new CancellationTokenSource();
                AssemblyLoadContext.Default.Unloading += (ctx) => cts.Cancel();
                Console.CancelKeyPress += (sender, cpe) => cts.Cancel();
                WhenCancelled(cts.Token).Wait();
            }

            /// <summary>
            /// Handles cleanup operations when app is cancelled or unloads
            /// </summary>
            public static Task WhenCancelled(CancellationToken cancellationToken)
            {
                var tcs = new TaskCompletionSource<bool>();
                cancellationToken.Register(s => ((TaskCompletionSource<bool>)s).SetResult(true), tcs);
                return tcs.Task;
            }

            /// <summary>
            /// Add certificate in local cert store for use by client for secure connection to IoT Edge runtime
            /// </summary>
            static void InstallCert()
            {
                string certPath = Environment.GetEnvironmentVariable("EdgeModuleCACertificateFile");
                if (string.IsNullOrWhiteSpace(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing path to certificate file.");
                }
                else if (!File.Exists(certPath))
                {
                    // We cannot proceed further without a proper cert file
                    Console.WriteLine($"Missing path to certificate collection file: {certPath}");
                    throw new InvalidOperationException("Missing certificate file.");
                }
                X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
                store.Open(OpenFlags.ReadWrite);
                store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
                Console.WriteLine("Added Cert: " + certPath);
                store.Close();
            }
            /// <summary>
            /// Initializes the DeviceClient and sets up the callback to receive
            /// messages containing temperature information
            /// </summary>
            static async Task Init(string connectionString, bool bypassCertVerification = false)
            {
                Console.WriteLine("Connection String {0}", connectionString);

                MqttTransportSettings mqttSetting = new MqttTransportSettings(TransportType.Mqtt_Tcp_Only);
                // During dev you might want to bypass the cert verification. It is highly recommended to verify certs systematically in production
                if (bypassCertVerification)
                {
                    mqttSetting.RemoteCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) => true;
                }
                ITransportSettings[] settings = { mqttSetting };

                // Open a connection to the Edge runtime
                DeviceClient ioTHubModuleClient = DeviceClient.CreateFromConnectionString(connectionString, settings);
                await ioTHubModuleClient.OpenAsync();
                Console.WriteLine("IoT Hub module client initialized.");

                // Register callback to be called when a message is received by the module
                // await ioTHubModuleClient.SetImputMessageHandlerAsync("input1", PipeMessage, iotHubModuleClient);

                // Read TemperatureThreshold from Module Twin Desired Properties
                var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
                var moduleTwinCollection = moduleTwin.Properties.Desired;
                try {
                    temperatureThreshold = moduleTwinCollection["TemperatureThreshold"];
                } catch(ArgumentOutOfRangeException) {
                    Console.WriteLine("Proerty TemperatureThreshold not exist");
                }

                // Attach callback for Twin desired properties updates
                await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(onDesiredPropertiesUpdate, null);

                // Register callback to be called when a message is received by the module
                await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
            }

            static Task onDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
            {
                try
                {
                    Console.WriteLine("Desired property change:");
                    Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                    if (desiredProperties["TemperatureThreshold"] != null)
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

            public static Message filter(Message message)
            {
                var counterValue = Interlocked.Increment(ref counter);

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
                    return filteredMessage;
                }
                return null;
            }

            static async Task<MessageResponse> FilterMessages(Message message, object userContext)
            {
                try
                {
                    DeviceClient deviceClient = (DeviceClient)userContext;

                    var filteredMessage = filter(message);
                    if (filteredMessage != null)
                    {
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
        }
    }
    ```

4. .Net Core 단위 테스트 프로젝트를 만듭니다. VS Code 파일 탐색기에서 작업 영역에 **tests\FilterModuleTest**라는 새 폴더를 만듭니다. 그런 다음, VS Code 통합 터미널(**Ctrl + `**)에서 다음 명령을 실행하여 xUnit 테스트 프로젝트를 만들고 **FilterModule** 프로젝트에 대한 참조를 추가합니다.

    ```cmd
    cd tests\FilterModuleTest
    dotnet new xunit
    dotnet add reference ../../modules/FilterModule/FilterModule.csproj
    ```

    ![폴더 구조](./media/how-to-ci-cd/add-test-project.png)

5. **FilterModuleTest** 폴더에서 **UnitTest1.cs**의 파일 이름을 **FilterModuleTest.cs**로 업데이트합니다. **FilterModuleTest.cs**를 선택하고 연 다음, FilterModule 프로젝트에 대한 단위 테스트가 포함된 전체 코드를 아래 코드 조각으로 바꿉니다.

    ```csharp
    using Xunit;
    using FilterModule;
    using Newtonsoft.Json;
    using System;
    using System.IO;
    using System.Runtime.InteropServices;
    using System.Runtime.Loader;
    using System.Security.Cryptography.X509Certificates;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Client.Transport.Mqtt;

    namespace FilterModuleTest
    {
        public class FilterModuleTest
        {
            [Fact]
            public void filterLessThanThresholdTest()
            {
                var source = createMessage(25 - 1);
                var result = Program.filter(source);
                Assert.True(result == null);
            }

            [Fact]
            public void filterMoreThanThresholdAlertPropertyTest()
            {
                var source = createMessage(25 + 1);
                var result = Program.filter(source);
                Assert.True(result.Properties["MessageType"] == "Alert");
            }

            [Fact]
            public void filterMoreThanThresholdCopyPropertyTest()
            {
                var source = createMessage(25 + 1);
                source.Properties.Add("customTestKey", "customTestValue");
                var result = Program.filter(source);
                Assert.True(result.Properties["customTestKey"] == "customTestValue");
            }

            private Message createMessage(int temperature)
            {
                var messageBody = createMessageBody(temperature);
                var messageString = JsonConvert.SerializeObject(messageBody);
                var messageBytes = Encoding.UTF8.GetBytes(messageString);
                return new Message(messageBytes);
            }

            private MessageBody createMessageBody(int temperature)
            {
                var messageBody = new MessageBody
                {
                    machine = new Machine
                    {
                        temperature = temperature,
                        pressure = 0
                    },
                    ambient = new Ambient
                    {
                        temperature = 0,
                        humidity = 0
                    },
                    timeCreated = string.Format("{0:O}", DateTime.Now)
                };

                return messageBody;
            }
        }
    }
    ```

6. 통합 터미널에서 단위 테스트를 로컬로 실행하는 다음 명령을 입력할 수 있습니다. 
    ```cmd
    dotnet test
    ```

    ![단위 테스트](./media/how-to-ci-cd/unit-test.png)

7. 이러한 프로젝트를 저장한 다음, VSTS 또는 TFS 리포지토리를 확인합니다.
    

> [!NOTE]
> VSTS Code 리포지토리를 사용하는 방법에 대한 자세한 내용은 [Visual Studio 및 VSTS Git와 코드 공유](https://docs.microsoft.com/vsts/git/share-your-code-in-git-vs?view=vsts)를 참조하세요.


## <a name="configure-continuous-integration"></a>연속 통합 구성
이 섹션에서는 샘플 IoT Edge 솔루션에 변경 사항을 체크 인하면 자동으로 실행되도록 구성된 빌드 정의를 만듭니다. 이 빌드 정의는 자체적으로 포함된 단위 테스트를 자동으로 실행하게 됩니다.

1. VSTS 계정(**https://**_your-account_**.visualstudio.com**)에 로그인하고 샘플 앱을 체크 인한 프로젝트를 엽니다.

    ![코드 체크 인](./media/how-to-ci-cd/init-project.png)

1. VSTS Marketplace에서 [Azure IoT Edge For VSTS](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy)를 방문합니다. **무료로 받기**를 클릭하고 마법사를 따라 이 확장 프로그램을 VSTS 계정에 설치하거나 TFS에 다운로드합니다.

    ![확장 설치](./media/how-to-ci-cd/install-extension.png)

1. VSTS에서 **빌드 &amp; 릴리스** 허브를 열고 **빌드** 탭에서 **+ 새 정의**를 선택합니다. 또는 빌드 정의가 이미 있는 경우 **+ 새로 만들기** 단추를 선택합니다. 

    ![새 빌드](./media/how-to-ci-cd/add-new-build.png)

1. 메시지가 표시되면 **VSTS Git** 원본 형식을 선택한 다음, 코드가 있는 프로젝트, 리포지토리 및 분기를 선택합니다. **계속**을 선택합니다.

    ![VSTS Git 선택](./media/how-to-ci-cd/select-vsts-git.png)

1. **템플릿 선택** 창에서 **빈 프로세스로 시작**을 선택합니다.

    ![빈 프로세스로 시작](./media/how-to-ci-cd/start-with-empty.png)

1. **단계 1**의 오른쪽에 있는 **+** 를 클릭하여 단계에 작업을 추가합니다. 그런 다음, **.Net Core**를 검색하여 선택하고 **추가**를 클릭하여 이 작업을 단계에 추가합니다.

    ![Dotnet 테스트](./media/how-to-ci-cd/add-dot-net-core.png)

1. **표시 이름**을 **dotnet 테스트**로 업데이트하고 **명령** 드롭다운 목록에서 **테스트**를 선택합니다. **프로젝트 경로**에 아래 경로를 추가합니다.

    ```
    tests/FilterModuleTest/*.csproj
    ```

    ![Dotnet 테스트 구성](./media/how-to-ci-cd/dotnet-test.png)

1. **단계 1**의 오른쪽에 있는 **+** 를 클릭하여 단계에 작업을 추가합니다. 그런 다음, **Azure IoT Edge**를 검색하여 선택하고 **추가** 단추를 **두 번** 클릭하여 이러한 작업을 단계에 추가합니다.

    ![IoT Edge](./media/how-to-ci-cd/add-azure-iot-edge.png)

1. 첫 번째 Azure IoT Edge 작업에서 **표시 이름**을 **모듈 빌드 및 푸시**로 업데이트하고 **작업** 드롭다운 목록에서 **빌드 및 푸시**를 선택합니다. **Module.json 파일** 텍스트 상자에서 아래 경로를 추가합니다. 그런 다음, **컨테이너 레지스트리 유형**을 선택하여 코드에서 동일한 레지스트리를 구성하고 선택하도록 합니다. 이 작업은 솔루션의 모든 모듈을 빌드 및 푸시하고 지정된 컨테이너 레지스트리로 게시합니다. 

    ```
    **/module.json
    ```

    ![모듈 빌드 및 푸시](./media/how-to-ci-cd/module-build-push.png)

1. 두 번째 Azure IoT Edge 작업에서 **표시 이름**을 **IoT Edge 장치에 배포**로 업데이트하고 **작업** 드롭다운 목록에서 **IoT Edge 장치에 배포**를 선택합니다. Azure 구독을 선택하고 IoT Hub 이름을 입력합니다. IoT Edge 배포 ID와 배포 우선 순위를 지정할 수 있습니다. 또한 단일 또는 여러 장치에 배포하도록 선택할 수 있습니다. 여러 장치에 배포하는 경우 장치 대상 조건을 지정해야 합니다. 예를 들어 장치 태그를 조건으로 사용하려는 경우 배포하기 전에 해당 장치 태그를 업데이트해야 합니다. 

    ![Edge에 배포](./media/how-to-ci-cd/deploy-to-edge.png)

1. **프로세스**를 클릭하고 **에이전트 큐**가 **호스팅되는 Linux 미리 보기**인지 확인합니다.

    ![구성](./media/how-to-ci-cd/configure-env.png)

1. **트리거** 탭을 열고 **연속 통합** 트리거를 켭니다. 코드를 포함하는 분기가 포함되어 있는지 확인합니다.

    ![트리거](./media/how-to-ci-cd/configure-trigger.png)

1. 새 빌드 정의를 저장하고 새 빌드를 큐에 넣습니다. **저장 및 큐에 넣기** 단추를 클릭합니다.

1. 메시지 표시줄이 나타나면 빌드에 대한 링크를 선택합니다. 또는 빌드 정의로 이동하여 큐에 대기 중인 최신 빌드 작업을 봅니다.

    ![빌드](./media/how-to-ci-cd/build-def.png)

1. 빌드가 완료된 후 실시간 로그 파일에서 각 작업 및 결과에 대한 요약을 볼 수 있습니다. 
    
    ![완료](./media/how-to-ci-cd/complete.png)

1. VS Code로 돌아가서 IoT Hub 장치 탐색기를 확인할 수 있습니다. 해당 모듈이 있는 Edge 장치가 실행을 시작해야 합니다(Edge 런타임에 레지스트리 자격 증명을 추가했는지 확인).

    ![Edge 실행 중](./media/how-to-ci-cd/edge-running.png)

## <a name="continuous-deployment-to-iot-edge-devices"></a>IoT Edge 장치에 대한 지속적인 배포

지속적인 배포를 활성화하려면 기본적으로 적절한 IoT Edge 장치로 CI 작업을 설정하고, 프로젝트의 분기에 대한 **트리거**를 활성화해야 합니다. 전형적인 DevOps 연습에는 프로젝트에 두 개의 주요 분기가 포함됩니다. 마스터 분기는 코드의 안정적인 버전이어야 하며, 개발 분기에는 최신 코드 변경 사항이 포함되어야 합니다. 팀의 모든 개발자는 코드를 업데이트하기 시작할 때 자신의 기능 분기에 개발 분기를 포크해야 합니다. 즉, 모든 커밋은 개발 분기와 떨어진 기능 분기에서 이루어집니다. 그리고 푸시되는 모든 커밋은 CI 시스템을 통해 테스트되어야 합니다. 코드가 로컬에서 완전히 테스트된 후에 기능 분기가 끌어오기 요청을 통해 개발 분기에 병합되어야 합니다. 개발자 분기의 코드가 CI 시스템을 통해 테스트되면 끌어오기 요청을 통해 마스터 분기에 병합될 수 있습니다.

따라서 IoT Edge 장치에 배포할 때는 세 가지 주요 환경이 있습니다.
- 기능 분기에서는 개발 컴퓨터의 시뮬레이션된 IoT Edge 장치를 사용하거나 물리적 IoT Edge 장치에 배포할 수 있습니다.
- 개발 분기에서는 물리적 IoT Edge 장치에 배포해야 합니다.
- 마스터 분기에서는 대상 IoT Edge 장치가 프로덕션 장치여야 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VSTS 또는 TFS의 연속 통합 및 지속적인 배포 기능을 사용하는 방법을 설명합니다. 

* [단일 장치 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 통해 IoT Edge 배포 이해
* [대규모 IoT Edge 모듈 배포 및 모니터링][how-to-deploy-monitor.md]에서 배포를 생성, 업데이트 또는 삭제하는 단계를 연습합니다.









