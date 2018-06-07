---
title: Azure Storage 분석을 사용하여 로그 및 메트릭 데이터 수집 | Microsoft Docs
description: 저장소 분석을 사용하면 모든 Storage 서비스에 대한 메트릭 데이터를 추적하고 Blob, 큐 및 Table Storage에 대한 로그를 수집할 수 있습니다.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 7894993b-ca42-4125-8f17-8f6dfe3dca76
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: rogarana
ms.openlocfilehash: edda01cbfe1b53d934f9f4a7bb01c645fa680873
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="storage-analytics"></a>저장소 분석

Azure Storage 분석은 로깅을 수행하며 Storage 계정에 대한 메트릭 데이터를 제공합니다. 이 데이터를 사용하여 요청을 추적하고 사용량 추세를 분석하며 저장소 계정에 대한 문제를 진단할 수 있습니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. [Azure Portal](https://portal.azure.com)에서 활성화할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. [Blob Service 속성 가져오기](https://msdn.microsoft.com/library/hh452239.aspx), [큐 서비스 속성 가져오기](https://msdn.microsoft.com/library/hh452243.aspx), [Table service 속성 가져오기](https://msdn.microsoft.com/library/hh452238.aspx) 및 [파일 서비스 속성 가져오기](https://msdn.microsoft.com/library/mt427369.aspx) 작업을 사용하여 각 서비스에 대해 저장소 분석을 사용하도록 설정합니다.

집계된 데이터는 알려진 로깅용 Blob 및 알려진 메트릭용 테이블에 저장됩니다. Blob service 및 Table service API를 사용하면 이러한 Blob와 테이블에 액세스할 수 있습니다.

저장소 분석에 저장되는 데이터의 양은 20TB로 제한됩니다. 이 제한은 총 Storage 계정 제한과 관계없이 적용됩니다. 청구 및 데이터 보존 정책에 대한 자세한 내용은 [저장소 분석 및 청구](https://msdn.microsoft.com/library/hh360997.aspx)를 참조하세요. 저장소 계정 제한에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

저장소 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

## <a name="about-storage-analytics-logging"></a>저장소 분석 로깅 정보
저장소 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 저장소 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

저장소 서비스 활동이 있는 경우에만 로그 항목이 작성됩니다. 예를 들어 저장소 계정의 Blob service에는 활동이 있지만 테이블 또는 큐 서비스에는 활동이 없으면 Blob service와 관련된 로그만 작성됩니다.

저장소 분석 로깅은 Azure Files에 사용할 수 없습니다.

### <a name="logging-authenticated-requests"></a>인증된 요청 로깅
다음과 같은 유형의 인증된 요청이 기록됩니다.

* 성공한 요청
* 실패한 요청(제한 시간, 제한, 네트워크, 권한 부여 및 기타 오류)
* 실패한 요청 및 성공한 요청을 포함하는 SAS(공유 액세스 서명)를 사용하는 요청
* 분석 데이터에 대한 요청

로그 만들기/삭제 등 저장소 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/hh343260.aspx) 및 [저장소 분석 로그 형식](https://msdn.microsoft.com/library/hh343259.aspx) 항목에 나와 있습니다.

### <a name="logging-anonymous-requests"></a>익명 요청 로깅
다음과 같은 유형의 익명 요청이 기록됩니다.

* 성공한 요청
* 서버 오류
* 클라이언트와 서버에 대한 시간 초과 오류
* 오류 코드가 304(수정되지 않음)인 실패한 GET 요청

기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/hh343260.aspx) 및 [저장소 분석 로그 형식](https://msdn.microsoft.com/library/hh343259.aspx) 항목에 나와 있습니다.

### <a name="how-logs-are-stored"></a>로그 저장 방법
모든 로그는 $logs 컨테이너의 블록 Blob에 저장됩니다. 이 컨테이너는 Storage 계정에 대해 저장소 분석을 사용하도록 설정하면 자동으로 작성됩니다. $logs 컨테이너는와 같은 저장소 계정의 Blob 네임스페이스에 있습니다. `http://<accountname>.blob.core.windows.net/$logs` Storage 계정을 사용하도록 설정한 후에는 이 컨테이너를 삭제할 수 없지만 해당 콘텐츠는 삭제할 수 있습니다.

> [!NOTE]
> $logs 컨테이너는 [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) 등의 메서드를 사용하여 컨테이너 나열 작업을 수행할 때는 표시되지 않으므로 직접 액세스해야 합니다. 예를 들어 [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) 메서드를 사용하여 `$logs` 컨테이너의 Blob에 액세스할 수 있습니다.
> 요청을 기록할 때 저장소 분석은 중간 결과를 블록으로 업로드합니다. 그리고 이러한 블록을 정기적으로 커밋하여 Blob로 제공합니다.
> 
> 

같은 시간에 생성되는 로그의 경우 중복 레코드가 있을 수 있습니다. **RequestId** 및 **Operation** 번호를 검사하여 레코드 중복 여부를 확인할 수 있습니다.

### <a name="log-naming-conventions"></a>로그 명명 규칙
각 로그는 다음 형식으로 작성됩니다.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

다음 표에서는 로그 이름의 각 특성에 대해 설명합니다.

| 특성 | 설명 |
| --- | --- |
| <service-name> |저장소 서비스의 이름입니다. 예: blob, table, queue |
| YYYY |로그의 4자리 연도입니다. 예: 2011 |
| MM |로그의 2자리 월입니다. 예: 07 |
| DD |로그의 2자리 월입니다. 예: 07 |
| hh |로그의 시작 시간을 나타내는 24시간 UTC 형식의 2자리 시간입니다. 예: 18 |
| MM |로그의 시작 분을 나타내는 2자리 숫자입니다. 현재 저장소 분석 버전에서는 이 값이 지원되지 않으며 해당 값은 항상 00입니다. |
| <counter> |1시간 동안 저장소 서비스에 대해 생성되는 로그 Blob의 수를 나타내는 0부터 시작되는 6자리 카운터입니다. 이 카운터는 000000부터 시작됩니다. 예: 000001 |

이전 예제가 결합된 완전한 샘플 로그 이름이 아래에 나와 있습니다.

    blob/2011/07/31/1800/000001.log

이전 로그에 액세스하는 데 사용할 수 있는 샘플 URI는 다음과 같습니다.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

저장소 요청이 기록되면 생성되는 로그 이름과 요청한 작업이 완료된 시간 간의 상관 관계가 지정됩니다. 예를 들어 GetBlob 요청이 2011/7/31 오후 6시 30분에 완료된 경우 로그는 다음 접두사로 작성됩니다. `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>로그 메타데이터
모든 로그 Blob는 해당 Blob에 포함된 로깅 데이터를 식별하는 데 사용할 수 있는 메타데이터와 함께 저장됩니다. 아래 표에서 각 메타데이터 특성에 대해 설명합니다.

| 특성 | 설명 |
| --- | --- |
| LogType |로그에 읽기, 쓰기 또는 삭제 작업과 관련된 정보가 포함되는지 여부를 설명합니다. 이 값에는 한 가지 형식이 포함될 수도 있고 3개 형식이 모두 조합(쉼표로 구분)되어 포함될 수도 있습니다. 예제 1: 쓰기; 예제 2: 읽기,쓰기; 예제 3: 읽기,쓰기,삭제하기. |
| StartTime |로그에서 가장 오래된 항목의 시간(YYYY-MM-DDThh:mm:ssZ 형식)입니다. 예: 2011-07-31T18:21:46Z |
| EndTime |로그에서 가장 최신 항목의 시간(YYYY-MM-DDThh:mm:ssZ 형식)입니다. 예: 2011-07-31T18:22:09Z |
| LogVersion |로그 형식의 버전입니다. 현재 지원되는 유일한 값: 1.0 |

다음 목록에는 이전 예제를 사용하는 전체 샘플 메타데이터가 표시되어 있습니다.

* LogType=write
* StartTime=2011-07-31T18:21:46Z
* EndTime=2011-07-31T18:22:09Z
* LogVersion=1.0

### <a name="accessing-logging-data"></a>로깅 데이터 액세스
Azure 관리되는 라이브러리에서 제공하는 .NET API를 비롯한 Blob service API를 사용하면 `$logs` 컨테이너의 모든 데이터에 액세스할 수 있습니다. 저장소 계정 관리자는 로그를 읽고 삭제할 수는 있지만 작성하거나 업데이트할 수는 없습니다. 로그를 쿼리할 때는 로그 메타데이터와 로그 이름을 모두 사용할 수 있습니다. 지정한 시간의 로그가 잘못된 순서로 표시될 수도 있지만 메타데이터는 항상 로그 내의 로그 항목 시간 간격을 지정합니다. 따라서 특정 로그를 검색할 때 로그 이름과 메타데이터 조합을 사용할 수 있습니다.

## <a name="about-storage-analytics-metrics"></a>저장소 분석 메트릭 정보
저장소 분석에서는 Storage 서비스에 대한 요청 관련 용량 데이터 및 집계된 트랜잭션 통계를 포함하는 메트릭을 저장할 수 있습니다. 트랜잭션은 API 작업 수준과 저장소 서비스 수준에서 모두 보고되며 용량은 저장소 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하면 저장소 서비스 사용량을 분석하고 저장소 서비스에 대한 요청의 문제를 진단하며 서비스를 사용하는 응용 프로그램의 성능을 개선할 수 있습니다.

저장소 분석을 사용하려면 모니터링할 각 서비스에 대해 저장소 분석을 개별적으로 사용하도록 설정해야 합니다. [Azure Portal](https://portal.azure.com)에서 활성화할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. **서비스 속성 가져오기** 작업을 사용하여 각 서비스에 대한 저장소 분석을 사용하도록 설정할 수 있습니다.

### <a name="transaction-metrics"></a>트랜잭션 메트릭
각 저장소 서비스 및 요청한 API 작업에 대해 시간 또는 분 간격으로 유용한 데이터 집합이 기록됩니다. 이러한 데이터에는 수신/송신, 가용성, 오류, 분류된 요청 비율 등이 포함됩니다. 트랜잭션 세부 정보의 전체 목록은 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/hh343264.aspx) 항목에서 확인할 수 있습니다.

트랜잭션 데이터는 두 개의 수준, 즉 서비스 수준과 API 작업 수준에서 기록됩니다. 서비스 수준에서는 서비스에 대한 요청이 없더라도 요청한 모든 API 작업을 요약하는 통계가 1시간마다 테이블 엔터티에 기록됩니다. API 작업 수준에서는 해당 시간 동안 작업이 요청된 경우에만 통계가 엔터티에 기록됩니다.

예를 들어 Blob service에 대해 **GetBlob** 작업을 수행하면 저장소 분석 메트릭이 요청을 기록한 다음 **GetBlob** 작업뿐만 아니라 Blob service에 대해 집계된 데이터에도 포함합니다. 하지만 해당 시간 동안 요청된 **GetBlob** 작업이 없으면 해당 작업에 대한 `$MetricsTransactionsBlob`에 엔터티가 기록되지 않습니다.

저장소 분석 자체에서 수행한 요청과 사용자 요청 둘 모두에 대해 트랜잭션 메트릭이 기록됩니다. 예를 들어 로그 및 테이블 엔터티 작성을 위한 저장소 분석의 요청이 기록됩니다. 이러한 요청에 대해 요금이 청구되는 방식에 대한 자세한 내용은 [저장소 분석 및 청구](https://msdn.microsoft.com/library/hh360997.aspx)를 참조하세요.

### <a name="capacity-metrics"></a>용량 메트릭
> [!NOTE]
> 현재는 Blob service에 대해서만 용량 메트릭이 제공됩니다. Table service와 큐 서비스에 대한 용량 메트릭은 이후 Storage 분석 버전에서 제공될 예정입니다.
> 
> 

용량 데이터는 저장소 계정의 Blob service에 대해 매일 기록되며 2개의 테이블 엔터티가 작성됩니다. 이러한 엔터티 중 하나는 사용자 데이터에 대한 통계를 제공하며 다른 하나는 저장소 분석에서 사용하는 `$logs` Blob 컨테이너에 대한 통계를 제공합니다. `$MetricsCapacityBlob` 테이블에는 다음 통계가 포함됩니다.

* **Capacity**: 저장소 계정의 Blob service가 사용하는 저장소의 양(바이트)입니다.
* **ContainerCount**: 저장소 계정의 Blob service에 포함된 Blob 컨테이너의 수입니다.
* **ObjectCount**: 저장소 계정의 Blob service에서 커밋된/커밋되지 않은 블록이나 페이지 Blob의 수입니다.

용량 메트릭에 대한 자세한 내용은 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/hh343264.aspx)를 참조하세요.

### <a name="how-metrics-are-stored"></a>메트릭 저장 방법
각 저장소 서비스의 모든 메트릭 데이터는 해당 서비스용으로 예약된 테이블 3개에 저장됩니다. 이러한 테이블은 각각 트랜잭션 정보용, 세부 트랜잭션 정보용, 용량 정보용 테이블입니다. 트랜잭션 및 세부 트랜잭션 정보는 요청과 응답 데이터로 구성되며 용량 정보는 저장소 사용량 데이터로 구성됩니다. 다음 표에서 설명하는 대로 이름이 지정된 테이블에서 저장소 계정 Blob service의 시간 메트릭, 분 메트릭 및 용량 정보에 액세스할 수 있습니다.

| 메트릭 수준 | 테이블 이름 | 지원되는 버전 |
| --- | --- | --- |
| 시간 메트릭, 기본 위치 |$MetricsTransactionsBlob  <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue |2013-08-15 이전 버전만 해당됩니다. 이러한 이름은 계속 지원되기는 하지만 아래에 나와 있는 표를 사용하도록 전환하는 것이 좋습니다. |
| 시간 메트릭, 기본 위치 |$MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue |2013-08-15를 포함한 모든 버전. |
| 분 메트릭, 기본 위치 |$MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue |2013-08-15를 포함한 모든 버전. |
| 시간 메트릭, 보조 위치 |$MetricsHourSecondaryTransactionsBlob  <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue |2013-08-15를 포함한 모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다. |
| 분 메트릭, 보조 위치 |$MetricsMinuteSecondaryTransactionsBlob  <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue |2013-08-15를 포함한 모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다. |
| 용량(Blob service만 해당) |$MetricsCapacityBlob |2013-08-15를 포함한 모든 버전. |

이러한 테이블은 Storage 계정에 대해 저장소 분석을 사용하도록 설정하면 자동으로 작성됩니다. 저장소 계정의 네임스페이스를 통해 이러한 테이블에 액세스할 수 있습니다. 예: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>메트릭 데이터 액세스
Azure 관리되는 라이브러리에서 제공하는 .NET API를 비롯한 Table service API를 사용하면 메트릭 테이블의 모든 데이터에 액세스할 수 있습니다. 저장소 계정 관리자는 테이블 엔터티를 읽고 삭제할 수는 있지만 작성하거나 업데이트할 수는 없습니다.

## <a name="billing-for-storage-analytics"></a>저장소 분석 청구
모든 메트릭 데이터는 저장소 계정 서비스를 통해 작성됩니다. 따라서 저장소 분석에서 수행하는 각 쓰기 작업에 대해 요금이 청구됩니다. 또한 메트릭 데이터에 사용되는 저장소에도 요금이 청구됩니다.

저장소 분석에서 수행하는 다음 작업에 대해 요금이 청구될 수 있습니다.

* 로깅용 Blob 작성 요청 
* 메트릭용 테이블 엔터티 작성 요청

데이터 보존 정책을 구성한 경우에는 저장소 분석에서 이전 로깅 및 메트릭 데이터를 삭제할 때 삭제 트랜잭션에 대해 요금이 부과되지 않습니다. 그러나 클라이언트의 삭제 트랜잭션에는 요금이 청구됩니다. 보존 정책에 대한 자세한 내용은 [저장소 분석 데이터 보존 정책 설정](https://msdn.microsoft.com/library/azure/hh343263.aspx)을 참조하세요.

### <a name="understanding-billable-requests"></a>청구 가능한 요청 이해
계정의 저장소 서비스에 대한 모든 요청에는 요금이 청구될 수도 있고 청구되지 않을 수도 있습니다. Storage 분석에서는 요청 처리 방법을 나타내는 상태 메시지를 포함하여 서비스에 대한 개별 요청을 기록합니다. 마찬가지로 서비스 및 해당 서비스의 API 작업에 대한 메트릭도 저장됩니다. 여기에는 특정 상태 메시지의 개수와 비율이 포함됩니다. 이러한 기능을 함께 사용하면 요금이 청구될 수 있는 요청을 분석하고 응용 프로그램을 개선할 수 있으며 서비스에 대한 요청의 문제를 진단할 수 있습니다. 청구에 대한 자세한 내용은 [Azure Storage 청구 이해 - 대역폭, 트랜잭션 및 용량](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)을 참조하세요.

저장소 분석 데이터를 확인할 때는 [저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/azure/hh343260.aspx) 토픽의 표를 통해 요금이 청구될 수 있는 요청을 확인할 수 있습니다. 그런 후에 실제 로그 및 메트릭 데이터를 상태 메시지와 비교하여 특정 요청에 대해 요금이 부과되었는지 파악할 수 있습니다. 또한 이전 항목의 표를 통해 저장소 서비스 또는 개별 API 작업의 사용 가능 여부도 조사할 수 있습니다.

## <a name="next-steps"></a>다음 단계
### <a name="setting-up-storage-analytics"></a>저장소 분석 설정
* [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)
* [저장소 분석 설정 및 구성](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>저장소 분석 로깅
* [저장소 분석 로깅 정보](https://msdn.microsoft.com/library/hh343262.aspx)
* [저장소 분석 로그 형식](https://msdn.microsoft.com/library/hh343259.aspx)
* [저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>저장소 분석 메트릭
* [저장소 분석 메트릭 정보](https://msdn.microsoft.com/library/hh343258.aspx)
* [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/hh343264.aspx)
* [저장소 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/hh343260.aspx)  

