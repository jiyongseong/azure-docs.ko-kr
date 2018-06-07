---
title: Azure Service Fabric CLI- sfctl cluster | Microsoft Docs
description: Service Fabric CLI sfctl cluster 명령을 설명합니다.
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
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: c83dc3eeb6ca0d66b0c70236354fd7bab80f355f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-cluster"></a>sfctl cluster
Service Fabric 클러스터를 선택하고, 관리하고, 운영합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
|    code-versions| Service Fabric 클러스터에 프로비전 되는 패브릭 코드 버전을 가져옵니다.|
|    config-versions | Service Fabric 클러스터에 프로비전 되는 패브릭 구성 버전의 목록을 가져옵니다.|
|    health       | Service Fabric 클러스터의 상태를 가져옵니다.|
|    manifest     | Service Fabric 클러스터 매니페스트를 가져옵니다.|
|    operation-cancel| 사용자로 인한 오류 작업을 취소합니다.|
|    operationgit | 제공된 입력으로 필터링한 사용자로 인한 오류 작업 목록을 가져옵니다.|
|    provision     | Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다.|
|    recover-system  | 현재 쿼럼 손실에 걸린 시스템 서비스를 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다.|
|report-health   | Service Fabric 클러스터 대한 상태 보고서를 보냅니다.|
|    선택       | Service Fabric 클러스터 끝점에 연결합니다.|
| unprovision     | Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다.|
|    업그레이드         | Service Fabric 클러스터의 코드 또는 구성 버전 업그레이드를 시작합니다.|
|    upgrade-resume  | 클러스터 업그레이드를 다음 업그레이드 도메인으로 이동합니다.|
|    upgrade-rollback| Service Fabric 클러스터의 업그레이드를 롤백합니다.|
|    upgrade-status  | 현재 클러스터 업그레이드의 진행률을 가져옵니다.|
|upgrade-update  | Service Fabric 클러스터의 업그레이드 매개 변수를 업데이트합니다.|


## <a name="sfctl-cluster-health"></a>sfctl cluster health
Service Fabric 클러스터의 상태를 가져옵니다.

Service Fabric 클러스터의 상태를 가져옵니다. 상태에 따라 클러스터에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다. 마찬가지로, 집계된 상태에 따라 반환된 노드 및 응용 프로그램 컬렉션을 필터링할 NodesHealthStateFilter 및 ApplicationsHealthStateFilter를 사용합니다. 

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --applications-health-state-filter| 상태에 따라 클러스터 상태 쿼리의 결과로 반환된 응용 프로그램 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용 가능한 값은 HealthStateFilter 열거형의 멤버 또는 멤버에 대한 비트 연산에서 가져온 정수 값입니다. 필터와 일치하는 응용 프로그램만 반환됩니다.  모든 응용 프로그램은 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 응용 프로그램의 상태가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --events-health-state-filter   | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다.  값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
|--exclude-health-statistics                   | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔터티 수를 보여줍니다.|
 |   --include-system-application-health-statistics| 상태 통계에 fabric:/System 응용 프로그램 상태 통계를 포함할지 여부를 지정합니다. False(기본값). IncludeSystemApplicationHealthStatistics가 true로 설정된 경우 fabric:/System 응용 프로그램에 속한 엔터티를 포함합니다. 그렇지 않으면 쿼리 결과는 사용자 응용 프로그램에 대해서만 상태 통계를 포함합니다. 이 매개 변수를 적용하려면 상태 통계를 쿼리 결과에 포함해야 합니다.|
| --nodes-health-state-filter    | 상태에 따라 클러스터 상태 쿼리의 결과로 반환된 노드 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 노드만 반환됩니다. 모든 노드는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 노드의 상태가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다.  값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --timeout -t                   | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                        | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                      | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                    | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.                    기본값: json.|
| --query                        | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose                      | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-cluster-manifest"></a>sfctl 클러스터 매니페스트
Service Fabric 클러스터 매니페스트를 가져옵니다.

Service Fabric 클러스터 매니페스트를 가져옵니다. 클러스터 매니페스트에는 클러스터, 보안 구성, 오류 및 업그레이드 도메인 토폴로지 등의 다양한 노드 형식을 포함하는 클러스터의 속성이 들어 있습니다. 이러한 속성은 독립 실행형 클러스터를 배포하는 동안 ClusterConfig.JSON 파일의 일부로 지정됩니다. 그러나 클러스터 매니페스트에 있는 대부분 정보는 다른 배포 시나리오(예를 사용 [Azure Portal](https://portal.azure.com) 사용 시)에서 클러스터 배포할 경우 서비스 패브릭에서 내부적으로 생성됩니다. 클러스터 매니페스트의 내용이 단지 정보용일 경우 사용자는 파일 내용 및 해석 형식에 의존하지 않아야 합니다. 

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t| 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug  | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h| 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query  | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose| 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다.
Service Fabric 클러스터의 코드 또는 구성 패키지의 유효성을 검사하거나 프로비전합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
|--cluster-manifest-file-path| 클러스터 매니페스트 파일 경로입니다.|
|    --code-file-path            | 클러스터 코드 패키지 파일 경로입니다.|
|    --timeout -t                | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h  | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o| 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose  | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Service Fabric 클러스터 끝점에 연결합니다.

보안 클러스터에 연결한 경우, 인증서(.crt) 및 키 파일(.key) 또는 단일 파일을 양쪽 모두(.pem)로 지정 합니다. 둘 다 지정하지 마세요. 보안 클러스터에 연결한 경우 필요에 따라 CA 번들 파일 또는 신뢰할 수 있는 CA 인증서 디렉터리에 대한 경로를 지정합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --endpoint [필수]| 포트 및 HTTP 또는 HTTPS 접두사를 포함하는 클러스터 끝점 URL입니다.|
| --aad             | 인증에 Azure Active Directory를 사용합니다.|
| --ca              | 유효로 처리될 CA 인증서 디렉터리 또는 CA 번들 파일에 대한 경로입니다.|
| --cert            | 클라이언트 인증서 파일 경로입니다.|
| --key             | 클라이언트 인증서 키 파일 경로입니다.|
| --no-verify       | HTTPS 사용 시 인증서에 대한 확인 비활성화, 참고: 안전 하지 않은 옵션 이므로 프로덕션 환경에 사용해서는 안됩니다.|
| --pem             | .pem 파일과 같은 클라이언트 인증서의 경로입니다.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug           | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h         | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o       | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query           | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose         | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Service Fabric 클러스터의 코드 또는 구성 패키지를 프로비전 해제합니다.

Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다. 코드 및 구성을 개별적으로 프로비전 해제하도록 지원합니다.

### <a name="arguments"></a>인수
|인수|설명|
| --- | --- |
|--code-version  | 클러스터 코드 패키지 버전입니다.|
|    --config-version| 클러스터 매니페스트 버전입니다.|
|    --timeout -t    | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수
|인수|설명|
| --- | --- |
|--debug         | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
 |   --help -h       | 이 도움말 메시지 및 종료를 표시합니다.|
 |   --output -o     | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
 |   --query         | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
 |   --verbose       | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|


## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Service Fabric 클러스터의 코드 또는 구성 버전 업그레이드를 시작합니다.
제공된 업그레이드 매개 변수의 유효성을 검사하고 매개 변수가 유효한 경우 Service Fabric 클러스터의 코드 또는 구성 버전 업그레이드를 시작합니다.

### <a name="arguments"></a>인수
|인수|설명|
| --- | --- |
|    --app-health-map                      | 오류를 발생시키기 전의 비정상 응용 프로그램 이름 및 최대 비율 쌍의 JSON 인코딩된 사전.|
 |   --app-type-health-map                 | 오류를 발생시키기 전의 비정상 응용 프로그램 형식 이름 및 최대 비율 쌍의 JSON 인코딩된 사전.|
 |   --code-version                        | 클러스터 코드 버전.|
 |   --config-version                      | 클러스터 구성 버전.|
 |   --delta-health-evaluation             | 각 업그레이드 도메인 완료 후 절대 상태 평가가 아닌 델타 상태 평가를 사용하도록 설정합니다.|
 |   --delta-unhealthy-nodes               | 클러스터를 업그레이드하는 동안 허용되는 노드 상태 저하의 최대 비율입니다.  기본값은 10입니다. 델타는 업그레이드 시작 시 노드 상태와 상태 평가 시 노드 상태 간에 측정됩니다. 클러스터의 전역 상태를 허용 한도 내로 유지하기 위해 업그레이드 도메인 업그레이드가 완료될 때마다 검사가 수행됩니다.|
 |   --failure-action                      | 사용할 수 있는 값: 'Invalid', 'Rollback', 'Manual'.|
 |   --force-restart                       | 다시 시작을 강제합니다.|
 |   --health-check-retry                  | 상태 점검 다시 시도 시간 제한(밀리초 단위로 측정).|
 |   --health-check-stable                 | 안정적 지속 기간 상태 점검(밀리초 단위로 측정).|
  |  --health-check-wait                   | 대기 기간 상태 점검(밀리초 단위로 측정).|
  |  --replica-set-check-timeout           | 복제본 세트 업그레이드 점검 시간 제한(밀리초 단위로 측정).|
 |   --rolling-upgrade-mode                | 사용할 수 있는 값: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  기본값: UnmonitoredAuto.|
  |  --timeout -t                          | 서버 시간 제한(초).  기본값: 60.|
  |  --unhealthy-applications              | 오류를 보고하기 전에 허용되는 비정상 응용 프로그램의 최대 백분율입니다. 예를 들어 응용 프로그램의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 응용 프로그램의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 응용 프로그램이 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 값은 클러스터에 있는 응용 프로그램 인스턴스의 총 수를 비정상 응용 프로그램의 수로 나눠 계산합니다. 이 때 ApplicationTypeHealthPolicyMap에 포함된 응용 프로그램 형식의 응용 프로그램은 제외합니다. 계산값은 적은 수의 응용 프로그램에서 오류 하나를 허용할 수 있도록 반올림됩니다.|
 |   --unhealthy-nodes                     | 오류를 보고하기 전에 허용되는 비정상 노드의 최대 백분율입니다. 예를 들어 노드의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 노드의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 노드가 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 백분율은 클러스터에 있는 노드 총 수를 비정상 노드 수로 나눠 계산합니다. 계산값은 적은 수의 노드에서 오류 하나를 허용할 수 있도록 반올림됩니다. 대형 클러스터에는 항상 복구를 위해 다운되거나 중단되는 노드가 있으므로 이를 감안하여 이 비율을 구성해야 합니다.|
 |   --upgrade-domain-delta-unhealthy-nodes| 클러스터를 업그레이드하는 동안 허용되는 업그레이드 도메인 노드 상태 저하의 최대 비율입니다. 기본값: 15. 델타는 업그레이드 시작 시 업그레이드 도메인 노드 상태와 상태 평가 시 업그레이드 도메인 노드 상태 간에 측정됩니다. 클러스터의 전역 상태를 허용 한도 내로 유지하기 위해 업그레이드 도메인 업그레이드가 완료될 때마다 완료된 업그레이드 도메인에 대한 검사가 수행됩니다.|
 |   --upgrade-domain-timeout              | 업그레이드 도메인 시간 제한(밀리초 단위로 측정).|
 |   --upgrade-timeout                     | 업그레이드 시간 제한(밀리초 단위로 측정).|
 |   --warning-as-error                    | 경고는 오류와 같은 심각도로 처리됩니다.|

### <a name="global-arguments"></a>전역 인수
|인수|설명|
| --- | --- |
|--debug                               | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
|    --help -h                             | 이 도움말 메시지 및 종료를 표시합니다.|
|    --output -o                           | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv. 기본값: json.|
|    --query                               | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
|    --verbose                             | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.