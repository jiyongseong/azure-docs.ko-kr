---
title: 'Azure Data Factory: 질문과 대답 | Microsoft Docs'
description: Azure Data Factory에 대한 질문과 대답입니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: shlo
ms.openlocfilehash: 8c240e1a654c80c34f6b612d9126058e5d67c4c2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-data-factory-faq"></a>Azure Data Factory FAQ
이 문서는 Azure Data Factory 서비스의 버전 2에 적용됩니다. Data Factory에 대한 질문과 대답입니다.  

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1의 질문과 대답](v1/data-factory-faq.md)을 참조하세요.

## <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요? 
Data Factory는 데이터의 이동과 변환을 자동화하는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 장비를 작동하여 원자재를 완제품으로 변형하는 공장처럼 Azure Data Factory는 원시 데이터를 수집하여 바로 사용할 수 있는 정보로 변환하는 기존 서비스를 오케스트레이션합니다. 

Azure Data Factory를 사용하여 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동하는 데이터 기반 워크플로를 만들 수 있습니다. 또한 Azure HDInsight, Azure Data Lake Analytics 및 SSIS(SQL Server Integration Services) 같은 계산 서비스를 사용하여 데이터를 처리하고 변환할 수 있습니다. 

Data Factory를 사용하여 Azure 기반 클라우드 서비스에서 데이터 처리를 실행하거나 SSIS, SQL Server 및 Oracle과 같은 자체 호스팅 계산 환경을 활용할 수 있습니다. 필요한 작업을 수행하는 파이프라인을 만든 후 정기적(예: 매시간, 매일 또는 매주)으로 실행되도록 예약하거나 이벤트 발생 시 파이프라인을 트리거할 수 있습니다. 자세한 내용은 [Azure Data Factory 소개](introduction.md)를 참조하세요.

## <a name="whats-different-in-version-2"></a>버전 2의 다른 점은 무엇입니까?
Azure Data Factory 버전 2는 원래 Azure Data Factory 데이터 이동 및 변환 서비스를 기반으로 더 넓은 클라우드 우선 데이터 통합 시나리오 집합으로 확장됩니다. Azure Data Factory 버전 2는 다음과 같은 기능을 제공합니다.

- 제어 흐름 및 크기 조정
- Azure에서 SSIS 패키지 배포 및 실행

버전 1 릴리스 후에 고객이 클라우드, 온-프레미스 및 클라우드 VM(가상 머신)에서 데이터 이동과 처리가 모두 필요한 복잡한 하이브리드 데이터 통합 시나리오를 필요로 한다는 사실을 알았습니다. 이러한 요구 사항에 따라 보안되는 가상 네트워크 환경 내에서 데이터를 전송 및 처리하고 요청 시 처리 능력을 통해 확장할 필요가 있습니다.

데이터 파이프라인이 비즈니스 분석 전략의 중요한 부분이 됨에 따라, 우리는 이러한 증가하는 데이터 로드 및 이벤트 트리거형 실행을 지원하는 유연한 일정 계획이 필요하다는 것을 확인했습니다. 복잡성이 늘어나면서 서비스가 분기, 루프 및 조건 처리를 포함한 일반적인 워크플로 패러다임을 지원해야 한다는 요구 사항도 증가하고 있습니다.

버전 2를 사용하여 기존 SSIS 패키지를 클라우드로 마이그레이션할 수도 있습니다. 이 작업은 SSIS를 Data Factory 내에서 관리되는 Azure 서비스로 전환하여 통합 런타임의 새로운 기능을 사용할 수 있도록 합니다. 버전 2에서 SSIS 통합 런타임을 확장함으로써 클라우드에서 SSIS를 실행, 관리, 모니터링 및 빌드할 수 있습니다.

### <a name="control-flows-and-scale"></a>제어 흐름 및 크기 조정 
현대적인 데이터 웨어하우스의 다양한 통합 흐름 및 패턴을 지원하기 위해 Data Factory는 더 이상 시계열 데이터와 결합하지 않는 유연한 새 데이터 파이프라인 모델을 사용할 수 있도록 했습니다. 이 릴리스에서는 데이터 파이프라인 제어 흐름의 조건 및 분기를 모델링하고 이러한 흐름 내에서 그리고 이러한 흐름 간에 명시적으로 매개 변수를 전달할 수 있습니다.

이제 데이터 통합에 필요한 모든 흐름 유형을 자유로이 모델링하고 요청 시 또는 일정에 따라 반복해서 디스패치할 수 있습니다. 이제 이전에는 불가능했던 몇 가지 일반적인 흐름이 가능해졌습니다. 예:   

- 제어 흐름:
    - 파이프라인 내에서 시퀀스의 작업을 체이닝합니다.
    - 파이프라인 내에서 작업을 분기합니다.
    - 매개 변수
        - 파이프라인 수준에서 매개 변수를 정의하고 요청 시 또는 트리거에서 파이프라인을 호출하는 동안 인수를 전달합니다.
        - 파이프라인에 전달되는 인수를 작업에 사용할 수 있습니다.
    - 사용자 지정 상태 전달
        - 상태를 포함한 작업 출력을 파이프라인의 이후 작업에서 사용할 수 있습니다.
    - 컨테이너 루프화
        - For-each 
- 트리거 기반 흐름:
    - 요청 시 또는 벽시계 시간에 따라 파이프라인을 트리거할 수 있습니다.
- 델타 흐름:
    - 차원 또는 참조 테이블을 온-프레미스 또는 클라우드의 관계형 저장소에서 이동하여 데이터를 레이크에 로드하는 동시에 매개 변수를 사용하고 데이터 복사용 높은 워터마크를 정의합니다. 

자세한 내용은 [자습서: 제어 흐름](tutorial-control-flow.md)을 참조하세요.

### <a name="deploy-ssis-packages-to-azure"></a>Azure에 SSIS 패키지 배포 
SSIS 워크로드를 이동하려는 경우 Data Factory 버전 2를 만들고 Azure-SSIS Integration Runtime을 프로비전할 수 있습니다. Azure-SSIS Integration Runtime은 클라우드에서 SSIS 패키지 실행을 전담하는 완전히 관리되는 Azure VM(노드) 클러스터입니다. 단계별 지침은 [Azure에 SSIS 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md) 자습서를 참조하세요. 
 

### <a name="sdks"></a>SDK
고급 사용자로서 프로그래밍 인터페이스를 찾는다면, 버전 2는 즐겨찾는 IDE를 사용하여 파이프라인을 저작, 관리 또는 모니터링할 수 있는 풍부한 SDK 집합을 제공합니다.

- **.NET SDK**: .NET SDK가 버전 2용으로 업데이트되었습니다. 
- **PowerShell**: PowerShell cmdlet이 버전 2용으로 업데이트되었습니다. 버전 2 cmdlet의 이름에는 *DataFactoryV2*가 있습니다. 예: *Get-AzureRmDataFactoryV2* 
- **Python SDK**: 이 SDK는 버전 2의 새로운 기능입니다.
- **REST API**: REST API가 버전 2용으로 업데이트되었습니다.  

버전 2용으로 업데이트된 SDK는 버전 1 클라이언트와 호환되지 않습니다. 

### <a name="monitoring"></a>모니터링
현재 버전 2는 SDK만을 사용하여 데이터 팩터리 모니터링을 지원합니다. 포털에는 버전 2 데이터 팩터리 모니터링에 대한 지원이 아직 없습니다. 

## <a name="what-is-integration-runtime"></a>Integration Runtime이란?
Integration Runtime은 서로 다른 네트워크 환경 간에 다음과 같은 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 계산 인프라입니다.

- **데이터 이동**: 공용 네트워크의 데이터 저장소 및 개인 네트워크(온-프레미스 또는 가상 사설망)의 데이터 저장소 간에 데이터를 이동합니다. 기본 제공 커넥터, 형식 변환, 열 매핑 및 성능이 뛰어나고 확장 가능한 데이터 전송에 대한 지원을 제공합니다.
- **디스패치 작업**: Azure HDInsight, Azure Machine Learning, Azure SQL Database, SQL Server 등 다양한 Compute Services에서 실행하는 변환 작업을 디스패치 및 모니터링합니다.
- **SSIS 패키지 실행**: SSIS 패키지를 관리되는 Azure 계산 환경에서 고유하게 실행합니다.

데이터를 이동 및 변환하는 데 필요한 만큼 하나 이상의 Integration Runtime 인스턴스를 배포할 수 있습니다. Integration Runtime은 Azure 공용 네트워크 또는 개인 네트워크(온-프레미스, Azure Virtual Network 또는 Amazon Web Services VPC(가상 사설 클라우드))에서 실행할 수 있습니다. 

