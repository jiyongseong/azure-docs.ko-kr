---
title: Azure Container Service의 DC/OS 에이전트 풀
description: 공용 및 사용자 에이전트 풀이 Azure Container Service 클러스터와 함께 작동하는 방식
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 81059fd75f0e61324221614c4bb8eccd94203478
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure Container Service의 DC/OS 에이전트 풀
Azure Container Service의 DC/OS 클러스터는 2개의 풀, 즉 공용 풀과 개인 풀에 에이전트 노드를 포함합니다. 응용 프로그램을 이러한 두 풀 중 하나에 배포할 수 있으며 이는 컨테이너 서비스의 컴퓨터 간의 액세스 가능성에 영향을 줍니다. 컴퓨터는 인터넷(공용)에 노출되거나 내부(개인)로 유지될 수 있습니다. 이 문서에서는 공용 및 개인 풀이 있는 이유에 대한 간략한 개요를 제공합니다.


* **개인 에이전트**: 개인 에이전트 노드는 라우팅할 수 없는 네트워크를 통해 실행됩니다. 이 네트워크는 관리 영역에서나 공용 영역 에지 라우터를 통해서만 액세스할 수 있습니다. 기본적으로 DC/OS는 사용자 에이전트 노드에서 앱을 시작합니다. 

* **공용 에이전트**: 공용 에이전트 노드는 공개적으로 액세스 가능한 네트워크를 통해 DC/OS 앱과 서비스를 실행합니다. 

DC/OS 네트워크 보안에 대한 자세한 내용은 [DC/OS 설명서](https://dcos.io/docs/1.7/administration/securing-your-cluster/)를 참조하세요.

## <a name="deploy-agent-pools"></a>에이전트 풀 배포

Azure Container Service의 DC/OS 에이전트 풀은 다음과 같이 만들어집니다.

* **개인 풀**은 [DC/OS 클러스터 배포](container-service-deployment.md) 시 지정하는 에이전트 노드 수를 포함합니다. 

* **공용 풀**은 처음에 미리 정의된 수의 에이전트 노드를 포함합니다. 이 풀은 DC/OS 클러스터가 프로비저닝될 때 자동으로 추가됩니다.

개인 풀과 공용 풀은 Azure 가상 머신 규모 집합입니다. 배포 후 이러한 풀의 크기를 조정할 수 있습니다.

## <a name="use-agent-pools"></a>에이전트 풀 사용
기본적으로 **Marathon** 은 새 응용 프로그램을 *사용자* 에이전트 노드에 배포합니다. 응용 프로그램 생성 중에 응용 프로그램을 *공용* 노드에 명시적으로 배포해야 합니다. **선택 사항** 탭을 선택하고 **수락된 리소스 역할** 값에 **slave_public**을 입력합니다. 이 과정은 [여기](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)와 [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) 설명서에 문서화되어 있습니다.

## <a name="next-steps"></a>다음 단계
* [DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.

* DC/OS 컨테이너에 대한 공용 액세스를 허용하기 위해 Azure에서 제공하는 [방화벽을 여는 방법](container-service-enable-public-access.md)에 대해 알아보세요.

