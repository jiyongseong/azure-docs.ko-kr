---
title: "Azure IoT Suite에 사용자 지정 규칙 만들기 | Microsoft Docs"
description: "미리 구성된 IoT Suite 솔루션에서 사용자 지정 규칙을 만드는 방법"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 9bf2a13035de141766fd935966ce18459dccdaab
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>미리 구성된 원격 모니터링 솔루션에서 사용자 지정 규칙 만들기

## <a name="introduction"></a>소개

미리 구성된 솔루션에서 [장치에 대한 원격 분석 값이 특정 임계값에 도달할 때 트리거되는 규칙][lnk-builtin-rule]을 구성할 수 있습니다. [원격 모니터링 사전 구성 솔루션으로 동적 원격 분석 사용][lnk-dynamic-telemetry]에서는 *ExternalTemperature*와 같은 사용자 지정 원격 분석 값을 솔루션에 추가하는 방법을 설명합니다. 이 문서에서는 솔루션에 동적 원격 분석 형식에 대한 사용자 지정 규칙을 만드는 방법을 보여 줍니다.

이 자습서에서는 간단한 Node.js 시뮬레이트 장치를 사용하여 미리 구성된 솔루션 백 엔드로 보낼 동적 원격 분석을 생성합니다. 그런 후 **RemoteMonitoring** Visual Studio 솔루션에서 사용자 지정 규칙을 추가하고 사용자 지정된 이 백 엔드를 Azure 구독에 배포합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 구독. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk_free_trial]을 참조하세요.
* 시뮬레이트된 장치를 만들기 위한 [Node.js][lnk-node] 버전 0.12.x 이상
* 새 규칙으로 미리 구성된 솔루션 백 엔드를 수정하기 위한 Visual Studio 2015 또는 Visual Studio 2017

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

배포를 위해 선택한 솔루션 이름을 기록해 둡니다. 이 솔루션은 이 자습서의 뒷부분에서 필요합니다.

[!INCLUDE [iot-suite-v1-send-external-temperature](../../includes/iot-suite-v1-send-external-temperature.md)]

**ExternalTemperature** 원격 분석을 미리 구성된 솔루션으로 전송하는지 확인한 다음 Node.js 콘솔 앱을 중지할 수 있습니다. 솔루션에 사용자 지정 규칙을 추가한 후에 이 Node.js 콘솔 앱을 다시 실행하게 되므로 콘솔 창을 열어 둡니다.

## <a name="rule-storage-locations"></a>규칙 저장소 위치

규칙에 대한 정보는 다음 두 위치에 유지됩니다.

* **DeviceRulesNormalizedTable** 테이블 - 이 테이블은 솔루션 포털에서 정의된 규칙에 대한 정규화된 참조를 저장합니다. 솔루션 포털에 장치 규칙이 표시되면 이 테이블에서 규칙 정의를 쿼리합니다.
* **DeviceRules** blob - 이 blob은 등록된 모든 장치에 대해 정의된 모든 규칙을 저장하며 Azure Stream Analytics 작업에 대한 참조 입력으로 정의됩니다.
 
기존 규칙을 업데이트하거나 솔루션 포털에서 새 규칙을 정의하는 경우 변경 내용을 반영하도록 테이블 및 blob이 모두 업데이트됩니다. 포털에 표시된 규칙 정의는 테이블 저장소에서 가져오며, Stream Analytics 작업에서 참조되는 규칙 정의는 blob에서 가져옵니다. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>RemoteMonitoring Visual Studio 솔루션 업데이트

다음 단계에서는 시뮬레이트된 장치에서 전송된 **ExternalTemperature** 원격 분석을 사용하는 새 규칙을 포함하도록 RemoteMonitoring Visual Studio 솔루션을 수정하는 방법을 보여 줍니다.

1. **azure-iot-remote-monitoring** 리포지토리를 로컬 컴퓨터의 적절한 위치로 복제하지 않은 경우 다음 Git 명령을 사용하여 지금 수행합니다.

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. Visual Studio에서 **azure-iot-remote-monitoring** 리포지토리의 로컬 복사본에서 RemoteMonitoring.sln 파일을 엽니다.