자세한 내용은 [Azure Data Factory의 Integration Runtime](concepts-integration-runtime.md)을 참조하세요.

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>통합 런타임의 수 제한은 얼마입니까?
Data Factory에서 사용할 수 있는 Integration Runtimes 인스턴스의 수에는 엄격한 제한이 없습니다. 하지만 Integration Runtime이 SSIS 패키지 실행을 위해 구독당 사용할 수 있는 VM 코어 수는 제한되어 있습니다. 자세한 내용은 [Data Factory 제한](../azure-subscription-service-limits.md#data-factory-limits)을 참조하세요.

## <a name="when-should-i-use-version-2-rather-than-version-1"></a>버전 1 대신 버전 2를 사용해야 하는 경우는 언제인가요? 
Azure Data Factory를 처음 사용하는 경우 바로 버전 2부터 시작하십시오. 버전 1을 이미 사용하고 있다면 버전 2에서 데이터 팩터리를 다시 작성하십시오.

> [!WARNING]
> Data Factory 버전 2는 미리 보기이며 GA(일반 공급)에 포함되지 않습니다. 따라서 GA에 있는 Data Factory의 버전 1과 동일한 Azure SLA(서비스 수준 약정) 규약에 해당되지 않습니다. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>버전 2의 최상위 개념은 무엇입니까?
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 Data Factory)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼으로서 함께 작동하는 네 가지 핵심 구성 요소로 구성됩니다.

### <a name="pipelines"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 작업 단위를 수행하는 작업의 논리적 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이점은 각 작업을 개별적으로 관리하는 대신, 파이프라인을 사용하여 여러 작업을 하나의 집합으로 관리할 수 있다는 것입니다. 파이프라인의 작업을 서로 연결하여 순차적으로 작동하거나 독립적으로 병렬 작동할 수 있습니다.

### <a name="activity"></a>작업
작업은 파이프라인의 처리 단계를 나타냅니다. 예를 들어, 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 *복사* 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 작업, 데이터 변환 작업 및 제어 작업이라는 세 종류의 작업을 지원합니다.

### <a name="data-sets"></a>데이터 집합
데이터 집합은 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 연결된 서비스는 데이터 원본에 연결을 정의하고 데이터 집합은 데이터 구조를 나타낸다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. 그리고 Azure Blob 데이터 집합은 Blob 컨테이너 및 데이터가 포함된 폴더를 지정합니다.

연결된 서비스는 Data Factory에서 다음 두 가지 용도로 사용됩니다.

- 온-프레미스 SQL Server 인스턴스, Oracle 데이터베이스 인스턴스 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 *데이터 저장소*를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록에 대해서는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 참조하세요.
- 활동의 실행을 호스팅할 수 있는 *계산 리소스* 를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 변환 작업 및 지원되는 계산 환경 목록은 [Azure Data Factory에서 데이터 변환](transform-data.md)을 참조하세요.

### <a name="triggers"></a>트리거
트리거는 파이프라인 실행을 시작할 시기를 결정하는 처리 단위를 나타냅니다. 다양한 유형의 이벤트에 대한 다른 종류의 트리거가 있습니다. 미리 보기의 경우 벽 시계 스케줄러 트리거를 지원합니다. 


### <a name="pipeline-runs"></a>파이프라인 실행
파이프라인 실행은 파이프라인 실행의 인스턴스입니다. 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 파이프라인 실행을 인스턴스화합니다. 인수는 수동으로 또는 트리거 정의 내에서 전달할 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 읽기 전용 구성의 키-값 쌍입니다. 파이프라인에서 매개 변수를 정의하고 실행 컨텍스트에서 실행하는 동안 정의된 매개 변수에 대한 인수를 전달합니다. 실행 컨텍스트는 트리거 또는 수동으로 실행하는 파이프라인에서 생성됩니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.

데이터 집합은 다시 사용하거나 참조할 수 있는 강력한 형식의 매개 변수 및 엔터티입니다. 작업은 데이터 집합을 참조할 수 있으며 데이터 집합 정의에 정의된 속성을 사용할 수 있습니다.

또한 연결된 서비스도 데이터 저장소 또는 계산 환경에 대한 연결 정보를 포함하는 강력한 형식 매개 변수입니다. 다시 사용하거나 참조할 수 있는 엔터티이기도 합니다.

### <a name="control-flows"></a>제어 흐름
제어 흐름은 파이프라인 수준에서 정의하는 시퀀스, 분기 및 매개 변수의 작업 연결과 요청 시 또는 트리거에서 파이프라인을 호출할 때 전달하는 인수를 포함하는 파이프라인 작업을 조율합니다. 제어 흐름은 사용자 지정 상태 전달 및 컨테이너 루핑, 즉 For-each 반복기도 포함합니다.


