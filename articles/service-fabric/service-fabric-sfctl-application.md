---
title: Microsoft Azure Service Fabric CLI- sfctl 응용프로그램 | Microsoft Docs
description: Service Fabric CLI sfctl 응용프로그램 명령을 설명합니다.
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
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: fe0ef5c81b1ef6bef298e65cde3649c9464089d8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="sfctl-application"></a>sfctl application
응용 프로그램 및 응용 프로그램 종류를 만들고, 삭제하고, 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| create       | 지정한 설명을 사용하여 Microsoft Azure Service Fabric 응용 프로그램을 만듭니다.|
| delete       | 기존 Microsoft Azure Service Fabric 응용 프로그램을 삭제합니다.|
| 배포됨     | Microsoft Azure Service Fabric 노드에서 배포된 응용 프로그램에 대한 정보를 가져옵니다.|
| deployed-health | Service Fabric 노드에 배포된 응용 프로그램의 상태에 대한 정보를 가져옵니다.|
| deployed-list| Microsoft Azure Service Fabric 노드에서 배포된 응용 프로그램의 목록을 가져옵니다.|
| health       | Microsoft Azure Service Fabric 응용 프로그램의 상태를 가져옵니다.|
| info         | Microsoft Azure Service Fabric 응용 프로그램에 대한 정보를 가져옵니다.|
| list         | 매개 변수로 지정된 필터와 일치하는 Microsoft Azure Service Fabric 클러스터에서 만든 응용 프로그램 목록을 가져옵니다.|
| load | Microsoft Azure Service Fabric 응용 프로그램에 대한 로드 정보를 가져옵니다. |
| manifest     | 응용 프로그램 유형을 설명하는 매니페스트를 가져옵니다.|
| provision    | 외부 저장소의 .sfpkg 패키지를 사용하거나 이미지 저장소의 응용 프로그램 패키지를 사용하는 클러스터를 통해 Service Fabric 응용 프로그램 유형을 프로비전하거나 등록합니다.|
| report-health| Microsoft Azure Service Fabric 응용 프로그램에 대한 상태 보고서를 보냅니다.|
| 형식         | 지정된 이름과 정확히 일치하는 Microsoft Azure Service Fabric 클러스터에서 응용 프로그램 종류의 목록을 가져옵니다.|
| type-list    | Microsoft Azure Service Fabric 클러스터에서 응용 프로그램의 유형 목록을 가져옵니다.|
| unprovision  | 클러스터에서 Microsoft Azure Service Fabric 응용 프로그램 유형을 제거하거나 등록 취소합니다.|
| 업그레이드      | Microsoft Azure Service Fabric 클러스터에서 응용 프로그램 업그레이드를 시작합니다.|
| upgrade-resume  | Microsoft Azure Service Fabric 클러스터에 응용 프로그램을 다시 업그레이드하기 시작합니다.|
| upgrade-rollback| Service Fabric 클러스터에서 현재 진행 중인 응용 프로그램 업그레이드의 롤백을 시작합니다.|
| upgrade-status  | 이 응용 프로그램에 수행된 최신 업그레이드에 대한 세부 정보를 가져옵니다.|
| upload       | Microsoft Azure Service Fabric 응용 프로그램 패키지를 이미지 저장소에 복사합니다.|

