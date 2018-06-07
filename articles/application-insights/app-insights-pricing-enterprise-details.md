---
title: Azure Application Insights의 레거시 엔터프라이즈 가격 책정 요금제 정보 | Microsoft Docs
description: Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6df013506e4541fee7850850776d26e5c69a799d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-plan-details"></a>엔터프라이즈 계획 세부 정보

Azure Application Insights에는 두 가지 가격 책정 옵션인 Basic과 Enterprise가 있습니다. [Basic](app-insights-pricing.md) 가격 책정 계획은 기본 계획입니다. 여기에는 추가 비용 없이 모든 Enterprise 계획 기능이 포함됩니다. Basic 계획에서는 기본적으로 수집된 데이터의 볼륨에 따라 비용이 청구됩니다. 

Enterprise 계획에는 노드별 청구가 사용되며 각 노드는 일일 데이터 허용량을 수신합니다. Enterprise 가격 책정 계획에서는 포함된 허용량 이상으로 수집된 데이터에 대해 요금이 부과됩니다. Operations Management Suite를 사용하는 경우 Enterprise 계획을 선택해야 합니다. 

사용자의 통화 및 지역에 따른 현재 가격은 [Application Insights 가격 책정](http://azure.microsoft.com/pricing/details/application-insights/)을 참조하세요.

## <a name="how-the-enterprise-plan-works"></a>Enterprise 계획의 작동 방식

* Enterprise 계획의 모든 앱에 대한 원격 분석을 보내는 노드당 비용을 지불해야 합니다.
 * *노드*는 앱을 호스트하는 실제/가상 서버 컴퓨터 또는 PaaS(Platform-as-a-Service) 역할 인스턴스입니다.
 * 개발 컴퓨터, 클라이언트 브라우저 및 모바일 장치는 노드로 계산되지 않습니다.
 * 원격 분석을 보내는 여러 구성 요소(예: 웹 서비스 및 백 엔드 작업자)가 앱에 있는 경우 해당 구성 요소는 개별적으로 집계됩니다.
 * [라이브 메트릭 스트림](app-insights-live-stream.md) 데이터는 가격 책정에 계산 되지 않습니다. 구독에서 요금은 앱 기준이 아니라 노드 기준으로 부과됩니다. 12개 앱에 대해 원격 분석을 보내는 노드가 5개 있는 경우 5개 노드에 대한 요금이 부과됩니다.
* 요금이 매월 견적되지만 노드에서 앱의 원격 분석을 보내는 모든 시간에 대해서만 부과됩니다. 시간당 요금은 견적된 월별 요금을 744(한 달이 31일일 때 시간 수)로 나눈 것입니다.
* 시간별로 감지되는 각 노드에 대해 1일 200MB의 데이터 볼륨 할당이 제공됩니다. 사용되지 않은 데이터 할당은 다음 날로 이월되지 않습니다.
 * Enterprise 가격 책정 계획을 선택하면 각 구독은 해당 구독의 Application Insights 리소스에 원격 분석을 보내는 노드 수에 따라 일일 데이터 허용량을 받습니다. 따라서 하루 종일 데이터를 전송하는 5개의 노드가 있는 경우 해당 구독의 모든 Application Insights 리소스에 풀링된 1GB 허용량이 적용됩니다. 모든 노드에서 포함된 데이터를 공유하기 때문에 특정 노드에서 다른 노드보다 더 많은 데이터를 보내는 것은 중요하지 않습니다. 지정된 날에 Application Insights 리소스에서 해당 구독의 일일 데이터 할당에 포함된 것보다 많은 데이터를 받으면 GB당 초과 데이터 요금이 적용됩니다. 
 * 일일 데이터 허용량은 각 노드에서 원격 분석을 보내는 날의 시간 수(UTC 사용)를 24로 나눈 값에 200MB를 곱해 계산합니다. 따라서 하루 24시간 중 15시간 동안 원격 분석을 보내는 4개의 노드가 있는 경우 해당일의 포함된 데이터는 ((4 x 15) / 24) x 200MB = 500MB가 됩니다. 데이터 초과분의 가격 조건이 GB당 2.30 달러일 때 노드에서 해당일 1GB의 데이터를 보내는 경우 요금은 1.15달러입니다.
 * Enterprise 계획 일일 허용량은 기본 계획을 선택한 응용 프로그램과는 공유되지 않습니다. 사용하지 않는 허용량은 다음 날로 넘어가지 않습니다. 

## <a name="examples-of-how-to-determine-distinct-node-count"></a>고유 노드 수를 결정하는 방법 예제

| 시나리오                               | 일일 총 노드 수 |
|:---------------------------------------|:----------------:|
| 1개 응용 프로그램에서 3개 Azure App Service 인스턴스 및 1개 가상 서버를 사용합니다. | 4 |
| 2개 VM에서 실행되는 3개 응용 프로그램 및 이러한 응용 프로그램의 Application Insights 리소스는 동일한 구독 및 Enterprise 계획에 있습니다. | 2 | 
| 4개의 응용 프로그램은 해당 Application Insights 리소스가 같은 구독에 있고 각 응용 프로그램은 사용률이 낮은 16시간 동안 2개의 인스턴스를 실행하고, 사용률이 최대로 높은 8시간 동안 4개의 인스턴스를 실행합니다. | 13.33 | 
| 1개 작업자 역할 및 1개 웹 역할이 부여된 클라우드 서비스에서 각 역할마다 2개 인스턴스를 실행합니다. | 4 | 
| 5개 노드 Azure Service Fabric 클러스터에서 50개의 마이크로 서비스를 실행하며 각 마이크로 서비스에서 3개의 인스턴스를 실행합니다. | 5|

* 정확한 노드 계산은 응용 프로그램에서 사용하는 Application Insights SDK에 따라 다릅니다. 
  * SDK 버전 2.2 이상에서 Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 및 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 둘 다 각 응용 프로그램 호스트를 노드로 보고합니다. 물리적 서버 및 VM 호스트에 대한 컴퓨터 이름이나 클라우드 서비스에 대한 인스턴스 이름을 예로 들 수 있습니다.  유일한 예외는 [.NET Core](https://dotnet.github.io/) 및 Application Insights Core SDK만 사용하는 응용 프로그램입니다. 이 경우 호스트 이름을 사용할 수 없으므로 모든 호스트에 대해 하나의 노드만 보고됩니다. 
  * 이전 버전의 SDK에서는 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)가 최신 SDK 버전과 마찬가지로 작동하지만, [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)는 응용 프로그램 호스트 수에 관계 없이 하나의 노드만 보고합니다. 
  * 응용 프로그램에서 SDK를 사용하여 **roleInstance**를 사용자 지정 값으로 설정하는 경우 기본적으로 이 동일한 값이 노드 수를 결정하는 데 사용됩니다. 
  * 클라이언트 컴퓨터 또는 모바일 장치에서 실행되는 앱과 함께 새 SDK 버전을 사용하는 경우, 노드 계산 시 매우 큰 숫자가 반환될 수 있습니다(클라이언트 컴퓨터 또는 모바일 장치 수가 많으므로). 
