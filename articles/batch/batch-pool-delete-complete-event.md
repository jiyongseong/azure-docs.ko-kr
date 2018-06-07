---
title: Azure Batch 풀 삭제 완료 이벤트 | Microsoft Docs
description: Batch 풀 삭제 완료 이벤트에 대한 참조입니다.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: bfcbcf40efc64ab1c79ee1a86e02502c68ad6d47
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-complete-event"></a>풀 삭제 완료 이벤트

 이 이벤트는 풀 삭제 작업이 완료되면 내보내집니다.

 다음 예에서는 풀 삭제 완료 이벤트의 본문을 보여 줍니다.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|요소|형식|메모|
|-------------|----------|-----------|
|id|문자열|풀의 ID입니다.|
|startTime|Datetime|풀 삭제가 시작된 시간입니다.|
|endTime|DateTime|풀 삭제가 완료된 시간입니다.|

## <a name="remarks"></a>설명
풀 크기 조정 작업의 상태 및 오류 코드에 대한 자세한 내용은 [계정에서 풀 삭제](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)를 참조하세요.