## <a name="sfctl-application-create"></a>sfctl application create
지정한 설명을 사용하여 Microsoft Azure Service Fabric 응용 프로그램을 만듭니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-name    [필수]| '패브릭'을 포함하는, 응용 프로그램 이름 URI 체계입니다.|
| --app-type    [필수]| 응용 프로그램 매니페스트에 있는 응용 프로그램 유형 이름입니다.|
| --app-version [필수]| 응용프로그램     매니페스트에서 정의된 대로 응용 프로그램 유형의 버전입니다.|
| --max-node-count     | Microsoft Azure Service Fabric이 이 응용 프로그램에 대해 용량을 예약하는 노드의 최대 수입니다. 이 응용 프로그램의 서비스가 모든 해당 노드에 배치되지는 않습니다.|
| --metrics            | JSON 인코딩된 응용프로그램 용량 메트릭 설명 목록입니다. 메트릭은 응용 프로그램이 있는 각각의 노드에 대한 용량의 집합과 연결된 이름으로 정의됩니다.|
| --min-node-count     | Microsoft Azure Service Fabric이 이 응용 프로그램에 대해 용량을 예약하는 노드의 최소 수입니다. 이 응용 프로그램의 서비스가 모든 해당 노드에 배치되지는 않습니다.|
| --parameters         | JSON 인코딩된 응용 프로그램 매개 변수 목록은 응용프로그램을 만들 때 적용되기 위해 재정의합니다.|
| --timeout -t         | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug              | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h            | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o          | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:     json.|
| --query              | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose            | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-delete"></a>sfctl application delete
기존 Microsoft Azure Service Fabric 응용 프로그램을 삭제합니다.

기존 Microsoft Azure Service Fabric 응용 프로그램을 삭제합니다. 응용 프로그램을 삭제하려면 먼저 만들어야 합니다. 응용프로그램을 삭제하면 해당 응용프로그램의 일부인 모든 서비스가 삭제됩니다. 기본적으로 Microsoft Azure Service Fabric은 서비스 복제를 정상적으로 닫은 후 다음 서비스를 삭제합니다. 하지만 서비스에서 복제본을 정상적으로 닫는 데 문제가 있으면 삭제 작업이 오래 걸리거나 중단될 수 있습니다. 선택적인 ForceRemove 플래그를 사용하여 정상적인 닫기 시퀀스를 건너 뛰고 응용 프로그램 및 모든 서비스를 강제로 삭제합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수]| 응용 프로그램의 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 ‘fabric://myapp/app1’인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp~app1”이고 이전 버전에서는 “myapp/app1”입니다.|
| --force-remove          | 정상적인 종료 시퀀스를 거치지 않고 강제로 Service Fabric 응용 프로그램이나 서비스를 제거합니다. 이 매개 변수는 복제본을 정상적으로 종료하지 못하게 하는 서비스 코드의 문제로 인해 삭제 시간이 초과되는 응용 프로그램이나 서비스를 강제로 삭제하는 데 사용할 수 있습니다.|
| --timeout -t            | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                 | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h               | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o             | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:        json.|
| --query                 | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose               | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Microsoft Azure Service Fabric 노드에서 배포된 응용 프로그램에 대한 정보를 가져옵니다.

Microsoft Azure Service Fabric 노드에서 배포된 응용 프로그램에 대한 정보를 가져옵니다.  이 쿼리는 제공되는 응용 프로그램 ID가 시스템 응용 프로그램용인 경우 시스템 응용 프로그램 정보를 반환합니다. 결과는 활성, 활성화 중 및 상태 다운로드 중인 배포된 응용 프로그램을 포함합니다. 이 쿼리에서는 노드 이름이 클러스터의 노드에 해당해야 합니다. 제공된 노드 이름이 클러스터의 활성 Service Fabric 노드를 가리키지 않으면 쿼리가 실패합니다.
     
### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수]| 응용 프로그램의 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 ‘fabric://myapp/app1’인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp~app1”이고 이전 버전에서는 “myapp/app1”입니다.|
| --node-name [필수]| 노드의 이름입니다.|
| --timeout -t            | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                 | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h               | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o             | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값:        json.|
| --query                 | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose               | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-health"></a>sfctl application health
Microsoft Azure Service Fabric 응용 프로그램의 상태를 가져옵니다.

Microsoft Azure Service Fabric 응용 프로그램의 상태를 반환합니다. 응답은 확인, 오류 또는 상태 경고를 보고합니다. 엔터티를 Health 스토어에서 찾을 수 없다면 Error를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id                 [필수]| 응용 프로그램의 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 ‘fabric:/myapp/app1’인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp~app1”이고 이전 버전에서는 “myapp/app1”입니다.|
| --deployed-applications-health-state-filter| 배포된 응용 프로그램 상태 개체의 필터링이 상태를 기반으로 한 응용 프로그램 상태 쿼리의 결과값으로 반환되도록 허용합니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 배포된 응용 프로그램만 반환됩니다. 모든 배포된 응용 프로그램은 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 배포된 응용 프로그램의 상태가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --events-health-state-filter            | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --exclude-health-statistics | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔티티 수를 보여줍니다.|
| --services-health-state-filter          | 상태에 따라 서비스 상태 쿼리의 결과로 반환된 서비스 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 서비스만 반환됩니다. 모든 서비스는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 서비스 상태가 반환됩니다. - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다. - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다. - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다. - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다. - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다. - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다.|
| --timeout -t                            | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                                 | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                               | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                             | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query                                 | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose                               | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-info"></a>sfctl application info
Microsoft Azure Service Fabric 응용 프로그램에 대한 정보를 가져옵니다.

Microsoft Azure Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 이름이 매개 변수로 지정된 것과 일치하는 응용 프로그램에 대한 정보를 반환합니다. 응답에는 이름, 유형, 상태, 매개 변수 및 응용 프로그램에 관한 기타 세부 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id      [필수]| 응용 프로그램의 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 “fabric:/myapp/app1”인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp~app1”이고 이전 버전에서는 “myapp/app1”입니다.|
| --exclude-application-parameters| 응용 프로그램 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다.|
| --timeout -t                 | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                      | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                    | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                  | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.             기본값: json.|
| --query                      | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose                    | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-list"></a>sfctl application list
매개 변수로 지정된 필터와 일치하는 Microsoft Azure Service Fabric 클러스터에서 만든 응용 프로그램 목록을 가져옵니다.

Microsoft Azure Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 매개 변수로 지정된 필터와 일치하는 응용 프로그램에 대한 정보를 가져옵니다. 응답에는 이름, 유형, 상태, 매개 변수 및 응용 프로그램에 관한 기타 세부 정보가 포함됩니다. 응용 프로그램이 한 페이지에 맞지 않는 경우, 결과 한 페이지가 연속 토큰으로 반환되어 다음 페이지를 가져오는 데 사용될 수 있습니다. 필터 ApplicationTypeName 및 ApplicationDefinitionKindFilter는 동시에 지정할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
|--application-definition-kind-filter| Service Fabric 응용 프로그램을 정의하는 데 사용되는 메커니즘인 ApplicationDefinitionKind에서 필터링하는 데 사용합니다. - 기본 - 기본값으로, “All”을 선택할 때와 동일한 함수를 수행합니다. 값은 0입니다. - All - 입력과 일치하는 모든 ApplicationDefinitionKind 값을 검색하는 필터입니다. 값은 65535입니다. - ServiceFabricApplicationDescription - 입력과 일치하는 ApplicationDefinitionKind 값 ServiceFabricApplicationDescription을 검색하는 필터입니다. 값은 1입니다. - Compose - 입력과 일치하는 ApplicationDefinitionKind 값 Compose를 검색하는 필터입니다. 값은 2입니다.|
| --application-type-name      | 쿼리할 응용 프로그램을 필터링하는 데 사용되는 응용 프로그램 유형 이름입니다. 이 값은 응용 프로그램 유형 버전을 포함할 수 없습니다.|
| --continuation-token         | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다.|
| --exclude-application-parameters| 응용 프로그램 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다.|
| --max-results|페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다.|
| --timeout -t                 | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                      | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                    | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                  | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.             기본값: json.|
| --query                      | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose                    | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-load"></a>sfctl application load
Microsoft Azure Service Fabric 응용 프로그램에 대한 로드 정보를 가져옵니다.

Microsoft Azure Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 이름이 매개 변수로 지정된 것과 일치하는 응용 프로그램에 대한 로드 정보를 반환합니다. 응답에는 이름, 최소 노드, 최대 노드, 앱이 현재 차지하는 노드 수, 응용 프로그램에 대한 응용 프로그램 부하 메트릭 정보가 포함됩니다.

