---
title: "Azure Functions에서 지원되는 언어"
description: "어떤 언어가 지원되는 언어(GA)이고 어떤 언어가 실험적 또는 미리 보기인지 알아봅니다."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/07/2017
ms.author: tdykstra
ms.openlocfilehash: 5786a206b258cfe7c48f52ead9b5a4cceb64cd5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="supported-languages-in-azure-functions"></a>Azure Functions에서 지원되는 언어

이 문서에서는 Azure Functions에서 사용할 수 있는 언어에 대해 제공되는 지원 수준을 설명합니다.

## <a name="levels-of-support"></a>지원 수준

세 가지 지원 수준이 있습니다.

* **일반 공급(GA)** - 완전하게 지원되며 프로덕션용으로 승인되었습니다.
* **미리 보기** - 아직 지원되지 않지만 향후 GA 상태가 될 예정입니다.
* **실험적** - 지원되지 않으며 향후 중단될 수 있습니다. 최종 미리 보기 또는 GA 상태가 보장되지 않습니다.

## <a name="languages-in-runtime-1x-and-2x"></a>런타임 1.x와 2.x의 언어

[두 가지 버전의 Azure Functions 런타임](functions-versions.md)을 사용할 수 있습니다. 1.x 런타임은 GA입니다. 프로덕션 응용 프로그램에 대해 승인된 유일한 런타임입니다. 2.x 런타임은 현재 미리 보기 상태이므로 지원되는 언어도 미리 보기 상태입니다. 다음 표는 각 런타임 버전에서 지원되는 언어를 나타냅니다.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>실험적 언어

1.x에서 실험적 언어는 제대로 확장되지 않고 모든 바인딩을 지원하지도 않습니다. 예를 들어 Python은 Functions 런타임이 각 함수 호출을 통해 *python.exe*를 실행하기 때문에 느립니다. 또한 Python이 HTTP 바인딩을 지원하지만 요청 개체를 액세스할 수는 없습니다.

PowerShell에 대한 실험적 지원은 Function 앱이 실행되는 VM에 설치된 항목이므로 버전 4.0으로 제한됩니다. PowerShell 스크립트를 실행하려면 [Azure Automation](https://azure.microsoft.com/services/automation/)을 고려합니다.

2.x 런타임은 실험적 언어를 지원하지 않습니다. 2.x는 잘 확장되고 고급 트리거를 지원할 때만 언어에 대한 지원이 추가됩니다.

1.x에서만 사용할 수 있는 언어 중 하나를 사용하려면 1.x 런타임을 유지합니다. 하지만 공식적으로 지원되지 않으므로 실험적 언어를 사용하지는 않도록 해야 합니다. [GitHub 문제를 생성](https://github.com/Azure/azure-webjobs-sdk-script/issues)하여 도움을 요청할 수 있지만 실험적 언어 관련 문제에 대한 지원 사례를 열 수는 없습니다. 

### <a name="language-extensibility"></a>언어 확장성

2.x 런타임은 [언어 확장성](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility)을 제공하도록 설계되었습니다. 이 확장성 모델을 기반으로 사용되는 첫 번째 언어 중에는 2.x에서 미리 보기인 Java가 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Functions에서 GA 또는 미리 보기 언어 중 하나를 사용하는 방법에 대해 알아보려면 다음 리소스를 참조하세요.

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)