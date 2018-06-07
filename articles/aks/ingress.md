---
title: AKS(Azure Kubernetes Service) 클러스터로 수신 구성
description: AKS(Azure Kubernetes Service) 클러스터에 NGINX 수신 컨트롤러를 설치하고 구성합니다.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: b999792876f82de9500dccf9e6263f85e3e3105e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="https-ingress-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 HTTPS 수신

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 외부 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에서 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 샘플을 연습합니다. 또한 [KUBE-LEGO][kube-lego] 프로젝트는 [Let's Encrypt][lets-encrypt] 인증서를 자동으로 생성하고 구성하는 데 사용됩니다. 마지막으로, 여러 응용 프로그램이 AKS 클러스터에서 실행되며 단일 주소를 통해 각 응용 프로그램에 액세스할 수 있습니다.

## <a name="prerequisite"></a>필수 요소

Helm CLI 설치 - 설치 지침에 대해서는 Helm CLI [설명서][helm-cli]를 참조하세요.

## <a name="install-an-ingress-controller"></a>수신 컨트롤러 설치

Helm을 사용하여 NGINX 수신 컨트롤러를 설치합니다. 자세한 배포 정보는 NGINX 수신 컨트롤러 [설명서][nginx-ingress]를 참조하세요.

차트 리포지토리를 업데이트합니다.

```console
helm repo update
```

NGINX 수신 컨트롤러를 설치합니다. 이 예제에서는 `kube-system` 네임스페이스에서 컨트롤러를 설치하고 선택한 네임스페이스에서 수정할 수 있습니다.

```
helm install stable/nginx-ingress --namespace kube-system --set rbac.create=false --set rbac.createRole=false --set rbac.createClusterRole=false
```

설치하는 동안 Azure 공용 IP 주소가 수신 컨트롤러에 대해 생성됩니다. 공용 IP 주소를 얻으려면 kubectl get service 명령을 사용합니다. 서비스에 IP 주소가 할당할 될 때까지 다소 시간이 걸릴 수 있습니다.

```console
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
eager-crab-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
eager-crab-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

수신 규칙이 만들어지지 않았기 때문에 공용 IP 주소를 검색하면 NGINX 수신 컨트롤러 기본 404 페이지로 라우팅됩니다.

![기본 NGINX 백 엔드](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>DNS 이름 구성

HTTPS 인증서를 사용하므로 수신 컨트롤러 IP 주소에 대한 FQDN 이름을 구성해야 합니다. 이 예에서는 Azure CLI를 사용하여 Azure FQDN을 만듭니다. FQDN에 사용할 수신 컨트롤러 IP 주소와 이름으로 스크립트를 업데이트합니다.

```
#!/bin/bash

# Public IP address
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

이제 FQDN을 통해 수신 컨트롤러에 액세스할 수 있게 됩니다.

## <a name="install-kube-lego"></a>KUBE-LEGO 설치

NGINX 수신 컨트롤러는 TLS 종료를 지원합니다. HTTPS 인증서를 검색하고 구성하는 여러 가지 방법이 있지만, 이 문서에서는 자동 [Lets Encrypt][lets-encrypt] 인증서 생성 및 관리 기능을 제공하는 [KUBE-LEGO][kube-lego]를 사용하는 방법을 보여줍니다.

KUBE-LEGO 컨트롤러를 설치하려면 다음과 같은 Helm 설치 명령을 사용합니다. 이메일 주소를 소속 조직의 이메일 주소로 업데이트합니다.

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

KUBE-LEGO 구성에 대한 자세한 내용은 [KUBE-LEGO 설명서][kube-lego]를 참조하세요.

## <a name="run-application"></a>응용 프로그램 실행

현재 수신 컨트롤러와 인증서 관리 솔루션이 구성되었습니다. 이제 AKS 클러스터에서 응용 프로그램 몇 개를 실행합니다.

이 예에서 Helm은 간단한 hello world 응용 프로그램의 여러 인스턴스를 실행하는 데 사용됩니다.

응용 프로그램을 실행하기 전에 개발 시스템에서 Azure 샘플 Helm 리포지토리를 추가합니다.

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 다음 명령을 사용하여 AKS hello world 차트를 실행합니다.

```
helm install azure-samples/aks-helloworld
```

이제 hello world 응용 프로그램의 두 번째 인스턴스를 설치합니다.

두 번째 인스턴스에서는 두 응용 프로그램을 시각적으로 구분할 수 있도록 새 제목을 지정합니다. 고유한 서비스 이름도 지정해야 합니다. 이러한 구성은 다음 명령에서 볼 수 있습니다.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>수신 경로 만들기

두 응용 프로그램은 모두 Kubernetes 클러스터에서 실행되고 있지만, `ClusterIP` 유형의 서비스로 구성되었습니다. 따라서 인터넷에서 응용 프로그램에 액세스할 수 있습니다. 액세스가 가능하도록 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 응용 프로그램 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

파일 이름 `hello-world-ingress.yaml`을 만들고 다음 YAML에 복사합니다.

주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우팅됩니다. 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

`kubectl apply` 명령을 사용하여 수신 리소스를 만듭니다.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>수신 구성 테스트

Kubernetes 수신 컨트롤러의 FQDN으로 이동하면 hello world 응용 프로그램이 보여야 합니다.

![응용 프로그램 예제 1](media/ingress/app-one.png)

`/hello-world-two` 경로를 사용하여 수신 컨트롤러의 FQDN으로 이동하면 사용자 지정 제목의 hello world 응용 프로그램이 보여야 합니다.

![응용 프로그램 예제 2](media/ingress/app-two.png)

또한 연결이 암호화되며 Let's Encrypt를 사용하여 발급된 인증서가 사용됩니다.

![Lets encrypt 인증서](media/ingress/certificate.png)

## <a name="next-steps"></a>다음 단계

이 문서에서 보여준 소프트웨어에 대해 자세히 알아보세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
