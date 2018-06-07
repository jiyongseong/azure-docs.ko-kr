---
title: Azure Service Fabric CLI- sfctl partition| Microsoft Docs
description: "Service Fabric CLI sfctl partition 명령을 설명합니다."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 01dd1900fe765618e5da20bd289b9c3a021ea9a3
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-partition"></a>sfctl partition
모든 서비스에 대한 파티션을 쿼리 및 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
|    data-loss      | 이 API는 지정한 파티션에 대한 데이터 손실을 유도합니다.|
|    data-loss-status  | StartDataLoss API를 사용하여 시작된 파티션 데이터 손실 작업의 진행률을 가져옵니다.|
|    health         | 지정된 Service Fabric 파티션의 상태를 가져옵니다.|
|    info           | Service Fabric 파티션에 관한 정보를 가져옵니다.|
|    list           | Service Fabric 서비스의 파티션 목록을 가져옵니다.|
|    load           | 지정된 Service Fabric 파티션의 로드를 가져옵니다.|
|    load-reset     | Service Fabric 파티션의 현재 부하를 다시 설정합니다.|
|    quorum-loss    | 주어진 상태 저장 서비스 파티션에 대한 쿼럼 손실을 유도합니다.|
|    quorum-loss-status| StartQuorumLoss API를 사용하여 시작된 파티션에 대한 쿼럼 손실 작업의 진행률을 가져옵니다.|
|    recover        | 현재 쿼럼 손실에 걸린 특정 파티션을 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다.|
|    recover-all    | 현재 쿼럼 손실에 걸린 모든 서비스(시스템 서비스 포함)를 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다.|
|    report-health  | Service Fabric 파티션에 관한 상태 보고서를 보냅니다.|
|    restart        | 이 API는 지정된 파티션의 일부 또는 모든 복제본 또는 인스턴스를 다시 시작합니다.|
|    restart-status | StartPartitionRestart를 사용하여 시작된 PartitionRestart 작업의 진행률을 가져옵니다.|
|    svc-name       | 파티션에 대한 Service Fabric 서비스의 이름을 가져옵니다.|


## <a name="sfctl-partition-health"></a>sfctl partition health
지정된 Service Fabric 파티션의 상태를 가져옵니다.

지정된 파티션의 상태 정보를 가져옵니다. 상태에 따라 서비스에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다.
ReplicasHealthStateFilter를 사용하여 파티션에 ReplicaHealthState 개체 컬렉션을 필터링합니다. Health store에 존재하지 않는 파티션을 지정하면 이 cmdlet은 오류를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id      [필수]| 파티션의 id입니다.|
| --events-health-state-filter  | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다.                필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다. - Default -                Default value. 모든 HealthState와 일치합니다. 값은 0입니다. - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.                - All - 모든 HealthState 값의 입력과 일치하는 필터입니다.                값은 65535입니다.|
|--exclude-health-statistics   | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔티티 수를 보여줍니다.|
| --replicas-health-state-filter| 파티션에서 ReplicaHealthState 개체 컬렉션을 필터링합니다. HealthStateFilter의 멤버에 대한 멤버 또는 비트 연산에서 값을 얻을 수 있습니다. 필터와 일치하는 복제본만 반환됩니다. 모든 복제본은 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --timeout -t               | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                    | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                  | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.                기본값: json.|
| --query                    | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요. |
| --verbose                  | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-partition-info"></a>sfctl partition info
Service Fabric 파티션에 관한 정보를 가져옵니다.

파티션 끝점은 지정된 파티션에 관한 정보를 반환합니다. 응답에는 파티션 ID, 파티셔닝 스키마 정보, 파티션에 의해 지원되는 키, 상태, 상태 및 파티션에 관한 기타 세부 정보가 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수]| 파티션의 id입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-partition-list"></a>sfctl partition list
Service Fabric 서비스의 파티션 목록을 가져옵니다.

Service Fabric 서비스의 파티션 목록을 가져옵니다. 파티션 ID, 파티셔닝 스키마 정보, 파티션에 의해 지원되는 키, 상태, 상태 및 파티션에 관한 기타 세부 정보.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --service-id [필수]| 서비스 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 서비스 이름이 “fabric:/myapp/app1/svc1”이면 서비스 ID는 6.0 이상에서는 “myapp~app1~svc1”이고 이전 버전에서는 “myapp/app1/svc1”입니다.|
| --continuation-token| 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다.         공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다.|
| --timeout -t        | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug             | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h           | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o         | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query             | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose           | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-partition-load"></a>sfctl partition load
지정된 Service Fabric 파티션의 로드를 가져옵니다.

지정된 파티션에 관한 정보를 반환합니다. 응답에는 부하 정보 목록이 들어 있습니다. 각 정보에는 부하 메트릭 이름, 값 및 마지막 보고 시간(UTC)이 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수]| 파티션의 id입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
현재 쿼럼 손실에 걸린 특정 파티션을 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다.

현재 쿼럼 손실에 걸린 특정 파티션을 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다. 이 작업은 다운된 복제본을 복구하지 못할 경우에만 수행해야 합니다. 이 API를 잘못 사용하면 잠재적인 데이터 손실이 발생할 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수]| 파티션의 id입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
이 API는 지정된 파티션의 일부 또는 모든 복제본 또는 인스턴스를 다시 시작합니다.

이 API는 장애 조치를 테스트하는 데 유용합니다. 상태 비저장 서비스 파티션을 대상으로 사용하는 경우, RestartPartitionMode는 AllReplicasOrInstances이어야 합니다. 진행률을 가져오려면 동일한 OperationId를 사용하여 GetPartitionRestartProgress API를 호출합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --operation-id           [필수]| 이 API의 호출을 식별하는 GUID입니다.  이 값은 해당 GetProgress API에 전달됩니다.|
| --partition-id           [필수]| 파티션의 id입니다.|
| --restart-partition-mode [필수]| 다시 시작하는 파티션을 설명합니다.|
| --service-id             [필수]| 서비스 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 서비스 이름이 “fabric:/myapp/app1/svc1”이면 서비스 ID는 6.0 이상에서는 “myapp~app1~svc1”이고 이전 버전에서는 “myapp/app1/svc1”입니다.|
| --timeout -t                    | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                         | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                       | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                     | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.                     기본값: json.|
| --query                         | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose                       | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
