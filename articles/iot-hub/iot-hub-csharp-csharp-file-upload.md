---
title: .NET을 사용하여 장치에서 Azure IoT Hub로 파일 업로드 | Microsoft Docs
description: .NET용 Azure IoT 장치 SDK를 사용하여 장치에서 클라우드로 파일을 업로드 하는 방법입니다. 업로드된 파일은 Azure Storage blob 컨테이너에 저장됩니다.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2017
ms.author: elioda
ms.openlocfilehash: 901b4b6c631d47a6c37eb232f66d8350faa9be76
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-using-net"></a>.NET을 사용하여 장치에서 IoT Hub가 있는 클라우드로 파일 업로드

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

이 자습서에서는 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기](iot-hub-csharp-csharp-c2d.md) 자습서의 코드를 기반으로 작성되었으며 IoT Hub의 파일 업로드 기능을 사용하는 방법을 보여 줍니다. 이 항목에서는 다음 방법을 설명합니다.

- 파일을 업로드하기 위한 Azure blob URI를 장치에 안전하게 제공합니다.
- IoT Hub 파일 업로드 알림을 사용하여 앱 백 엔드에서 파일 처리를 트리거합니다.

[IoT Hub 시작](iot-hub-csharp-csharp-getstarted.md) 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기](iot-hub-csharp-csharp-c2d.md) 자습서는 IoT Hub의 기본적인 장치-클라우드 및 클라우드-장치 메시징 기능을 보여 줍니다. [장치-클라우드 메시지 프로세스](iot-hub-csharp-csharp-process-d2c.md) 자습서에서는 장치-클라우드 메시지를 Azure Blob 저장소에 안정적으로 저장하는 방법에 대해 설명합니다. 그러나 일부 시나리오에서는 장치에서 전송하는 데이터를 IoT Hub에서 허용하는 비교적 작은 장치-클라우드 메시지에 쉽게 매핑할 수 없습니다. 예: 

* 이미지가 포함된 대형 파일
* 동영상
* 자주 샘플링되는 진동 데이터
* 특정 형태의 전처리된 데이터

이러한 파일은 일반적으로 [Azure Data Factory](../data-factory/introduction.md) 또는 [Hadoop](../hdinsight/index.yml) 스택과 같은 도구를 사용하여 클라우드에서 배치 방식으로 처리됩니다. 장치에서 파일을 업로드해야 할 때 IoT Hub의 보안 및 안정성을 여전히 사용할 수 있습니다.

이 자습서의 끝 부분에서 다음의 두 .NET 콘솔 앱을 실행합니다.

* **SimulatedDevice** - [IoT Hub를 사용하여 클라우드-장치 메시지 보내기](iot-hub-csharp-csharp-c2d.md) 자습서에서 만든 앱의 수정된 버전입니다. 이 앱은 IoT Hub에서 제공하는 SAS URI를 사용하여 저장소에 파일을 업로드합니다.
* **ReadFileUploadNotification** - IoT Hub에서 파일 업로드 알림을 받습니다.

> [!NOTE]
> IoT Hub는 Azure IoT 장치 SDK를 통해 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 지원합니다. Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>장치 앱에서 파일 업로드

이 섹션에서는 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기](iot-hub-csharp-csharp-c2d.md)에서 만든 장치 앱을 수정하여 IoT Hub로부터 클라우드-장치 메시지를 수신합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가**를 클릭하고 **기존 항목**을 클릭합니다. 이미지 파일을 찾아 프로젝트에 포함합니다. 이 자습서에서는 이미지 이름을 `image.jpg`로 지정한다고 가정합니다.

1. 이미지를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. **출력 디렉터리로 복사**가 **항상 복사**로 설정되어 있는지 확인합니다.

    ![][1]

1. **Program.cs** 파일 맨 위에 다음 문을 추가합니다.

    ```csharp
    using System.IO;
    ```

1. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` 메서드는 업로드할 파일의 파일 이름 및 스트림 원본을 받은 후 업로드를 저장소로 전달합니다. 콘솔 앱은 파일을 업로드하는 데 걸리는 시간을 표시합니다.

1. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="receive-a-file-upload-notification"></a>파일 업로드 알림 수신

이 섹션에서는 IoT Hub에서 파일 업로드 알림 메시지를 수신하는 .NET 콘솔 앱을 작성합니다.

1. 최신 Visual Studio 솔루션에서 **콘솔 응용 프로그램** 프로젝트 템플릿을 사용하여 Visual C# Windows 프로젝트를 만듭니다. 프로젝트 이름을 **ReadFileUploadNotification**으로 지정합니다.

    ![Visual Studio의 새 프로젝트][2]

1. 솔루션 Explorer에서 **ReadFileUploadNotification** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리...** 를 클릭합니다.

1. **NuGet 패키지 관리자** 창에서 **Microsoft.Azure.Devices**를 검색하고 **설치**를 클릭하고 사용 약관에 동의합니다.

    이 작업은 **ReadFileUploadNotification** 프로젝트에서 [Azure IoT 서비스 SDK NuGet 패키지]에 대한 참조를 다운로드, 설치 및 추가합니다.

1. **Program.cs** 파일 맨 위에 다음 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 [IoT Hub 시작]에서 만든 IoT Hub 연결 문자열로 대체합니다.

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    수신 패턴은 장치 앱으로부터 클라우드-장치 메시지를 받는 데 사용되는 것과 동일합니다.

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ReadFileUploadNotification** 및 **SimulatedDevice**에 대한 **시작** 작업을 선택합니다.

1. **F5**키를 누릅니다. 두 응용 프로그램이 모두 시작됩니다. 한 콘솔 앱에서 완료된 업로드와 다른 콘솔 앱에서 수신한 업로드 알림 메시지가 표시됩니다. [Azure Portal] 또는 Visual Studio 서버 탐색기를 사용하여 Azure Storage 계정에서 업로드한 파일의 현재 상태를 확인할 수 있습니다.

    ![][50]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 장치에서 파일 업로드를 단순화하기 위해 IoT Hub의 파일 업로드 기능을 사용하는 방법을 알아보았습니다. 다음 문서를 사용하여 IoT Hub 기능 및 시나리오를 계속 탐색할 수 있습니다.

* [프로그래밍 방식으로 IoT Hub 만들기][lnk-create-hub]
* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/file-upload-project-csharp1.png

<!-- Links -->

[Azure Portal]: https://portal.azure.com/

[Azure IoT 개발자 센터]: http://azure.microsoft.com/develop/iot

[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure IoT 서비스 SDK NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
