---
title: "리소스 공급자 REST API를 사용하여 Azure IoT Hub 만들기 | Microsoft Docs"
description: "리소스 공급자 REST API를 사용하여 IoT Hub를 만드는 방법입니다."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 52814ee5-bc10-4abe-9eb2-f8973096c2d8
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: d9372f8345257c45ae6b3b915383788f698a0e35
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>리소스 공급자 REST API(.NET)를 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

[IoT Hub 리소스 공급자 REST API][lnk-rest-api]를 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서는 IoT Hub 리소스 공급자 REST API를 사용하여 C# 프로그램에서 IoT Hub를 만드는 방법을 보여 줍니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하는 [Azure Resource Manager와 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 Azure Resource Manager 배포 모델 사용에 대해 설명합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 내에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
* [Azure PowerShell 1.0][lnk-powershell-install] 이상.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio 프로젝트 준비

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕 화면 프로젝트를 만듭니다. 프로젝트 이름을 **CreateIoTHubREST**로 지정합니다.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. NuGet 패키지 관리자에서 **시험판 포함**을 선택하고 **찾아보기** 페이지에서 **Microsoft.Azure.Management.ResourceManager**를 검색합니다. 패키지를 선택하고 **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

4. NuGet 패키지 관리자에서 **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색합니다.  **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

5. Program.cs에서 기존 **using** 문을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Program.cs에서 다음 정적 변수를 추가하여 자리 표시자 값을 바꿉니다. 이 자습서의 앞부분에서 **ApplicationId**, **SubscriptionId**, **TenantId** 및 **암호**를 적어 두었습니다. **리소스 그룹 이름**은 IoT Hub를 만들 때 사용할 리소스 그룹의 이름입니다. 기존 또는 새 리소스 그룹을 사용할 수 있습니다. **IoT Hub 이름**은 **MyIoTHub**와 같이 사용자가 만든 IoT Hub의 이름입니다. IoT hub의 이름은 전역적으로 고유해야 합니다. **배포 이름**은 **Deployment_01**과 같은 배포의 이름입니다.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>리소스 공급자 REST API를 사용하여 IoT Hub 만들기

[IoT Hub 리소스 공급자 REST API][lnk-rest-api]를 사용하여 리소스 그룹에 새 IoT Hub를 만듭니다. 리소스 공급자 REST API를 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. Program.cs에 다음 메서드를 추가합니다.

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. **CreateIoTHub** 메서드에 다음 코드를 추가합니다. 이 코드에서는 헤더에서 인증 토큰을 사용하여 **HttpClient** 개체를 만듭니다.

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. **CreateIoTHub** 메서드에 다음 코드를 추가합니다. 이 코드는 JSON 표현을 만들고 생성하는 IoT Hub를 설명합니다. IoT Hub를 지원하는 현재 위치 목록에 대해서는 [Azure 상태][lnk-status]를 참조하세요.

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. **CreateIoTHub** 메서드에 다음 코드를 추가합니다. 이 코드는 Azure에 REST 요청을 제출합니다. 그런 다음, 코드는 응답을 확인하며 배포 작업의 상태를 모니터링하는 데 사용할 수 있는 URL을 검색합니다.

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. **CreateIoTHub** 메서드의 끝에 다음 코드를 추가합니다. 이 코드는 배포가 완료되기를 대기하는 이전 단계에서 검색된 **asyncStatusUri** 주소를 사용합니다.

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. **CreateIoTHub** 메서드의 끝에 다음 코드를 추가합니다. 이 코드는 사용자가 만들고 콘솔에 출력한 IoT Hub의 키를 검색합니다.

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>응용 프로그램을 완료하고 실행합니다.

이제 응용 프로그램을 빌드하고 실행하기 전에 **CreateIoTHub** 메서드를 호출하여 완료합니다.

1. **Main** 메서드의 끝에 다음 코드를 추가합니다.

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. **빌드**, **솔루션 빌드**를 차례로 클릭합니다. 오류를 수정합니다.

3. **디버그**, **디버깅 시작**을 차례로 클릭하여 응용 프로그램을 실행합니다. 배포를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. 응용 프로그램이 새 IoT Hub에 추가되었는지 확인하려면 [Azure Portal][lnk-azure-portal]을 방문하여 리소스 목록을 확인합니다. 또는 **Get AzureRmResource** PowerShell cmdlet을 사용합니다.

> [!NOTE]
> 이 예제 응용 프로그램은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 작업이 완료되면 [Azure Portal][lnk-azure-portal]을 통해 또는 **Remove-AzureRmResource** PowerShell cmdlet을 사용하여 IoT Hub를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
리소스 공급자 REST API를 사용하여 IoT Hub를 배포했으면 구체적인 내용을 알아볼 차례입니다.

* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
* Azure Resource Manager의 기능에 대해 자세히 알아보려면 [Azure Resource Manager 개요][lnk-azure-rm-overview]를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
