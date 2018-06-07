---
title: Azure Container Instances에서 비밀 볼륨 탑재
description: Container Instances에서 액세스할 수 있도록 중요한 정보를 저장하기 위해 비밀 볼륨을 탑재하는 방법을 알아봅니다.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: marsma
ms.openlocfilehash: 47abb4c305b1997fa3f44c07aa85dd01e5a94f1f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Azure Container Instances에서 비밀 볼륨 탑재

Container Instances에서 *비밀* 볼륨을 탑재하여 컨테이너 그룹의 컨테이너에서 중요한 정보를 저장하고 검색하는 방법을 알아봅니다.

> [!NOTE]
> *비밀* 볼륨 탑재는 현재 Linux 컨테이너로 제한됩니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="secret-volume"></a>비밀 볼륨

*비밀* 볼륨을 사용하여 중요한 정보를 컨테이너 그룹의 컨테이너에 제공할 수 있습니다. *비밀* 볼륨은 볼륨 내의 파일에 지정된 비밀을 저장하므로 컨테이너 그룹의 컨테이너가 액세스할 수 있습니다. *비밀* 볼륨의 비밀을 사용하면 응용 프로그램 코드의 데이터베이스 자격 증명 또는 SSH 키와 같은 중요한 데이터 배치를 방지할 수 있습니다.

모든 *비밀* 볼륨은 [tmpfs][tmpfs], RAM 지원 파일 시스템에 의해 지원되며 비휘발성 저장소에는 해당 콘텐츠를 쓸 수 없습니다.

## <a name="mount-a-secret-volume"></a>비밀 볼륨 탑재

컨테이너 인스턴스에서 *secret* 볼륨을 탑재하려면 [Azure Resource Manager 템플릿](/azure/templates/microsoft.containerinstance/containergroups)을 사용하여 배포해야 합니다.

먼저 템플릿의 `volumes`컨테이너 그룹의 배열`properties` 섹션을 채웁니다. 다음으로 *secret* 볼륨을 탑재하려는 컨테이너 그룹에 있는 각 컨테이너의 경우 컨테이너 정의의 `properties` 섹션에서 `volumeMounts` 배열을 채웁니다.

예를 들어 다음과 같은 Resource Manager 템플릿은 단일 컨테이너로 구성된 컨테이너 그룹을 만듭니다. 컨테이너가 두 Base64 인코딩 보안으로 구성된 *비밀* 볼륨을 탑재합니다.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Azure Resource Manager 템플릿을 사용하는 컨테이너 인스턴스 배포의 예제를 보려면 [Azure Container Instances에서 다중 컨테이너 그룹 배포](container-instances-multi-container-group.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Container Instances에서 다른 볼륨 유형을 탑재하는 방법을 알아봅니다.

* [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](container-instances-volume-azure-files.md)
* [Azure Container Instances에서 emptyDir 볼륨 탑재](container-instances-volume-emptydir.md)
* [Azure Container Instances에서 gitRepo 볼륨 탑재](container-instances-volume-gitrepo.md)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs