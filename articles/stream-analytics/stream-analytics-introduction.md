---
title: Azure Stream Analytics의 개요
description: IoT(사물 인터넷)의 스트리밍 데이터를 실시간으로 분석하도록 도와주는 관리 서비스인 Stream Analytics에 대해 알아봅니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.workload: data-services
ms.custom: mvc
ms.date: 03/27/2018
ms.openlocfilehash: 4d68ee7f38c317b4ee42f5b40cc4c4196114e159
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-stream-analytics"></a>Stream Analytics란 무엇인가요?

Azure Stream Analytics는 장치에서 대용량의 데이터 스트리밍을 검사할 수 있도록 하는 이벤트 처리 엔진입니다. 들어오는 데이터는 장치, 센서, 웹 사이트, 소셜 미디어 피드, 응용 프로그램 등에서 가져올 수 있습니다. 또한 데이터 스트림의 정보 압축, 패턴 및 관계 식별을 지원합니다. 이러한 패턴을 사용하여 경고, 보고 도구에 대한 피드 정보와 같은 다른 작업 다운스트림을 트리거하거나 향후 사용을 위해 저장할 수 있습니다.

다음은 Azure Stream Analytics를 사용할 수 있는 몇 가지 예입니다. 

* 장치 원격 분석에서 IoT(사물 인터넷) 센서 퓨전 및 실시간 분석
* 웹 로그/클릭 스트림 분석
* fleet 관리 및 드라이버가 없는 자동차에 대한 지리 공간적 분석
* 원격 모니터링 및 높은 가치 자산의 예측 유지 관리
* 인벤토리 제어 및 변칙 검색에 대한 판매 시점 데이터에 대한 실시간 분석

## <a name="how-does-stream-analytics-work"></a>Stream Analytics는 어떻게 작동합니까?

Azure Stream Analytics는 Azure Event Hub, Azure IoT Hub로 수집된 스트리밍 데이터의 원본 또는 Azure Blob Storage와 같은 데이터 저장소에서 시작합니다. 스트림을 검사하려면 데이터를 스트리밍하는 입력 원본을 지정하는 Stream Analytics 작업을 만듭니다. 작업은 또한 데이터, 패턴 또는 관계를 검색하는 방법을 지정하는 변환 쿼리를 지정합니다. 변환 쿼리는 기간에 따라 스트리밍 데이터를 필터링, 정렬, 집계 및 조인하는 데 사용되는 SQL과 유사한 쿼리 언어를 활용합니다. 작업을 실행할 때 이벤트 순서 지정 옵션, 집계 작업을 수행할 때 시간 창의 기간을 조정할 수 있습니다.

들어오는 데이터를 분석한 후 변환된 데이터에 대한 출력을 지정하고 분석한 정보에 대한 응답으로 수행할 작업을 제어할 수 있습니다. 예를 들어 다음과 같은 작업을 수행할 수 있습니다.

* 모니터링되는 큐에 데이터를 보내 사용자 지정 워크플로 다운스트림을 트리거합니다.
* Power BI 대시보드에 실시간 시각화에 대한 데이터를 보냅니다.
* 다른 Azure 저장소 서비스에 데이터를 보관합니다.

다음 이미지에서는 Stream Analytics 파이프라인을 보여줍니다. Stream Analytics 작업은 모두를 사용하거나 입력하고 출력하는 경우 선택된 설정을 사용할 수 있습니다. 이 이미지는 데이터가 Stream Analytics에 전송되고, 저장소 또는 프레젠테이션과 같은 다른 작업에 대해 분석되고 전송되는 방법을 보여줍니다.

