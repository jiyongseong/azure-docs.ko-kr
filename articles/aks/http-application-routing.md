---
title: Azure Container Service(AKS)의 HTTP 응용 프로그램 라우팅 추가 기능
description: Azure Container Service(AKS)에서 HTTP 응용 프로그램 라우팅 추가 기능 사용
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/25/2018
ms.author: laevenso
ms.openlocfilehash: 6e5a81742b4a6b21e5cfa28d8e772430f8ae30ba
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="http-application-routing"></a>HTTP 응용 프로그램 라우팅

HTTP 응용 프로그램 라우팅 솔루션을 사용하면 AKS 클러스터에 배포된 응용 프로그램에 쉽게 액세스할 수 있습니다. 사용하도록 설정되면 솔루션이 AKS 클러스터에 수신 컨트롤러를 구성합니다. 또한 응용 프로그램을 배포할 때 솔루션이 응용 프로그램 끝점에 대해 공개적으로 액세스할 수 있는 DNS 이름도 만듭니다.

이 추가 기능을 사용하도록 설정하면 구독에 DNS 영역을 만듭니다. DNS 비용에 대한 자세한 내용은 [DNS 가격 책정][dns-pricing]을 참조하세요.

## <a name="http-routing-solution-overview"></a>HTTP 라우팅 솔루션 개요

추가 기능은 두 구성 요소, [Kubernetes 수신 컨트롤러][ingress] 및 [외부 DNS][external-dns] 컨트롤러를 배포합니다.

- **수신 컨트롤러** - 수신 컨트롤러가 LoadBalancer 유형의 Kubernetes 서비스를 사용하여 인터넷에 노출됩니다. 수신 컨트롤러는 응용 프로그램 끝점에 대한 경로를 만드는 [Kubernetes 수신 리소스][ingress-resource]를 감시하고 구현합니다.
- **외부 DNS 컨트롤러** - Kubernetes 수신 리소스를 감시하고 클러스터별 DNS 영역에 DNS A 레코드를 만듭니다.

## <a name="deploy-http-routing"></a>HTTP 라우팅 배포

AKS 클러스터를 배포할 때 Azure Portal을 통해 HTTP 응용 프로그램 라우팅 추가 기능을 사용하도록 설정할 수 있습니다.

![HTTP 라우팅 기능을 사용하도록 설정](media/http-routing/create.png)

클러스터가 배포되면 자동 생성 AKS 리소스 그룹을 찾아 DNS 영역을 선택합니다. DNS 영역 이름을 기록해 둡니다. 이 이름은 응용 프로그램을 AKS 클러스터에 배포할 때 필요합니다.

![DNS 영역 이름 가져오기](media/http-routing/dns.png)

## <a name="use-http-routing"></a>HTTP 라우팅 사용

HTTP 응용 프로그램 라우팅 솔루션은 다음과 같이 주석 처리된 수신 리소스에서만 트리거될 수 있습니다.

```
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

파일 `samples-http-application-routing.yaml`을 만들고 다음 YAML에 복사합니다. 43줄에서 이 문서의 마지막 단계에서 수집한 DNS 영역 이름으로 `<CLUSTER_SPECIFIC_DNS_ZONE>`을 업데이트합니다.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

[kubectl apply][kubectl-apply] 명령을 사용하여 리소스를 만듭니다.

```
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

cURL 또는 브라우저를 사용하여 `samples-http-application-routing.yaml` 파일의 호스트 섹션에 지정된 호스트 이름으로 이동합니다. 응용 프로그램이 인터넷을 통해 사용하기까지 최대 1분이 걸릴 수 있습니다.

```
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="troubleshooting"></a>문제 해결

외부 DNS 응용 프로그램의 응용 프로그램 로그를 보려면 [kubectl logs][kubectl-logs] 명령을 사용합니다. 로그에서 A 및 TXT DNS 레코드가 성공적으로 만들어졌는지 확인합니다.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Azure Portal의 DNS 영역 리소스에서 이러한 레코드를 볼 수도 있습니다.

![DNS 레코드 가져오기](media/http-routing/clippy.png)

[kubectl logs][kubectl-logs] 명령을 사용하여 Nginx 수신 컨트롤러에 대한 응용 프로그램 로그를 봅니다. 로그에서 수신 리소스 생성 및 컨트롤러의 재로드를 확인합니다. 모든 HTTP 작업이 기록됩니다.

```
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="cleanup"></a>정리

이 단계에서 만든 연결된 Kubernetes 개체를 제거합니다.

```
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>다음 단계

AKS에 HTTPS 보안 수신 컨트롤러를 설치하는 방법에 대한 자세한 내용은 [Azure Container Service(AKS)의 HTTPS 수신][ingress-https]을 참조하세요.

<!-- LINKS - internal -->
[ingress-https]: ./ingress.md

<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource