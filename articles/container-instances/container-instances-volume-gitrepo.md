---
title: Azure Container Instances에서 gitRepo 볼륨 탑재
description: gitRepo 볼륨을 탑재하여 컨테이너 인스턴스에 Git 리포지토리를 복제하는 방법을 알아봅니다.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Azure Container Instances에서 gitRepo 볼륨 탑재

*gitRepo* 볼륨을 탑재하여 컨테이너 인스턴스에 Git 리포지토리를 복제하는 방법을 알아봅니다.

> [!NOTE]
> *gitRepo* 볼륨 탑재는 현재 Linux 컨테이너로 제한됩니다. 모든 기능을 Windows 컨테이너에서 제공하려고 합니다. 그 동안 [Azure Container Instances에 대한 할당량 및 지역 가용성](container-instances-quotas.md)에서 현재 플랫폼의 차이점을 찾을 수 있습니다.

## <a name="gitrepo-volume"></a>gitRepo 볼륨

*gitRepo* 볼륨은 디렉터리를 탑재하고 컨테이너 시작 시 지정된 Git 리포지토리를 해당 디렉터리에 복제합니다. 컨테이너 인스턴스의 *gitRepo* 볼륨을 사용하면 응용 프로그램에서 코드를 추가하지 않아도 됩니다.

*gitRepo* 볼륨을 탑재할 때 세 개의 속성을 설정하여 볼륨을 구성할 수 있습니다.

| 자산 | 필수 | 설명 |
| -------- | -------- | ----------- |
| `repository` | 예 | 복제할 Git 리포지토리의 `http://` 또는 `https://`를 포함한 전체 URL입니다.|
| `directory` | 아니오 | 리포지토리를 복제해야 하는 디렉터리입니다. 경로는 "`..`"을 포함하거나 이것으로 시작되지 않아야 합니다.  "`.`"을 지정하면 리포지토리가 볼륨의 디렉터리에 복제됩니다. 그렇지 않을 경우 Git 리포지토리가 볼륨 디렉터리 내 지정된 이름의 하위 디렉터리에 복제됩니다. |
| `revision` | 아니오 | 복제될 수정 내용의 커밋 해시입니다. 지정하지 않으면 `HEAD` 수정 내용이 복제됩니다. |

## <a name="mount-gitrepo-volume-azure-cli"></a>gitRepo 볼륨 탑재: Azure CLI

[Azure CLI](/cli/azure)로 컨테이너 인스턴스를 배포할 때 gitRepo 볼륨을 탑재하려면 `--gitrepo-url` 및 `--gitrepo-mount-path` 매개 변수를 [az container create][az-container-create] 명령에 제공합니다. 선택적으로 복제할 볼륨 내 디렉터리(`--gitrepo-dir`)와 복제된 수정 작업의 커밋 해시(`--gitrepo-revision`)를 지정할 수 있습니다.

이 예제 명령은 [aci-helloworld][aci-helloworld] 샘플 응용 프로그램을 컨테이너 인스턴스의 `/mnt/aci-helloworld`에 복제합니다.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

gitRepo 볼륨이 탑재되었는지 확인하려면 [az container exec][az-container-exec]로 컨테이너에서 셸을 시작하고 디렉터리를 나열합니다.

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>gitRepo 볼륨 탑재: Resource Manager

[Azure Resource Manager 템플릿](/azure/templates/microsoft.containerinstance/containergroups)을 사용하여 gitRepo 볼륨을 탑재하려면 먼저 템플릿의 컨테이너 그룹 `properties` 섹션에서 `volumes` 배열을 입력합니다. 다음으로 *gitRepo* 볼륨을 탑재하려는 컨테이너 그룹에 있는 각 컨테이너의 경우 컨테이너 정의의 `properties` 섹션에서 `volumeMounts` 배열을 채웁니다.

예를 들어 다음과 같은 Resource Manager 템플릿은 단일 컨테이너로 구성된 컨테이너 그룹을 만듭니다. 컨테이너는 *gitRepo* 볼륨 블록으로 지정된 GitHub 리포지토리 두 개를 복제합니다. 두 번째 볼륨에는 복제할 디렉터리를 지정하는 추가 속성과 복제할 특정 수정 내용의 커밋 해시가 포함됩니다.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

이전 템플릿에 정의된 두 복제 리포지토리의 결과 디렉터리 구조는 다음과 같습니다.

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Azure Resource Manager 템플릿을 사용하는 컨테이너 인스턴스 배포의 예제를 보려면 [Azure Container Instances에서 다중 컨테이너 그룹 배포](container-instances-multi-container-group.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Container Instances에서 다른 볼륨 유형을 탑재하는 방법을 알아봅니다.

* [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](container-instances-volume-azure-files.md)
* [Azure Container Instances에서 emptyDir 볼륨 탑재](container-instances-volume-emptydir.md)
* [Azure Container Instances에서 비밀 볼륨 탑재](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec