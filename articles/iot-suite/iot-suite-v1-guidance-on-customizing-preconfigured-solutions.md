---
title: "미리 구성된 솔루션 사용자 지정 | Microsoft Docs"
description: "미리 구성된 Azure IoT Suite 솔루션을 사용자 지정하는 방법에 대한 지침을 제공합니다."
services: 
suite: iot-suite
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: ba965b9bc23b96adb2b1b7c9306cb7f508f820bf
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2017
---
# <a name="customize-a-preconfigured-solution"></a>미리 구성된 솔루션 사용자 지정

Azure IoT Suite와 함께 제공되는 미리 구성된 솔루션은 제품 내에서 함께 협력하는 서비스를 보여주어 종단간 솔루션을 제공합니다. 이 시작 지점에서 특정 시나리오에 대한 솔루션을 확장하고 사용자 지정할 수 있는 다양한 위치가 있습니다. 다음 섹션에서는 이러한 일반적인 사용자 지정 위치를 설명합니다.

## <a name="find-the-source-code"></a>소스 코드 찾기

미리 구성된 솔루션에 대한 소스 코드는 다음 리포지토리의 GitHub에서 사용할 수 있습니다.

* 원격 모니터링: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* 예측 유지 관리: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)
* 연결된 팩터리: [https://github.com/Azure/azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory)

미리 구성된 솔루션에 대한 소스 코드는 패턴과 Azure IoT Suite를 사용하여 IoT 솔루션의 종단 간 기능을 구현하는 데 사용하는 작업 방식과 패턴을 보여 주기 위해 제공됩니다. GitHub 리포지토리에서 솔루션을 빌드 및 배포하는 방법에 대한 자세한 내용을 확인할 수 있습니다.

## <a name="change-the-preconfigured-rules"></a>미리 구성된 규칙 변경

원격 모니터링 솔루션은 솔루션에서 장치 정보, 원격 분석 및 규칙 논리를 다루는 세 개의 [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) 작업을 포함합니다.

세 개의 Stream Analytics 작업 및 해당 구문은 [원격 모니터링 미리 구성된 솔루션 연습](iot-suite-v1-remote-monitoring-sample-walkthrough.md)에 자세히 설명되어 있습니다. 

논리를 변경하거나 시나리오에 관련된 논리를 추가하도록 이러한 작업을 직접 편집할 수 있습니다. 다음과 같이 Stream Analytics 작업을 찾을 수 있습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. IoT 솔루션과 이름이 동일한 새 리소스 그룹으로 이동합니다. 
3. 수정하려는 Azure Stream Analytics 작업을 선택합니다. 
4. 명령 집합에서 **중지**를 선택하여 작업을 중지합니다. 
5. 입력, 쿼리 및 출력을 편집합니다.
   
    수정을 간편하게 수행하려면 **">"** 대신 **"<"**를 사용하도록 **규칙** 작업에 대한 쿼리를 변경합니다. 규칙을 편집할 때 솔루션 포털에는 계속 **">"**가 표시되지만 기본 작업이 변경되어 동작이 반대로 수행됩니다.
6. 작업 시작

> [!NOTE]
> 원격 모니터링 대시보드는 특정 데이터에 따라 달라지므로 작업 변경은 대시보드 실패를 일으킬 수 있습니다.

## <a name="add-your-own-rules"></a>사용자 고유 규칙 추가

미리 구성된 Azure Stream Analytics 작업 변경 외에도 Azure Portal을 사용하여 새 작업을 추가하거나 기존 작업에 새 쿼리를 추가할 수 있습니다.

## <a name="customize-devices"></a>장치 사용자 지정

가장 일반적인 확장 작업 중 하나는 시나리오와 관련된 장치를 사용하는 것입니다. 장치를 사용하는 여러 방법이 있습니다. 이 방법은 해당 시나리오에 맞게 시뮬레이션된 장치를 변경하거나 [IoT 장치 SDK][IoT Device SDK] 를 사용하여 솔루션에 대한 실제 장치에 연결하는 것이 포함됩니다.

장치를 추가하기 위한 단계별 가이드는 [Iot Suite 연결 장치](iot-suite-v1-connecting-devices.md) 문서 및 [원격 모니터링 C SDK 샘플](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring)을 참조하세요. 이 샘플은 미리 구성된 원격 모니터링 솔루션을 사용하도록 설계되었습니다.

### <a name="create-your-own-simulated-device"></a>고유한 시뮬레이션된 장치 만들기

.NET 시뮬레이터는 [원격 모니터링 솔루션 소스 코드](https://github.com/Azure/azure-iot-remote-monitoring)에 포함되어 있습니다. 이 시뮬레이터는 솔루션의 일부로 프로비전되었으며 다른 메타데이터, 원격 분석을 보내거나 다른 명령 및 메서드에 응답하도록 변경될 수 있습니다.

미리 구성된 솔루션 시뮬레이터의 원격 모니터링에서 미리 구성된 시뮬레이터는 온도 및 습도 원격 분석을 내보내는 냉각 장치입니다. GitHub 리포지토리를 분기한 경우 [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) 프로젝트에서 시뮬레이터를 수정할 수 있습니다.

### <a name="available-locations-for-simulated-devices"></a>시뮬레이션된 장치에 사용 가능한 위치

위치의 기본 집합은 시애틀/레드먼드, 워싱턴, 미국입니다. [SampleDeviceFactory.cs][lnk-sample-device-factory]에서 이러한 위치를 변경할 수 있습니다.

### <a name="add-a-desired-property-update-handler-to-the-simulator"></a>시뮬레이터에 desired 속성 업데이트 처리기 추가

솔루션 포털에서 장치의 desired 속성에 대한 값을 설정할 수 있습니다. 장치가 desired 속성 값을 검색하는 경우 속성 변경 요청을 처리하는 것이 장치의 책임입니다. desired 속성을 통해 속성 값 변경에 대한 지원을 추가하려면 시뮬레이터에 처리기를 추가해야 합니다.

시뮬레이터는 솔루션 포털에서 desired 값을 설정하여 업데이트할 수 있는 **SetPointTemp** 및 **TelemetryInterval** 속성에 대한 처리기를 포함합니다.

다음 예제는 **CoolerDevice** 클래스에서 **SetPointTemp** desired 속성에 대한 처리기를 보여 줍니다.

```csharp
protected async Task OnSetPointTempUpdate(object value)
{
    var telemetry = _telemetryController as ITelemetryWithSetPointTemperature;
    telemetry.SetPointTemperature = Convert.ToDouble(value);

    await SetReportedPropertyAsync(SetPointTempPropertyName, telemetry.SetPointTemperature);
}
```

이 메서드는 원격 분석 지점 온도를 업데이트한 다음 reported 속성을 설정하여 변경 내용을 IoT Hub에 다시 보고합니다.

앞의 예제에서 패턴을 따라 사용자 고유의 속성에 대한 사용자 고유의 처리기를 추가할 수 있습니다.

또한 **CoolerDevice** 생성자에서 다음 예제와 같이 desired 속성을 처리기에 바인딩해야 합니다.

```csharp
_desiredPropertyUpdateHandlers.Add(SetPointTempPropertyName, OnSetPointTempUpdate);
```

**SetPointTempPropertyName**은 "Config.SetPointTemp"로 정의되는 상수입니다.

### <a name="add-support-for-a-new-method-to-the-simulator"></a>시뮬레이터에 새 메서드에 대한 지원 추가

시뮬레이터를 사용자 지정하여 새 [메서드(직접 메서드)][lnk-direct-methods]에 대한 지원을 추가할 수 있습니다. 필요한 두 가지 주요 단계는 다음과 같습니다.

- 시뮬레이터는 메서드의 세부 정보를 사용하여 미리 구성된 솔루션에서 IoT Hub에 알려야 합니다.
- 시뮬레이터는 솔루션 탐색기에서 또는 작업을 통해 **장치 세부 정보** 패널에서 호출할 때 메서드 호출을 처리하는 코드를 포함해야 합니다.

미리 구성된 원격 모니터링 솔루션은 *reported 속성*을 사용하여 IoT Hub에 지원되는 메서드의 세부 정보를 보냅니다. 솔루션 백 엔드는 메서드 호출 기록과 함께 각 장치에서 지원하는 모든 메서드 목록을 유지 관리합니다. 장치에 대한 이 정보를 볼 수 있으며 솔루션 포털에서 메서드를 호출할 수 있습니다.

장치에서 메서드를 지원하는 것을 IoT Hub에 알리기 위해 장치는 reported 속성의 **SupportedMethods** 노드에 메서드의 세부 정보를 추가해야 합니다.

```json
"SupportedMethods": {
  "<method signature>": "<method description>",
  "<method signature>": "<method description>"
}
```

메서드 서명은 다음 형식을 가집니다. `<method name>--<parameter #0 name>-<parameter #1 type>-...-<parameter #n name>-<parameter #n type>` 예를 들어 **FwPackageURI**라는 문자열 매개 변수를 예상하는 **InitiateFirmwareUpdate** 메서드를 지정하려면 다음 메서드 서명을 사용합니다.

```json
InitiateFirmwareUpate--FwPackageURI-string: "description of method"
```

지원되는 매개 변수 형식의 목록은 인프라 프로젝트에서 **CommandTypes** 클래스를 참조하세요.

메서드를 삭제하려면 reported 속성에서 메서드 서명을 `null`로 설정합니다.

> [!NOTE]
> 솔루션 백 엔드는 장치에서 *장치 정보* 메시지를 받을 때 지원되는 메서드에 대한 정보만을 업데이트합니다.

Common 프로젝트에서 **SampleDeviceFactory** 클래스의 다음 코드 예제는 장치에서 전송된 reported 속성에서 **SupportedMethods** 목록에 메서드를 추가하는 방법을 보여 줍니다.

```csharp
device.Commands.Add(new Command(
    "InitiateFirmwareUpdate",
    DeliveryType.Method,
    "Updates device Firmware. Use parameter 'FwPackageUri' to specifiy the URI of the firmware file, e.g. https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin",
    new[] { new Parameter("FwPackageUri", "string") }
));
```

이 코드 조각은 솔루션 포털에 표시할 텍스트를 포함하는 **InitiateFirmwareUpdate** 메서드의 세부 정보 및 필수 메서드 매개 변수의 세부 정보를 추가합니다.

시뮬레이터는 시뮬레이터가 시작될 때 지원되는 메서드의 목록을 포함한 reported 속성을 IoT Hub에 보냅니다.

지원하는 각 메서드에 대한 시뮬레이터 코드에 처리기를 추가합니다. Simulator.WebJob 프로젝트의 **CoolerDevice** 클래스에서 기존 처리기를 볼 수 있습니다. 다음 예제는 **InitiateFirmwareUpdate** 메서드에 대한 처리기를 보여 줍니다.

```csharp
public async Task<MethodResponse> OnInitiateFirmwareUpdate(MethodRequest methodRequest, object userContext)
{
    if (_deviceManagementTask != null && !_deviceManagementTask.IsCompleted)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "Device is busy"
        }, 409));
    }

    try
    {
        var operation = new FirmwareUpdate(methodRequest);
        _deviceManagementTask = operation.Run(Transport).ContinueWith(async task =>
        {
            // after firmware completed, we reset telemetry
            var telemetry = _telemetryController as ITelemetryWithTemperatureMeanValue;
            if (telemetry != null)
            {
                telemetry.TemperatureMeanValue = 34.5;
            }

            await UpdateReportedTemperatureMeanValue();
        });

        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = "FirmwareUpdate accepted",
            Uri = operation.Uri
        }));
    }
    catch (Exception ex)
    {
        return await Task.FromResult(BuildMethodRespose(new
        {
            Message = ex.Message
        }, 400));
    }
}
```

메서드 처리기 이름은 뒤에 메서드 이름이 오는 `On`으로 시작해야 합니다. **methodRequest** 매개 변수는 솔루션 백 엔드에서 메서드 호출을 통해 전달된 모든 매개 변수를 포함합니다. 반환 값은 **Task&lt;MethodResponse&gt;**의 형식이어야 합니다. **BuildMethodResponse** 유틸리티 메서드를 사용하면 반환 값을 만들 수 있습니다.

메서드 처리기 내에서 다음 작업을 할 수 있습니다.

- 비동기 작업을 시작합니다.
- IoT Hub의 *장치 쌍*에서 desired 속성을 검색합니다.
- **CoolerDevice** 클래스의 **SetReportedPropertyAsync** 메서드를 사용하여 단일 reported 속성을 업데이트합니다.
- **TwinCollection** 인스턴스를 만들고 **Transport.UpdateReportedPropertiesAsync** 메서드를 호출하여 여러 reported 속성을 업데이트합니다.

앞의 펌웨어 업데이트 예제는 다음 단계를 수행합니다.

- 장치가 펌웨어 업데이트 요청을 수락할 수 있는지 확인합니다.
- 비동기적으로 펌웨어 업데이트 작업을 시작하고 작업이 완료되면 원격 분석을 다시 설정합니다.
- "FirmwareUpdate 수락" 메시지를 즉시 반환하여 장치에서 요청이 수락되었다는 것을 나타냅니다.

### <a name="build-and-use-your-own-physical-device"></a>고유한 (물리적) 장치 빌드 및 사용

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) 는 IoT 솔루션으로 다양한 장치 유형(언어 및 운영 체제)를 연결하기 위한 라이브러리를 제공합니다.

## <a name="modify-dashboard-limits"></a>대시보드 제한 수정

### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>대시보드 드롭다운에 표시되는 장치 수

기본값은 200입니다. [DashboardController.cs][lnk-dashboard-controller]에서 이 수를 변경할 수 있습니다.

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Bing 지도 컨트롤에 표시할 핀 수

기본값은 200입니다. [TelemetryApiController.cs][lnk-telemetry-api-controller-01]에서 이 수를 변경할 수 있습니다.

### <a name="time-period-of-telemetry-graph"></a>원격 분석 그래프의 시간 간격

기본값은 10분입니다. [TelmetryApiController.cs][lnk-telemetry-api-controller-02]에서 이 값을 변경할 수 있습니다.

## <a name="feedback"></a>사용자 의견

이 문서에서 포함했으면 하는 사용자 지정이 있나요? [사용자 의견](https://feedback.azure.com/forums/321918-azure-iot)에 기능 제안을 추가하거나 이 문서에 대한 의견을 입력해 주시기 바랍니다. 

## <a name="next-steps"></a>다음 단계

미리 구성된 솔루션을 사용자 지정하기 위한 옵션에 대한 자세한 내용은 다음을 참조하세요.

* [미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 논리 앱 연결][lnk-logicapp]
* [원격 모니터링 사전 구성 솔루션으로 동적 원격 분석 사용][lnk-dynamic]
* [미리 구성된 원격 모니터링 솔루션의 장치 정보 메타데이터][lnk-devinfo]
* [연결된 공장 솔루션이 OPC UA 서버의 데이터를 표시하는 방식 사용자 지정][lnk-cf-customize]

[lnk-logicapp]: iot-suite-v1-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-v1-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-cf-customize]: iot-suite-connected-factory-customize.md