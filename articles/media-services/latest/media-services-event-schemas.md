---
title: Media Services 이벤트에 대한 Azure Event Grid 스키마
description: Azure Event Grid에서 Media Services 이벤트에 대해 제공되는 속성을 설명합니다.
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
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Media Services 이벤트에 대한 Azure Event Grid 스키마

이 문서에서는 Media Services 이벤트에 대한 스키마와 속성을 제공합니다.

## <a name="media-services-job-state-change-event"></a>Media Services 작업 상태 변경 이벤트

이 섹션에서는 Media Services 작업 상태 변경 이벤트에 대한 속성과 스키마를 제공합니다.  

### <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Media Services **Job**은 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| 작업의 상태가 변경되면 발생합니다. |

### <a name="example-event"></a>예제 이벤트

다음 예제는 완료된 작업 상태 이벤트의 스키마를 보여줍니다. 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | string | Event Grid는 이 값을 제공합니다. |
| subject | string | Media Services 계정 리소스의 하위 리소스 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. 예: "Microsoft.Media.JobStateChange". |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Media Services 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | type | 설명 |
| -------- | ---- | ----------- |
| previousState | string | 이벤트 전의 작업 상태입니다. |
| state | string | 이 이벤트에서 알리는 직업의 새로운 상태입니다. 예: "큐에 대기됨: 작업이 리소스를 기다리고 있습니다." 또는 “예약됨: 작업을 시작할 준비가 되었습니다."|

작업 상태는 다음 중 하나일 수 있습니다. *큐에 대기됨*, *예약됨*, *처리 중*, *완료됨*, *오류*, *취소됨*, *취소 중*

## <a name="next-steps"></a>다음 단계

[작업 상태 변경 이벤트 등록](job-state-events-cli-how-to.md)
