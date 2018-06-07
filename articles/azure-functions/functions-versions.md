---
title: "Azure Functions 런타임 버전 개요"
description: "Azure Functions는 여러 버전의 런타임을 지원합니다. 버전 간 차이점과 적합한 버전을 선택하는 방법을 알아봅니다."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 9f916aaa8032ff519709d73a1c1f51195f811686
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions 런타임 버전 개요

 Azure Functions 런타임 주 버전은 1.x 및 2.x입니다. 이 문서에서는 주 버전을 선택하는 방법을 설명합니다.

> [!IMPORTANT] 
> 런타임 1.x는 프로덕션용으로 승인된 유일한 버전입니다.

| 런타임 | 상태 |
|---------|---------|
|1.x|일반 공급(GA)|
|2.x|미리 보기|

특정 버전에 맞게 함수 앱 또는 개발 환경을 구성하는 방법은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md) 및 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md)를 참조하세요.

## <a name="cross-platform-development"></a>교차 플랫폼 개발

1.x 런타임은 포털 또는 Windows에서만 개발 및 호스팅을 지원합니다. 2.x 런타임은 .NET Core에서 실행되는데, 이는 macOS 및 Linux를 포함하여 .NET Core에서 지원하는 모든 플랫폼에서 실행된다는 의미입니다. 따라서 1.x에서는 불가능한 교차 플랫폼 개발 및 호스팅 시나리오를 구현할 수 있습니다.

## <a name="languages"></a>언어

2.x 런타임은 새 언어 확장성 모델을 사용합니다. 처음에는 JavaScript 및 Java가 이 새 모델을 활용합니다. Azure Functions 1.x 실험 언어는 새 모델을 사용하도록 업데이트되지 않았기 때문에 2.x에서 지원되지 않습니다. 다음 표는 각 런타임 버전에서 지원되는 프로그래밍 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="bindings"></a>바인딩 

2.x 런타임은 다음과 같은 장점이 있는 새 [바인딩 확장성 모델](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)을 사용합니다.

* 타사 바인딩 확장 지원.
* 런타임과 바인딩을 분리. 이렇게 하면 바인딩 확장의 버전을 지정하고 독립적으로 릴리스할 수 있습니다. 예를 들어 기본 SDK의 최신 버전을 사용하는 확장 버전으로 업그레이드하도록 선택할 수 있습니다.
* 사용 중인 바인딩만 런타임에 알려지고 로드되는 가벼운 실행 환경.

모든 기본 제공 Azure Functions 바인딩은 이 모델을 채택했으며 타이머 트리거 및 HTTP 트리거를 제외하고 더 이상 포함되지 않습니다. 이러한 확장은 Visual Studio 같은 도구를 통해 또는 포털을 통해 함수를 만들 때 자동으로 설치됩니다.

다음 표에는 각 런타임 버전에서 지원되는 바인딩이 정리되어 있습니다.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>2.x의 알려진 문제

바인딩 지원 및 2.x의 다른 기능 차이에 대한 자세한 내용은 [런타임 2.0의 알려진 문제점](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 개발 환경에서 2.0 런타임을 대상으로 지정](functions-run-local.md)

> [!div class="nextstepaction"]
> [런타임 버전에 대한 릴리스 정보 참조](https://github.com/Azure/azure-webjobs-sdk-script/releases)