### <a name="arguments"></a>인수
|인수|설명|
| --- | --- |
|--application-id [필수]| 응용 프로그램의 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "~" 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 ‘fabric://myapp/app1’인 경우 응용 프로그램 ID가 6.0 이상에서는 “myapp~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --timeout -t               | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수
|인수|설명|
| --- | --- |
|--debug                    | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
    --help -h                  | 이 도움말 메시지 및 종료를 표시합니다.|
    --output -o                | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
    --query                    | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
    --verbose                  | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
응용 프로그램 유형을 설명하는 매니페스트를 가져옵니다.
        
응용 프로그램 유형을 설명하는 매니페스트를 가져옵니다. 응답에 응용 프로그램 매니페스트 XML이 문자열로 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name    [필수]| 응용 프로그램 유형의 이름입니다.|
| --application-type-version [필수]| 응용 프로그램 유형의 버전입니다.|
| --timeout -t                      | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                           | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                         | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                       | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.                  기본값: json.|
| --query                           | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose                         | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-provision"></a>sfctl application provision
외부 저장소의 .SFPKG 패키지를 사용하거나 이미지 저장소의 응용 프로그램 패키지를 사용하는 클러스터를 통해 Service Fabric 응용 프로그램 유형을 프로비전하거나 등록합니다.

클러스터를 통해 Service Fabric 응용 프로그램 유형을 프로비전합니다. 이것이 있어야 새로운 응용 프로그램을 인스턴스화할 수 있습니다. 프로비전 작업은 relativePathInImageStore 또는 외부 SFPKG의 URI를 사용하여 지정된 응용 프로그램 패키지에서 수행할 수 있습니다. 외부 프로비전이 설정되어 있지 않으면 이 명령은 이미지 저장소에서 응용 프로그램 패키지를 프로비전합니다.
        


### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-package-download-uri| HTTP 또는 HTTPS 프로토콜을 사용하여 응용 프로그램 패키지를 다운로드할 수 있는 ‘.sfpkg’ 응용 프로그램 패키지의 경로입니다. 외부 저장소에서 프로비전에만 해당합니다. 응용 프로그램 패키지는 파일을 다운로드하도록 가져오기 작업을 제공하는 외부 저장소에 저장할 수 있습니다. 지원되는 프로토콜은 HTTP 및 HTTPS이며, 경로는 읽기 권한을 허용해야 합니다.|
| --application-type-build-path       | 프로비전 종류 이미지 저장소에만 해당합니다. 이전 업로드 작업 중 지정된 이미지 저장소에 있는 응용 프로그램 패키지에 대한 상대 경로입니다. |
| --application-type-name| 외부 저장소에서 프로비전에만 해당합니다. 응용 프로그램 유형 이름은 응용 프로그램 매니페스트에 있는 응용 프로그램 유형의 이름을 나타냅니다.|
| --application-type-version| 외부 저장소에서 프로비전에만 해당합니다. 응용 프로그램 유형 버전은 응용 프로그램 매니페스트에 있는 응용 프로그램 유형의 버전을 나타냅니다.|
| --external-provision| 응용 프로그램 패키지를 등록하거나 프로비전할 수 있는 위치입니다. 외부 저장소에 이전에 업로드된 응용 프로그램 패키지를 위한 프로비전임을 나타냅니다. 응용 프로그램 패키지는 확장명이 *.sfpkg로 끝납니다.|
| --no-wait| 프로비전을 비동기적으로 수행할지 여부를 나타냅니다.  true로 설정한 경우 요청이 시스템에 의해 수락되면 프로비전 작업이 반환되며, 프로비전 작업은 어떠한 시간 제한 없이 계속됩니다. 기본값은 False입니다. 대형 응용 프로그램 패키지의 경우 값을 true로 설정하는 것이 좋습니다.|
| --timeout -t                      | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                              | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                            | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                          | 출력 형식.  허용되는 값: json, jsonc, 테이블,                     tsv.  기본값: json.|
| --query                              | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose                            | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-type"></a>sfctl application type

