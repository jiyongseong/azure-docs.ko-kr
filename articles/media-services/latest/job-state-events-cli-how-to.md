---
title: Azure Media Services 이벤트를 사용자 지정 웹 엔드포인트로 라우팅 | Microsoft Docs
description: Azure Event Grid를 사용하여 Media Services 작업 상태 변경 이벤트를 구독합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 2780438e715b6f6cb04d820c02d09f14e14b480f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="route-azure-media-services-events-to-a-custom-web-endpoint-using-cli"></a>CLI를 사용하여 Azure Media Services 이벤트를 사용자 지정 웹 엔드포인트로 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 Azure CLI를 사용하여 Azure Media Services 작업 상태 변경 이벤트를 구독하고 이벤트를 트리거하여 결과를 확인합니다. 

일반적으로 이벤트에 응답하는 끝점(예: 웹후크 또는 Azure Function)으로 이벤트를 보냅니다. 이 자습서는 웹후크를 만들고 설정하는 방법을 보여줍니다.

이 문서에서 설명하는 단계를 완료하면 이벤트 데이터가 끝점으로 보내졌음을 알 수 있습니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

다음 단계와 같이[Azure Portal](http://portal.azure.com)에 로그인하고 **CloudShell**을 시작하여 CLI명령을 실행합니다.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

Media Services 계정 이름, 저장소 이름 및 리소스 이름에 사용한 값을 기억해야 합니다.

## <a name="enable-event-grid-resource-provider"></a>Event Grid 리소스 공급자를 사용하도록 설정

먼저 필요한 작업은 구독 시 Event Grid 리소스 공급자를 사용하도록 설정하는 것입니다. 

**Azure** Portal에서 다음을 수행합니다.

1. 구독으로 이동합니다.
2. 사용 중인 구독을 선택합니다.
3. 설정에서 리소스 공급자를 선택합니다.
4. "EventGrid"를 검색합니다.
5. Event Grid가 등록되어 있는지 확인합니다. 그렇지 않으면 **등록** 단추를 누릅니다.  

## <a name="create-a-generic-azure-function-webhook"></a>제네릭 Azure Function 웹후크 만들기 

### <a name="create-a-message-endpoint"></a>메시지 끝점 만들기

Event Grid의 문서를 구독하기 전에 메시지를 볼 수 있도록 메시지를 수집하는 엔드포인트를 만듭니다.

[제네릭 웹후크](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function) 문서의 설명에 따라 제네릭 웹후크를 통해 트리거되는 함수를 만듭니다. 이 자습서에는 **C#** 코드가 사용됩니다.

웹후크가 생성되면 **Azure** Portal 창 맨 위에 있는 *함수 URL 가져오기* 링크를 클릭하여 URL을 복사합니다. URL의 마지막 부분(*&clientID=default*)은 필요하지 않습니다.

![웹후크 만들기](./media/job-state-events-cli-how-to/generic_webhook_files.png)

### <a name="validate-the-webhook"></a>웹후크 유효성 검사

자신의 웹후크 엔드포인트를 Event Grid에 등록하면 엔드포인트 소유권을 증명하는 간단한 유효성 검사 코드가 포함된 POST 요청이 전송됩니다. 앱은 유효성 검사 코드를 다시 반환하여 응답해야 합니다. Event Grid는 유효성 검사를 통과하지 못한 웹후크 엔드포인트에 이벤트를 전달하지 않습니다. 자세한 내용은 [Event Grid 보안 및 인증](https://docs.microsoft.com/azure/event-grid/security-authentication)을 참조하세요. 이 섹션에서는 유효성 검사를 통과하기 위해 정의해야 하는 두 부분을 정의합니다.

#### <a name="update-the-source-code"></a>소스 코드 업데이트

웹후크를 만들면 **run.csx** 파일이 브라우저에 나타납니다. 기본 코드를 다음 코드로 바꿉니다. 

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Webhook was triggered!");

    string jsonContent = await req.Content.ReadAsStringAsync();
    string eventGridValidation = 
        req.Headers.FirstOrDefault( x => x.Key == "Aeg-Event-Type" ).Value?.FirstOrDefault();

    dynamic eventData = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"event: {eventData}");

    if (eventGridValidation != String.Empty)
    {
        if (eventData[0].data.validationCode !=String.Empty && eventData[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent")
        {
            return req.CreateResponse(HttpStatusCode.OK, new 
            {
                validationResponse = eventData[0].data.validationCode
            });
        }
    }
    
    log.Info(jsonContent);

    return req.CreateResponse(HttpStatusCode.OK);
}
```

#### <a name="update-test-request-body"></a>테스트 요청 본문 업데이트

**Azure** Portal 창 오른쪽에 **파일 보기**와 **테스트**라는 두 개의 탭이 있습니다. **테스트** 탭을 선택합니다. **요청 본문**에 다음 json을 입력합니다. 그대로 붙여넣을 수 있으며 값을 변경할 필요가 없습니다.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2017-08-06T22:09:30.740323Z"
}
]
```

창의 맨 위에 있는 **저장 및 실행**을 누릅니다.

![요청 본문](./media/job-state-events-cli-how-to/generic_webhook_test.png)

## <a name="register-for-the-event-grid-subscription"></a>Event Grid 구독 등록 

추적하려는 이벤트를 Event Grid에 알리도록 문서를 구독합니다. 다음 예제에서는 사용자가 만든 Media Services 계정을 구독하고 이벤트 알림을 위해 엔드포인트로 만든 Azure Function 웹후크의 URL을 전달합니다. 

`<event_subscription_name>`을 이벤트 구독의 고유 이름으로 바꿉니다. `<resource_group_name>` 및 `<ams_account_name>`에는 앞에서 만든 값을 사용합니다.  `<endpoint_URL>`에 대해 엔드포인트 URL을 붙여넣습니다. URL에서 *&clientID=default*를 제거합니다. 구독할 때 끝점을 지정하면 Event Grid에서 해당 끝점으로 이벤트 라우팅을 처리합니다. 

```cli
amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $amsResourceId \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
```

Media Services 계정 리소스 ID 값은 다음과 유사합니다.

/subscriptions/81212121-2f4f-4b5d-a3dc-ba0015515f7b/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amstestaccount

## <a name="test-the-events"></a>이벤트 테스트

인코딩 작업을 실행합니다. 예를 들어 [비디오 파일 스트리밍](stream-files-dotnet-quickstart.md) 빠른 시작을 참조합니다.

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 끝점으로 메시지를 보냈습니다. 앞에서 작성한 웹후크로 이동합니다. **모니터**와 **새로 고침**을 클릭합니다. 작업의 상태 변경 이벤트는 "큐에 대기됨", "예약됨", "처리 중", "완료됨", "오류", "취소됨", "취소 중"으로 표시됩니다.  자세한 내용은 [Media Services 이벤트 스키마](media-services-event-schemas.md)를 참조하세요.

예: 

```json
[{
  "topic": "/subscriptions/<subscription id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job id>",
  "eventType": "Microsoft.Media.JobStateChange",
  "eventTime": "2018-04-20T21:17:26.2534881",
  "id": "<id>",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

![테스트 이벤트](./media/job-state-events-cli-how-to/test_events.png)

## <a name="clean-up-resources"></a>리소스 정리

이 저장소 계정 및 이벤트 구독을 계속 사용하려면 이 문서에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

`<resource_group_name>`을 위에서 만든 리소스 그룹으로 바꿉니다.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>다음 단계

[이벤트에 대응](reacting-to-media-services-events.md)
