---
title: 웹 UI를 사용하여 Azure Kubernetes 클러스터 관리
description: Azure Container Service에서 Kubernetes 웹 UI 사용
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 0680551d3a87c942574a4eac70fa380cc1e9b5d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Azure Container Service에서 Kubernetes 웹 UI 사용

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

## <a name="prerequisites"></a>필수 조건
이 연습에서는 [Azure Container Service를 사용하여 Kubernetes 클러스터를 만들었다고](container-service-kubernetes-walkthrough.md) 가정합니다.


또한 Azure CLI 2.0 및 `kubectl` 도구가 설치되어 있다고 가정합니다.

다음을 실행하여 `az` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ az --version
```

`az` 도구가 설치되어 있지 않으면 [여기](https://github.com/azure/azure-cli#installation)의 지침을 따르세요.

다음을 실행하여 `kubectl` 도구가 설치되어 있는지 테스트할 수 있습니다.

```console
$ kubectl version
```

`kubectl`이 설치되어 있지 않으면 다음을 실행할 수 있습니다.

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>개요

### <a name="connect-to-the-web-ui"></a>웹 UI에 연결
다음을 실행하여 Kubernetes 웹 UI를 시작할 수 있습니다.

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

그러면 로컬 컴퓨터를 Kubernetes 웹 UI에 연결하는 보안 프록시와 통신하도록 구성된 웹 브라우저가 열립니다.

### <a name="create-and-expose-a-service"></a>서비스 만들기 및 공개
1. Kubernetes 웹 UI의 오른쪽 위에 있는 창에서 **만들기** 단추를 클릭합니다.

    ![Kubernetes 만들기 UI](./media/container-service-kubernetes-ui/create.png)

    응용 프로그램을 만들기 시작할 수 있는 대화 상자가 열립니다.

2. 이름 `hello-nginx`를 지정합니다. Docker의 [`nginx` 컨테이너](https://hub.docker.com/_/nginx/)를 사용하고 이 웹 서비스의 3개 복제본을 배포합니다.

    ![Kubernetes 포드 만들기 대화 상자](./media/container-service-kubernetes-ui/nginx.png)

3. **서비스**에서 **외부**를 선택하고 포트 80을 입력합니다.

    이 설정은 세 개의 복제본에 트래픽 부하를 분산합니다.

    ![Kubernetes 서비스 만들기 대화 상자](./media/container-service-kubernetes-ui/service.png)

4. **배포**를 클릭하여 이러한 컨테이너 및 서비스를 배포합니다.

    ![Kubernetes 배포](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>컨테이너 보기
**배포**를 클릭한 후에 UI에 배포 시의 서비스 보기가 표시됩니다.

![Kubernetes 상태](./media/container-service-kubernetes-ui/status.png)

**Pod** 아래에 있는 UI의 왼쪽에서 원으로 나타낸 각 Kubernetes 개체의 상태를 확인할 수 있습니다. 원이 부분적으로만 채워져 있으면 개체가 아직 배포 중인 것입니다. 개체가 완전히 배포되면 녹색 확인 표시가 표시됩니다.

![Kubernetes가 배포됨](./media/container-service-kubernetes-ui/deployed.png)

모든 기능이 실행 중이면 Pod 중 하나를 클릭하여 실행 중인 웹 서비스에 대한 자세한 내용을 확인할 수 있습니다.

![Kubernetes 포드](./media/container-service-kubernetes-ui/pods.png)

**Pod** 보기에서는 Pod의 컨테이너에 대한 정보와 해당 컨테이너에서 사용되는 CPU 및 메모리 리소스를 확인할 수 있습니다.

![Kubernetes 리소스](./media/container-service-kubernetes-ui/resources.png)

리소스가 보이지 않으면 모니터링 데이터가 전파될 때까지 몇 분 정도 기다려야 할 수 있습니다.

컨테이너에 대한 로그를 보려면 **로그 보기**를 클릭합니다.

![kubernetes 로그](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>서비스 보기
컨테이너를 실행하는 것 외에도, Kubernetes UI는 부하 분산 장치를 프로비전하여 클러스터에 컨테이너로 트래픽을 가져오는 외부 `Service`를 만들었습니다.

왼쪽 탐색 창에서 **서비스**를 클릭하여 모든 서비스를 볼 수 있습니다(하나여야 함).

![Kubernetes 서비스](./media/container-service-kubernetes-ui/service-deployed.png)

해당 보기에서 서비스에 할당된 외부 끝점(IP 주소)을 볼 수 있습니다.
해당 IP 주소를 클릭하면 부하 분산 장치 뒤에서 Nginx 컨테이너가 실행되는 것이 표시됩니다.

![nginx 보기](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>서비스 크기 조정
UI에서 개체를 보는 것 외에도 Kubernetes API 개체를 편집하고 업데이트할 수 있습니다.

먼저 왼쪽 탐색 창에서 **배포**를 클릭하여 서비스에 대한 배포를 확인할 수 있습니다.

해당 보기에서 복제본 세트를 클릭한 다음 위쪽 탐색 모음에서 **편집**을 클릭합니다.

![kubernetes 편집](./media/container-service-kubernetes-ui/edit.png)

`spec.replicas` 필드를 `2`로 편집하고 **업데이트**를 클릭합니다.

이렇게 하면 Pod 중 하나가 삭제되어 삭제할 복제본이 두 개가 됩니다.

 