지정된 이름과 정확히 일치하는 Microsoft Azure Service Fabric 클러스터에서 응용 프로그램 종류의 목록을 가져옵니다.

Microsoft Azure Service Fabric 클러스터에 프로비전된 또는 프로비전되는 프로세스에 있는 응용 프로그램 유형에 대한 정보를 반환합니다. 이러한 결과는 이름이 매개 변수로 지정된 것과 정확히 일치하며 주어진 쿼리 매개 변수를 준수해야 하는 응용 프로그램 유형에서 나온 값입니다. 하나의 응용 프로그램 형식으로 반환된 각 버전과 함께 응용 프로그램 형식 이름과 일치하는 응용 프로그램 형식의 모든 버전입니다. 응답에는 이름 , 버전, 상태 및 응용 프로그램에 관한 기타 세부 정보가 포함됩니다. 이것은 페이징된 쿼리로 응용 프로그램 유형 모두가 한 페이지에 맞지 않는 경우, 결과 한 페이지가 연속 토큰으로 반환되어 다음 페이지를 가져오는 데 사용될 수 있습니다. 예를 들어 10개의 응용 프로그램 유형이 있지만 페이지는 처음 세 개의 응용 프로그램 유형에만 맞거나, 최대 결과가 3으로 설정된다면, 3이 반환됩니다. 결과의 나머지 부분에 액세스하려면 다음 쿼리에 반환된 연속 토큰을 사용하여 후속 페이지를 검색합니다. 후속 페이지가 없다면 공백인 연속 토큰이 반환됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name [필수]| 응용 프로그램 유형의 이름입니다.|
| --application-type-version        | 응용 프로그램 유형의 버전입니다.|
| --continuation-token           | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다.|
| --exclude-application-parameters  | 응용 프로그램 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다.|
| --max-results                  | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다.|
| --timeout -t                   | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                        | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                      | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                    | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.               기본값: json.|
| --query                        | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose                      | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
클러스터에서 Microsoft Azure Service Fabric 응용 프로그램 유형을 제거하거나 등록 취소합니다.

클러스터에서 Microsoft Azure Service Fabric 응용 프로그램 유형을 제거하거나 등록 취소합니다. 이 작업은 응용 프로그램 형식의 모든 응용 프로그램 인스턴스가 삭제된 경우에만 수행될 수 있습니다. 응용 프로그램 형식이 등록 취소되면 이 특정 응용 프로그램 형식에 대해서는 새 응용 프로그램 인스턴스가 만들어질 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name    [필수]| 응용 프로그램 유형의 이름입니다.|
| --application-type-version [필수]| 응용프로그램 매니페스트에서 정의된 응용 프로그램 유형의 버전입니다.|
|--async-parameter                    | 프로비전 해제를 비동기적으로 수행할지 여부를 나타내는 플래그입니다. true로 설정한 경우 요청이 시스템에 의해 수락되면 프로비전 해제 작업이 반환되며, 프로비전 해제 작업은 어떠한 시간 제한 없이 계속됩니다. 기본값은 False입니다. 단, 프로비전된 대형 응용 프로그램 패키지의 경우 true로 설정하는 것이 좋습니다.|
| --timeout -t                      | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                           | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                         | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                       | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.                  기본값: json.|
| --query                           | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose                         | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Microsoft Azure Service Fabric 클러스터에서 응용 프로그램 업그레이드를 시작합니다.

