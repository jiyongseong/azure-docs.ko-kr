---
title: Azure 진단 확장 1.3 이상 구성 스키마 | Microsoft Docs
description: Azure 진단용 스키마 버전 1.3 이상은 Microsoft Azure SDK 2.4 이상의 일부로 제공됩니다.
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
ms.openlocfilehash: 02656c5bb4d2acd944f565d1397984ce94ced0bd
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure 진단 1.3 이상 구성 스키마
> [!NOTE]
> Azure 진단 확장은 성능 카운터 및 기타 통계를 수집하는 데 사용하는 구성 요소입니다.
> - Azure Virtual Machines 
> - Virtual Machine Scale Sets
> - Service Fabric 
> - Cloud Services 
> - 네트워크 보안 그룹
> 
> 이 페이지는 이러한 서비스 중 하나를 사용하는 경우에만 해당됩니다.

이 페이지는 버전 1.3 이상(Azure SDK 2.4 이상)에 유효합니다. 최신 구성 섹션은 추가된 버전에 표시하도록 주석 처리되었습니다.  

여기에서 설명하는 구성 파일은 진단 모니터가 시작될 때 진단 구성 설정을 설정하는 데 사용됩니다.  

확장은 Azure Monitor, Application Insights 및 Log Analytics와 같은 기타 Microsoft 진단 제품과 함께 사용됩니다.



다음 PowerShell 명령을 실행하여 공용 구성 파일 스키마 정의를 다운로드합니다.  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Azure 진단 사용에 대한 자세한 내용은 [Azure 진단 확장](azure-diagnostics.md)을 참조하세요.  

## <a name="example-of-the-diagnostics-configuration-file"></a>진단 구성 파일 예제  
 다음 예제에서는 일반적인 진단 구성 파일을 보여 줍니다.  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 --> 
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
   
    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>
   
    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  

이전 XML 구성 파일에 해당하는 JSON입니다. 

PublicConfig와 PrivateConfig는 구분되는데, 대부분의 json 사용 사례에서 다른 변수로 전달되기 때문입니다. 이러한 경우에는 Resource Manager 템플릿, Virtual Machine 확장 집합 PowerShell 및 Visual Studio가 있습니다. 

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

## <a name="reading-this-page"></a>이 페이지 읽기  
 다음 태그는 대략 앞의 예제에 표시된 순서를 따릅니다.  필요한 위치에 전체 설명이 표시되지 않으면 요소 또는 특성에 대한 페이지를 검색합니다.  

## <a name="common-attribute-types"></a>일반 특성 유형  
 **scheduledTransferPeriod** 특성이 몇 가지 요소에 나타납니다. 저장소에 예약된 전송 사이의 간격은 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다.


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration*

버전 1.3에 추가되었습니다.  

진단 구성 파일의 최상위 요소입니다.  

**특성** xmlns - 진단 구성 파일에 대한 XML 네임스페이스는 다음과 같습니다.  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|자식 요소|설명|  
|--------------------|-----------------|  
|**PublicConfig**|필수 사항입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**PrivateConfig**|선택 사항입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**IsEnabled**|부울 값입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  

## <a name="publicconfig-element"></a>PublicConfig 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig*

 공용 진단 구성을 설명합니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**WadCfg**|필수 사항입니다. 이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**StorageAccount**|데이터를 저장할 Azure Storage 계정의 이름입니다. Set-AzureServiceDiagnosticsExtension cmdlet을 실행할 때 매개 변수로 지정할 수도 있습니다.|  
|**StorageType**|*Table*, *Blob* 또는 *TableAndBlob*일 수 있습니다. Table이 기본값입니다. TableAndBlob을 선택하면 진단 데이터는 각 형식당 한 번씩, 두 번 기록됩니다.|  
|**LocalResourceDirectory**|모니터링 에이전트가 이벤트 데이터를 저장하는 가상 컴퓨터의 디렉터리입니다. 설정되어 있지 않은 경우 기본 디렉터리가 사용됩니다.<br /><br /> 작업자/웹 역할의 경우: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Virtual Machine의 경우: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> 필수 특성은 다음과 같습니다.<br /><br /> - **경로** - Azure 진단에서 사용되는 시스템의 디렉터리입니다.<br /><br /> - **expandEnvironment** - 환경 변수가 경로 이름에서 확장되는지 여부를 제어합니다.|  

## <a name="wadcfg-element"></a>WadCFG 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*
 
 수집할 원격 분석 데이터를 식별 및 구성합니다.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 요소 
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 필수 