![Stream Analytics 파이프라인](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

## <a name="key-capabilities-and-benefits"></a>주요 기능 및 이점

Azure Stream Analytics는 사용하기 쉽고 유연하며 안정적이고 작업의 규모에 맞게 확장 가능하도록 설계되었습니다. 소버린 클라우드 뿐만 아니라 여러 데이터 센터 간에 사용 가능합니다. 다음 이미지에서는 Azure Stream Analytics의 주요 기능을 보여줍니다.

![Stream Analytics 주요 기능](./media/stream-analytics-introduction/stream_analytics_key_capabilities.png)

## <a name="ease-of-getting-started"></a>시작의 용이성

Azure Stream Analytics는 시작하기 쉽습니다. 몇 번의 클릭으로 여러 원본 및 싱크에 연결하고 종단 간 파이프라인을 만듭니다. Stream Analytics는 스트리밍 데이터 수집을 위해 [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/)에 연결할 수 있습니다. 기록 데이터를 수집하는 [Azure Blob 저장소](https://docs.microsoft.com/azure/storage/storage-introduction) 서비스에 연결할 수도 있습니다. 이벤트 허브의 데이터를 다른 데이터 원본 및 처리 엔진과 결합할 수 있습니다. 작업 입력은 정적이거나 느리게 변하는 데이터인 참조 데이터를 포함할 수도 있으며 조회 작업을 수행하기 위해 이 참조 데이터에 스트리밍 데이터를 조인할 수 있습니다.

Stream Analytics는 [Azure Blob](https://docs.microsoft.com/azure/storage/storage-introduction), [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/), [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/) 또는 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)와 같은 많은 저장소 시스템에 작업 출력을 라우팅할 수 있습니다. 저장한 후 Azure HDInsight를 사용하여 일괄 처리 분석을 실행하거나 소비를 위해 이벤트 허브와 같은 다른 서비스 또는 Power Bi 스트리밍 API를 사용하여 실시간 시각화를 위해 [Power BI](https://docs.microsoft.com/power-bi/)에 출력을 전송할 수 있습니다.

## <a name="programmer-productivity"></a>프로그래머 생산성

Azure Stream Analytics는 강력한 임시 제약 조건으로 강화된 간단한 SQL 기반 쿼리 언어를 사용하여 이동 중인 데이터를 분석합니다. 작업 변환을 정의하려면 간단한 SQL 구문을 사용하여 복잡한 임시 쿼리 및 분석을 작성하도록 하는 간단하고 선언적인 [Stream Analytics 쿼리 언어](https://msdn.microsoft.com/library/azure/dn834998.aspx)를 사용합니다. Stream Analytics 쿼리 언어는 SQL 언어와 일관되며 SQL 언어와의 친숙함은 작업 만들기를 시작하는 데 충분합니다. Azure PowerShell, [Stream Analytics Visual Studio 도구](stream-analytics-tools-for-visual-studio-install.md) 또는 Azure Resource Manager 템플릿과 같은 개발자 도구를 사용하여 작업을 만들 수도 있습니다. 개발자 도구를 사용하면 변환 쿼리를 오프라인에서 개발하고 [CI/CD 파이프라인](stream-analytics-tools-for-visual-studio-cicd.md)을 사용하여 Azure에 작업을 제출할 수 있습니다. 

Stream Analytics 쿼리 언어는 스트리밍 데이터 분석 및 처리를 위한 다양한 배열의 함수를 제공합니다. 이 쿼리 언어는 복잡한 지역 공간 함수에 대한 간단한 데이터 조작, 집계 함수를 지원합니다. 포털에서 쿼리를 편집하고 라이브 스트림에서 추출된 샘플 데이터를 사용하여 테스트할 수 있습니다.

추가 함수를 정의하고 호출하여 쿼리 언어의 기능을 확장할 수 있습니다. Azure Machine Learning 서비스에서 함수 호출을 정의하여 Azure Machine Learning 솔루션을 활용하고 JavaScript UDF(사용자 정의 함수) 또는 사용자 정의 집계를 통합하여 Stream Analytics 쿼리의 일환으로 복잡한 계산을 수행할 수 있습니다.

## <a name="fully-managed"></a>완전히 관리 

Azure Stream Analytics는 Azure에서 완벽하게 관리되는 서버가 없는(PaaS) 제품입니다. 즉, 모든 하드웨어를 프로비전하거나 작업을 실행하는 클러스터를 관리할 필요가 없습니다. Azure Stream Analytics는 클라우드에서 복잡한 계산 클러스터 설정 및 작업 실행에 필요한 성능 조정을 처리하여 완벽하게 작업을 관리합니다. Azure Event Hubs 및 Azure IoT Hub와 통합하면 작업에서 연결된 장치, 클릭 스트림 및 로그 파일에서 발생하는 초당 수백만 개의 이벤트를 수집할 수 있습니다. 이벤트 허브의 파티션 기능을 사용하면 계산을 논리 단계로 분할할 수 있고, 좀 더 분할하면 확장성을 높일 수 있습니다.

## <a name="low-total-cost-of-ownership"></a>낮은 총 소유 비용

클라우드 서비스인 Stream Analytics는 비용에 최적화되어 있습니다. 선불 비용이 없습니다. [사용하는 스트리밍 단위](stream-analytics-streaming-unit-consumption.md) 및 처리된 데이터의 양에 대해서만 지불합니다. 약정 또는 필요한 클러스터 프로비전이 없습니다. 비즈니스 요구 사항에 따라 스트리밍 작업을 확장 또는 축소할 수 있습니다. 

## <a name="reliability"></a>안정성 

관리되는 서비스로 Stream Analytics는 99.9%의 가용성으로 이벤트 처리를 보장하고, 데이터 손실을 방지하며, 비즈니스 연속성을 제공합니다. 자세한 내용은 [Stream Analytics SLA](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) 페이지를 참조하세요. Stream Analytics는 매초 수백만 개의 이벤트를 처리할 수 있으며 짧은 대기 시간으로 결과를 제공할 수 있습니다.
Stream Analytics는 정확히 한 번의 이벤트 처리 및 한 번 이상의 이벤트 배달을 보장합니다. 이벤트 배달에 실패하는 경우에 기본 제공 복구 기능이 있습니다. Stream Analytics는 작업의 상태를 내부적으로 유지 관리할 수 있으며, 사용자는 마지막 출력 시간부터 작업을 시작할 수 있고, 항상 동일한 결과를 제공하여 반복 가능한 결과를 제공합니다. Stream Analytics의 이 기능을 통해 특정 시점으로 복구하고 근본 원인 분석을 수행하는 경우 계산을 조사할 수 있습니다. 

## <a name="performance"></a>성능

Azure Stream Analytics는 높은 성능을 위해 최적화되었으며, 스트리밍 데이터를 처리하고 메모리 계산을 수행할 수 있습니다. 실시간 및 복잡한 이벤트 처리 응용 프로그램을 처리하기 위해 확장 또는 축소할 수 있습니다. Stream Analytics는 분할하여 성능을 지원합니다. 복잡한 쿼리는 병렬화되고 여러 스트리밍 노드에서 실행될 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 Azure Stream Analytics의 개요를 살펴봤습니다. 다음으로 심층적으로 알아보고 첫 번째 Stream Analytics 작업을 만들 수 있습니다.

* [Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
