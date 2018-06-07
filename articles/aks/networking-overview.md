---
title: AKS(Azure Kubernetes Service)의 네트워크 구성
description: AKS(Azure Kubernetes Service)의 기본 및 고급 네트워크 구성에 대해 알아봅니다.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/07/2018
ms.author: marsma
ms.openlocfilehash: 818bae2e05f6a3256ccbf0cbcc901dd337b9a260
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="network-configuration-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 네트워크 구성

AKS(Azure Kubernetes Service) 클러스터를 만들 때 두 가지 네트워킹 옵션 인 **기본** 또는 **고급** 중에서 선택할 수 있습니다.

## <a name="basic-networking"></a>기본 네트워킹

**기본** 네트워킹 옵션은 AKS 클러스터 생성을 위한 기본 구성입니다. 클러스터 및 해당 Pod의 네트워크 구성은 완전히 Azure에서 관리되므로, 사용자 지정 VNet 구성을 요구하지 않는 배포에 적합합니다. 기본 네트워킹을 선택하면 클러스터에 할당된 서브넷 또는 IP 주소 범위와 같은 네트워크 구성을 제어할 필요가 없습니다.

기본 네트워킹용으로 구성된 AKS 클러스터의 노드는 [kubenet][kubenet] Kubernetes 플러그 인을 사용합니다.

## <a name="advanced-networking"></a>고급 네트워킹

**고급** 네트워킹에서는 구성하는 Azure VNet(Virtual Network)에 Pod가 배치되며, VNet 리소스에 자동으로 연결되고 VNet이 제공하는 풍부한 기능 집합과 통합될 수 있습니다.
현재, 고급 네트워킹은 [Azure Portal][portal]에서 또는 Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포할 때만 사용할 수 있습니다.

고급 네트워킹용으로 구성된 AKS 클러스터의 노드는 [Azure CNI(컨테이너 네트워킹 인터페이스)][cni-networking] Kubernetes 플러그 인을 사용합니다.

![각 노드를 단일 Azure VNet에 연결하는 브리지가 있는 두 노드를 보여주는 다이어그램][advanced-networking-diagram-01]

## <a name="advanced-networking-features"></a>고급 네트워킹 기능

고급 네트워킹은 다음과 같은 이점을 제공합니다.

* 기존 VNet에 AKS 클러스터를 배포하거나 클러스터에 대해 새 VNet 및 서브넷을 만듭니다.
* 클러스터의 모든 Pod는 VNet의 IP 주소가 할당되고, 클러스터의 다른 Pod 및 VNet의 다른 VM과 직접 통신할 수 있습니다.
* Pod는 피어링된 VNet의 다른 서비스에 연결되고, ExpressRoute와 S2S(사이트 간) VPN 연결을 통해 온-프레미스 네트워크에 연결될 수 있습니다. 또한 온-프레미스에서 Pod에 연결할 수도 있습니다.
* Azure Load Balancer를 통해 외부 또는 내부로 Kubernetes 서비스를 노출합니다. 또한 기본 네트워킹의 기능도 제공합니다.
* 서비스 끝점이 사용되도록 설정된 서브넷의 Pod는 Azure 서비스(예: Azure Storage 및 SQL DB)에 안전하게 연결될 수 있습니다.
* UDR(사용자 정의 경로)을 사용하여 Pod에서 네트워크 가상 어플라이언스로 트래픽을 라우팅합니다.
* Pod는 공용 인터넷의 리소스에 액세스할 수 있습니다. 또한 기본 네트워킹의 기능도 제공합니다.

> [!IMPORTANT]
> 고급 네트워킹용으로 구성된 AKS 클러스터의 각 노드는 최대 **30개의 Pod**를 호스트할 수 있습니다. Azure CNI 플러그 인에서 사용하기 위해 프로비전된 각 VNet은 **4096개의 IP 주소**(/20)로 제한됩니다.

## <a name="configure-advanced-networking"></a>고급 네트워킹 구성

Azure Portal에서 [AKS 클러스터를 만들 경우](kubernetes-walkthrough-portal.md) 고급 네트워킹을 위해 다음 매개 변수를 구성할 수 있습니다.

**가상 네트워크**: Kubernetes 클러스터를 배포하려는 VNet입니다. 클러스터에 대해 새 VNet을 만들려는 경우 *새로 만들기*를 선택하고 *가상 네트워크 만들기* 섹션의 단계를 따릅니다.

**서브넷**: 클러스터를 배포하려는 VNet 내의 서브넷입니다. 클러스터에 대해 VNet에 새 서브넷을 만들려는 경우 *새로 만들기*를 선택하고 *서브넷 만들기* 섹션의 단계를 따릅니다.

