---
title: "특정 위치에서 Azure 지역에 연결할 때의 상대적 대기 시간 보기 | Microsoft Docs"
description: "특정 위치에서 Azure 지역에 연결할 때 인터넷 공급자 간 상대적 대기 시간을 확인하는 방법을 알아봅니다."
services: network-watcher
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: a6c2ffa619eeff8b455df8a8b2157525af12c640
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>특정 위치에서 Azure 지역에 연결할 때의 상대적 대기 시간 보기

이 자습서에서는 Azure [Network Watcher](network-watcher-monitoring-overview.md) 서비스를 사용하여 사용자 인구 통계를 기준으로 응용 프로그램 또는 서비스를 배포할 Azure 지역을 결정하는 데 도움을 얻는 방법을 알아봅니다. 또한 이 서비스를 사용하여 Azure에 대한 서비스 공급자 연결을 평가할 수 있습니다.  
        
## <a name="create-a-network-watcher"></a>네트워크 감시자 만들기

하나 이상의 Azure [지역](https://azure.microsoft.com/regions)에 네트워크 감시자가 이미 있는 경우 이 섹션의 작업을 건너뛰어도 됩니다. 네트워크 감시자에 대한 리소스 그룹을 만듭니다. 이 예제에서는 리소스 그룹이 미국 동부 지역에서 만들어지지만 어떤 Azure 지역에서도 리소스 그룹을 만들 수 있습니다.

```powershell
New-AzureRmResourceGroup -Name NetworkWatcherRG -Location eastus
```

네트워크 감시자를 만듭니다. 하나 이상의 Azure 지역에서 만든 네트워크 감시자가 있어야 합니다. 이 예제에서는 미국 동부 Azure 지역에 네트워크 감시자가 만들어집니다.

```powershell
New-AzureRmNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>특정 위치에서 단일 Azure 지역에 연결할 때의 상대적 네트워크 대기 시간 비교

서비스 공급자를 평가하거나, 특정 위치에서 서비스가 배포된 Azure 지역에 연결할 때 "사이트가 느림"과 같은 문제를 해결합니다. 예를 들어, 다음 명령은 2017년 12월 13~15일 동안 미국 워싱턴주와 미국 서부 2 Azure 지역 간의 평균 상대 인터넷 서비스 공급자 대기 시간을 반환합니다.

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> 네트워크 감시자를 검색할 때 이전 명령에서 지정된 지역과 같은 지역을 지정할 필요는 없습니다. 이전 명령에서는 기존 네트워크 감시자를 지정하기만 하면 됩니다. 네트워크 감시자는 어떤 지역에도 있을 수 있습니다. `-Country` 및 `-State`에 대한 값을 지정할 때는 유효한 값을 지정해야 합니다. 이 값은 대/소문자를 구분합니다. 제한된 수의 국가, 시/도 및 구/군/시에 대한 데이터를 사용할 수 있습니다. [사용 가능한 국가, 시/도, 구/군/시 및 공급자 보기](#view-available)의 명령을 실행하여 이전 명령에서 사용할 사용 가능한 국가, 시/도 및 구/군/시 목록을 확인합니다. 

> [!WARNING]
> `-StartTime` 및 `-EndTime`에 대해 2017년 11월 14일 이후 날짜를 지정해야 합니다. 2017년 11월 14일 이전 날짜를 지정하면 데이터가 반환되지 않습니다. 

이전 명령의 출력은 다음과 같습니다.

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

반환된 출력에서 **Score** 값은 지역 및 공급자 간 상대적 대기 시간입니다. 점수 1은 가장 나쁜(높은) 대기 시간이고, 100은 가장 낮은 대기 시간입니다. 상대적 대기 시간은 해당 날짜에 대한 평균입니다. 이전 예제에서는 대기 시간이 두 날짜에서 동일했으며, 두 공급자의 대기 시간이 약간만 차이를 보였지만, 두 공급자의 대기 시간이 1-100 점수 척도에서 낮은 수준을 보였습니다. 이렇게 예상할 수 있지만, 미국의 워싱턴주가 미국 서부 2 Azure 지역과 실제로 가깝기 때문에 결과가 예상과 다른 경우가 가끔 있습니다. 지정하는 날짜 범위가 클수록, 시간에 따른 평균 대기 시간이 더 커질 수 있습니다.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>특정 위치에서 여러 Azure 지역에 연결할 때의 상대적 네트워크 대기 시간 비교

`-Location`을 사용하여 특정 위치와 특정 Azure 지역 간의 상대적 대기 시간을 지정하는 대신, 특정 물리적 위치에서 모든 Azure 지역에 연결할 때의 상대적 대기 시간을 확인하려는 경우에도 이 작업을 수행할 수 있습니다. 예를 들어, 다음 명령은 기본 사용자가 워싱턴주에 있는 Comcast 사용자인 경우 서비스를 배포할 Azure 지역을 평가하는 데 도움이 됩니다.

```powershell
Get-AzureRmNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

>[!NOTE]
단일 위치를 지정할 때와 달리, 위치를 지정하지 않거나, 여러 위치("미국 서부 2", "미국 서부")를 지정하는 경우 명령을 실행할 때 인터넷 서비스 공급자를 지정해야 합니다. 

## <a name="view-available"></a>사용 가능한 국가, 시/도, 구/군/시 및 공급자 보기

특정 인터넷 서비스 공급자, 국가, 시/도 및 구/군/시에 대한 데이터를 사용할 수 있습니다. 데이터를 볼 수 있는 모든 인터넷 서비스 공급자, 국가, 시/도 및 구/군/시 목록을 보려면 다음 명령을 입력합니다.

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

이전 명령에 의해 반환된 국가, 시/도 및 구/군/시에 대한 데이터만 사용할 수 있습니다. 이전 명령에서는 기존 네트워크 감시자를 지정해야 합니다. 이 예제에서는 *NetworkWatcherRG*라는 리소스 그룹에 *NetworkWatcher_eastus* 네트워크 감시자를 지정했지만, 기존의 어떤 네트워크 감시자도 지정할 수 있습니다. 기존 네트워크 감시자가 없는 경우 [네트워크 감시자 만들기](#create-a-network-watcher)의 작업을 완료하여 만듭니다. 

이전 명령을 실행한 후에 **국가**, **시/도** 및 **구/군/시**에 대해 유효한 값을 지정하여 반환된 출력을 필터링할 수 있습니다(원할 경우).  예를 들어, 미국, 워싱턴주, 시애틀에서 사용할 수 있는 인터넷 서비스 공급자의 목록을 보려면 다음 명령을 입력합니다.

```powershell
Get-AzureRmNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> **국가**에 대해 지정된 값은 대문자와 소문자로만 구성되어야 합니다. **시/도** 및 **구/군/시**에 대해 지정된 값은 소문자로만 구성되어야 합니다. **국가**, **시/도** 및 **구/군/시**에 대해 값을 지정하지 않고 명령을 실행한 후에 반환되는 출력에는 값이 나열됩니다. 잘못된 사례를 지정하거나 이러한 속성 값 없이 명령을 실행한 후에 반환된 출력에 없는 **국가**, **시/도**, 또는 **구/군/시** 값을 지정하면 반환된 출력이 비어 있습니다.
