---
title: ACR Build(Azure Container Registry Build)를 사용하여 OS 및 프레임워크 패치 자동화
description: 클라우드에서 안전하고 자동화된 컨테이너 이미지 빌드 및 패치를 제공하는 Azure Container Registry의 기능 모음인 ACR Build에 대해 간략히 소개합니다.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 3ef91270bceb5865bdbdf9c436e4519595a3dc09
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>ACR Build를 사용하여 OS 및 프레임워크 패치 자동화

컨테이너는 새 수준의 가상화를 제공하여 인프라 및 운영 요구 사항에서 응용 프로그램 및 개발자 종속성을 격리합니다. 그러나 이 응용 프로그램 가상화가 패치되는 방법을 해결해야 하는 문제가 남아 있습니다.

Azure Container Registry의 기능 모음인 **ACR Build**는 네이티브 컨테이너 이미지 빌드 기능을 제공할 뿐만 아니라 Docker 컨테이너에 대한 [OS 및 프레임워크 패치](#automate-os-and-framework-patching)도 자동화합니다.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>ACR Build란?

Azure Container Registry Build는 Azure 네이티브 컨테이너 이미지 빌드 서비스입니다. ACR Build는 주문형 컨테이너 이미지 빌드와 소스 코드 커밋 및 기본 이미지 업데이트에 대한 자동화된 빌드를 사용하여 클라우드에서 내부 루프를 개발할 수 있게 합니다.

코드가 Git 리포지토리에 커밋되거나 컨테이너의 기본 이미지가 업데이트되면 트리거 컨테이너 이미지가 자동으로 빌드됩니다. 기본 이미지 업데이트 트리거를 사용하면 OS 및 응용 프로그램 프레임워크 패치 워크플로를 자동화하여 변경할 수 없는 컨테이너의 보안 주체를 준수하면서 보안 환경을 유지 관리할 수 있습니다.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>빠른 빌드: 클라우드로 확장되는 내부 루프

수명 주기 관리의 시작은 개발자가 첫 번째 코드 줄을 커밋하기 전에 시작됩니다. ACR Build의 [빠른 빌드](container-registry-tutorial-quick-build.md) 기능을 사용하면 통합된 로컬 내부 루프 개발 환경을 통해 빌드를 Azure로 오프로드할 수 있습니다. 빠른 빌드를 사용하면 코드를 커밋하기 전에 자동화된 빌드 정의를 확인할 수 있습니다.

친숙한 `docker build` 형식을 사용하여 Azure CLI의 [az acr build][az-acr-build] 명령에서 로컬 컨텍스트를 사용하고, 이를 ACR Build 서비스로 보내고, 기본적으로 완료되면 빌드된 이미지를 해당 레지스트리에 푸시합니다. ACR Build는 지리적으로 복제된 레지스트리를 따르므로 분산된 개발 팀에서 가장 가까운 복제된 레지스트리를 사용할 수 있습니다. 미리 보기 동안 ACR Build는 미국 동부 및 유럽 서부 지역에서 사용할 수 있습니다.

ACR Build는 기본 컨테이너 수명 주기로 설계되었습니다. 예를 들어 ACR Build를 CI/CD 솔루션에 통합합니다. CI/CD 솔루션은 [서비스 주체][az-login-service-principal]로 [az login][az-login]을 실행한 다음, [az acr build][az-acr-build] 명령으로 이미지 빌드를 시작할 수 있습니다.

첫 번째 ACR Build 자습서인 [Azure Container Registry Build를 사용하여 클라우드에서 컨테이너 이미지 빌드](container-registry-tutorial-quick-build.md)에서 빠른 빌드를 사용하는 방법을 알아보세요.

## <a name="automatic-build-on-source-code-commit"></a>소스 코드 커밋 시 자동 빌드

코드가 Git 리포지토리에 커밋될 때 컨테이너 이미지 빌드를 자동으로 트리거하려면 ACR Build를 사용합니다. [az acr build-task][az-acr-build-task] Azure CLI 명령으로 구성할 수 있는 빌드 작업을 사용하면 Git 리포지토리를 지정하고 필요에 따라 분기와 Dockerfile을 지정할 수 있습니다. 팀에서 리포지토리에 코드를 커밋하면 ACR Build에서 만든 웹후크에서 리포지토리에 정의된 컨테이너 이미지 빌드를 트리거합니다.

두 번째 ACR Build 자습서인 [Azure Container Registry Build를 사용하여 컨테이너 이미지 빌드 자동화](container-registry-tutorial-build-task.md)에서 소스 코드 커밋 시 빌드를 트리거하는 방법을 알아보세요.

## <a name="automate-os-and-framework-patching"></a>OS 및 프레임워크 패치 자동화

컨테이너 빌드 파이프라인을 실제로 향상시키는 ACR Build의 기능은 기본 이미지에 대한 업데이트를 검색하는 기능에서 제공됩니다. 업데이트된 기본 이미지가 레지스트리에 푸시되면 ACR Build에서 이를 기반으로 하여 모든 응용 프로그램 이미지를 자동으로 빌드할 수 있습니다.

컨테이너 이미지는 *기본* 이미지와 *응용 프로그램* 이미지로 크게 분류할 수 있습니다. 기본 이미지에는 일반적으로 응용 프로그램이 빌드되는 운영 체제 및 응용 프로그램 프레임워크가 다른 사용자 지정 항목과 함께 포함됩니다. 이러한 기본 이미지 자체는 일반적으로 [Alpine Linux][base-alpine] 또는 [Node.js][base-node]와 같은 공용 업스트림 이미지를 기반으로 합니다. 일부 응용 프로그램 이미지에서는 일반적인 기본 이미지를 공유할 수 있습니다.

중요한 OS 보안 패치와 같이 업스트림 유지 관리자가 OS 또는 응용 프로그램 프레임워크 이미지를 업데이트하는 경우, 중요한 수정 프로그램이 포함되도록 기본 이미지도 업데이트해야 합니다. 그러면 각 응용 프로그램 이미지도 이제 기본 이미지에 포함된 이러한 업스트림 수정 프로그램을 포함하도록 다시 빌드해야 합니다.

ACR Build는 컨테이너 이미지를 빌드할 때 기본 이미지 종속성을 동적으로 검색하므로 응용 프로그램 이미지의 기본 이미지가 업데이트되는 시기를 검색할 수 있습니다. 미리 구성된 하나의 [빌드 작업](container-registry-tutorial-base-image-update.md#create-build-task)을 사용하면 ACR Build에서 **모든 응용 프로그램 이미지를 자동으로 다시 빌드합니다**. 이 자동 검색 및 다시 빌드를 통해 ACR Build에서 업데이트된 기본 이미지를 참조하는 각 응용 프로그램 이미지를 빠짐없이 수동으로 추적하고 업데이트하는 데 필요한 시간과 노력을 절약할 수 있습니다.

세 번째 ACR Build 자습서인 [Azure Container Registry Build를 사용하여 기본 이미지 업데이트 시 이미지 빌드 자동화](container-registry-tutorial-base-image-update.md)에서 OS 및 프레임워크 패치에 대해 알아보세요.

> [!NOTE]
> 초기 미리 보기의 경우 기본 이미지와 응용 프로그램 이미지가 모두 동일한 Azure 컨테이너 레지스트리에 있는 경우에만 기본 이미지 업데이트에서 빌드를 트리거합니다.

## <a name="next-steps"></a>다음 단계

클라우드에서 컨테이너 이미지를 빌드하여 OS 및 프레임워크 패치를 자동화할 준비가 되면 세 부분으로 구성된 ACR Build 자습서 시리즈를 확인합니다.

> [!div class="nextstepaction"]
> [자습서 - Azure Container Registry Build를 사용하여 클라우드에서 컨테이너 이미지 빌드](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
