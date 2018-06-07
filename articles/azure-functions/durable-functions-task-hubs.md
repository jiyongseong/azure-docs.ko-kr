---
title: 지속성 함수의 작업 허브 - Azure
description: Azure Functions의 지속성 함수 확장에 있는 작업 허브 및 작업 허브 구성 방법을 알아봅니다.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 563667684accf8b434052cd412bf6e93c77ea63a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>지속성 함수의 작업 허브(Azure Functions)

[지속형 함수](durable-functions-overview.md)의 *작업 허브*는 오케스트레이션에 사용되는 Azure Storage 리소스에 대한 논리적 컨테이너입니다. 오케스트레이터 및 작업 함수는 동일한 작업 허브에 속할 때만 상호 작용할 수 있습니다.

각 함수 앱에는 별도의 작업 허브가 있습니다. 여러 함수 앱이 저장소 계정을 공유하는 경우 저장소 계정에 여러 작업 허브가 포함됩니다. 다음 다이어그램에서는 공유 및 전용 저장소 계정의 함수 앱당 단일 작업 허브를 보여 줍니다.

![공유 및 전용 저장소 계정을 표시하는 다이어그램](media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage 리소스

작업 허브는 다음과 같은 저장소 리소스로 구성됩니다. 

* 하나 이상의 제어 큐
* 하나의 작업 항목 큐
* 하나의 기록 테이블
* 하나의 인스턴스 테이블
* 하나 이상의 저장소 Blob을 포함하는 하나의 저장소 컨테이너

오케스트레이터 또는 작업 함수가 실행되거나 실행되도록 예약된 경우 이러한 모든 리소스는 기본 Azure Storage 계정에 자동으로 만들어집니다. [성능 및 크기 조정](durable-functions-perf-and-scale.md) 문서에서는 이러한 리소스를 사용하는 방법을 설명합니다.

## <a name="task-hub-names"></a>작업 허브 이름

작업 허브는 다음 예제와 같이 *host.json*에 선언된 이름으로 식별됩니다.

```json
{
  "durableTask": {
    "HubName": "MyTaskHub"
  }
}
```

작업 허브 이름은 문자로 시작하고 문자와 숫자로만 구성되어야 합니다. 지정하지 않으면 기본 이름은 **DurableFunctionsHub**입니다.

> [!NOTE]
> 이 이름은 공유 저장소 계정에 여러 작업 허브가 있을 때 작업 허브를 다른 작업 허브에서 구분해줍니다. 공유 저장소 계정을 공유하는 함수 앱이 여러 개 있는 경우 *host.json* 파일에서 각 작업 허브에 대해 다른 이름을 구성해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [버전 관리 처리](durable-functions-versioning.md)
