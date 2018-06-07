---
title: Azure Container Instances 컨테이너 그룹
description: 컨테이너 그룹이 Azure Container Instances에서 작동하는 방법 이해
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: cb3d8c27a82c7dfc5fd71c1c7d589e81890e5cfb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances의 컨테이너 그룹

Azure Container Instances의 최상위 리소스는 *컨테이너 그룹*입니다. 이 문서에서는 컨테이너 그룹의 정의와 이들이 지원하는 시나리오 유형에 대해 설명합니다.

## <a name="how-a-container-group-works"></a>컨테이너 그룹 작동 방식

컨테이너 그룹은 같은 호스트 컴퓨터에서 예약되어 있는 컨테이너 컬렉션입니다. 컨테이너 그룹의 컨테이너는 수명 주기, 로컬 네트워크 및 저장소 볼륨을 공유합니다. [Kubernetes][kubernetes-pod] 및 [DC/OS][dcos-pod]의 *Pod* 개념과 유사합니다.

다음 다이어그램은 여러 컨테이너가 포함된 컨테이너 그룹의 예를 보여줍니다.

![컨테이너 그룹 다이어그램][container-groups-example]

이 예제 컨테이너 그룹은 다음과 같습니다.

* 단일 호스트 컴퓨터에서 예약됩니다.
* DNS 이름 레이블이 할당됩니다.
* 1개의 노출 포트가 있는 단일 공용 IP 주소를 노출합니다.
* 두 개의 컨테이너로 구성됩니다. 하나의 컨테이너는 포트 80을 수신하고, 다른 컨테이너는 포트 5000을 수신합니다.
* 볼륨 탑재로 2개의 Azure 파일 공유가 포함되어 있으며, 각 컨테이너는 공유 중 하나를 로컬에서 탑재합니다.

> [!NOTE]
> 현재 다중 컨테이너 그룹은 Linux 컨테이너에 제한됩니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="deployment"></a>배포

컨테이너 *그룹*에는 최소 1개의 vCPU 및 1GB의 메모리 할당 리소스가 있습니다. 컨테이너 그룹 내의 개별 *컨테이너*에 vCPU 1개 및 메모리 1GB 미만을 프로비전할 수 있습니다. 컨테이너 그룹 내에서 리소스 배포는 컨테이너 그룹 수준에서 설정된 한도 내에서 여러 컨테이너로 사용자 지정할 수 있습니다. 예를 들어 1개의 vCPU가 할당된 컨테이너 그룹 내에 각각 0.5개의 vCPU가 있는 두 컨테이너가 있을 수 있습니다.

## <a name="networking"></a>네트워킹

컨테이너 그룹은 IP 주소와 해당 IP 주소에서 포트 네임스페이스를 공유합니다. 외부 클라이언트가 그룹 내 컨테이너에 도달하게 지원하려면 IP 주소와 컨테이너에서 해당 포트를 공개해야 합니다. 그룹 내의 컨테이너가 포트 네임스페이스를 공유하고 있으므로 포트 매핑이 지원되지 않습니다. 그룹 내 컨테이너는 해당 포트가 그룹의 IP 주소에 외부적으로 노출되어 있지 않은 경우에도 이들이 노출한 포트의 localhost를 통해 서로 도달할 수 있습니다.

## <a name="storage"></a>Storage

컨테이너 그룹 내에서 탑재할 외부 볼륨을 지정할 수 있습니다. 해당 볼륨을 그룹의 개별 컨테이너 내에 있는 특정 경로에 매핑할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

다중 컨테이너 그룹은 단일 기능 작업을 적은 수의 컨테이너 이미지로 나누려는 경우에 유용합니다. 이러한 이미지는 다른 팀에서 제공될 수 있으며 별도의 리소스 요구 사항을 가질 수 있습니다.

사용 예는 다음과 같습니다.

* 응용 프로그램 컨테이너 및 로깅 컨테이너. 로깅 컨테이너는 주 응용 프로그램에서 출력한 로그 및 메트릭을 수집하여 장기 저장소로 기록합니다.
* 응용 프로그램 컨테이너 및 모니터링 컨테이너. 모니터링 컨테이너는 응용 프로그램이 올바르게 실행 중이고 응답하고 있는지 확인하기 위해 응용 프로그램에 정기적으로 요청을 보내고, 그렇지 않은 경우 경고를 올립니다.
* 웹 응용 프로그램을 처리하는 컨테이너와 원본 제어에서 최신 콘텐츠를 풀링하는 컨테이너

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 통해 다중 컨테이너 그룹 배포 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [컨테이너 그룹 배포](container-instances-multi-container-group.md)

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
