---
title: SQL Data Warehouse와 통합된 솔루션 구축 | Microsoft Docs
description: 'SQL Data Warehouse와 통합된 솔루션과 파트너 및 도구 '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>SQL Data Warehouse와 기타 서비스 통합
핵심 기능 외에도 SQL Data Warehouse는 사용자가 Azure에서 다양한 기타 서비스를 통합할 수 있게 설정합니다. 일부 이러한 서비스에는 다음이 포함됩니다.

* Power BI
* Azure 데이터 팩터리
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse는 더 많은 [통합 파트너](sql-data-warehouse-partner-data-integration.md) 및 Azure에서 더 많은 서비스와 통합을 계속합니다.

## <a name="power-bi"></a>Power BI
Power BI 통합을 통해 Power BI의 동적 보고 및 시각화와 SQL Data Warehouse의 계산 능력을 결합할 수 있습니다. Power BI 통합에는 현재 다음이 포함됩니다.

* **직접 연결**: SQL Data Warehouse에 대해 논리적 푸시다운을 통한 보다 고급 연결. 푸시다운은 더 큰 규모를 더욱 빠르게 분석합니다.
* **Power BI에서 열기**: 'Power BI에서 열기' 단추를 통해 인스턴스 정보를 Power BI에 전달하여 간단한 연결 방법을 제공합니다.

자세한 내용은 [Power BI와 통합](sql-data-warehouse-get-started-visualize-with-power-bi.md) 또는 [Power BI 설명서](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx)를 참조하세요.

## <a name="azure-data-factory"></a>Azure 데이터 팩터리
Azure 데이터 팩터리는 복잡한 추출을 만들고 파이프라인을 로드할 수 있는 관리되는 플랫폼을 사용자에게 제공합니다. Azure Data Factory와 SQL Data Warehouse의 통합에는 다음이 포함됩니다.

* **저장 프로시저**: SQL Data Warehouse에서 저장 프로시저의 실행을 오케스트레이션합니다.
* **복사**: ADF를 사용하여 SQL Data Warehouse로 데이터를 이동합니다. 이 작업에서는 백그라운드에서 ADF의 표준 데이터 이동 메커니즘 또는 PolyBase가 사용될 수 있습니다. 

자세한 내용은 [Azure Data Factory와 통합](sql-data-warehouse-get-started-visualize-with-power-bi.md)을 참조하세요.

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning은 사용자가 다양한 예측 도구를 활용하여 복잡한 모델을 만들 수 있는 완전히 관리되는 분석 서비스입니다. SQL Data Warehouse는 다음 기능을 포함하는 이러한 모델에 대한 소스 및 대상으로 지원됩니다.

* **데이터 읽기:** SQL Data Warehouse에 대해 T-SQL을 사용하는 규모에 따른 드라이브 모델입니다.
* **데이터 쓰기:** 모델에서 SQL Data Warehouse로 변경 사항을 뒤로 커밋합니다.

자세한 내용은 [Azure Machine Learning과 통합](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)을 참조하세요.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics는 Azure Event Hub에서 생성된 이벤트 데이터를 처리 및 사용하기 위한 복잡하고 완전히 관리되는 인프라입니다.  SQL Data Warehouse와 통합을 통해 스트리밍 데이터를 효과적으로 처리하고 관계형 데이터와 함께 저장하여 보다 심도있는 고급 분석이 가능합니다.  

* **작업 출력:** Stream Analytics 작업에서 SQL Data Warehouse로 출력을 직접 보냅니다.

자세한 내용은 [Azure Stream Analytics와 통합](sql-data-warehouse-integrate-azure-stream-analytics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure SQL Database와 통합하려면 [SQL Database 탄력적 쿼리 구성](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)을 참조