|특성|설명|  
|----------------|-----------------|  
| **overallQuotaInMB** | Azure 진단으로 수집된 진단 데이터의 다양한 형식에서 사용될 수 있는 로컬 디스크 공간의 최대 크기입니다. 기본 설정은 4096MB입니다.<br />
|**useProxyServer** | IE 설정에서 설정한 대로 프록시 서버 설정을 사용하도록 Azure 진단을 구성합니다.|  

<br /> <br />

|자식 요소|설명|  
|--------------------|-----------------|  
|**CrashDumps**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**DiagnosticInfrastructureLogs**|Azure 진단에 의해 생성된 로그의 컬렉션을 사용하도록 설정합니다. 진단 인프라 로그는 진단 시스템 자체의 문제 해결에 유용합니다. 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 수집된 로그의 최소 심각도 수준을 구성합니다.<br /><br /> - **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다. |  
|**Directories**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**EtwProviders**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**metrics**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**PerformanceCounters**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  
|**WindowsEventLog**|이 페이지의 다른 곳에 있는 설명을 참조하세요.| 
|**DockerSources**|이 페이지의 다른 곳에 있는 설명을 참조하세요. | 



## <a name="crashdumps-element"></a>CrashDumps 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*
 
 크래시 덤프의 수집을 사용하도록 설정합니다.  

|특성|설명|  
|----------------|-----------------|  
|**containerName**|선택 사항입니다. 크래시 덤프를 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|  
|**crashDumpType**|선택 사항입니다.  최소 또는 전체 크래시 덤프를 수집하도록 Azure 진단을 구성 합니다.|  
|**directoryQuotaPercentage**|선택 사항입니다.  VM에서 크래시 덤프에 대해 예약될 **overallQuotaInMB**의 비율을 구성합니다.|  

|자식 요소|설명|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|필수 사항입니다. 각 프로세스에 대한 구성 값을 정의합니다.<br /><br /> 다음과 같은 특성도 필요합니다.<br /><br /> **processName** - Azure 진단에서 크래시 덤프를 수집하도록 할 프로세스의 이름입니다.|  

## <a name="directories-element"></a>Directories 요소 
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration -  Directories*

 디렉터리, IIS 실패한 액세스 요청 로그 및/또는 IIS 로그의 콘텐츠 수집을 활성화합니다.  

 선택적 **scheduledTransferPeriod** 특성입니다. 이전 설명을 참조하세요.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**IISLogs**|이 요소를 구성에 포함하면 IIS 로그의 컬렉션이 활성화됩니다.<br /><br /> **containerName** - IIS를 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|   
|**FailedRequestLogs**|이 요소를 구성에 포함하면 IIS 사이트 또는 응용 프로그램에 실패 한 요청에 대한 로그 컬렉션이 활성화합니다. 또한 **Web.config**의 **system.WebServer**에 있는 추적 옵션도 사용하도록 설정해야 합니다.|  
|**DataSources**|모니터링할 디렉터리의 목록입니다.| 




## <a name="datasources-element"></a>DataSources 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources*

 모니터링할 디렉터리의 목록입니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|필수 사항입니다. 필수 특성:<br /><br /> **containerName** - 로그 파일을 저장하는 데 사용할 Azure Storage 계정의 blob 컨테이너의 이름입니다.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Directories - DataSources - DirectoryConfiguration*

 **Absolute** 또는 **LocalResource** 요소 중 하나를 포함하되, 모두 포함할 수는 없습니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**Absolute**|모니터링할 디렉터리의 절대 경로입니다. 다음과 같은 특성이 필요합니다.<br /><br /> - **Path** - 모니터링할 디렉터리의 절대 경로입니다.<br /><br /> - **expandEnvironment** - 경로의 환경 변수가 확장되어 있는지 여부를 구성합니다.|  
|**LocalResource**|모니터링할 로컬 리소스의 상대 경로입니다. 필수 특성은 다음과 같습니다.<br /><br /> - **Name** - 모니터링할 디렉터리를 포함하는 로컬 리소스<br /><br /> - **relativePath** -모니터링할 디렉터리를 포함하는 이름의 상대 경로|  



## <a name="etwproviders-element"></a>EtwProviders 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 EventSource의 ETW 이벤트 및/또는 공급자를 기반으로 하는 ETW 매니페스트의 컬렉션을 구성합니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|[EventSource 클래스](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)에서 생성된 이벤트 컬렉션을 구성합니다. 필수 특성:<br /><br /> **provider** - EventSource 이벤트의 클래스 이름입니다.<br /><br /> 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 저장소 계정으로 전송할 최소 심각도 수준입니다.<br /><br /> - **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다. |  
|**EtwManifestProviderConfiguration**|필수 특성:<br /><br /> **provider** - 이벤트 공급자의 GUID<br /><br /> 선택적 특성은 다음과 같습니다.<br /><br /> - **scheduledTransferLogLevelFilter** - 저장소 계정으로 전송할 최소 심각도 수준입니다.<br /><br /> - **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다. |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderConfiguration*

 [EventSource 클래스](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)에서 생성된 이벤트 컬렉션을 구성합니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**DefaultEvents**|선택적 특성:<br/><br/> **eventDestination** - 이벤트를 저장할 테이블의 이름|  
|**Event**|필수 특성:<br /><br /> **id** - 이벤트의 ID입니다.<br /><br /> 선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|자식 요소|설명|  
|--------------------|-----------------|  
|**DefaultEvents**|선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  
|**Event**|필수 특성:<br /><br /> **id** - 이벤트의 ID입니다.<br /><br /> 선택적 특성:<br /><br /> **eventDestination** - 이벤트를 저장할 테이블의 이름|  



