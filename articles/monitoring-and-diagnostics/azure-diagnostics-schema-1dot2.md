---
title: Azure 진단 1.2 구성 스키마 | Microsoft Docs
description: Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric 또는 Cloud Services에서 Azure SDK 2.5를 사용하는 경우에만 해당됩니다.
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: 1e9cc6d0950945df8c4fba74d8e1f6196be224f0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Azure 진단 1.2 구성 스키마
> [!NOTE]
> Azure 진단은 Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric 및 Cloud Services에서 성능 카운터 및 기타 통계를 수집하는 데 사용되는 구성 요소입니다.  이 페이지는 이러한 서비스 중 하나를 사용하는 경우에만 해당됩니다.
>

Azure 진단은 Azure Monitor, Application Insights 및 Log Analytics와 같은 기타 Microsoft 진단 제품과 함께 사용됩니다.

이 스키마는 진단 모니터가 시작될 때 진단 구성 설정을 초기화하는 데 사용할 수 있는 값을 정의합니다.  


 다음 PowerShell 명령을 실행하여 공용 구성 파일 스키마 정의를 다운로드합니다.  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Azure 진단에 대한 자세한 내용은 [Azure Cloud Services에서 진단을 사용하도록 설정](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/)을 참조하세요.  

## <a name="example-of-the-diagnostics-configuration-file"></a>진단 구성 파일 예제  
 다음 예제에서는 일반적인 진단 구성 파일을 보여 줍니다.  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>진단 구성 네임스페이스  
 진단 구성 파일에 대한 XML 네임스페이스는 다음과 같습니다.  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig 요소  
 진단 구성 파일의 최상위 요소입니다. 다음 표에서는 구성 파일의 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**WadCfg**|필수 사항입니다. 수집할 원격 분석 데이터에 대한 구성 설정입니다.|  
|**StorageAccount**|데이터를 저장할 Azure Storage 계정의 이름입니다. 이는 Set-AzureServiceDiagnosticsExtension cmdlet을 실행할 때 매개 변수로 지정할 수도 있습니다.|  
|**LocalResourceDirectory**|모니터링 에이전트가 이벤트 데이터를 저장하는 데 사용할 가상 컴퓨터의 디렉터리입니다. 설정되어 있지 않은 경우 기본 디렉터리가 사용됩니다.<br /><br /> 작업자/웹 역할의 경우: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Virtual Machine의 경우: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 필수 특성은 다음과 같습니다.<br /><br /> -                      **경로** - Azure 진단에서 사용되는 시스템의 디렉터리입니다.<br /><br /> -                      **expandEnvironment** - 환경 변수가 경로 이름에서 확장되는지 여부를 제어합니다.|  