3. Infrastructure\Models\DeviceRuleBlobEntity.cs 파일을 열고 다음과 같이 **ExternalTemperature** 속성을 추가합니다.

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. 같은 파일에 다음과 같이 **ExternalTemperatureRuleOutput** 속성을 추가합니다.

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Infrastructure\Models\DeviceRuleDataFields.cs 파일을 열고, 기존 **Humidity** 속성 뒤에 다음 **ExternalTemperature** 속성을 추가합니다.

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. 동일한 파일에서 다음과 같이 **ExternalTemperature**를 포함하도록 **_availableDataFields** 메서드를 업데이트합니다.

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Infrastructure\Repository\DeviceRulesRepository.cs 파일을 열고 다음과 같이 **BuildBlobEntityListFromTableRows** 메서드를 수정합니다.

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>솔루션을 다시 빌드하고 다시 배포합니다.

이제 업데이트된 솔루션을 Azure 구독에 배포할 수 있습니다.

1. 관리자 권한 명령 프롬프트를 열고 azure-iot-remote-monitoring 리포지토리의 로컬 복사본 루트로 이동합니다.

2. 업데이트된 솔루션을 배포하려면 **{deployment name}**을 이전에 적어둔 미리 구성된 솔루션 배포의 이름으로 바꾸어 다음 명령을 실행합니다.

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Stream Analytics 작업 업데이트

배포가 완료되면 새 규칙 정의를 사용하도록 Stream Analytics 작업을 업데이트할 수 있습니다.

1. Azure Portal에서 미리 구성된 솔루션 리소스를 포함하는 리소스 그룹으로 이동합니다. 이 리소스 그룹은 배포하는 동안 솔루션에 대해 지정한 동일한 이름을 갖습니다.

2. {배포 이름}-규칙 Stream Analytics 작업으로 이동합니다. 

3. **중지**를 클릭하여 Stream Analytics 작업 실행을 중지합니다. (쿼리를 편집하려면 스트리밍 작업이 중지될 때까지 기다려야 합니다.)

4. **쿼리**를 클릭합니다. **ExternalTemperature**에 대한 **SELECT** 문을 포함하도록 쿼리를 편집합니다. 다음 샘플에서는 새 **SELECT** 문을 사용하는 완전한 쿼리를 보여 줍니다.

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. **저장**을 클릭하여 업데이트된 규칙 쿼리를 변경합니다.

6. **시작**을 클릭하여 Stream Analytics 작업 실행을 다시 시작합니다.

## <a name="add-your-new-rule-in-the-dashboard"></a>대시보드에서 새 규칙 추가

이제 솔루션 대시보드의 장치에 **ExternalTemperature** 규칙을 추가할 수 있습니다.

1. 솔루션 포털로 이동합니다.

2. **장치** 패널로 이동합니다.

3. **ExternalTemperature** 원격 분석을 전송하는 사용자 지정 장치를 찾은 후 **장치 세부 정보** 패널에서 **규칙 추가**를 클릭합니다.

4. **데이터 필드**에서 **ExternalTemperature**를 선택합니다.

5. **임계값**을 56으로 설정합니다. 그런 후 **규칙 저장 및 보기**를 클릭합니다.

6. 대시보드로 돌아가 경보 기록을 확인합니다.

7. 열어 둔 콘솔 창에서 Node.js 콘솔 앱을 시작하여 **ExternalTemperature** 원격 분석 데이터 전송을 시작합니다.

8. **경보 기록** 테이블에는 새 규칙이 트리거될 때 새 경보가 표시됩니다.
 
## <a name="additional-information"></a>추가 정보

연산자  **>** 을 변경하는 것은 좀 더 복잡하여 이 자습서에서 다루지 않습니다. 원하는 연산자를 사용하도록 Stream Analytics 작업을 변경할 수는 있으나 솔루션 포털에서 해당 연산자를 적용하는 것이 더 복잡한 작업입니다. 

## <a name="next-steps"></a>다음 단계
사용자 지정 규칙을 만드는 방법을 살펴보았으므로 이제 미리 구성된 솔루션에 대해 자세히 알아볼 수 있습니다.

- [미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 논리 앱 연결][lnk-logic-app]
- [미리 구성된 원격 모니터링 솔루션의 장치 정보 메타데이터][lnk-devinfo].

[lnk-devinfo]: iot-suite-v1-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-v1-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-v1-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-v1-logic-apps-tutorial.md