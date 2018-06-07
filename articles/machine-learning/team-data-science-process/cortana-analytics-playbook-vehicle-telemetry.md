---
title: 차량 상태 및 주행 습관 예측 - Azure | Microsoft Docs
description: Cortana Intelligence의 기능을 사용하여 차량 상태 및 주행 습관에 대한 예측 가능한 통찰력 및 실시간 정보를 얻습니다.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 6fa8f11d48909a003deb976ad4d7408e84694222
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>차량 원격 분석 솔루션 플레이북
이 메뉴는 이 플레이북 장에 연결됩니다. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>개요
슈퍼 컴퓨터가 랩 밖으로 나와 이제 차고에 주차되어 있습니다. 이제 많은 센서를 포함하는 첨단 자동차에 배치되고 있습니다. 이러한 센서를 통해 매순간 수백만 개의 이벤트를 추적하고 모니터링할 수 있습니다. 2020년이 되면 이러한 차량 대부분이 인터넷에 연결될 것입니다. 이러한 엄청난 데이터를 활용하여 더 안전하고 안정성이 높은 좋은 승차 환경을 제공할 수 있습니다. Microsoft는 Cortana Intelligence를 통해 이 꿈을 현실화했습니다.

Cortana 인텔리전스는 데이터를 지능형 작업으로 변환하는 데 사용되는 완전히 관리되는 빅데이터 및 고급 분석 제품군입니다. Cortana Intelligence 차량 원격 분석 솔루션 템플릿은 자동차 대리점, 자동차 제조업체 및 보험 회사가 차량 상태 및 주행 습관에 대한 예측 가능한 정보를 실시간으로 얻을 수 있는 방법을 보여줍니다.

이 솔루션은 실시간 일괄 처리를 위해 Cortana Intelligence 플랫폼의 전체 잠재력을 보여 주는 [람다 아키텍처 패턴](https://en.wikipedia.org/wiki/Lambda_architecture) 으로 구현되었습니다.

## <a name="architecture"></a>아키텍처
다음 다이어그램은 차량 원격 분석 솔루션 아키텍처를 보여 줍니다.

![솔루션 아키텍처 다이어그램](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


이 솔루션은 다음 Cortana Intelligence 구성 요소를 포함하고 통합을 보여줍니다.

* **Azure Event Hubs**는 수백만 개의 차량 원격 분석 이벤트를 Azure에 수집합니다.
* **Stream Analytics**는 차량 상태에 대한 실시간 통찰력을 제공하고 다양한 배치 분석을 위해 해당 데이터를 장기간용 저장소에 보관합니다.
* **Azure Machine Learning**은 이상적으로 이상을 검색하고 일괄 처리를 사용하여 예측적인 통찰력을 제공합니다.
* **Azure HDInsight**는 확장 시 데이터를 변환합니다.
* **Azure Data Factory**는 일괄 처리 파이프라인의 오케스트레이션, 예약, 리소스 관리 및 모니터링을 처리합니다.
* **Power BI** - 실시간 데이터 및 예측 분석 시각화를 위해 이 솔루션을 다양한 대시보드에 제공합니다.

이 솔루션은 서로 다른 두 개의 데이터 원본에 액세스합니다. 

* **시뮬레이트된 차량 신호 및 진단**: 차량 텔레매틱스 시뮬레이터는 지정된 시간에 차량의 상태 및 주행 패턴에 해당하는 진단 정보 및 신호를 내보냅니다. 
* **차량 카탈로그**: 이 참조 데이터 집합은 모델에 VIN 수를 매핑합니다.