## <a name="wadcfg-element"></a>WadCFG 요소  
수집할 원격 분석 데이터에 대한 구성 설정을 정의합니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|필수 사항입니다. 선택적 특성은 다음과 같습니다.<br /><br /> -                     **overallQuotaInMB** - Azure 진단으로 수집된 진단 데이터의 다양한 형식에서 사용될 수 있는 로컬 디스크 공간의 최대 크기입니다. 기본 설정은 5120MB입니다.<br /><br /> -                     **useProxyServer** - IE 설정에서 설정한 대로 프록시 서버 설정을 사용하도록 Azure 진단을 구성합니다.|  
|**CrashDumps**|크래시 덤프의 수집을 사용하도록 설정합니다. 선택적 특성은 다음과 같습니다.<br /><br /> -                     **containerName** - 크래시 덤프를 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.<br /><br /> -                     **crashDumpType** - 최소 또는 전체 크래시 덤프를 수집하도록 Azure 진단을 구성합니다.<br /><br /> -                     **directoryQuotaPercentage**- VM에서 크래시 덤프에 대해 예약될 **overallQuotaInMB**의 비율을 구성합니다.|  
|**DiagnosticInfrastructureLogs**|Azure 진단에 의해 생성된 로그의 컬렉션을 사용하도록 설정합니다. 진단 인프라 로그는 진단 시스템 자체의 문제 해결에 유용합니다. 선택적 특성은 다음과 같습니다.<br /><br /> -                     **scheduledTransferLogLevelFilter** - 수집된 로그의 최소 심각도 수준을 구성합니다.<br /><br /> -                     **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  
|**Directories**|디렉터리, IIS 실패한 액세스 요청 로그 및/또는 IIS 로그의 콘텐츠 수집을 활성화합니다. 선택적 특성:<br /><br /> **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  
|**EtwProviders**|EventSource의 ETW 이벤트 및/또는 공급자를 기반으로 하는 ETW 매니페스트의 컬렉션을 구성합니다.|  
|**metrics**|이 요소는 빠른 쿼리를 위해 최적화된 성능 카운터 테이블을 생성할 수 있도록 합니다. **PerformanceCounters** 요소에 정의되어 있는 각 성능 카운터는 성능 카운터 테이블에 추가된 메트릭 테이블에 저장되어 있습니다. 필수 특성:<br /><br /> **resourceId** - 이는 Azure 진단을 배포하는 Virtual Machine의 리소스 ID입니다. [Azure Portal](https://portal.azure.com)에서 **resourceID**를 가져옵니다. **찾아보기** -> **리소스 그룹** -> **<이름\>**을 선택합니다. **속성** 타일을 클릭하고 **ID** 필드입에서 값을 복사합니다.|  
|**PerformanceCounters**|성능 카운터의 컬렉션을 활성화합니다. 선택적 특성:<br /><br /> **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  
|**WindowsEventLog**|Windows 이벤트 로그의 컬렉션을 활성화합니다. 선택적 특성:<br /><br /> **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  

## <a name="crashdumps-element"></a>CrashDumps 요소  
 크래시 덤프의 수집을 사용하도록 설정합니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|필수 사항입니다. 필수 특성:<br /><br /> **processName** - Azure 진단에서 크래시 덤프를 수집하도록 할 프로세스의 이름입니다.|  
|**crashDumpType**|최소 또는 전체 크래시 덤프를 수집하도록 Azure 진단을 구성합니다.|  
|**directoryQuotaPercentage**|VM에서 크래시 덤프에 대해 예약될 **overallQuotaInMB**의 비율을 구성합니다.|  

## <a name="directories-element"></a>Directories 요소  
 디렉터리, IIS 실패한 액세스 요청 로그 및/또는 IIS 로그의 콘텐츠 수집을 활성화합니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**DataSources**|모니터링할 디렉터리의 목록입니다.|  
|**FailedRequestLogs**|이 요소를 구성에 포함하면 IIS 사이트 또는 응용 프로그램에 실패 한 요청에 대한 로그 컬렉션이 활성화합니다. 또한 **Web.config**의 **system.WebServer**에 있는 추적 옵션도 사용하도록 설정해야 합니다.|  
|**IISLogs**|이 요소를 구성에 포함하면 IIS 로그의 컬렉션이 활성화됩니다.<br /><br /> **containerName** - IIS를 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|  

## <a name="datasources-element"></a>DataSources 요소  
 모니터링할 디렉터리의 목록입니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**DirectoryConfiguration**|필수 사항입니다. 필수 특성:<br /><br /> **containerName** - 로그 파일을 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 요소  
 **DirectoryConfiguration** 은 **Absolute** 또는 **LocalResource** 요소 중 하나를 포함하되, 모두 포함할 수는 없습니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**Absolute**|모니터링할 디렉터리의 절대 경로입니다. 다음과 같은 특성이 필요합니다.<br /><br /> -                     **Path** - 모니터링할 디렉터리의 절대 경로입니다.<br /><br /> -                      **expandEnvironment** - 경로의 환경 변수가 확장되어 있는지 여부를 구성합니다.|  
|**LocalResource**|모니터링할 로컬 리소스의 상대 경로입니다. 필수 특성은 다음과 같습니다.<br /><br /> -                     **Name** - 모니터링할 디렉터리를 포함하는 로컬 리소스<br /><br /> -                     **relativePath** -모니터링할 디렉터리를 포함하는 이름의 상대 경로|  

## <a name="etwproviders-element"></a>EtwProviders 요소  
 EventSource의 ETW 이벤트 및/또는 공급자를 기반으로 하는 ETW 매니페스트의 컬렉션을 구성합니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource 클래스](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)에서 생성된 이벤트 컬렉션을 구성합니다. 필수 특성:<br /><br /> **provider** - EventSource 이벤트의 클래스 이름입니다.<br /><br /> 선택적 특성은 다음과 같습니다.<br /><br /> -                     **scheduledTransferLogLevelFilter** - 저장소 계정으로 전송할 최소 심각도 수준입니다.<br /><br /> -                     **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML 기간 데이터 형식](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  
|**EtwManifestProviderConfiguration**|필수 특성:<br /><br /> **provider** - 이벤트 공급자의 GUID<br /><br /> 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 저장소 계정으로 전송할 최소 심각도 수준입니다.<br /><br /> -                     **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML 기간 데이터 형식](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 요소  
 [EventSource 클래스](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)에서 생성된 이벤트 컬렉션을 구성합니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**DefaultEvents**|선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  
|**Event**|필수 특성:<br /><br /> **id** - 이벤트의 ID입니다.<br /><br /> 선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 요소  
 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**DefaultEvents**|선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  
|**Event**|필수 특성:<br /><br /> **id** - 이벤트의 ID입니다.<br /><br /> 선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  

## <a name="metrics-element"></a>Metrics 요소  
 빠른 쿼리를 위해 최적화된 성능 카운터 테이블을 생성할 수 있습니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**MetricAggregation**|필수 특성:<br /><br /> **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML 기간 데이터 형식](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.|  

## <a name="performancecounters-element"></a>PerformanceCounters 요소  
 성능 카운터의 컬렉션을 활성화합니다. 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|다음과 같은 특성이 필요합니다.<br /><br /> -                     **counterSpecifier** - 성능 카운터의 이름입니다. 예: `\Processor(_Total)\% Processor Time` 호스트에서 카운터의 성능 카운터의 목록을 가져오려면 명령 `typeperf`를 실행합니다.<br /><br /> -                     **sampleRate** - 카운터가 샘플링되는 주기입니다.<br /><br /> 선택적 특성:<br /><br /> **unit** - 카운터의 측정 단위입니다.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration 요소  
 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**annotation**|필수 특성:<br /><br /> **displayName** - 카운터의 표시 이름<br /><br /> 선택적 특성:<br /><br /> **locale** - 카운터 이름을 표시할 때 사용할 로캘|  

## <a name="windowseventlog-element"></a>WindowsEventLog 요소  
 다음 표는 자식 요소에 대해 설명합니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**DataSource**|수집할 Windows 이벤트 로그입니다. 필수 특성:<br /><br /> **name** - 수집할 Windows 이벤트를 설명하는 XPath 쿼리입니다. 예: <br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> 모든 이벤트를 수집하려면 "*"를 지정합니다.|
