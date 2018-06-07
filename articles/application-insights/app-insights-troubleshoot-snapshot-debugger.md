---
title: Azure Application Insights Snapshot Debugger 문제 해결 가이드 | Microsoft Docs
description: Azure Application Insights Snapshot에 대한 질문과 대답입니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: ''
ms.service: application-insights
ms.workload: ''
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 2b4a5f548578b563c92f8d7ff85457b50b02fbd4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Snapshot Debugger: 문제 해결 가이드

Application Insights Snapshot Debugger를 사용하면 라이브 웹 응용 프로그램에서 디버그 스냅숏을 자동으로 수집할 수 있습니다. 스냅숏은 예외가 발생했을 시점의 소스 코드 및 변수 상태를 보여 줍니다. 이 아티클에서는 디버거가 작동하는 방법을 설명하고 일반적인 문제에 대한 솔루션을 제공합니다.

## <a name="how-does-application-insights-snapshot-debugger-work"></a>AApplication Insights Snapshot Debugger 작동 방식

Application Insights 스냅숏 디버거는 Application Insights 원격 분석 파이프라인(ITelemetryProcessor의 인스턴스)의 일부입니다. 스냅숏 수집기는 코드에서 throw된 예외(AppDomain.FirstChanceException) 및 `TelemetryClient.TrackException`을 통해 Application Insights에 보고된 예외 원격 분석을 모두 모니터링합니다. 프로젝트에 스냅숏 수집기 패키지를 추가하고 원격 분석 파이프라인에서 하나의 이벤트를 감지하면 사용자 지정 데이터에서 'AppInsightsSnapshotCollectorLogs' 및 'SnapshotCollectorEnabled'라는 사용자 지정 이벤트를 보냅니다. 동시에 'SnapshotUploader.exe'(버전 1.2.0 이상) 또는 'MinidumpUploader.exe'(1.2.0 버전 이전)이라는 프로세스를 시작하여 수집된 스냅숏 데이터 파일을 Application Insights에 업로드합니다.  업로더 프로세스가 시작되면 이름이 'UploaderStart'인 사용자 지정 이벤트를 보냅니다. 이후에 스냅숏 수집기가 일반 모니터링 동작을 시작합니다.

스냅숏 수집기가 Application Insights Exception Telemetry를 모니터링할 때는 구성에 정의된 매개 변수(예: ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval)를 사용 하여 스냅숏 수집 시기를 판단합니다. 모든 규칙이 충족되면 수집기가 같은 위치에서 발생한 다음 예외에 대한 스냅숏을 요청합니다. 동시에 이름이 'AppInsightsSnapshotCollectorLogs' 및 'RequestSnapshots'인 Application Insights 사용자 지정 이벤트를 보냅니다. 컴파일러가 'Release' 코드를 최적화하므로 수집된 스냅숏에 로컬 변수가 표시되지 않을 수 있습니다. 스냅숏 수집기는 스냅숏을 요청할 때 예외가 발생한 메서드의 최적화를 해제합니다. 이 시간 동안 사용자 지정 데이터에서 이름이 'AppInsightsSnapshotCollectorLogs' 및 'ProductionBreakpointsDeOptimizeMethod'인 Application Insights 사용자 지정 이벤트를 보냅니다.  다음 예외의 스냅숏을 수집할 때 로컬 변수를 사용할 수 있습니다. 스냅숏을 수집한 후에 코드를 다시 최적화합니다. 

> [!NOTE]
> 최적화 해제를 위해서는 Application Insights 사이트 확장을 설치해야 합니다.

