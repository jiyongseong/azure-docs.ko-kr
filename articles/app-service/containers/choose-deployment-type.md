---
title: Linux 배포의 Azure App Service - 사용자 지정 이미지, 다중 컨테이너 또는 기본 제공 플랫폼 이미지 중에서 선택  | Microsoft Docs
description: 사용자 지정 Docker 컨테이너 배포, 다중 컨테이너 및 Linux의 App Service를 위한 기본 제공 응용 프로그램 프레임워크 중 하나를 결정하는 방법
keywords: azure app service, 웹앱, linux, oss
services: app-service
documentationCenter: ''
authors: msangapu
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.openlocfilehash: 012f78fc07f237e8ed532246c81a3c86bb6ab4ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>사용자 지정 이미지, 다중 컨테이너 또는 기본 제공 플랫폼 이미지

[Linux의 App Service](app-service-linux-intro.md)는 웹에 게시된 응용 프로그램을 가져오는 3가지 서로 다른 경로를 제공합니다.

- **사용자 지정 이미지 배포**: 즉시 실행 가능한 패키지에 모든 파일 및 종속성을 포함하는 Docker 이미지로 앱을 “Docker화”합니다.
- **다중 컨테이너 배포**: Docker Compose 또는 Kubernetes 구성 파일을 사용하여 앱을 여러 컨테이너 간에 "Docker화"합니다. 자세한 내용은 [다중 컨테이너 앱](#multi-container-apps-supportability)을 참조하세요.
- **기본 제공 플랫폼 이미지를 사용한 앱 배포**: 기본 제공 플랫폼 이미지는 노드 및 PHP와 같은 일반적인 웹앱 런타임 및 종속성을 포함합니다. [Azure App Service 배포 방법](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 중 하나를 사용하여 앱을 웹앱의 저장소에 배포한 후 기본 제공 플랫폼 이미지를 사용하여 실행합니다.

##<a name="which-method-is-right-for-your-app"></a>앱에 적합한 방법은 무엇인가요? 

고려해야 할 주요 요소는 다음과 같습니다.

- **개발 워크플로에서 Docker의 가용성**: 사용자 지정 이미지 개발을 위해서는 Docker 개발 워크플로에 대한 기본적인 지식이 필요합니다. 사용자 지정 이미지를 웹앱에 배포하려면 사용자 지정 이미지를 Docker 허브와 같은 리포지토리 호스트에 게시해야 합니다. Docker에 대해 잘 알고 있으며 빌드 워크플로에 Docker 작업을 추가할 수 있는 경우, 앱을 Docker 이미지로 이미 게시하는 경우 대부분 사용자 지정 이미지가 최선의 선택입니다.
- **다중 계층 아키텍처**: 다중 컨테이너를 통해 웹 응용 프로그램 계층 및 API 계층과 같은 다중 컨테이너를 배포하여 기능을 분리합니다. 
- **응용 프로그램 성능**: Redis와 같은 캐시 계층을 사용하여 다중 컨테이너 앱의 성능을 향상시킵니다. 이를 위해 다중 컨테이너를 선택합니다.
- **고유한 런타임 요구 사항**: 기본 제공 플랫폼 이미지는 대부분의 웹앱 요구를 충족하도록 설계되었지만 사용자 지정 가능성은 제한됩니다. 앱에는 기본 제공 이미지로는 불가능한 고유한 종속성 또는 기타 런타임 요구 사항이 포함될 수 있습니다.
- **빌드 요구 사항**: [지속적인 배포](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)를 통해 앱을 가져오고 소스 코드로부터 구동되어 Azure에 직접 실행되는 앱을 가져올 수 있습니다. 외부 빌드 또는 게시 프로세스가 필요하지 않습니다. 그러나 [Kudu](https://github.com/projectkudu/kudu/wiki) 배포 엔진 내에서 빌드 도구의 사용자 지정 가능성과 가용성에는 제한이 있습니다. 사용자 정의 빌드 논리에 대한 종속성이나 요구 사항이 증가함에 따라 Kudu의 기능으로는 앱을 감당하지 못할 수 있습니다.
- **디스크 읽기/쓰기 요구 사항**: 모든 웹앱에는 웹 콘텐츠에 대한 저장소 볼륨이 할당됩니다. Azure Storage에서 지원하는 이 볼륨은 앱의 파일 시스템에서 `/home`에 탑재됩니다. 컨테이너 파일 시스템의 파일과 달리, 콘텐츠 볼륨의 파일은 앱의 모든 스케일 인스턴스 간에 액세스할 수 있으며 수정 내용은 앱이 다시 시작되어도 지속됩니다. 그러나 콘텐츠 볼륨의 디스크 대기 시간은 로컬 컨테이너 파일 시스템의 대기 시간보다 높고 가변적이며, 플랫폼 업그레이드, 계획되지 않은 중단 시간 및 네트워크 연결 문제로 인해 액세스에 영향이 있을 수 있습니다. 콘텐츠 파일에 대한 많은 읽기 전용 액세스를 필요로 하는 앱은 사용자 지정 이미지 배포를 활용할 수 있으며 콘텐츠 볼륨 대신 이미지 파일 시스템에 파일을 배치합니다.
- **빌드 리소스 사용량**: 소스에서 앱을 배포하는 경우 Kudu가 실행하는 배포 스크립트는 실행 중인 앱과 동일한 App Service 계획 계산 및 저장소 리소스를 사용합니다. 대규모 앱 배포에서는 예상보다 더 많은 리소스 또는 시간을 사용할 수 있습니다. 특히 배포 워크플로가 많은 경우 이러한 작업에 최적화되어 있지 않은 앱 콘텐츠 볼륨에는 상당한 디스크 작업이 발생합니다. 사용자 지정 이미지는 추가 파일 전송 또는 배포 작업을 수행하지 않고도 Azure에 대한 모든 앱 파일 및 종속성을 단일 패키지로 제공합니다.
- **신속한 반복 필요**: 앱을 Docker화하려면 추가 빌드 단계가 필요합니다. 변경 내용을 적용하려면 각 업데이트를 통해 새 이미지를 리포지토리에 밀어넣어야 합니다. 그러면 이러한 업데이트가 Azure 환경에 끌어오기됩니다. 기본 제공 컨테이너 중 하나가 앱의 요구를 충족하는 경우 소스에서 배포를 통해 개발 워크플로가 더 빨라질 수 있습니다.

## <a name="multi-container-apps-supportability"></a>다중 컨테이너 앱 지원 가능성

### <a name="supported-docker-compose-configuration-options"></a>지원되는 Docker Compose 구성 옵션
- command
- entrypoint
- 환경
- 이미지
- ports
- restart
- services
- volumes

### <a name="unsupported-docker-compose-configuration-options"></a>지원되지 않는 Docker Compose 구성 옵션
- build(허용 안 함)
- depends_on(무시됨)
- networks(무시됨)
- secrets(무시됨)
- 포트 80 및 8080 이외의 포트(무시됨)

> [!NOTE]
> 명시적으로 호출되지 않는 다른 모든 옵션은 공개 미리 보기에서도 무시됩니다.

### <a name="supported-kubernetes-configuration-options"></a>지원되는 Kubernetes 구성 옵션
- args
- command
- 컨테이너
- 이미지
- 이름
- ports
- spec

> [!NOTE]
>명시적으로 호출되지 않는 다른 모든 Kubernetes 옵션은 공개 미리 보기에서 지원되지 않습니다.
>