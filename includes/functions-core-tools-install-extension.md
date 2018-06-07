---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4c9b579534d9a7f2c55e9c589b1738fe060b1cf2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
함수를 로컬로 개발하는 경우, 터미널 또는 명령 프롬프트에서 Azure Functions 핵심 도구를 사용하여 필요한 확장을 설치할 수 있습니다. 다음 `func extensions install` 명령은 Azure Cosmos DB 바인딩 확장을 설치합니다.

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

`<taget_version>`을 패키지의 특정 버전으로 바꿉니다. 유효한 버전은 [NuGet.org](https://nuget.org)의 개별 패키지 페이지에 나열되어 있습니다.