특정 예외에 대해 스냅숏이 요청된 경우 스냅숏 수집기가 응용 프로그램의 예외 처리 파이프라인(AppDomain.FirstChanceException)의 모니터링을 시작합니다. 예외가 다시 발생하면 수집기가 스냅숏을 시작합니다(사용자 지정 데이터에서 이름이 'AppInsightsSnapshotCollectorLogs' 및 'SnapshotStart'인 Application Insights 사용자 지정 이벤트). 그런 다음 실행 중인 프로세스의 섀도 복사본을 만듭니다(페이지 테이블이 복제됨). 일반적으로 이 작업은 10~20밀리초가 소요됩니다. 이 후에는 사용자 지정 데이터에서 이름이 'AppInsightsSnapshotCollectorLogs' 및 'SnapshotStop'인 Application Insights 사용자 지정 이벤트를 보냅니다. 포크 프로세스가 만들어지면 총 페이지 메모리가 실행 중인 응용 프로그램의 페이지 메모리만큼 증가합니다(작업 집합은 훨씬 작음). 응용 프로그램 프로세스가 정상 실행되는 동안 섀도 복사된 프로세스의 메모리는 디스크에 덤프되고 Application Insights에 업로드됩니다. 스냅숏을 업로드한 후 이름이 'UploadSnapshotFinish'인 Application Insights 사용자 지정 이벤트를 보냅니다.

## <a name="is-the-snapshot-collector-working-properly"></a>스냅숏 수집기가 제대로 작동하나요?

### <a name="how-to-find-snapshot-collector-logs"></a>스냅숏 수집기 로그를 확인하는 방법
[Snapshot Collector NuGet 패키지](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector)가 버전 1.1.0 이상인 경우 스냅숏 수집기 로그는 Application Insight 계정으로 전달됩니다. *ProvideAnonymousTelemetry*가 False로 설정되지 않았는지 확인합니다(기본값이 true).

* Azure Portal에서 Application Insights 리소스로 이동합니다.
* 개요 섹션에서 *검색*을 클릭합니다.
* 검색 상자에 다음 문자열을 입력합니다.
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* 필요한 경우 *시간 범위*를 변경합니다.

