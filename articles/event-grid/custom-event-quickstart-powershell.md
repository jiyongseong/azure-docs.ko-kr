---
title: PowerShell과 Azure Event Grid에 대한 사용자 지정 이벤트 | Microsoft Docs
description: Azure Event Grid 및 PowerShell을 사용하여 토픽을 게시하고 해당 이벤트를 구독합니다.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 03/20/2018
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 695aa5c567882ef7742666146877e1fbc660492b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34300954"
---
# <a name="create-and-route-custom-events-with-azure-powershell-and-event-grid"></a>Azure PowerShell 및 Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 Azure PowerShell을 사용하여 사용자 지정 토픽을 만들고 해당 토픽을 구독하며 이벤트를 트리거하여 결과를 확인합니다. 일반적으로 웹후크 또는 Azure Function과 같은 이벤트에 응답하는 끝점으로 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 이벤트를 메시지를 수집하기만 하는 URL로 보냅니다. [Hookbin](https://hookbin.com/)에서 타사 도구를 사용하여 이 URL을 만듭니다.

>[!NOTE]
>처리량이 높은 경우에는 **Hookbin** 사용이 적합하지 않습니다. 이 도구는 순전히 시연을 위해서만 사용됩니다. 한 번에 둘 이상의 이벤트를 푸시하면 도구에서 모든 이벤트가 표시되지 않을 수 있습니다.

작업을 완료하면 이벤트 데이터가 엔드포인트에 보내진 것을 확인할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

이 문서에서는 Azure PowerShell의 최신 버전을 실행해야 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/install-azurerm-ps)을 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 명령으로 리소스 그룹을 만듭니다.

다음 예제에서는 *westus2* 위치에 *gridResourceGroup*이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name gridResourceGroup -Location westus2
```

## <a name="create-a-custom-topic"></a>사용자 지정 토픽 만들기

Event Grid 항목은 이벤트를 게시하는 사용자 정의 엔드포인트를 제공합니다. 다음 예제에서는 리소스 그룹에 사용자 지정 토픽을 만듭니다. `<topic_name>`을 토픽의 고유한 이름으로 바꿉니다. DNS 항목으로 표시되기 때문에 토픽 이름은 고유해야 합니다.

```powershell
New-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Location westus2 -Name <topic_name>
```

## <a name="create-a-message-endpoint"></a>메시지 끝점 만들기

사용자 지정 토픽을 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 이벤트에 응답하는 코드를 작성하지 않고 메시지를 볼 수 있도록 메시지를 수집하는 끝점을 만들어 보겠습니다. Hookbin은 타사 도구로, 이 도구를 통해 엔드포인트를 만들고 이 엔드포인트에 전송된 요청을 볼 수 있습니다. [Hookbin](https://hookbin.com/)으로 이동하여 **새 엔드포인트 만들기**를 클릭합니다.  토픽을 구독할 때 필요하기 때문에 bin URL을 복사합니다.

## <a name="subscribe-to-a-topic"></a>토픽 구독

토픽을 구독하여 Event Grid에 추적하려는 이벤트를 알립니다. 다음 예제에서는 사용자가 만든 사용자 지정 토픽을 구독하고 Hookbin의 URL을 이벤트 알림에 대한 엔드포인트로 전달합니다. `<event_subscription_name>`을 구독의 고유한 이름으로, `<endpoint_URL>`을 이전 섹션의 값으로 바꿉니다. 구독할 때 끝점을 지정하면 Event Grid에서 해당 끝점으로 이벤트 라우팅을 처리합니다. `<topic_name>`에는 앞에서 만든 값을 사용합니다.

```powershell
New-AzureRmEventGridSubscription -EventSubscriptionName <event_subscription_name> -Endpoint <endpoint_URL> -ResourceGroupName gridResourceGroup -TopicName <topic_name>
```

## <a name="send-an-event-to-your-topic"></a>토픽에 이벤트 보내기

이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴보겠습니다. 먼저, 토픽에 대한 URL 및 키를 가져오겠습니다. 다시, `<topic_name>`의 토픽 이름을 사용합니다.

```powershell
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName gridResourceGroup -Name <topic-name>
```

이 문서를 간소화하기 위해 사용자 지정 토픽에 보낼 샘플 이벤트 데이터를 설정해 보겠습니다. 일반적으로 응용 프로그램 또는 Azure 서비스는 이벤트 데이터를 보냅니다. 다음 예제에서는 해시 테이블을 사용하여 이벤트의 `htbody` 데이터를 생성한 다음, 올바른 형식의 `$body` JSON 페이로드 개체로 변환합니다.

```powershell
$eventID = Get-Random 99999

#Date format should be SortableDateTimePattern (ISO 8601)
$eventDate = Get-Date -Format s

#Construct body using Hashtable
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/motorcycles"
    eventTime= $eventDate   
    data= @{
        make="Ducati"
        model="Monster"
    }
    dataVersion="1.0"
}

#Use ConvertTo-Json to convert event body from Hashtable to JSON Object
#Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
$body = "["+(ConvertTo-Json $htbody)+"]"
```

`$body`를 보는 경우 전체 이벤트를 확인할 수 있습니다. JSON의 `data` 요소는 이벤트의 페이로드입니다. 모든 잘 구성된(Well-Formed) JSON은 이 필드에 배치될 수 있습니다. 또한 고급 라우팅 및 필터링을 위해 제목 필드를 사용할 수 있습니다.

이제 항목에 이벤트를 보냅니다.

```powershell
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 이전에 만든 끝점 URL로 이동합니다. 또는 열려 있는 브라우저에서 새로 고침을 클릭합니다. 방금 전송 받은 이벤트가 표시됩니다.

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2018-01-25T15:58:13",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/topics/{topic}"
}]
```

## <a name="clean-up-resources"></a>리소스 정리

이 이벤트로 작업을 계속하려는 경우 이 문서에서 만든 리소스를 정리하지 마세요. 그렇지 않으면 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

```powershell
Remove-AzureRmResourceGroup -Name gridResourceGroup
```

## <a name="next-steps"></a>다음 단계

토픽 및 이벤트 구독을 만드는 방법을 배웠으므로 어떤 Event Grid가 도움이 되는지 자세히 알아보세요.

- [Event Grid 정보](overview.md)
- [Blob Storage 이벤트를 사용자 지정 웹 끝점으로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md)