제공된 응용 프로그램 업그레이드 매개 변수의 유효성을 검사하고 매개 변수가 유효한 경우 응용 프로그램 업그레이드를 시작합니다. 업그레이드 설명은 기존의 응용 프로그램 설명을 대신합니다. 즉, 매개 변수가 지정되지 않은 경우 응용 프로그램의 기존 매개 변수를 빈 매개 변수 목록으로 덮어씁니다. 이 경우 응용 프로그램은 응용 프로그램 매니페스트에서의 매개 변수 기본값을 사용합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --앱-id             [필수]| 응용 프로그램의 id입니다. 일반적으로 'fabric:' URI 스키마가 없는 응용 프로그램의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 '~' 문자로 구분됩니다. 예를 들어 응용 프로그램 이름이 ‘fabric:/myapp/app1’인 경우 응용 프로그램 ID가 6.0 이상에서는 ‘myapp~app1’이고 이전 버전에서는 ‘myapp/app1’입니다.|
| --app-version [필수]| 대상 응용 프로그램 버전입니다.|
| --app-version [필수]| JSON 인코딩된 응용 프로그램 매개 변수 목록은 응용 프로그램을 업그레이드할 때 적용되기 위해 재정의합니다.|
| --default-service-health-policy| 기본적으로 서비스 유형의 상태를 평가하는 데 사용되는 JSON 인코딩된 상태 정책 사양입니다.|
| --failure-action            | 모니터링된 업그레이드가 모니터링 정책 또는 상태 정책 위반을 발견할 때 수행할 작업입니다.|
| --force-restart             | 코드 버전이 변경되지 않은 경우에도 업그레이드 동안 프로세스를 강제로 다시 시작합니다.|
| --health-check-retry-timeout| 응용 프로그램이나 클러스터가 정상 상태가 아닌 경우 실패 작업을 실행하기 전에 상태 평가를 다시 시도하는 기간입니다. 밀리초 단위로 측정.  기본값: PT0H10M0S|
| --health-check-stable-duration | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 응용 프로그램 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다.            밀리초 단위로 측정.  기본값: PT0H2M0S|
| --health-check-wait-duration| 업그레이드 도메인을 완료한 후 상태 정책을 적용하기 전에 대기하는 시간입니다. 밀리초 단위로 측정.            기본값: 0.|
| --max-unhealthy-apps        | 비정상적인 배포 응용 프로그램의 최대 허용치입니다. 0과 100 사이의 숫자로 표시합니다.|
| --mode                      | 롤링 업그레이드 동안 상태를 모니터링하는 데 사용되는 모드입니다.            기본값: UnmonitoredAuto.|
| --replica-set-check-timeout | 예기치 않은 문제가 있을 때 업그레이드 도메인의 처리를 차단하고 가용성의 손실을 방지하는 최대 시간입니다. 초 단위로 측정됩니다.|
| --service-health-policy     | 서비스 형식 이름 단위 서비스 형식 상태 정책으로 JSON 인코딩된 맵입니다. 맵은 기본적으로 비어 있습니다.|
| --timeout -t                | 서버 시간 제한(초).  기본값: 60.|
| --upgrade-domain-timeout    | FailureAction이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다. 밀리초 단위로 측정.  기본값: P10675199DT02H48M05.4775807S|
| --upgrade-timeout           | FailureAction이 실행되기 전에 전체 업그레이드를 완료해야 하는 시간입니다. 밀리초 단위로 측정.  기본값: P10675199DT02H48M05.4775807S|
| --warning-as-error          | 동일한 심각도를 갖는 상태 평가 경고를 오류로 처리합니다.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                     | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                   | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                 | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.            기본값: json.|
| --query                     | JMESPath 쿼리 문자열. 자세한 내용과 예제는 http://jmespath.org/를 참조하세요.|
| --verbose                   | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Microsoft Azure Service Fabric 응용 프로그램 패키지를 이미지 저장소에 복사합니다.

필요에 따라 패키지의 각 파일에 대한 업로드 진행률을 표시합니다. 업로드 진행률을 `stderr`로 보냅니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --path [필수]| 로컬 응용 프로그램 패키지 경로입니다.|
|--imagestore-string| 응용 프로그램 패키지를 업로드할 대상 이미지 저장소입니다.  기본값: fabric:ImageStore|
| --show-progress  | 큰 패키지에 대한 파일 업로드 진행률을 표시합니다.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug       | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h     | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o   | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query       | JMESPath 쿼리 문자열. 자세한 내용은 http://jmespath.org/을 참조하세요.|
| --verbose     | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
