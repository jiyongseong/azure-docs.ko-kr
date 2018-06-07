---
title: AKS(Azure Kubernetes Service) 내부 부하 분산 장치 만들기
description: AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치를 사용합니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 3/29/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a1a5d6d455086ee34767e92b277936840717bcd6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치 사용

내부 부하 분산은 Kubernetes 서비스가 Kubernetes 클러스터와 동일한 가상 네트워크에서 실행되는 응용 프로그램에 액세스할 수 있게 합니다. 이 문서에서는 AKS(Azure Kubernetes Service)를 사용하여 내부 부하 분산 장치를 만드는 방법을 자세히 설명합니다.

## <a name="create-internal-load-balancer"></a>내부 부하 분산 장치 만들기

내부 부하 분산 장치를 만들려면 다음 예제에 표시된 것처럼 서비스 유형 `LoadBalancer` 및 `azure-load-balancer-internal` 주석으로 서비스 매니페스트를 빌드합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

일단 배포되면 AKS 클러스터와 동일한 가상 네트워크에서 Azure 부하 분산 장치를 만들고 사용할 수 있습니다.

![AKS 내부 부하 분산 장치 이미지](media/internal-lb/internal-lb.png)

서비스 세부 정보를 검색할 경우 `EXTERNAL-IP` 열의 IP 주소는 내부 부하 분산 장치의 IP 주소입니다.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   10s
```

## <a name="specify-an-ip-address"></a>IP 주소 지정

내부 부하 분산 장치를 통해 특정 IP 주소를 사용하려는 경우 부하 분산 장치 사양에 `loadBalancerIP` 속성을 추가합니다. 지정된 IP 주소는 AKS 클러스터와 동일한 서브넷에 상주해야 하며 아직 리소스에 할당되면 안 됩니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

서비스 세부 정보를 검색할 경우 `EXTERNAL-IP`의 IP 주소는 지정된 IP 주소를 반영해야 합니다.

```console
$ kubectl get service azure-vote-front

NAME               TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="delete-the-load-balancer"></a>부하 분산 장치 삭제하기

내부 부하 분산 장치를 사용하여 모든 서비스를 삭제한 경우 부하 분산 장치 자체도 삭제됩니다.

## <a name="next-steps"></a>다음 단계

[Kubernetes 서비스 설명서][kubernetes-services]에서 Kubernetes 서비스를 알아봅니다.

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/