Data Factory 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 집합 및 연결된 서비스](concepts-datasets-linked-services.md)
- [파이프라인 및 활동](concepts-pipelines-activities.md)
- [통합 런타임](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>Data Factory의 가격 책정 모델은 무엇입니까?
Azure Data Factory에 대한 가격 정보는 [Data Factory 가격 책정 정보](https://azure.microsoft.com/pricing/details/data-factory/)를 참조하세요.

## <a name="what-regions-support-azure-data-factory-version-2"></a>Azure Data Factory 버전 2를 지원하는 지역은 어디입니까?
현재 미국 동부, 미국 동부 2 및 유럽 서부 지역에서 버전 2의 Data Factory를 만들 수 있습니다. 하지만 Data Factory는 다른 지역의 Integration Runtime을 사용하여 데이터 저장소간 데이터를 이동하거나 Compute Services에 대한 활동을 디스패치하거나 SSIS 패키지를 디스패치할 수 있습니다. 자세한 내용은 [Data Factory 위치](concepts-integration-runtime.md#integration-runtime-location)를 참조하세요.

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Data Factory에 대한 최신 정보를 얻으려면 어떻게 해야 하나요?
Azure Data Factory에 대한 최신 정보를 얻으려면 다음 사이트로 이동하세요.

- [블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [설명서 홈페이지](/azure/data-factory)
- [제품 홈페이지](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>기술 심층 분석 

### <a name="can-version-1-and-version-2-pipelines-run-side-by-side"></a>버전 1과 버전 2 파이프라인을 함께 실행할 수 있나요?
번호 버전 2 및 버전 1 Data Factory는 다른 버전의 엔터티(예: 연결된 서비스, 데이터 집합 또는 파이프라인)를 포함할 수 없습니다.   

### <a name="do-i-still-need-to-define-data-sets-in-version-2"></a>버전 2에서도 데이터 집합을 정의해야 하나요?
데이터 집합은 대부분의 활동에서 더 이상 필수 엔터티가 아닙니다. 변환을 위해 데이터 집합의 스키마 및 기타 메타데이터 정보를 사용하는 복사, Machine Learning, 조회, 유효성 검사 및 사용자 지정 활동에 필요합니다. 나머지 작업에도 데이터 집합이 더 이상 필요하지 않습니다.

### <a name="can-i-chain-two-activities-without-a-data-set-in-version-2"></a>버전 2에서 데이터 집합 없이 두 가지 활동을 연결할 수 있나요?
예. 버전 2에서는 데이터 집합을 요구하지 않고 활동을 연결할 수 있습니다. 파이프라인의 JSON 정의에서 **dependsOn** 속성을 사용하여 활동을 연결합니다. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>모든 버전 1 활동이 버전 2에서 지원됩니까? 
예, 모든 버전 1 활동이 버전 2에서 지원됩니다.

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>버전 2 파이프라인을 어떻게 예약할 수 있습니까? 
스케줄러 트리거를 사용하여 버전 2 파이프라인을 예약할 수 있습니다. 트리거는 벽시계 캘린더 일정을 사용하며 사용자는 이를 통해 정기적으로 또는 캘린더 기반 반복 패턴(예: 매주 월요일 오후 6시 및 목요일 오후 9시)을 사용하여 파이프라인을 예약할 수 있습니다. 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>버전 2에서 실행되는 파이프라인에 매개 변수를 전달할 수 있습니까?
예, 매개 변수는 버전 2에서 최상위 수준 개념입니다. 파이프라인 수준에서 매개 변수를 정의하고 요청 시 실행되는 파이프라인 실행 중에 또는 트리거를 사용하여 인수를 전달할 수 있습니다.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>파이프라인 매개 변수의 기본값을 정의할 수 있습니까? 
예. 파이프라인의 매개 변수에 대한 기본값을 정의할 수 있습니다. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-that-are-passed-to-a-pipeline-run"></a>파이프라인의 작업이 파이프라인 실행에 전달된 인수를 사용할 수 있습니까? 
예. 파이프라인 내의 각 작업은 `@parameter` 구문으로 실행되는 파이프라인에 전달된 매개 변수 값을 사용할 수 있습니다. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>활동 출력 속성이 다른 활동에서 사용될 수 있습니까? 
예. 활동 출력은 `@activity` 구문으로 후속 활동에서 사용될 수 있습니다.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>활동 출력의 Null 값을 정상적으로 처리하려면 어떻게 해야 합니까? 
식에서 `@coalesce` 구문을 사용하여 Null 값을 정상적으로 처리할 수 있습니다. 

### <a name="can-i-use-retry-and-timeout-at-the-activity-level-in-version-2"></a>버전 2의 활동 수준에서 재시도, 시간 제한을 사용할 수 있나요?
예. 버전 1과 같이 버전 2에서 작업의 실행을 관리하기 위해 작업 수준에서 다시 시도 및 시간 제한을 구성할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
버전 2의 데이터 팩터리를 만드는 단계별 지침은 다음 자습서를 참조하십시오.

- [빠른 시작: Data Factory 만들기](quickstart-create-data-factory-dot-net.md)
- [자습서: 클라우드에서 데이터 복사](tutorial-copy-data-dot-net.md)