![스냅숏 수집기 로그 검색 스크린샷](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>스냅숏 수집기 로그 확인
스냅숏 수집기 로그를 검색할 때 대상 시간 범위에 'UploadSnapshotFinish' 이벤트가 있어야 합니다. 스냅숏을 열기 위한 '스냅숏 디버그 열기' 단추가 여전히 표시되지 않으면 Application Insights 계측 키를 사용하여 snapshothelp@microsoft.com으로 이메일을 보내세요.

![스냅숏 수집기 로그 확인 스크린샷](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>열 스냅숏이 없습니다.
다음 단계를 통해 문제가 해결되지 않으면 Application Insights 계측 키를 사용하여 snapshothelp@microsoft.com으로 이메일을 보내세요.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>1단계: 응용 프로그램이 원격 분석 데이터와 예외 데이터를 Application Insights에 보내고 있는지 확인
Application Insights 리소스로 이동하여 응용 프로그램에서 보낸 데이터가 있는지 확인합니다.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>2단계: 응용 프로그램의 Application Insights Telemetry 파이프라인에 스냅숏 수집기가 제대로 추가되었는지 확인
'스냅숏 수집기 로그를 찾는 방법'에서 로그를 찾을 수 있는 경우 스냅숏 수집기가 프로젝트에 정확하게 추가되었으므로 이 단계를 무시해도 됩니다.

스냅숏 수집기 로그가 없으면 다음을 확인합니다.
* 클래식 ASP.NET 응용 프로그램의 경우 *ApplicationInsights.config* 파일에서 *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* 행을 확인합니다.

* ASP.NET Core 응용 프로그램의 경우 *ITelemetryProcessorFactory*가*SnapshotCollectorTelemetryProcessor*와 함께 *IServiceCollection* 서비스에 추가되었는지 확인합니다.

* 게시된 응용 프로그램에서 올바른 계측 키를 사용하는 있는지도 확인합니다.

* 스냅숏 수집기가 하나의 응용 프로그램 내에서 여러 계측 키를 지원하지 않는 경우 관측한 최초 예외의 계측 키에 스냅숏을 보냅니다.

* 코드에서 수동으로 *InstrmentationKey*를 설정한 경우 *ApplicationInsights.config*에서 *InstrumentationKey* 요소를 업데이트합니다.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>3단계: 미니덤프 업로더가 시작되었는지 확인
스냅숏 수집기 로그에서 *UploaderStart*를 검색합니다(검색 텍스트 상자에 UploaderStart 입력). 스냅숏 수집기가 최초 예외를 모니터링했다면 이벤트가 있어야 합니다. 이 이벤트가 없다면 다른 로그에서 자세한 내용을 확인합니다. 이 문제의 한 가지 가능한 해결 방법은 응용 프로그램을 다시 시작하는 것입니다.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>4단계: 스냅숏 수집기가 스냅숏을 수집하려는 의지를 보이는지 확인
스냅숏 수집기 로그에서 *RequestSnapshots*를 검색합니다(검색 텍스트 상자에 ```RequestSnapshots``` 입력).  항목이 없으면 구성을 확인합니다. 예를 들어 *ThresholdForSnapshotting*은 스냅숏 수집을 시작하기 전에 발생할 수 있는 특정 예외의 수를 나타냅니다.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>5단계: 메모리 보호로 인해 스냅숏이 비활성화되지 않았는지 확인
응용 프로그램 성능을 보호하기 위해 스냅숏은 메모리 버퍼가 양호할 때만 수집됩니다. 스냅숏 수집기 로그에서 'CannotSnapshotDueToMemoryUsage'를 검색합니다. 이벤트의 사용자 지정 데이터에 상세 원인이 있을 것입니다. 응용 프로그램이 Azure Web App을 실행 중인 경우 제한이 엄격할 수 있습니다. Azure Web App은 특정 메모리 규칙에 부합할 때만 앱을 다시 시작합니다. 컴퓨터에 메모리를 추가하도록 서비스 계획을 확장하여 이 문제를 해결할 수 있습니다.

### <a name="step-6-make-sure-snapshots-were-captured"></a>6단계: 스냅숏을 캡처했는지 확인
스냅숏 수집기 로그에서 ```RequestSnapshots```를 검색합니다.  표시되지 않는 경우 구성을 확인합니다. 예를 들어 *ThresholdForSnapshotting*은 스냅숏을 수집하기 전에 특정 예외의 수를 나타냅니다.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>7단계: 스냅숏이 올바르게 업로드되는지 확인
스냅숏 수집기 로그에서 ```UploadSnapshotFinish```를 검색합니다.  없는 경우 Application Insights의 계측 키를 사용하여 snapshothelp@microsoft.com으로 이메일을 보내세요. 이 이벤트가 있는 경우 로그 중 하나를 열고 사용자 정의 데이터의 'SnapshotId' 값을 복사합니다. 이 값을 검색합니다. 이렇게 하면 스냅숏에 해당하는 예외를 찾을 수 잇습니다. 그런 다음 예외를 클릭하고 디버그 스냅숏을 엽니다. 해당하는 예외가 없으면 볼륨이 커서 예외 원격 분석이 샘플링될 수 있습니다. 다른 snapshotId를 시도해 봅니다.

![snapshotId 찾기 스크린샷](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![예외 열기 스크린샷](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![스냅숏 디버그 열기 스크린샷](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>스냅숏 보기 로컬 변수가 완전하지 않음

일부 로컬 변수가 없습니다. 응용 프로그램이 릴리스 코드를 실행 중인 경우 컴파일러가 일부 변수를 최적화합니다. 예: 

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

다른 경우라면 버그일 수 있습니다. 코드 스니펫과 함께 Application Insights의 계측 키를 사용하여 snapshothelp@microsoft.com으로 이메일을 보내세요.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>스냅숏 보기: 로컬 변수 또는 인수 값을 가져올 수 없음
[Application Insights 사이트 확장](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/)이 설치되었는지 확인합니다. 문제가 계속되면 Application Insights의 계측 키를 사용하여 snapshothelp@microsoft.com으로 이메일을 보내세요.
