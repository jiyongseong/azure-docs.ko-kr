---
title: 문제 해결 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34199299"
---
# <a name="troubleshooting-guide"></a>문제 해결 가이드

이 가이드는 Azure Dev Spaces를 사용할 때 발생할 수 있는 일반적인 문제에 대한 정보를 포함합니다.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>오류 '업스트림 연결 오류 또는 헤더 전에 연결 끊김/다시 설정'
서비스에 액세스하려고 할 때 이 오류가 표시될 수 있습니다. 예를 들어 브라우저에서 서비스의 URL로 이동하는 경우입니다. 

### <a name="reason"></a>이유 
컨테이너 포트를 사용할 수 없습니다. 다음 이유 때문일 수 있습니다. 
* 컨테이너가 아직 빌드 및 배포되는 중입니다. `azds up`을 시작하거나 디버거를 시작한 다음, 성공적으로 배포하기 전에 컨테이너에 액세스하려고 하는 경우일 수 있습니다.
* 포트 구성이 Dockerfile, Helm 차트 및 포트를 여는 모든 서버 코드에서 일치하지 않습니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 컨테이너가 빌드/배포되는 중인 경우 2-3초 기다렸다가 서비스에 액세스를 다시 시도할 수 있습니다. 
1. 포트 구성을 확인합니다. 지정된 포트 번호는 아래 모든 자산에서 **동일**해야 합니다.
    * **Dockerfile:** `EXPOSE` 명령으로 지정됩니다.
    * **[Helm 차트](https://docs.helm.sh):** 서비스에 대해 `externalPort` 및 `internalPort` 값으로 지정됩니다(종종 `values.yml` 파일에 위치함).
    * 응용 프로그램 코드에서 열리는 모든 포트(예: Node.js에서 `var server = app.listen(80, function () {...}`)


## <a name="config-file-not-found"></a>구성 파일을 찾을 수 없음
`azds up`을 실행하고 다음 오류가 표시됩니다. `Config file not found: .../azds.yaml`

### <a name="reason"></a>이유
실행하려는 코드의 루트 디렉터리에서 `azds up`을 실행하고, Azure Dev Spaces로 실행하도록 코드 폴더를 초기화해야 합니다.

### <a name="try"></a>다음을 시도해 보세요.
1. 현재 디렉터리를 서비스 코드를 포함하는 루트 폴더로 변경합니다. 
1. 코드 폴더에 azds.yaml 파일이 없는 경우 `azds prep`를 실행하여 Docker, Kubernetes 및 Azure Dev Spaces 자산을 생성합니다.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>오류: '파이프 프로그램 'azds'가 코드 126으로 예기치 않게 종료되었습니다.'
VS Code 디버거를 시작하면 때때로 이 오류가 발생할 수 있습니다. 이는 알려진 문제입니다.

### <a name="try"></a>다음을 시도해 보세요.
1. VS Code를 닫았다가 다시 엽니다.
2. F5 키를 다시 누릅니다.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>디버깅 오류 '구성된 디버그 형식 'coreclr'은 지원되지 않습니다.'
VS Code 디버거를 실행하면 오류를 보고합니다. `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>이유
개발 컴퓨터에 설치된 Azure Dev Spaces용 VS Code 확장이 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
[Azure Dev Spaces용 VS Code 확장](get-started-netcore.md)을 설치합니다.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>형식 또는 네임스페이스 이름 'MyLibrary'를 찾을 수 없음

### <a name="reason"></a>이유 
빌드 컨텍스트는 기본적으로 프로젝트/서비스 수준에 있으므로 사용하는 라이브러리 프로젝트를 찾을 수 없습니다.

### <a name="try"></a>다음을 시도해 보세요.
수행해야 하는 작업:
1. 빌드 컨텍스트를 솔루션 수준으로 설정하도록 azds.yaml 파일을 수정합니다.
2. 새 빌드 컨텍스트를 기준으로 올바르게 csproj 파일을 참조하도록 Dockerfile 및 Dockerfile.develop 파일을 수정합니다.
3. .sln 파일 옆에 .dockerignore 파일을 배치하고 필요에 따라 수정합니다.

https://github.com/sgreenmsft/buildcontextsample에서 예제를 찾을 수 있습니다.

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>'Microsoft.ConnectedEnvironment/register/action' 권한 부여 오류
Azure Dev Space를 관리하고 소유자 또는 기여자 액세스 권한이 없는 Azure 구독에서 작업하는 경우 다음과 같은 오류가 표시될 수 있습니다.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>이유
선택한 Azure 구독에서 Microsoft.ConnectedEnvironment 네임스페이스를 등록하지 않았습니다.

### <a name="try"></a>다음을 시도해 보세요.
Azure 구독에 대한 소유자 또는 기여자 액세스 권한이 있는 사용자는 다음 Azure CLI 명령을 실행하여 Microsoft.ConnectedEnvironment 네임스페이스를 수동으로 등록할 수 있습니다.

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev Spaces에서 컨테이너를 빌드하는 데 내 기존 Dockerfile을 사용하지 않는 것처럼 보임 

### <a name="reason"></a>이유
프로젝트에서 특정 Dockerfile을 가리키도록 Azure Dev Spaces를 구성할 수 있습니다. Azure Dev Spaces에서 컨테이너를 빌드하는 데 예상하는 Dockerfile를 사용하지 않는 경우 Azure Dev Spaces에 명시적으로 위치를 알려야 할 수 있습니다. 

### <a name="try"></a>다음을 시도해 보세요.
프로젝트에서 Azure Dev Spaces가 생성한 `azds.yaml` 파일을 엽니다. `configurations->develop->build->dockerfile` 지시문을 사용하여 사용하려는 Dockerfile을 가리킵니다.

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```