## <a name="metrics-element"></a>Metrics 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metrics*

 빠른 쿼리를 위해 최적화된 성능 카운터 테이블을 생성할 수 있습니다. **PerformanceCounters** 요소에 정의되어 있는 각 성능 카운터는 성능 카운터 테이블에 추가된 메트릭 테이블에 저장되어 있습니다.  

 **resourceId** 특성이 필요합니다.  Azure 진단을 배포하는 가상 머신 또는 Virtual Machine Scale Set의 리소스 ID입니다. [Azure Portal](https://portal.azure.com)에서 **resourceID**를 가져옵니다. **찾아보기** -> **리소스 그룹** -> **<이름\>**을 선택합니다. **속성** 타일을 클릭하고 **ID** 필드에서 값을 복사합니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**MetricAggregation**|필수 특성:<br /><br /> **scheduledTransferPeriod** - 저장소에 예약된 전송 사이의 간격으로 가장 가까운 시간(분)으로 반올림됩니다. 값은 [XML "기간 데이터 형식"](http://www.w3schools.com/schema/schema_dtypes_date.asp)입니다. |  



## <a name="performancecounters-element"></a>PerformanceCounters 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 성능 카운터의 컬렉션을 활성화합니다.  

 선택적 특성:  

 선택적 **scheduledTransferPeriod** 특성입니다. 이전 설명을 참조하세요.

|자식 요소|설명|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|다음과 같은 특성이 필요합니다.<br /><br /> - **counterSpecifier** - 성능 카운터의 이름입니다. 예: `\Processor(_Total)\% Processor Time` 호스트에서 성능 카운터의 목록을 가져오려면 명령 `typeperf`를 실행합니다.<br /><br /> - **sampleRate** - 카운터가 샘플링되는 주기입니다.<br /><br /> 선택적 특성:<br /><br /> **unit** - 카운터의 측정 단위입니다.|  




## <a name="windowseventlog-element"></a>WindowsEventLog 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*
 
 Windows 이벤트 로그의 컬렉션을 활성화합니다.  

 선택적 **scheduledTransferPeriod** 특성입니다. 이전 설명을 참조하세요.  

|자식 요소|설명|  
|-------------------|-----------------|  
|**DataSource**|수집할 Windows 이벤트 로그입니다. 필수 특성:<br /><br /> **name** - 수집할 Windows 이벤트를 설명하는 XPath 쿼리입니다. 예: <br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> 모든 이벤트를 수집하려면 “*”를 지정합니다.|  




## <a name="logs-element"></a>Logs 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Logs*

 버전 1.0 및 1.1에서 제공됩니다. 1.2에는 제공되지 않습니다. 1.3에서 다시 추가됩니다.  

 기본 Azure 로그의 버퍼 구성을 정의합니다.  

|특성|유형|설명|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|선택 사항입니다. 지정된 데이터에 사용할 수 있는 파일 시스템 저장소의 최대 크기를 지정합니다.<br /><br /> 기본값은 0입니다.|  
|**scheduledTransferLogLevelFilterr**|**string**|선택 사항입니다. 전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined**로, 모든 로그를 전송합니다. 정보가 적은 순서대로 사용 가능한 다른 값을 나열하면 다음과 같습니다. **자세한 정보**, **정보**, **경고**, **오류**, **중요**|  
|**scheduledTransferPeriod**|**duration**|선택 사항입니다. 예약된 데이터 전송 사이의 간격(가장 가까운 시간(분)으로 반올림)을 지정합니다.<br /><br /> 기본값은 PT0S입니다.|  
|**싱크** 1.5에서 추가됨|**string**|선택 사항입니다. 또한 진단 데이터를 보내는 싱크 위치를 가리킵니다. 예를 들어 Application Insights입니다.|  

## <a name="dockersources"></a>DockerSources
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 1.9에 추가되었습니다.

|요소 이름|설명|  
|------------------|-----------------|  
|**Stats**|Docker 컨테이너에 대한 통계를 수집하도록 시스템에 지시|  

## <a name="sinksconfig-element"></a>SinksConfig 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig*

 진단 데이터를 보낼 위치와 그러한 위치와 관련된 구성의 목록 입니다.  

|요소 이름|설명|  
|------------------|-----------------|  
|**싱크**|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  

## <a name="sink-element"></a>싱크 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink*

 버전 1.5에 추가되었습니다.  

 진단 데이터를 보낼 위치를 정의합니다. 예를 들어 Application Insights 서비스입니다.  

|특성|유형|설명|  
|---------------|----------|-----------------|  
|**name**|string|sinkname을 식별하는 문자열입니다.|  

|요소|유형|설명|  
|-------------|----------|-----------------|  
|**Application Insights**|string|데이터를 Application Insights로 전송하는 경우에만 사용됩니다. 액세스 권한이 있는 활성 Application Insights 계정에 대한 계측 키를 포함합니다.|  
|**Channels**|string|스트림하는 각 추가 필터링에 대한|  

## <a name="channels-element"></a>Channels 요소  
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels*

 버전 1.5에 추가되었습니다.  

 싱크를 통해 전달되는 로그 데이터의 스트림에 대한 필터를 정의합니다.  

|요소|유형|설명|  
|-------------|----------|-----------------|  
|**채널**|string|이 페이지의 다른 곳에 있는 설명을 참조하세요.|  

## <a name="channel-element"></a>채널 요소
 *Tree: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - Sink - Channels - Channel*

 버전 1.5에 추가되었습니다.  

 진단 데이터를 보낼 위치를 정의합니다. 예를 들어 Application Insights 서비스입니다.  

|특성|유형|설명|  
|----------------|----------|-----------------|  
|**logLevel**|**string**|전송되는 로그 항목에 대한 최소 심각도 수준을 지정합니다. 기본값은 **Undefined**로, 모든 로그를 전송합니다. 정보가 적은 순서대로 사용 가능한 다른 값을 나열하면 다음과 같습니다. **자세한 정보**, **정보**, **경고**, **오류**, **중요**|  
|**name**|**string**|참조 하는 채널의 고유 이름|  


## <a name="privateconfig-element"></a>PrivateConfig 요소 
 *Tree: Root - DiagnosticsConfiguration - PrivateConfig*

 버전 1.3에 추가되었습니다.  

 옵션  

 저장소 계정(이름, 키 및 끝점)의 개인 정보를 저장합니다. 이 정보는 가상 컴퓨터에 전송되지만 여기에서 검색할 수 없습니다.  

|자식 요소|설명|  
|--------------------|-----------------|  
|**StorageAccount**|사용할 저장소 계정입니다. 다음과 같은 특성이 필요<br /><br /> - **이름** - 저장소 계정의 이름입니다.<br /><br /> - **키** - 저장소 계정의 키입니다.<br /><br /> - **끝점** - 저장소 계정에 액세스하는 끝점입니다. <br /><br /> -**sasToken**(1.8.1에 추가됨) - 개인 구성에서 저장소 계정 키 대신 SAS 토큰을 지정할 수 있습니다. 제공된 경우 저장소 계정 키가 무시됩니다. <br />SAS 토큰에 대한 요구 사항: <br />- 계정 SAS 토큰만 지원합니다. <br />- *b*, *t* 서비스 형식이 필요합니다. <br /> - *a*, *c*, *u*, *w* 권한이 필요합니다. <br /> - *c*, *o* 리소스 형식이 필요합니다. <br /> - HTTPS 프로토콜만 지원합니다. <br /> - 시작 및 만료 시간이 유효해야 합니다.|  


## <a name="isenabled-element"></a>IsEnabled 요소  
 *Tree: Root - DiagnosticsConfiguration - IsEnabled*

 부울 값입니다. `true`를 사용하여 진단을 사용하도록 설정하거나 `false`를 사용하여 진단을 사용하지 않도록 설정합니다.