**Kubernetes 서비스 주소 범위**: Kubernetes 클러스터 서비스 IP에 대한 IP 주소 범위입니다. 이 범위는 클러스터의 VNet IP 주소 범위에 속하지 않아야 합니다.

**Kubernetes DNS 서비스 IP 주소**: 클러스터의 DNS 서비스의 IP 주소입니다. 이 주소는 *Kubernetes 서비스 주소 범위*에 속해야 합니다.

**Docker 브리지 주소**: Docker 브리지에 할당할 IP 주소 및 네트워크 마스크입니다. 이 IP 주소는 클러스터의 VNet IP 주소 범위에 속하지 않아야 합니다.

Azure Portal의 다음 스크린샷은 AKS 클러스터를 만드는 동안 이러한 설정을 구성하는 방법의 예를 보여줍니다.

![Azure Portal의 고급 네트워킹 구성][portal-01-networking-advanced]

## <a name="plan-ip-addressing-for-your-cluster"></a>클러스터에 대한 IP 주소 지정 계획

고급 네트워킹으로 구성한 클러스터에는 추가 계획이 필요합니다. VNet 및 해당 서브넷의 크기는 클러스터에서 동시에 실행하려는 Pod의 수와 크기 조정 요건에 부합되어야 합니다.

Pod 및 클러스터 노드의 IP 주소는 VNet 내의 지정된 서브넷에서 할당됩니다. 각 노드는 노드 자체의 IP인 기본 IP와 노드에 예약된 Pod에 할당되어 있는 Azure CNI에서 미리 구성된 30개의 추가 IP 주소로 구성됩니다. 클러스터를 스케일 아웃할 때 각 노드는 서브넷의 IP 주소로 비슷하게 구성됩니다.

앞서 언급한 것처럼 Azure CNI 플러그 인에서 사용하기 위해 프로비전된 각 VNet은 **4096개의 IP 주소**(/20)로 제한됩니다. 고급 네트워킹용으로 구성된 클러스터의 각 노드는 최대 **30개의 Pod**를 호스트할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

다음과 같은 질문과 대답이 **고급** 네트워킹 구성에 적용됩니다.

* *Azure CLI를 사용하여 고급 네트워킹을 구성할 수 있나요?*

  번호 현재, 고급 네트워킹은 Azure Portal에서 또는 Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포할 때만 사용할 수 있습니다.

* *내 클러스터 서브넷에 VM을 배포할 수 있나요?*

  번호 Kubernetes 클러스터에서 사용되는 서브넷에 VM을 배포하는 것은 지원되지 않습니다. VM은 동일한 VNet의 다른 서브넷에 배포할 수 있습니다.

* *Pod별 네트워크 정책을 구성할 수 있나요?*

  번호 Pod별 네트워크 정책은 현재 지원되지 않습니다.

* *구성 가능한 노드로 배포할 수 있는 Pod의 최대 수는 얼마나 되나요?*

  기본적으로 각 노드는 최대 30개의 포드를 호스트할 수 있습니다. 현재, Resource Manager 템플릿을 사용하여 클러스터를 배포할 때 `maxPods` 속성을 수정하여 최대값만 변경할 수 있습니다.

* *AKS 클러스터를 만드는 동안 만든 서브넷에 대해 추가 속성을 구성하려면 어떻게 해야 하나요? 예를 들어, 서비스 끝점을 구성한다고 가정해보세요.*

  AKS 클러스터를 만드는 동안 만든 VNet과 서브넷에 대한 속성의 전체 목록은 Azure Portal의 표준 VNet 구성 페이지에서 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

### <a name="networking-in-aks"></a>AKS의 네트워킹

AKS의 네트워킹에 대한 자세한 내용은 다음 문서를 참조하세요.

[AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소 사용](static-ip.md)

[AKS(Azure Container Service)에서 HTTPS 수신](ingress.md)

[AKS(Azure 컨테이너 서비스)를 통해 내부 부하 분산 장치 사용](internal-lb.md)

### <a name="acs-engine"></a>ACS 엔진

[ACS 엔진(Azure Container Service 엔진)][acs-engine]은 Azure에서 Docker 지원 클러스터를 배포하는 데 사용할 수 있는 Azure Resource Manager 템플릿을 생성하는 오픈 소스 프로젝트입니다. Kubernetes, DC/OS, Swarm 모드 및 Swarm 오케스트레이터는 ACS 엔진을 사용하여 배포할 수 있습니다.

ACS 엔진을 사용하여 만든 Kubernetes 클러스터는 [kubenet][kubenet] 및 [Azure CNI][cni-networking] 플러그 인을 둘 다 지원합니다. 따라서 ACS 엔진은 기본 및 고급 네트워킹 시나리오를 둘 다 지원합니다.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[aks-ssh]: aks-ssh.md
