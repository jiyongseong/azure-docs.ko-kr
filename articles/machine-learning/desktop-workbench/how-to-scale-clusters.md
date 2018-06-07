---
title: Machine Learning을 위해 Azure Container Service 클러스터의 크기를 조정하는 방법 | Microsoft Docs
description: ACS 클러스터 크기 조정 - 자동 크기 조정 및 고정 크기 조정, 클러스터의 노드 수를 규모 조정
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 58709b5a5b060552e1bc349a6f720d3f45348ba0
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>클러스터 크기를 조정하여 웹 서비스 처리량 관리

## <a name="why-scale-the-cluster"></a>클러스터 크기를 조정하는 이유

ACS(Azure Container Service) 클러스터 크기를 조정하는 것은 클러스터 크기를 최소로 유지하여 비용을 절감하면서 서비스 처리량을 최적화하는 효과적인 방법입니다. 

자동 크기 조정 기능을 더 잘 이해하려면 세 가지 웹 서비스를 실행하는 다음 클러스터 예제를 고려합니다.

![예제: 클러스터의 세 가지 서비스](media/how-to-scale-clusters/three-services.png)

서비스는 다양한 최대 요구량을 갖습니다. 서비스 1(파란색 선)은 최대 요구 시 40개 Pod가 필요하고, 서비스 2(주황색 선)는 최대 요구 시 38개 Pod가 필요하고, 서비스 3(회색 선)은 최대 요구 시 50개 Pod가 필요합니다. 각 서비스에 필요한 최대 용량을 개별적으로 예약하는 경우 이 클러스터에는 최소한 40+38+50 = 총 128개 Pod가 필요할 수 있습니다.

그러나 특정 시점의 실제 Pod 사용량(그래프에서 검은색 파선으로 표시)을 고려해보세요. 이 경우 *한 번에 사용되는 최대 Pod 수*는 64로, 서비스 3이 최대 사용 상태인 20시에 발생했습니다. 이때 서비스 3은 50개 Pod를 사용하지만 서비스 2는 9개 Pod만 사용하고, 서비스 1은 5개 Pod용합니다. 이것이 이 클러스터의 *최대 사용량*입니다. 즉, 클러스터가 최대 사용량을 개별적으로 고려해서 3개 서비스에 대해 계산된 128개 Pod 요구 사항의 절반인 64개 Pod를 초과해서 사용하는 경우는 없습니다.

단순히 모든 서비스의 최대 요구량을 위한 충분한 리소스를 요구하지 않고 각 서비스의 현재 요구량을 충족하려면 클러스터의 Pod를 다시 할당하여, 즉 크기를 다시 조정하여 클러스터 크기를 줄일 수 있습니다. 이 간단한 예제에서 자동 크기 조정은 필요한 Pod 수를 128개에서 64개로 줄이고 필요한 클러스터 크기를 절반으로 낮춥니다.

Pod 수를 조정하는 것을 비교적 빠른 작업으로, 1분 이내에 진행되므로 서비스의 응답성은 심각하게 영향을 받지 않습니다.

> [!NOTE]
> 클러스터 크기 조정은 요청 대기 시간 문제에 도움이 되지 않습니다. 연산화 목적에 맞게 크기 조정은 성공 수를 늘리고 “서비스를 사용할 수 없음” 오류를 줄여줍니다. 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>ACS 클러스터에서 웹 서비스 수를 조정하는 방법

모델 관리 CLI의 클러스터 설치 옵션은 기본적으로 작업 환경에서 2개의 에이전트와 1개의 Pod를 구성합니다. Kubernetes CLI도 설치합니다.

다음을 조정하여 ACS에 배포한 웹 서비스 수를 조정할 수 있습니다.

* 클러스터의 에이전트 노드 수
* 에이전트 노드에서 실행되는 Kubernetes Pod 복제본 수

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>클러스터의 노드 수 조정

다음 명령은 클러스터의 에이전트 노드 수를 설정합니다.

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

이 설정은 완료하는 데 몇 분 정도 걸립니다. 클러스터의 노드 수 변경에 대한 자세한 내용은 [Container Service 클러스터의 에이전트 노드 수 변경](https://docs.microsoft.com/azure/container-service/container-service-scale)을 참조하세요.

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>클러스터의 Kubernetes Pod 복제본 수 조정
 
Azure Machine Learning CLI 또는 [Kubernetes 대시보드](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/))를 사용하여 클러스터에 할당된 Pod 복제본의 수를 조정할 수 있습니다.

Kubernetes 복제본 Pod에 자세한 내용은 [Kubernetes Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 설명서를 참조하세요.

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>Azure Machine Learning CLI를 사용하여 클러스터 확장

CLI를 사용하여 클러스터 크기를 조정하는 방법에는 다음 두 가지가 있습니다.

- Autoscale
- 고정 크기 조정

자동 크기 조정은 서비스가 생성될 때 기본적으로 활성화되며 대부분의 경우 선호되는 크기 조정 방법입니다.

##### <a name="autoscale"></a>Autoscale

다음 명령은 자동 크기 조정을 사용하도록 설정하고 서비스에 대해 최소 및 최대 복제본 수를 설정합니다.

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

예를 들어 `autoscale-min-replicas`를 5로 설정하면 5개의 복제본이 만들어집니다. 웹 서비스에 대한 최적 수에 도달하려면 이 수를 10과 같은 값으로 설정하고 503 오류 메시지의 수를 모니터링합니다. 그에 따라 해당 수를 조정합니다.


| 매개 변수 이름 | type | 설명 |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | 부울 | 자동 크기 조정의 사용 여부를 지정합니다. 기본값: true |
| `autoscale-min-replicas` | 정수 | Pod의 최소 수를 지정합니다. 0 이상이어야 합니다. 기본값: 1 |
| `autoscale-max-replicas` | 정수 | Pod의 최대 수를 지정합니다. 1 이상이어야 합니다. autoscale-max-replicas가 autoscale-min-replicas보다 작으면 autoscale-max-replicas는 무시됩니다. 기본값: 10 |
| `autoscale-refresh-period-seconds` | 정수 | 자동 크기 조정 새로 고침 간격(초)을 지정합니다. 기본값: 1 |
| `autoscale-target-utilization` | 정수 | 자동 크기 조정의 목표 사용량을 1에서 100 사이 값으로 지정합니다. 기본값: 70 |

자동 크기 조정은 다음 두 조건을 확인하는 방식으로 작동합니다.

1. 대상 사용량이 충족되는지 여부
2. 크기 조정 결과가 최소 및 최대 설정을 절대 초과하지 않는지 여부

클러스터의 서비스는 클러스터 리소스에 대해 경합합니다. 크기가 자동으로 조정된 서비스는 RPS(초당 요청 수)가 늘어날 때 클러스터 리소스 사용량을 늘리고, RPS가 줄어들 때 리소스를 서서히 해제합니다. 서비스가 획득할 수 있는 이러한 리소스가 존재하는 한, 클러스터 리소스는 요청 시에 획득됩니다.

자동 크기 조정 매개 변수 사용에 대한 자세한 내용은 [모델 관리 명령줄 인터페이스 참조](model-management-cli-reference.md) 설명서를 참조하세요.

##### <a name="static-scale"></a>고정 크기 조정

일반적으로 고정 크기 조정은 자동 크기 조정에 따라 클러스터 크기가 줄어들지 않으므로 사용하지 않도록 합니다. 그렇더라도 고정 크기 조정이 권장되는 경우가 있을 수 있습니다. 예를 들어 클러스터가 단일 서비스 전용으로 사용될 경우 자동 크기 조정은 이점이 없습니다. 모든 클러스터 리소스가 해당 서비스에 할당되어야 하기 때문입니다.

클러스터 크기를 고정적으로 조정하려면 자동 크기 조정을 해제해야 합니다. 다음 명령을 실행하여 자동 크기 조정을 사용하지 않도록 설정합니다.

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

자동 크기 조정을 해제한 후 다음 명령을 실행하면 서비스에 대한 복제본 수가 직접적으로 조정됩니다.

```
az ml service update realtime -i <service id> -z <replica count>
```
 
클러스터의 노드 수 변경에 대한 자세한 내용은 “Container Service 클러스터의 에이전트 노드 수 변경”을 참조하세요.

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>Kubernetes 대시보드를 사용하여 복제본 수 조정

명령줄에서 다음을 입력합니다.

```
kubectl proxy
```

Windows에서 Kubernetes 설치 위치는 경로에 자동으로 추가되지 않습니다. 먼저 설치 폴더로 이동합니다.

```
c:\users\<user name>\bin
```

명령을 실행하면 다음과 정보용 메시지가 표시됩니다.

```
Starting to serve on 127.0.0.1:8001
```

해당 포트가 이미 사용 중인 경우 다음 예제와 비슷한 메시지가 표시됩니다.

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

*--port* 매개 변수를 사용하여 대체 포트 번호를 지정할 수 있습니다.

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

대시보드 서버를 시작한 후 브라우저를 열고 다음 URL을 입력합니다.

```
127.0.0.1:<port number>/ui
```

대시보드 기본 화면의 왼쪽 탐색 모음에서 **배포**를 클릭합니다. 탐색 창이 표시되지 않으면 왼쪽 위에서 이 아이콘 ![짧은 세 개의 가로선으로 이루어진 메뉴](media/how-to-scale-clusters/icon-hamburger.png)을 선택합니다.

수정할 배포를 찾은 후 오른쪽에서 이 아이콘 ![세 개의 세로 점으로 이루어진 메뉴 아이콘](media/how-to-scale-clusters/icon-kebab.png)을 클릭하고 **YAML 보기/편집**을 클릭합니다.

배포 편집 화면에서 *사양* 노드를 찾은 후 *복제본* 값을 수정하고 **업데이트**를 클릭합니다.
