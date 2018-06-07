---
title: 하이브리드 연결로 Azure Event Grid에 대한 사용자 지정 이벤트 보내기 | Microsoft Docs
description: Azure Event Grid 및 Azure CLI를 사용하여 토픽을 게시하고 해당 이벤트를 구독합니다. 하이브리드 연결은 엔드포인트에 사용됩니다.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 05/04/2018
ms.topic: tutorial
ms.service: event-grid
ms.openlocfilehash: 31c8dd520079046808b32dad0d338415bed71c58
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302980"
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Azure CLI 및 Event Grid를 사용하여 Azure Relay 하이브리드 연결로 사용자 지정 이벤트 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. Azure Relay 하이브리드 연결은 지원되는 이벤트 처리기 중 하나입니다. 공용 엔드포인트가 없는 응용 프로그램에서 이벤트를 처리해야 할 때 하이브리드 연결을 이벤트 처리기로 사용합니다. 이러한 응용 프로그램은 회사 엔터프라이즈 네트워크 내에서 작성될 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 사용자 지정 토픽을 만들고 해당 토픽을 구독하며 이벤트를 트리거하여 결과를 확인합니다. 이벤트를 하이브리드 연결로 보냅니다.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 이미 하이브리드 연결 및 수신기 응용 프로그램이 있는 것으로 가정합니다. 하이브리드 연결을 시작하려면 [Relay 하이브리드 연결 시작 - .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) 또는 [Relay 하이브리드 연결 시작 - 노드](../service-bus-relay/relay-hybrid-connections-node-get-started.md)를 참조하세요.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. 

다음 예제에서는 *westus2* 위치에 *gridResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

Event Grid 항목은 이벤트를 게시하는 사용자 정의 엔드포인트를 제공합니다. 다음 예제에서는 리소스 그룹에 사용자 지정 토픽을 만듭니다. `<topic_name>`을 토픽의 고유한 이름으로 바꿉니다. DNS 항목으로 표시되기 때문에 토픽 이름은 고유해야 합니다.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>토픽 구독

토픽을 구독하여 Event Grid에 추적하려는 이벤트를 알립니다. 다음 예제에서는 앞에서 만든 토픽을 구독하고, 엔드포인트에 대한 하이브리드 연결의 리소스 ID를 전달합니다. 하이브리드 연결 ID는 다음과 같은 형식입니다.

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

다음 스크립트는 릴레이 네임스페이스의 리소스 ID를 가져옵니다. 하이브리드 연결의 ID를 생성하고 Event Grid 토픽을 구독합니다. 엔드포인트 유형을 `hybridconnection`으로 설정하고 엔드포인트에 대한 하이브리드 연결 ID를 사용합니다.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="send-an-event-to-your-topic"></a>토픽에 이벤트 보내기

이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴보겠습니다. 먼저, 사용자 지정 토픽에 대한 URL 및 키를 가져오겠습니다. 다시, `<topic_name>`의 토픽 이름을 사용합니다.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

이 문서를 간소화하기 위해 토픽에 보낼 샘플 이벤트 데이터를 사용합니다. 일반적으로 응용 프로그램 또는 Azure 서비스는 이벤트 데이터를 보냅니다. CURL은 HTTP 요청을 보내는 유틸리티입니다. 이 문서에서는 토픽에 이벤트를 보내는 데 CURL을 사용합니다.  다음 예제는 세 개의 이벤트를 Event Grid 토픽으로 보냅니다.

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

수신기 응용 프로그램이 이벤트 메시지를 수신해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리
이 이벤트로 작업을 계속하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 그렇지 않으면 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>다음 단계

토픽 및 이벤트 구독을 만드는 방법을 배웠으므로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

- [Event Grid 정보](overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 끝점으로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)
