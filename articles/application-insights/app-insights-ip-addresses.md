---
title: Application Insights 및 Log Analytics에 사용된 IP 주소 | Microsoft Docs
description: Application Insights에 필요한 서버 방화벽 예외
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: mbullwin
ms.openlocfilehash: 4ed0c84dfab58f8a92e0f366bb65634b9e3dab82
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935655"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights 및 Log Analytics에 사용된 IP 주소
[Azure Application Insights](app-insights-overview.md) 서비스는 많은 IP 주소를 사용합니다. 모니터링하는 앱이 방화벽 뒤에서 호스팅되는 경우 이러한 주소를 알아야 할 수도 있습니다.

> [!NOTE]
> 이러한 주소는 정적이지만 경우에 따라 변경해야 할 수 있습니다.
> 
> 

## <a name="outgoing-ports"></a>발신 포트
Application Insights SDK 및/또는 상태 모니터가 데이터를 포털에 보낼 수 있도록 서버 방화벽에서 일부 나가는 포트를 열어야 합니다.

| 목적 | URL | IP | 포트 |
| --- | --- | --- | --- |
| 원격 분석 |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244 |443 |
| 라이브 메트릭 스트림 |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |
| 내부 원격 분석 |breeze.aimon.applicationinsights.io |52.161.11.71 |443 |

## <a name="status-monitor"></a>상태 모니터
상태 모니터 구성 - 변경하는 경우에만 필요합니다.

| 목적 | URL | IP | 포트 |
| --- | --- | --- | --- |
| 구성 |`management.core.windows.net` | |`443` |
| 구성 |`management.azure.com` | |`443` |
| 구성 |`login.windows.net` | |`443` |
| 구성 |`login.microsoftonline.com` | |`443` |
| 구성 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 구성 |`auth.gfx.ms` | |`443` |
| 구성 |`login.live.com` | |`443` |
| 설치 |`packages.nuget.org`, `nuget.org`, `api.nuget.org`, `az320820.vo.msecnd.net`(NuGet 다운로드) | |`443` |

## <a name="availability-tests"></a>가용성 테스트
[가용성 웹 테스트](app-insights-monitor-web-app-availability.md) 가 실행되는 주소 목록입니다. 앱에서 웹 테스트를 실행하려고 하지만 웹 서버가 특정 클라이언트 서비스를 제공하도록 제한된 경우 가용성 테스트 서버에서 들어오는 트래픽을 허용해야 합니다.

이 주소에서 들어오는 트래픽에 대한 포트 80(http) 및 443(https)을 엽니다(IP 주소가 위치별로 그룹화됨).

```
Australia East
13.70.83.252
13.75.150.96
13.75.153.9
13.75.158.185
Brazil South
191.232.32.122
191.232.172.45
191.232.176.218
191.232.191.225
France South
52.136.140.221
52.136.140.222
52.136.140.223
52.136.140.226
France Central
52.143.140.242
52.143.140.246
52.143.140.247
52.143.140.249
East Asia
13.75.121.122
23.99.115.153
23.99.123.38
23.102.232.186
52.175.38.49
52.175.39.103
North Europe
13.74.184.101
13.74.185.160
40.69.200.198
52.164.224.46
52.169.12.203
52.169.14.11
52.169.237.149
52.178.183.105
Japan East
52.243.33.33
52.243.33.141
52.243.35.253
52.243.41.117
West Europe
52.174.166.113
52.174.178.96
52.174.31.140
52.174.35.14
52.178.104.23
52.178.109.190
52.178.111.139
52.233.166.221
UK South
51.140.79.229
51.140.84.172
51.140.87.211
51.140.105.74
UK West
51.141.25.219
51.141.32.101
51.141.35.167
51.141.54.177
Southeast Asia
52.187.29.7
52.187.179.17
52.187.76.248
52.187.43.24
52.163.57.91
52.187.30.120
West US
104.45.228.236
104.45.237.251
13.64.152.110
13.64.156.54
13.64.232.251
13.64.236.105
13.91.94.59
40.118.131.182
40.83.189.192
40.83.215.122
Central US
52.165.130.58
52.173.142.229
52.173.147.190
52.173.17.41
52.173.204.247
52.173.244.190
52.173.36.222
52.176.1.226
North Central US
23.96.247.139
23.96.249.113
52.162.124.242
52.162.126.139
52.162.241.147
52.162.246.222
52.162.247.136
52.237.153.231
52.237.154.216
52.237.156.14
52.237.157.218
52.237.157.37
South Central US
104.210.145.106
13.84.176.24
13.84.49.16
13.85.11.137
13.85.26.248
13.85.69.145
52.171.136.162
52.171.141.253
52.171.57.172
52.171.58.140
East US
13.82.218.95
13.90.96.71
13.90.98.52
13.92.137.70
40.85.187.235
40.87.61.61
52.168.8.247
52.170.38.79
52.170.80.61
52.179.9.26

```  

## <a name="application-insights-api"></a>Application Insights API
| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80,443 |
| API 문서 |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80,443 |
| 내부 API |aigs.aisvc.visualstudio.com<br/>aigs1.aisvc.visualstudio.com<br/>aigs2.aisvc.visualstudio.com<br/>aigs3.aisvc.visualstudio.com<br/>aigs4.aisvc.visualstudio.com<br/>aigs5.aisvc.visualstudio.com<br/>aigs6.aisvc.visualstudio.com |동적|443 |

## <a name="log-analytics-api"></a>Log Analytics API
| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |동적 |80,443 |
| API 문서 |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |동적 |80,443 |

## <a name="application-insights-analytics"></a>Application Insights Analytics

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 분석 포털 | analytics.applicationinsights.io | 동적 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 동적 | 80,443 |
| 미디어 CDN | applicationanalyticsmedia.azureedge.net | 동적 | 80,443 |

참고: *.applicationinsights.io 도메인은 Application Insights 팀이 소유합니다.

## <a name="log-analytics-portal"></a>Log Analytics 포털

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 포털 | portal.loganalytics.io | 동적 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 동적 | 80,443 |

참고: *. loganalytics.io 도메인은 Log Analytics 팀 소유입니다.

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure Portal 확장

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| Application Insights 확장 | stamp2.app.insightsportal.visualstudio.com | 동적 | 80,443 |
| Application Insights 확장 CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 동적 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | 동적 | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | 동적 | 80,443 |

## <a name="profiler"></a>프로파일러

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 에이전트 | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 51.143.96.206<br/>51.143.98.157<br/>52.161.8.88<br/>52.161.29.225<br/>52.178.149.106<br/>52.178.147.66<br/>40.68.32.221<br/>104.40.217.71<br/>52.230.124.46<br/>52.230.122.9 | 443
| 포털 | gateway.azureserviceprofiler.net | 동적 | 443
| Storage | *.core.windows.net | 동적 | 443

## <a name="snapshot-debugger"></a>스냅숏 디버거

> [!NOTE]
> 프로파일러 및 스냅숏 디버거는 동일한 IP 주소 집합을 공유합니다.

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 에이전트 | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 51.143.96.206<br/>51.143.98.157<br/>52.161.8.88<br/>52.161.29.225<br/>52.178.149.106<br/>52.178.147.66<br/>40.68.32.221<br/>104.40.217.71<br/>52.230.124.46<br/>52.230.122.9 | 443
| 포털 | ppe.gateway.azureserviceprofiler.net | 동적 | 443
| Storage | *.core.windows.net | 동적 | 443
