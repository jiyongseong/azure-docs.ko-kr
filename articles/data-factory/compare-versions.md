---
title: Azure Data Factory 버전 1과 2 비교 | Microsoft Docs
description: 이 문서에서는 Azure Data Factory V1과 Azure Data Factory V2를 비교합니다.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: e78051c4eb0cd79d636a6af31db8ffb6cd7e6aef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="compare-azure-data-factory-v1-and-v2"></a>Azure Data Factory V1과 V2 비교
이 문서에서는 Azure Data Factory V1과 V2를 비교합니다. V1에 대한 소개는 [Azure Data Factory 소개](v1/data-factory-introduction.md)를 참조하세요. V2에 대한 소개는 [Data Factory(V2 - 미리 보기) 소개](introduction.md)를 참조하세요.

## <a name="feature-comparison"></a>기능 비교
다음 테이블에서는 V1과 V2의 기능을 비교합니다. 

| 기능 | 버전 1 | 버전 2 | 
| ------- | --------- | --------- | 
| 데이터 집합 | 활동에서 입력 및 출력으로 사용하려는 데이터를 참조하는 데이터의 명명된 보기입니다. 데이터 집합은 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Blob 데이터 집합은 활동에서 데이터를 읽어 들여야 하는 Azure Blob 저장소의 Blob 컨테이너와 폴더를 지정합니다.<br/><br/>**가용성**은 데이터 집합에 대한 처리 기간 조각화 모델을 정의합니다(예: 매시간, 매일 등). | V2에서 데이터 집합은 동일합니다. 하지만 데이터 집합에 대한 **가용성** 일정을 정의할 필요가 없습니다. 클록 스케줄러 패러다임에서 파이프라인을 예약할 수 있는 트리거 리소스를 정의할 수 있습니다. 자세한 내용은 [트리거](concepts-pipeline-execution-triggers.md#triggers) 및 [데이터 집합](concepts-datasets-linked-services.md)을 참조하세요. | 
| 연결된 서비스 | 연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. | 연결된 서비스는 Data Factory V1에서와 동일하지만 Data Factory V2 Integration Runtime 계산 환경을 활용하는 새로운 **connectVia** 속성이 있습니다. 자세한 내용은 [Azure Data Factory의 통합 런타임](concepts-integration-runtime.md) 및 [Azure Blob 저장소의 연결된 서비스 속성](connector-azure-blob-storage.md#linked-service-properties)을 참조하세요. |
| 파이프라인 | 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 함께 작업을 수행하는 활동의 논리적 그룹화입니다. startTime, endTime 및 isPaused를 사용하여 파이프라인을 예약하고 실행합니다. | 파이프라인은 데이터에 수행되는 활동의 그룹입니다. 단, 파이프 라인에서 활동의 일정 계획이 새로운 트리거 리소스로 분리되었습니다. Data Factory V2의 파이프라인은 트리거를 통해 개별적으로 예약하는 "워크플로 단위"라고 생각할 수 있습니다. <br/><br/>Data Factory V2에서는 파이프라인에 시간 실행의 "기간"이 없습니다. Data Factory V1의 startTime, endTime, isPaused 개념은 Data Factory V2에 더 이상 존재하지 않습니다. 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)와 [파이프라인 및 활동](concepts-pipelines-activities.md)을 참조하세요. |
| 활동 | 활동은 파이프라인 내에서 데이터에 수행할 동작을 정의합니다. 데이터 이동(복사 활동) 및 데이터 변환 활동(예: Hive, Pig 및 MapReduce)이 지원됩니다. | Data Factory V2에서 활동은 여전히 파이프라인 내에 정의된 동작입니다. V2는 새로운 [제어 흐름 활동](concepts-pipelines-activities.md#control-activities)을 소개합니다. 이러한 활동은 제어 흐름(루핑 및 분기)에서 사용합니다. V1에서 지원되었던 데이터 이동 및 데이터 변환 활동이 V2에서 지원됩니다. V2에서는 데이터 집합을 사용하지 않고 변환 활동을 정의할 수 있습니다. |
| 하이브리드 데이터 이동 및 활동 디스패치 | 이제 Integration Runtime이라고 하는 [데이터 관리 게이트웨이](v1/data-factory-data-management-gateway.md)는 온-프레미스와 클라우드 간에 데이터 이동을 지원했습니다.| 데이터 관리 게이트웨이를 이제 자체 호스팅 Integration Runtime이라고 합니다. V1에서와 동일한 기능을 제공합니다. <br/><br/> V2의 Azure-SSIS Integration Runtime은 클라우드에서 SSIS(SQL Server Integration Services) 패키지를 배포하고 실행하는 기능도 지원합니다. 자세한 내용은 [Azure Data Factory의 Integration Runtime](concepts-integration-runtime.md)을 참조하세요.|
| 매개 변수 | 해당 없음 | 매개 변수는 파이프라인에 정의된 읽기 전용 구성 설정의 키-값 쌍입니다. 파이프라인을 수동으로 실행할 때 매개 변수에 대한 인수를 전달할 수 있습니다. 스케줄러 트리거를 사용하는 경우 트리거가 매개 변수에 대한 값을 전달할 수도 있습니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.  |
| 식 | Data Factory V1을 사용하면 데이터 선택 쿼리 및 활동/데이터 집합 속성에 함수 및 시스템 변수를 사용할 수 있습니다. | Data Factory V2에서는 JSON 문자열 값의 아무 곳에나 식을 사용할 수 있습니다. 자세한 내용은 [V2의 식 및 함수](control-flow-expression-language-functions.md)를 참조하세요.|
| 파이프라인 실행 | 해당 없음 | 파이프라인 실행의 단일 인스턴스입니다. 예를 들어 오전 8시, 오전 9시 및 오전 10시에 실행하는 파이프라인이 있다고 합시다. 이 경우 파이프라인의 별도 실행(파이프라인 실행) 3개가 있습니다. 각 파이프라인 실행에는 고유한 파이프라인 실행 ID가 있습니다. 파이프라인 실행 ID는 특정 파이프 라인 실행을 고유하게 정의하는 GUID입니다. 파이프라인 실행은 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 인스턴스화됩니다. |
| 작업 실행 | 해당 없음 | 파이프라인 내의 활동 실행 인스턴스입니다. | 
| 트리거 실행 | 해당 없음 | 트리거 실행 인스턴스입니다. 자세한 내용은 [트리거](concepts-pipeline-execution-triggers.md)를 참조하세요. |
| 일정 계획 | 일정 예약은 파이프라인 시작/종료 시간 및 데이터 집합 가용성을 기반으로 합니다. | 외부 스케줄러를 통한 Scheduler 트리거 또는 실행입니다. 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요. |

다음 섹션에서는 V2의 기능에 대한 자세한 정보를 제공합니다. 

## <a name="control-flow"></a>흐름 제어  
최신 데이터 웨어하우스의 다양한 통합 흐름 및 패턴을 지원하기 위해 Data Factory V2는 더 이상 시계열 데이터에 연결되지 않는 유연한 새 데이터 파이프라인 모델을 구현했습니다. 이전에는 불가능했던 몇 가지 일반적인 흐름이 가능해졌습니다. 이 내용은 다음 섹션에 설명되어 있습니다.   

### <a name="chaining-activities"></a>활동 연결
V1에서는 활동을 연결하기 위해 한 활동의 출력을 다른 활동의 입력으로 구성해야 했습니다. V2에서는 파이프라인 내에서 시퀀스로 활동을 연결할 수 있습니다. 활동 정의에 **dependsOn** 속성을 사용하여 업스트림 활동과 연결할 수 있습니다. 자세한 내용과 예제는 [파이프라인 및 활동](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline)과 [분기 및 연결 활동](tutorial-control-flow.md)을 참조하세요. 

### <a name="branching-activities"></a>분기 활동
V2에서는 파이프라인 내에서 활동을 분기할 수 있습니다. [If 조건 활동](control-flow-if-condition-activity.md)은 `if` 문이 프로그래밍 언어로 제공하는 것과 동일한 기능을 제공합니다. 조건이 `true`로 평가되면 작업 집합을 평가하고, 조건이 `false`로 평가되면 다른 작업 집합을 평가합니다. 분기 활동에 대한 예제는 [분기 및 연결 활동](tutorial-control-flow.md) 자습서를 참조하세요.

### <a name="parameters"></a>매개 변수 
파이프라인 수준에서 매개 변수를 정의하고 요청 시 또는 트리거에서 파이프라인을 호출하는 동안 인수를 전달할 수 있습니다. 파이프라인에 전달되는 인수를 작업에 사용할 수 있습니다. 자세한 내용은 [파이프라인 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요. 

### <a name="custom-state-passing"></a>사용자 지정 상태 전달
상태를 포함한 작업 출력을 파이프라인의 이후 작업에서 사용할 수 있습니다. 예를 들어, 활동의 JSON 정의에서 다음 구문을 사용하여 이전 활동의 출력에 액세스 할 수 있습니다. `@activity('NameofPreviousActivity').output.value`. 이 기능을 사용하면 값이 활동을 통과할 수 있는 워크플로를 만들 수 있습니다.

### <a name="looping-containers"></a>컨테이너 루프화
[ForEach 활동](control-flow-for-each-activity.md)은 파이프라인의 반복 제어 흐름을 정의합니다. 이 활동은 컬렉션을 반복하고 루프의 지정된 활동을 실행합니다. 이 활동의 루프 구현은 프로그래밍 언어의 Foreach 반복 구조와 비슷합니다. 

[Until](control-flow-until-activity.md) 활동은 do-until 루핑 구조가 프로그래밍 언어로 제공하는 것과 동일한 기능을 제공합니다. 활동과 연결된 조건이 `true`로 평가될 때까지 반복에서 일련의 활동을 실행합니다. Data Factory에서 until 작업의 시간 제한 값을 지정할 수 있습니다.  

### <a name="trigger-based-flows"></a>트리거 기반 흐름
요청 시 또는 벽시계 시간에 의해 파이프라인을 트리거할 수 있습니다. [파이프라인 및 트리거](concepts-pipeline-execution-triggers.md) 문서에 트리거에 대한 자세한 내용이 있습니다. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>다른 파이프라인에서 파이프라인 호출
[파이프라인 실행 활동](control-flow-execute-pipeline-activity.md)을 사용하면 하나의 Data Factory 파이프라인에서 다른 파이프라인을 호출할 수 있습니다.

### <a name="delta-flows"></a>델타 흐름
ETL 패턴의 주요 사용 사례는 "델타 로드" 즉, 파이프라인의 마지막 반복 이후 변경된 데이터만 로드하는 것입니다. [조회 활동](control-flow-lookup-activity.md), 유연한 일정 예약 및 제어 흐름과 같은 V2의 새로운 기능을 통해 이러한 사용 사례를 자연스럽게 사용할 수 있습니다. 단계별 지침이 포함된 자습서는 [자습서: 증분 복사](tutorial-incremental-copy-powershell.md)를 참조하세요.

### <a name="other-control-flow-activities"></a>다은 제어 흐름 활동
다음은 Data Factory V2에서 지원하는 몇 가지 제어 흐름 활동입니다. 

제어 작업 | 설명
---------------- | -----------
[ForEach 작업](control-flow-for-each-activity.md) | 파이프라인의 반복 제어 흐름을 정의합니다. 이 활동을 사용하여 컬렉션을 반복하고 루프의 지정된 활동을 실행합니다. 이 작업의 루프 구현은 프로그래밍 언어에서 구조를 반복하는 Foreach와 비슷합니다.
[웹 작업](control-flow-web-activity.md) | Data Factory 파이프라인에서 사용자 지정 REST 엔드포인트를 호출합니다. 작업에서 사용하고 액세스하도록 데이터 집합 및 연결된 서비스를 전달할 수 있습니다. 
[조회 작업](control-flow-lookup-activity.md) | 외부 소스에서 레코드 또는 테이블 이름 값을 읽거나 조회합니다. 이 출력을 다음 작업에서 추가로 참조할 수 있습니다. 
[메타데이터 가져오기 작업](control-flow-get-metadata-activity.md) | Azure Data Factory에서 데이터의 메타 데이터를 검색합니다. 
[대기 작업](control-flow-wait-activity.md) | 지정된 기간 동안 파이프라인을 일시 중지합니다.

## <a name="deploy-ssis-packages-to-azure"></a>Azure에 SSIS 패키지 배포 
SSIS 워크로드를 클라우드로 이동하고 V2를 사용하여 데이터 팩터리를 만들고 Azure-SSIS Integration Runtime을 프로비전하려면 Azure-SSIS를 사용합니다.

Azure-SSIS Integration Runtime은 클라우드에서 SSIS 패키지 실행을 전담하는 완전히 관리되는 Azure VM(노드) 클러스터입니다. Azure-SSIS Integration Runtime을 프로비전한 후에 온-프레미스 SSIS 환경에 SSIS 패키지를 배포하는 데 사용한 것과 동일한 도구를 사용할 수 있습니다. 

예를 들어 SQL Server Data Tools 또는 SQL Server Management Studio를 사용하여 Azure에서 이 런타임에 SSIS 패키지를 배포할 수 있습니다. 단계별 지침은 자습서 [Azure에 SQL Server 통합 서비스 패키지 배포](tutorial-create-azure-ssis-runtime-portal.md)를 참조하세요. 

## <a name="flexible-scheduling"></a>유연한 일정 계획
Data Factory V2에서는 데이터 집합 가용성 일정을 정의할 필요가 없습니다. 클록 스케줄러 패러다임에서 파이프라인을 예약할 수 있는 트리거 리소스를 정의할 수 있습니다. 유연한 일정 예약/실행 모델을 위해 트리거에서 파이프라인으로 매개 변수를 전달할 수도 있습니다. 

Data Factory V2에서는 파이프라인에 시간 실행의 "기간"이 없습니다. Data Factory V1의 startTime, endTime, isPaused 개념이 Data Factory V2에는 존재하지 않습니다. Data Factory V2에서 파이프라인을 빌드하고 일정을 예약하는 방법에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md)를 참조하세요.

## <a name="support-for-more-data-stores"></a>더 많은 데이터 저장소 지원
V2는 V1보다 많은 데이터 저장소의 데이터를 복사하고 데이터 저장소에 데이터를 복사할 수 있습니다. 지원되는 데이터 저장소 목록은 다음 문서를 참조하세요.

- [V1 - 지원되는 데이터 저장소](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [V2 - 지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>주문형 Spark 클러스터에 대한 지원
V2는 주문형 HDInsight Spark 클러스터 만들기를 지원합니다. 주문형 Spark 클러스터를 만들려면 주문형 HDInsight 연결된 서비스 정의에 클러스터 유형을 Spark로 지정합니다. 그런 다음 파이프라인에서 Spark 작업이 이 연결된 서비스를 사용하도록 정의합니다. 

런타임 시 활동이 실행되면 Data Factory 서비스에서 Spark 클러스터가 자동으로 만들어집니다. 자세한 내용은 다음 문서를 참조하세요.

- [V2의 Spark 활동](transform-data-using-spark.md)
- [Azure HDInsight 주문형 연결된 서비스](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>사용자 지정 활동
V1에서는 IDotNetActivity 인터페이스의 Execute 메서드를 구현하는 클래스를 통해 .NET 클래스 라이브러리 프로젝트를 만들어 (사용자 지정) DotNet 작업 코드를 구현합니다. 따라서 사용자 지정 코드를 .NET Framework 4.5.2에서 작성하고 Windows 기반 Azure Batch 풀 노드에서 실행해야 합니다. 

V2 사용자 지정 작업에서는 .NET 인터페이스를 구현할 필요가 없습니다. 명령, 스크립트 및 실행 파일로 컴파일된 자체 사용자 지정 코드를 직접 실행할 수 있습니다. 

자세한 내용은 [V1과 V2의 사용자 지정 활동의 차이](transform-data-using-dotnet-custom-activity.md#compare-v2-v1)를 참조하세요.

## <a name="sdks"></a>SDK
 Data Factory V2는 파이프라인을 작성, 관리 및 모니터링하는 데 사용할 수 있는 다양한 SDK 집합을 제공합니다.

- **.NET SDK**: .NET SDK가 V2용으로 업데이트되었습니다.

- **PowerShell**: PowerShell cmdlet이 V2용으로 업데이트되었습니다. V2 cmdlet은 이름에 **DataFactoryV2**가 포함됩니다(예: Get-AzureRmDataFactoryV2). 

- **Python SDK**: 이 SDK는 V2의 새로운 기능입니다.

- **REST API**: REST API가 V2용으로 업데이트되었습니다. 

V2용으로 업데이트된 SDK는 V1 클라이언트와 호환되지 않습니다. 

## <a name="authoring-experience"></a>작성 환경

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| Azure portal | [예](quickstart-create-data-factory-portal.md) | [예](data-factory-build-your-first-pipeline-using-editor.md) |
| Azure PowerShell | [예](quickstart-create-data-factory-powershell.md) | [예](data-factory-build-your-first-pipeline-using-powershell.md) |
| .NET SDK | [예](quickstart-create-data-factory-dot-net.md) | [예](data-factory-build-your-first-pipeline-using-vs.md) |
| REST API | [예](quickstart-create-data-factory-rest-api.md) | [예](data-factory-build-your-first-pipeline-using-rest-api.md) |
| Python SDK | [예](quickstart-create-data-factory-python.md) | 아니오 |
| Resource Manager 템플릿 | [예](quickstart-create-data-factory-resource-manager-template.md) | [예](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>역할 및 권한

Data Factory 버전 1 참가자 역할을 Data Factory v2 리소스를 만들고 관리하는 데 사용할 수 있습니다.

## <a name="monitoring-experience"></a>모니터링 환경
V2에서는 [Azure Monitor](monitor-using-azure-monitor.md)를 사용하여 데이터 팩터리를 모니터링할 수도 있습니다. 새로운 PowerShell cmdlet은 [통합 런타임](monitor-integration-runtime.md)에 대한 모니터링을 지원합니다. V1과 V2 모두 Azure Portal에서 시작할 수 있는 모니터링 응용 프로그램을 통해 시각적인 모니터링을 지원합니다.


## <a name="next-steps"></a>다음 단계
다음 빠른 시작의 단계별 지침에 따라 데이터 팩터리를 만드는 방법에 대해 알아보기: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [REST API](quickstart-create-data-factory-rest-api.md). 
