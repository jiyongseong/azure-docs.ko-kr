---
title: 웹 UI를 사용하여 Azure Kubernetes 클러스터 관리
description: AKS에서 Kubernetes 대시보드 사용
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: cdb406c5a0a314562ae886c797c5ebd9dc5f8796
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)와 Kubernetes 대시보드

Azure CLI를 사용하여 Kubernetes 대시보드를 시작할 수 있습니다. 이 문서는 Azure CLI를 사용하여 Kubernetes 대시보드를 시작하는 과정 및 기본적인 일부 대시보드 작업 과정도 안내합니다. Kubernetes 대시보드에 대한 자세한 내용은 [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 AKS 클러스터를 만들고 클러스터와 kubectl 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

또한 Azure CLI 버전 2.0.27 이상이 설치되고 구성되어 있어야 합니다. 버전을 찾으려면 az --version을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="start-kubernetes-dashboard"></a>Kubernetes 대시보드 시작

`az aks browse` 명령을 사용하여 Kubernetes 대시보드를 시작합니다. 이 명령을 실행할 때 리소스 그룹과 클러스터 이름을 바꿉니다.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

이 명령은 개발 시스템과 Kubernetes API 사이에 프록시를 만들고 웹 브라우저에서 Kubernetes 대시보드를 엽니다.

## <a name="run-an-application"></a>응용 프로그램 실행

Kubernetes 대시보드에서 오른쪽 상단 창의 **만들기** 단추를 클릭합니다. 배포 이름을 `nginx`로 지정하고 이미지 이름으로 `nginx:latest`를 입력합니다. **서비스**에서 **외부**를 선택하고 포트와 대상 포트 모두에 대해 `80`을 입력합니다.

준비가 되면 **배포**를 클릭하여 배포를 만듭니다.

![Kubernetes 서비스 만들기 대화 상자](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>응용 프로그램 보기

응용 프로그램에 대한 상태는 Kubernetes 대시보드에서 확인할 수 있습니다. 응용 프로그램이 실행되면 각 구성 요소 옆에 녹색 확인란이 표시됩니다.

![Kubernetes 포드](./media/container-service-kubernetes-ui/complete-deployment.png)

응용 프로그램 Pod에 대한 자세한 내용을 보려면 왼쪽 메뉴에서 **Pod**를 클릭하고 **NGINX** Pod를 선택합니다. 여기에서 리소스 사용량과 같은 Pod별 정보를 볼 수 있습니다.

![Kubernetes 리소스](./media/container-service-kubernetes-ui/running-pods.png)

응용 프로그램의 IP 주소를 찾으려면 왼쪽 메뉴에서 **서비스**를 클릭하고 **NGINX** 서비스를 선택합니다.

![nginx 보기](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>응용 프로그램 편집

Kubernetes 대시보드는 응용 프로그램을 만들고 보는 것 외에도 응용 프로그램 배포를 편집하고 업데이트하는 데 사용할 수 있습니다.

배포를 편집하려면 왼쪽 메뉴에서 **배포**를 클릭한 다음 **NGINX** 배포를 선택합니다. 마지막으로, 오른쪽 위 탐색 표시줄에서 **편집**을 선택합니다.

![kubernetes 편집](./media/container-service-kubernetes-ui/view-deployment.png)

1인 `spec.replica` 값을 찾아서 이 값을 3으로 변경합니다. 이렇게 하면 NGINX 배포의 복제본 수가 1에서 3으로 증가합니다.

준비가 되면 **업데이트**를 선택합니다.

![kubernetes 편집](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>다음 단계

Kubernetes 대시보드에 대한 자세한 내용은 Kubernetes 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli