---
title: Azure Service Fabric CLI- sfctl 복제본 | Microsoft Docs
description: Service Fabric CLI sfctl 복제본 명령을 설명합니다.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: ba67a2a20d3f3e8e9fbccb2674cea500bfbde3fb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-replica"></a>sfctl 복제본
서비스 파티션에 속한 복제본을 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
|    배포됨  | Service Fabric 노드에서 배포된 복제본의 세부 정보를 가져옵니다.|
|    deployed-list| Service Fabric 노드에서 배포된 복제본 목록을 가져옵니다.|
|    health    | Service Fabric 상태 저장 서비스 복제본 또는 상태 비저장 서비스 인스턴스를 가져옵니다.|
|    info      | Service Fabric 파티션의 복제본에 대한 정보를 가져옵니다.|
|    list      | Service Fabric 서비스 파티션 복제본에 대한 정보를 가져옵니다.|
|    제거    | 노드에서 실행되는 서비스 복제본을 제거합니다.|
|    report-health| Service Fabric 복제본에 대한 상태 보고서를 보냅니다.|
|    restart   | 노드에서 실행되는 지속된 서비스의 서비스 복제본을 다시 시작합니다.|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Service Fabric 노드에서 배포된 복제본의 세부 정보를 가져옵니다.

Service Fabric 노드에서 배포된 복제본의 세부 정보를 가져옵니다. 정보로는 서비스 종류, 서비스 이름, 현재 서비스 작업, 현재 서비스 작업 시작 날짜 시간, 파티션 ID, 복제본/인스턴스 ID, 보고된 로드 및 기타 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수]| 노드의 이름입니다.|
| --partition-id [필수]| 파티션의 id입니다.|
| --replica-id   [필수]| 복제본의 식별자입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-replica-health"></a>sfctl 복제본 상태
Service Fabric 상태 저장 서비스 복제본 또는 상태 비저장 서비스 인스턴스를 가져옵니다.

Service Fabric 복제본의 상태를 가져옵니다. 상태에 따라 복제본에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id    [필수]| 파티션의 id입니다.|
| --replica-id      [필수]| 복제본의 식별자입니다.|
| --events-health-state-filter| 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --timeout -t             | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                  | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o              | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:              json.|
| --query                  | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose                | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-replica-info"></a>sfctl 복제본 정보
Service Fabric 파티션의 복제본에 대한 정보를 가져옵니다.

응답에는 ID, 역할, 상태, 상태, 노드 이름, 작동 시간 및 기타 복제본에 관한 세부 정보가 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수]| 파티션의 id입니다.|
| --replica-id   [필수]| 복제본의 식별자입니다.|
| --continuation-token  | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-replica-list"></a>sfctl 복제본 목록
Service Fabric 서비스 파티션 복제본에 대한 정보를 가져옵니다.

GetReplicas 끝점은 지정된 파티션의 복제본에 관한 정보를 반환합니다.
응답에는 ID, 역할, 상태, 상태, 노드 이름, 작동 시간 및 기타 복제본에 관한 세부 정보가 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수]| 파티션의 id입니다.|
| --continuation-token  | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-replica-remove"></a>sfctl 복제본 제거
노드에서 실행되는 서비스 복제본을 제거합니다.

이 API는 Service Fabric 클러스터에서 복제본을 제거하여 Service Fabric 복제 오류를 시뮬레이션합니다. 제거는 복제본을 닫고, 복제본을 None 역할로 전환한 다음, 클러스터에서 복제본의 상태 정보 모두를 제거합니다. 이 API는 복제 상태 제거 경로를 테스트하고, 클라이언트 API를 통해 보고서 오류 영구 경로를 시뮬레이션합니다. 경고 ‑ 이 API가 사용될 때 수행되는 안전 검사가 없습니다. 이 API를 잘못 사용하면 상태 저장 서비스에 대한 데이터가 손실될 수 있습니다. 또한 forceRemove 플래그는 동일한 프로세스에서 호스팅되는 다른 모든 복제본에 영향을 미칩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수]| 노드의 이름입니다.|
| --partition-id [필수]| 파티션의 id입니다.|
| --replica-id   [필수]| 복제본의 식별자입니다.|
| --force-remove        | 정상적인 종료 시퀀스를 거치지 않고 강제로 Service Fabric 응용 프로그램이나 서비스를 제거합니다. 이 매개 변수는 복제본을 정상적으로 종료하지 못하게 하는 서비스 코드의 문제로 인해 삭제 시간이 초과되는 응용 프로그램이나 서비스를 강제로 삭제하는 데 사용할 수 있습니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-replica-restart"></a>sfctl 복제본 다시 시작
노드에서 실행되는 지속된 서비스의 서비스 복제본을 다시 시작합니다.

노드에서 실행되는 지속된 서비스의 서비스 복제본을 다시 시작합니다. 경고 ‑ 이 API가 사용될 때 수행되는 안전 검사가 없습니다. 이 API를 잘못 사용하면 상태 저장 서비스에 대한 가용성이 손실될 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수]| 노드의 이름입니다.|
| --partition-id [필수]| 파티션의 id입니다.|
| --replica-id   [필수]| 복제본의 식별자입니다.|
| --timeout -t          | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h             | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:           json.|
| --query               | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
