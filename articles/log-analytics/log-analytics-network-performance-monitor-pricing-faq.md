---
title: Azure 네트워크 성능 모니터의 가격 책정 FAQ | Microsoft Docs
description: 질문과 대답 - Azure 네트워크 성능 모니터
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 1e7e43dc2e7ed386f8f77fd1ab186d2ff34af405
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Azure 네트워크 성능 모니터에 대한 가격 책정 변경

사용자의 의견을 수렴하여 최근에 Azure의 다양한 모니터링 서비스에 대한 [새로운 가격 책정 환경](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)을 도입했습니다. 이 문서는 Azure [NPM(네트워크 성능 모니터)](https://docs.microsoft.com/azure/networking/network-monitoring-overview)과 관련된 가격 책정 변경 사항을 읽기 쉽게 질문과 대답 형식으로 담아낸 것입니다.

네트워크 성능 모니터는 세 가지 구성 요소로 이루어집니다.
* [성능 모니터](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [서비스 엔드포인트 모니터](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [ExpressRoute 모니터](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

다음 섹션에서는 NPM 구성 요소에 대한 가격 책정 변경 내용을 설명합니다.

## <a name="performance-monitor"></a>성능 모니터링

**이전 모델에서는 성능 모니터가 어떻게 청구되었나요?**

NPM의 청구는 사용량 및 소비량의 두 가지 구성 요소를 기준으로 했습니다.
* **노드**: 모든 가상 트랜잭션은 노드에서 시작되고 종료됩니다. 노드를 에이전트 또는 Microsoft Management Agents라고도 합니다.
* **데이터**: 다양한 네트워크 테스트 결과는 Azure Log Analytics 리포지토리에 저장됩니다.

이전 모델에서 청구서는 노드 수 및 생성된 데이터의 볼륨에 따라 계산되었습니다. 

**새 모델에서는 성능 모니터 사용 요금이 어떻게 청구되나요?**

이제 NPM의 성능 모니터 기능은 다음 조합을 기준으로 요금이 청구됩니다. 

* 모니터링된 서브넷 링크
* 데이터 볼륨

**서브넷 링크란 무엇인가요?**

성능 모니터는 네트워크에서 두 개 이상의 위치 간 연결을 모니터링합니다. 한 서브넷에 있는 노드 또는 에이전트 그룹과 다른 서브넷에 있는 노드 그룹 간의 연결을 서브넷 링크라고 합니다.

**두 개의 서브넷(A와 B)이 있고 각 서브넷에 여러 에이전트가 있습니다. 성능 모니터는 서브넷 A에 있는 모든 에이전트에서 서브넷 B에 있는 모든 에이전트까지의 연결을 모니터링합니다. 그러면 서브넷 간 연결의 수에 따라 요금이 청구되나요?**

번호 요금을 청구하기 위해 서브넷 A에서 서브넷 B로의 모든 연결은 하나의 서브넷 링크로 그룹화됩니다. 그리고 단일 연결에 대한 요금이 청구됩니다. 성능 모니터는 각 서브넷에 있는 다양한 에이전트 간의 연결을 계속 모니터링하게 됩니다.

**서브넷 링크를 모니터링하기 위한 요금은 얼마인가요?**

단일 서브넷 링크의 한 달 모니터링 비용은 [Ping 메시](https://azure.microsoft.com/pricing/details/network-watcher/) 섹션을 참조하세요.

**성능 모니터에서 생성하는 데이터 요금은 얼마인가요?**

데이터 수집(Log Analytics에 데이터 업로드, 데이터 처리 및 인덱싱) 요금은 데이터 수집 섹션에서 Log Analytics에 대한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/log-analytics/)에서 확인할 수 있습니다. 데이터 보존(즉, 첫 번째 달 이후 고객의 옵션에 의해 유지된 데이터) 요금은 데이터 보존 섹션의 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/log-analytics/)에서 확인할 수 있습니다.


## <a name="expressroute-monitor"></a>ExpressRoute 모니터

**ExpressRoute 모니터의 사용에 대한 요금은 얼마인가요?**

ExpressRoute 모니터에 대한 요금은 모니터링 중에 생성된 데이터 볼륨에 따라 청구됩니다. 자세한 내용은 "성능 모니터에서 생성하는 데이터 요금은 얼마인가요?"를 참조하세요.

**ExpressRoute 모니터를 사용하여 여러 ExpressRoute 회로를 모니터링하고 있습니다. 그러면 모니터링하고 있는 회로의 수에 따라 요금이 청구되나요?**

비용은 회로의 수 또는 피어링의 형식(예: 개인 피어링, Microsoft 피어링)에 따라 청구되지 않습니다. 앞에서 설명했듯이, 데이터 볼륨을 기준으로 요금이 청구됩니다.

**ExpressRoute가 단일 회로를 모니터링할 때 생성되는 데이터 볼륨은 어떻게 되나요?**

ExpressRoute가 개인 피어링 연결을 모니터링할 때 생성되는 매달 데이터 볼륨은 다음과 같습니다.

|백분위수      |데이터/월(MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


이 표에 따라 50번째 백분위에 속하는 고객은 데이터 192MB에 대한 비용을 지불합니다. 첫 번째 달 USD $2.30/GB에서 회로 모니터링에 대해 발생한 비용은 USD 0.43(= 192 * 2.30 / 1024)입니다.

**데이터의 볼륨에 차이가 있는 이유는 무엇입니까?**

생성되는 모니터링 데이터 볼륨은 다음과 같은 몇 가지 요인에 따라 달라집니다.
* 에이전트 수. 에이전트 수가 증가하면 오류 격리의 정확도가 증가합니다.
* 네트워크의 홉 수.
* 원본과 대상 간의 경로 수.

더 높은 백분위(앞의 표에서)에 속하는 고객은 일반적으로 온-프레미스 네트워크에 몇 가지 유리한 지점에서 해당 회로를 모니터링합니다. 또한 여러 에이전트는 네트워크에서 더 깊이, 서비스 공급자 에지 라우터에서 더 멀리 배치됩니다. 종종 에이전트는 데이터 센터의 여러 사용자 사이트, 분기 및 랙에 배치됩니다.

## <a name="service-endpoint-monitor"></a>서비스 끝점 모니터

**서비스 엔드포인트 모니터의 사용에 대한 요금은 얼마인가요?**

서비스 엔드포인트 모니터 사용 요금은 다음을 기준으로 계산됩니다.
* 연결 수
* 데이터 볼륨

**연결이란 무엇인가요?**

연결이란 한 달 동안 단일 에이전트에서 한 엔드포인트(URL 또는 네트워크 서비스)로의 연결 가능성에 대한 테스트입니다. 예를 들어 세 개의 에이전트에서 bing.com으로의 연결 모니터링은 세 가지 연결로 구성됩니다.

**서비스 엔드포인트 모니터에 대한 비용은 얼마인가요?**

한 달 간 엔드포인트를 모니터링하는 데 드는 비용은 [연결 모니터링](https://azure.microsoft.com/pricing/details/network-watcher/) 섹션을 참조하세요. 데이터 요금은 데이터 수집 섹션에서 Log Analytics에 대한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/log-analytics/)에서 확인할 수 있습니다.

## <a name="references"></a>참조

[Log Analytics 가격 책정 FAQ](https://azure.microsoft.com/pricing/details/log-analytics/): FAQ 섹션에는 무료 계층, 노드당 가격 책정, 기타 가격 책정 정보가 있습니다.

