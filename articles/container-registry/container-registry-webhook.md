---
title: Azure Container Registry 웹후크
description: 레지스트리 리포지토리 중 하나에서 특정 작업이 수행되는 경우 웹후크를 사용하여 이벤트를 트리거하는 방법을 알아봅니다.
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: cf71deda9ec9053a91596c17e18fc797995754e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry 웹후크 사용

Azure Container Registry는 Docker 허브에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 개인 Docker 컨테이너 이미지를 저장하고 관리합니다. 레지스트리 리포지토리 중 하나에서 특정 작업이 수행되는 경우 웹후크를 사용하여 이벤트를 트리거할 수 있습니다. 웹후크는 레지스트리 수준에서 이벤트에 응답하거나 특정 리포지토리 태그로 범위를 줄일 수 있습니다.

Webhook 요청에 대한 세부 정보는 [Azure Container Registry 웹후크 스키마 참조](container-registry-webhook-reference.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure Container Registry - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
* Docker CLI - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 [Docker 엔진](https://docs.docker.com/engine/installation/)을 설치합니다.

## <a name="create-webhook-azure-portal"></a>웹후크 Azure Portal 만들기

1. [Azure 포털](https://portal.azure.com)
1. 웹후크를 만들려는 컨테이너 레지스트리로 이동합니다.
1. **서비스** 아래에서 **Webhook**를 선택합니다.
1. 웹후크 도구 모음에서 **추가**를 선택합니다.
1. 다음 정보로 *웹후크 만들기* 양식을 완성합니다.

| 값 | 설명 |
|---|---|
| Name | 웹후크에 지정하려는 이름입니다. 소문자와 숫자만 포함할 수 있으며 길이는 5-50자여야 합니다. |
| 서비스 URI | 웹후크가 POST 알림을 보내야 하는 URI입니다. |
| 사용자 지정 헤더 | POST 요청과 함께 전달하려는 헤더입니다. "키: 값" 형식이어야 합니다. |
| 트리거 동작 | 웹후크를 트리거하는 동작입니다. 현재 웹후크는 이미지 밀어넣기 및/또는 삭제 동작에 의해 트리거될 수 있습니다. |
| 상태 | 웹후크가 만들어진 후의 상태입니다. 기본적으로 사용하도록 설정되어 있습니다. |
| 범위 | 웹후크가 작동하는 범위입니다. 기본적으로 이 범위는 레지스트리의 모든 이벤트입니다. "리포지토리:태그" 형식을 사용하여 리포지토리 또는 태그에 대해 지정할 수 있습니다. |

웹후크 양식 예제:

![Azure Portal의 ACR 웹후크 만들기 UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>웹후크 Azure CLI 만들기

Azure CLI를 사용하여 웹후크를 만들려면 [az acr webhook create](/cli/azure/acr/webhook#az_acr_webhook_create) 명령을 사용합니다.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>웹후크 테스트

### <a name="azure-portal"></a>Azure portal

컨테이너 이미지 밀어넣기 및 삭제 작업에서 웹후크를 사용하기 전에 **Ping** 단추를 사용하여 테스트할 수 있습니다. Ping은 지정된 끝점에 일반 POST 요청을 보내고 응답을 기록합니다. ping 기능을 사용하면 웹후크를 올바르게 구성했는지 확인하는 데 도움이 될 수 있습니다.

1. 테스트하려는 웹후크를 선택합니다.
2. 맨 위의 도구 모음에서 **Ping**을 선택합니다.
3. **HTTP 상태** 열에서 끝점의 응답을 확인합니다.

![Azure Portal의 ACR 웹후크 만들기 UI](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 ACR 웹후크를 테스트하려면 [az acr webhook ping](/cli/azure/acr/webhook#az_acr_webhook_ping) 명령을 사용합니다.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

결과를 보려면 [az acr webhook list-events](/cli/azure/acr/webhook#list-events) 명령을 사용합니다.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>웹후크 삭제

### <a name="azure-portal"></a>Azure portal

각 웹후크는 Azure Portal에서 웹후크를 선택하고 **삭제** 단추를 선택하여 삭제할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>다음 단계

[Azure Container Registry 웹후크 스키마 참조](container-registry-webhook-reference.md)