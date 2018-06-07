---
title: Azure Monitor의 Azure Relay 메트릭(미리 보기) | Microsoft Docs
description: Azure 모니터링을 사용하여 Azure Relay 모니터링
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: b919f1fb0799ec497742115b2611f34872af2027
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure Monitor의 Azure Relay 메트릭(미리 보기)

Azure Relay 메트릭은 Azure 구독에서 리소스의 상태를 제공합니다. 풍부한 메트릭 데이터 집합을 사용하여 네임스페이스 수준뿐만 아니라 엔터티 수준에서 Relay 리소스의 전반적인 상태를 평가할 수 있습니다. 이러한 통계는 Azure Relay의 상태를 모니터링하는 데 도움을 주므로 중요할 수 있습니다. Azure 지원에 문의할 필요 없이 메트릭을 통해 근본 원인 문제를 해결할 수도 있습니다.

Azure Monitor는 다양한 Azure 서비스를 모니터링하기 위한 통합된 사용자 인터페이스를 제공합니다. 자세한 내용은 GitHub의 [Microsoft Azure에서 모니터링](../monitoring-and-diagnostics/monitoring-overview.md) 및 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) 샘플을 참조하세요.

## <a name="access-metrics"></a>메트릭에 액세스

Azure Monitor는 메트릭에 액세스하는 여러 가지 방법을 제공합니다. [Azure Portal](https://portal.azure.com)을 통해 메트릭에 액세스하거나 Azure Monitor API(REST 및 .NET) 및 Operation Management Suite 및 Event Hubs 같은 분석 솔루션을 사용할 수 있습니다. 자세한 내용은 [Azure Monitor 메트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api)을 참조하세요.

메트릭은 기본적으로 활성화되며 최근 30일분 데이터에 액세스할 수 있습니다. 더 오랜 기간에 대한 데이터를 보존해야 하는 경우 메트릭 데이터를 Azure Storage 계정에 보관할 수 있습니다. Azure Monitor의 [진단 설정](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)에서 이렇게 구성합니다.

## <a name="access-metrics-in-the-portal"></a>포털에서 메트릭에 액세스

[Azure Portal](https://portal.azure.com)에서 시간 경과에 따른 메트릭을 모니터링할 수 있습니다. 다음 예제에서는 성공한 요청 및 계정 수준에서 들어오는 요청을 확인하는 방법을 보여 줍니다.

![][1]

네임스페이스를 통해 메트릭에 직접 액세스할 수도 있습니다. 이렇게 하려면 네임스페이스를 선택한 다음 **메트릭(미리 보기)**을 클릭합니다. 

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 필터링해야 합니다.

## <a name="billing"></a>결제

Azure Monitor에서 메트릭 사용은 미리 보기 상태인 동안 현재 무료입니다. 그러나 메트릭 데이터를 수집하는 추가 솔루션을 사용하는 경우 해당 솔루션에서 요금을 청구할 수 있습니다. 예를 들어 메트릭 데이터를 Azure Storage 계정에 보관하는 경우 Azure Storage에서 요금을 청구합니다. 고급 분석을 위해 Log Analytics에 메트릭 데이터를 스트리밍할 경우 Log Analytics에서 요금을 청구합니다.

다음 메트릭은 서비스의 상태에 대한 개요를 제공합니다. 

> [!NOTE]
> 다른 이름으로 이동되므로 여러 가지 메트릭을 사용 중단하는 중입니다. 참조를 업데이트해야 할 수도 있습니다. “사용되지 않음” 키워드로 표시된 메트릭은 앞으로 지원되지 않습니다.

모든 메트릭 값은 매분마다 Azure Monitor에 전송됩니다. 시간 세분성은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 Azure Relay 메트릭에 대해 지원되는 시간 간격은 1분입니다.

## <a name="connection-metrics"></a>연결 메트릭

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| ListenerConnections-Success(미리 보기) | 지정된 기간 동안 Azure Relay에 대해 성공한 리스너 연결 수입니다. <br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|ListenerConnections-ClientError(미리 보기)|지정된 기간 동안 리스너 연결에서 발생한 클라이언트 오류 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|ListenerConnections-ServerError(미리 보기)|지정된 기간 동안 리스너 연결에서 발생한 서버 오류 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|SenderConnections-Success(미리 보기)|지정된 기간 동안 성공한 발신자 연결 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|SenderConnections-ClientError(미리 보기)|지정된 기간 동안 발신자 연결에서 발생한 클라이언트 오류 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|SenderConnections-ServerError(미리 보기)|지정된 기간 동안 발신자 연결에서 발생한 서버 오류 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|ListenerConnections-TotalRequests(미리 보기)|지정된 기간 동안의 총 리스너 연결 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|SenderConnections-TotalRequests(미리 보기)|지정된 기간 동안 발신자가 수행한 연결 요청입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|ActiveConnections(미리 보기)|지정된 기간 동안의 활성 연결 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|ActiveListeners(미리 보기)|지정된 기간 동안의 활성 리스너 수입니다.<br/><br/> 단위: 개수 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|ListenerDisconnects(미리 보기)|지정된 기간 동안 연결이 끊어진 리스너 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 형식: 총계 <br/> 차원: EntityName|
|SenderDisconnects(미리 보기)|지정된 기간 동안 연결이 끊어진 발신자 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 형식: 총계 <br/> 차원: EntityName|

## <a name="memory-usage-metrics"></a>메모리 사용 메트릭

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
|BytesTransferred(미리 보기)|지정된 기간 동안 전송된 바이트 수입니다.<br/><br/> 단위: 바이트 <br/> 집계 형식: 총계 <br/> 차원: EntityName|

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Relay는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다. 메트릭에 차원을 추가하는 것은 선택 사항입니다. 차원을 추가하지 않는 경우 메트릭은 네임스페이스 수준에서 지정됩니다. 

|차원 이름|설명|
| ------------------- | ----------------- |
|EntityName| Azure Relay는 네임스페이스에서 메시징 엔터티를 지원합니다.|

## <a name="next-steps"></a>다음 단계

[Azure Monitor 개요](../monitoring-and-diagnostics/monitoring-overview.md)를 참조하세요.

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




