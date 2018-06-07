---
title: "Azure 진단 1.0 구성 스키마 | Microsoft Docs"
description: "Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric 또는 Cloud Services에서 Azure SDK 2.4 이하를 사용하는 경우에만 해당됩니다."
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/15/2017
ms.author: robb
ms.openlocfilehash: a8fdfb52d5091d3fc9779657737c7430fcfada51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure 진단 1.0 구성 스키마
> [!NOTE]
> Azure 진단은 Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric 및 Cloud Services에서 성능 카운터 및 기타 통계를 수집하는 데 사용되는 구성 요소입니다.  이 페이지는 이러한 서비스 중 하나를 사용하는 경우에만 해당됩니다.
>

Azure 진단은 Azure Monitor, Application Insights 및 Log Analytics와 같은 기타 Microsoft 진단 제품과 함께 사용됩니다.

Azure 진단 구성 파일은 진단 모니터를 초기화하는 데 사용되는 값을 정의합니다. 이 파일은 진단 모니터가 시작될 때 진단 구성 설정을 초기화하는 데 사용됩니다.  

 기본적으로 Azure 진단 구성 스키마 파일은 `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` 디렉터리에 설치됩니다. `<version>`을 [Azure SDK](http://www.windowsazure.com/develop/downloads/)의 설치된 버전으로 바꿉니다.  

> [!NOTE]
>  진단 구성 파일은 일반적으로 시작 프로세스 초기에 진단 데이터 수집을 요하는 시작 작업에 사용됩니다. Azure 진단에 대한 자세한 내용은 [Azure 진단을 사용하여 로깅 데이터 수집](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7)을 참조하세요.  

## <a name="example-of-the-diagnostics-configuration-file"></a>진단 구성 파일 예제  
 다음 예제에서는 일반적인 진단 구성 파일을 보여 줍니다.  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration 네임스페이스  
 진단 구성 파일에 대한 XML 네임스페이스는 다음과 같습니다.  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>스키마 요소  
 진단 구성 파일에는 다음과 같은 요소가 포함되어 있습니다.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 요소  
진단 구성 파일의 최상위 요소입니다.  

특성:

|특성  |유형   |필수| 기본값 | 설명|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|옵션 | PT1M| 진단 모니터가 진단 구성 변경을 폴링하는 간격을 지정합니다.|  
|**overallQuotaInMB**|unsignedInt|옵션| 4000MB 값을 제공하는 경우 이 크기를 초과하지 않아야 합니다. |모든 로깅 버퍼에 할당된 파일 시스템 저장소의 총 크기입니다.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs 요소  
기본 진단 인프라에서 생성하는 로그의 버퍼 구성을 정의합니다.

부모 요소: [DiagnosticMonitorConfiguration 요소](#DiagnosticMonitorConfiguration).  

특성:

|특성|형식|설명|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|선택 사항입니다. 지정된 데이터에 사용할 수 있는 파일 시스템 저장소의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferLogLevelFilter**|string|선택 사항입니다. 전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined**입니다. 사용 가능한 다른 값은 **Verbose**, **Information**, **Warning**, **Error**, 및 **Critical**입니다.|  
|**scheduledTransferPeriod**|duration|선택 사항입니다. 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  

## <a name="logs-element"></a>Logs 요소  
 기본 Azure 로그의 버퍼 구성을 정의합니다.

 부모 요소: [DiagnosticMonitorConfiguration 요소](#DiagnosticMonitorConfiguration).  

특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|선택 사항입니다. 지정된 데이터에 사용할 수 있는 파일 시스템 저장소의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferLogLevelFilter**|string|선택 사항입니다. 전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined**입니다. 사용 가능한 다른 값은 **Verbose**, **Information**, **Warning**, **Error**, 및 **Critical**입니다.|  
|**scheduledTransferPeriod**|duration|선택 사항입니다. 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  

## <a name="directories-element"></a>Directories 요소  
정의할 수 있는 파일 기반 로그의 버퍼 구성을 정의합니다.

부모 요소: [DiagnosticMonitorConfiguration 요소](#DiagnosticMonitorConfiguration).  


특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|선택 사항입니다. 지정된 데이터에 사용할 수 있는 파일 시스템 저장소의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferPeriod**|duration|선택 사항입니다. 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  

## <a name="crashdumps-element"></a>CrashDumps 요소  
 크래시 덤프 디렉터리를 정의합니다.

 부모 요소: [Directories 요소](#Directories).  

특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**container**|string|디렉터리의 내용이 전송될 컨테이너의 이름입니다.|  
|**directoryQuotaInMB**|unsignedInt|선택 사항입니다. 디렉터리의 최대 크기(MB)를 지정합니다.<br /><br /> 기본값은 0입니다.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs 요소  
 실패한 요청 로그 디렉터리를 정의합니다.

 부모 요소[Directories 요소](#Directories).  

특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**container**|string|디렉터리의 내용이 전송될 컨테이너의 이름입니다.|  
|**directoryQuotaInMB**|unsignedInt|선택 사항입니다. 디렉터리의 최대 크기(MB)를 지정합니다.<br /><br /> 기본값은 0입니다.|  

##  <a name="iislogs-element"></a>IISLogs 요소  
 IIS 로그 디렉터리를 정의합니다.

 부모 요소[Directories 요소](#Directories).  

특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**container**|string|디렉터리의 내용이 전송될 컨테이너의 이름입니다.|  
|**directoryQuotaInMB**|unsignedInt|선택 사항입니다. 디렉터리의 최대 크기(MB)를 지정합니다.<br /><br /> 기본값은 0입니다.|  

## <a name="datasources-element"></a>DataSources 요소  
 0개 이상의 추가 로그 디렉터리를 정의합니다.

 부모 요소: [Directories 요소](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 요소  
 모니터링할 로그 파일의 디렉터리를 정의합니다.

 부모 요소: [DataSources 요소](#DataSources)

특성:

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**container**|string|디렉터리의 내용이 전송될 컨테이너의 이름입니다.|  
|**directoryQuotaInMB**|unsignedInt|선택 사항입니다. 디렉터리의 최대 크기(MB)를 지정합니다.<br /><br /> 기본값은 0입니다.|  

## <a name="absolute-element"></a>Absolute 요소  
 선택적 환경 확장을 사용하여 모니터링할 디렉터리의 절대 경로를 정의합니다.

 부모 요소: [DirectoryConfiguration 요소](#DirectoryConfiguration).  

특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**path**|string|필수입니다. 모니터링할 디렉터리의 절대 경로입니다.|  
|**expandEnvironment**|부울|필수입니다. **true**로 설정하면 경로의 환경 변수가 확장됩니다.|  

## <a name="localresource-element"></a>LocalResource 요소  
 서비스 정의에 정의된 로컬 리소스의 상대 경로를 정의합니다.

 부모 요소: [DirectoryConfiguration 요소](#DirectoryConfiguration).  

특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**name**|string|필수입니다. 모니터링할 디렉터리를 포함하는 로컬 리소스의 이름입니다.|  
|**relativePath**|string|필수입니다. 모니터링할 로컬 리소스의 상대 경로입니다.|  

## <a name="performancecounters-element"></a>PerformanceCounters 요소  
 수집할 성능 카운터의 경로를 정의합니다.

 부모 요소: [DiagnosticMonitorConfiguration 요소](#DiagnosticMonitorConfiguration).


 특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|선택 사항입니다. 지정된 데이터에 사용할 수 있는 파일 시스템 저장소의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferPeriod**|duration|선택 사항입니다. 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration 요소  
 수집할 성능 카운터를 정의합니다.

 부모 요소: [PerformanceCounters 요소](#PerformanceCounters).  

 특성:  

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**counterSpecifier**|string|필수입니다. 수집할 성능 카운터의 경로입니다.|  
|**sampleRate**|duration|필수입니다. 성능 카운터를 수집할 속도입니다.|  

## <a name="windowseventlog-element"></a>WindowsEventLog 요소  
 모니터링할 이벤트 로그를 정의합니다.

 부모 요소: [DiagnosticMonitorConfiguration 요소](#DiagnosticMonitorConfiguration).

  특성:

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|선택 사항입니다. 지정된 데이터에 사용할 수 있는 파일 시스템 저장소의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferLogLevelFilter**|string|선택 사항입니다. 전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined**입니다. 사용 가능한 다른 값은 **Verbose**, **Information**, **Warning**, **Error**, 및 **Critical**입니다.|  
|**scheduledTransferPeriod**|duration|선택 사항입니다. 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  

## <a name="datasource-element"></a>DataSource 요소  
 모니터링할 이벤트 로그를 정의합니다.

 부모 요소: [WindowsEventLog 요소](#windowsEventLog)  

 특성:

|특성|형식|설명|  
|---------------|----------|-----------------|  
|**name**|string|필수입니다. 수집할 로그를 지정하는 XPath 식입니다.|  
