---
title: Azure Machine Learning 배포 전개 문제 해결 가이드 | Microsoft Docs
description: 배포 및 서비스 만들기에 대한 문제 해결 가이드
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 7b8d2f4fb52c6d4faed8e813779e3b01d1817355
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="troubleshooting-service-deployment-and-environment-setup"></a>문제 해결 서비스 배포 및 환경 설정
다음 정보는 모델 관리 환경을 설정할 때 오류의 원인을 확인하는 데 도움이 될 수 있습니다.

## <a name="model-management-environment"></a>모델 관리 환경
### <a name="contributor-permission-required"></a>참여자 권한 필요
웹 서비스의 배포를 위한 클러스터를 설정하려면 구독 또는 리소스 그룹에 대한 참여자 액세스 권한이 필요합니다.

### <a name="resource-availability"></a>리소스 가용성
환경 리소스를 프로비전할 수 있도록 구독에서 사용 가능한 리소스가 충분히 있어야 합니다.

### <a name="subscription-caps"></a>구독 한도
사용자 구독에 환경 리소스를 프로비전할 수 없는 청구 한도가 있을 수 있습니다. 프로비전을 사용하려면 한도를 제거하세요.

### <a name="enable-debug-and-verbose-options"></a>디버그 및 자세한 정보 표시 옵션을 사용하도록 설정
환경이 프로비전될 때 정보를 디버그 및 추적하려면 설정 명령에서 `--debug` 및 `--verbose` 플래그를 사용합니다.

```
az ml env setup -l <location> -n <name> -c --debug --verbose 
```

## <a name="service-deployment"></a>서비스 배포
다음 정보는 배포 중 또는 웹 서비스 호출 시 오류의 원인을 파악하는 데 도움이 될 수 있습니다.

### <a name="service-logs"></a>서비스 로그
서비스 CLI의 `logs` 옵션은 Docker 및 Kubernetes의 로그 데이터를 제공합니다.

```
az ml service logs realtime -i <web service id>
```

추가로 로그 설정을 하려면 `--help`(또는 `-h`) 옵션을 사용합니다.

```
az ml service logs realtime -h
```

### <a name="debug-and-verbose-options"></a>디버그 및 자세한 정보 표시 옵션
서비스가 전개 될 때 디버그 로그를 표시하려면 `--debug` 플래그를 사용합니다.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --debug
```

서비스가 전개 될 때 추가적인 세부 정보를 표시하려면 `--verbose` 플래그를 사용합니다.

```
az ml service create realtime -m <modelfile>.pkl -f score.py -n <service name> -r python --verbose
```

### <a name="enable-request-logging-in-app-insights"></a>App Insights에서 로그인 요청을 사용하도록 설정
요청 수준 로깅을 사용하도록 웹 서비스를 만들 때 `-l` 플래그를 true로 설정합니다. 요청 로그는 Azure에서 사용자 환경에 대한 App Insights 인스턴스에 기록됩니다. 이 인스턴스는 `az ml env setup` 명령을 사용할 때 사용한 환경 이름을 사용하여 검색합니다.

- 서비스를 만들 때 `-l`을 true로 설정합니다.
- Azure Portal에서 App Insights를 엽니다. 환경 이름을 사용하여 App Insights 인스턴스를 찾습니다.
- App Insights에서 최상위 메뉴의 검색을 클릭하여 결과를 확인합니다.
- 또는 `Analytics` > `Exceptions` > `exceptions take | 10`으로 이동합니다.


### <a name="add-error-handling-in-scoring-script"></a>점수 매기기 스크립트에 오류 처리 추가
`scoring.py` 스크립트의 **run** 함수에서 예외 처리를 사용하여 웹 서비스 출력의 일부로 오류 메시지를 반환합니다.

Python 예제:
```
    try:
        <code to load model and score>
   except Exception as e:
        return(str(e))
```

## <a name="other-common-problems"></a>다른 일반적인 문제
- azure-cli-ml의 pip 설치가 Windows 컴퓨터에서 `cannot find the path specified` 오류와 함께 실패하는 경우 긴 경로 지원을 사용하도록 설정해야 합니다. https://blogs.msdn.microsoft.com/jeremykuhne/2016/07/30/net-4-6-2-and-long-paths-on-windows-10/을 참조하세요. 
- `env setup` 명령이 `LocationNotAvailableForResourceType`을 나타내며 실패하면 Machine Learning 리소스에 대해 잘못된 위치(지역)를 사용하고 있는 것일 수 있습니다. `-l` 매개 변수로 지정된 위치가 `eastus2`, `westcentralus` 또는 `australiaeast`인지 확인하세요.
- `env setup` 명령이 `Resource quota limit exceeded`를 나타내며 실패하면 구독에서 사용할 수 있는 충분한 코어가 있는지와 리소스가 다른 프로세스에서 사용되고 있지 않은지 확인합니다.
- `env setup` 명령이 `Invalid environment name. Name must only contain lowercase alphanumeric characters`를 나타내며 실패하면 서비스 이름에 대문자, 기호 또는 밑줄(_)이 포함되어 있지 않은지 확인합니다(예: *my_environment*).
- `service create` 명령이 `Service Name: [service_name] is invalid. The name of a service must consist of lower case alphanumeric characters (etc.)`를 나타내며 실패하면 서비스 이름이 3~32자 사이이고, 소문자 영숫자로 시작 및 끝나고, 대문자, 기호(하이픈(-) 및 마침표(. ) 또는 밑줄(_) 제외)를 포함하지 않는지 확인합니다(예: *my_webservice*).
- 웹 서비스를 호출할 때 `502 Bad Gateway` 오류가 발생하면 다시 시도합니다. 이 오류는 일반적으로 컨테이너가 클러스터에 아직 배포되지 않았음을 의미합니다.
- 서비스를 만들 때 `CrashLoopBackOff` 오류가 발생하면 로그를 확인합니다. 일반적으로 **init** 함수에 종속성이 누락된 결과입니다.
