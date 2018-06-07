---
title: "Azure Functions 개발 지침 | Microsoft Docs"
description: "프로그래밍 언어 및 바인딩에 관계 없이 Azure에서 함수를 개발하는 데 필요한 Azure Functions 개념 및 기술에 대해 알아봅니다."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "개발자 가이드, Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: 461557b415ec816860acb5308e7aeba34468f4ae
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-functions-developers-guide"></a>Azure Functions 개발자 가이드
Azure Functions에서 특정 함수는 사용하는 언어나 바인딩에 관계없이 몇 가지 핵심적 기술 개념과 구성 요소를 공유합니다. 특정 언어나 바인딩에 해당하는 세부 정보를 학습하기 전에, 모든 항목에 해당하는 이 개요를 꼼꼼히 읽어 보시기 바랍니다.

이 문서에서는 [Azure Functions 개요](functions-overview.md)를 이미 읽었고 [트리거, 바인딩, JobHost 런타임 같은 WebJobs SDK 개념](https://github.com/Azure/azure-webjobs-sdk/wiki)에 익숙하다고 가정합니다. Azure Functions는 WebJobs SDK를 기반으로 합니다. 

## <a name="function-code"></a>함수 코드
*함수* 는 Azure Functions의 기본 개념입니다. 원하는 언어로 함수 코드를 작성하고 코드와 구성 파일을 같은 폴더에 저장합니다. 구성의 이름은 `function.json`이며 JSON 구성 데이터가 들어 있습니다. 다양한 언어가 지원되며, 각각의 언어에는 해당 언어에 맞춰 가장 잘 작동하도록 최적화된 약간 다른 환경이 갖춰져 있습니다. 

function.json 파일은 함수 바인딩 및 기타 구성 설정을 정의합니다. 런타임은 이 파일을 사용하여 모니터링할 이벤트와 함수 실행에서 데이터를 전달하고 반환하는 방법을 결정합니다. 다음은 function.json 파일의 예제입니다.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

함수가 실행되지 않도록 `disabled` 속성을 `true`로 설정합니다.

`bindings` 속성은 트리거와 바인딩을 모두 구성하는 곳에 위치합니다. 각 바인딩은 몇 가지 공통적인 설정과 특정 바인딩 형식에 해당하는 일부 설정을 공유합니다. 모든 바인딩에는 다음 설정이 필요합니다.

| 자산 | 값/형식 | 설명 |
| --- | --- | --- |
| `type` |string |바인딩 형식 예: `queueTrigger` |
| `direction` |'in', 'out' |함수 안으로 데이터를 수신할 바인딩인지 또는 함수의 데이터를 전송할 바인딩인지를 나타냅니다. |
| `name` |string |함수에서 바인딩 데이터에 사용되는 이름입니다. C#의 경우 인수 이름이며, JavaScript의 경우 키/값 목록의 키입니다. |

## <a name="function-app"></a>함수 앱
함수 앱은 Azure App Service에서 함께 관리되는 하나 이상의 개별 함수 함수로 구성됩니다. 함수 앱의 모든 함수는 동일한 가격 책정 계획, 연속 배포 및 런타임 버전을 공유합니다. 여러 언어로 작성된 함수는 동일한 함수 앱을 공유할 수 있습니다. 함수 앱을 함수를 구성하고 전체적으로 관리하는 방법으로 생각합니다. 

## <a name="runtime-script-host-and-web-host"></a>런타임(스크립트 호스트 및 웹 호스트)
런타임 또는 스크립트 호스트는 이벤트를 수신하고, 데이터를 수집하여 보내고, 최종적으로 코드를 실행하는 기본 WebJobs SDK 호스트입니다. 

HTTP 트리거를 용이하게 하기 위해 프로덕션 시나리오에서 스크립트 호스트 앞에 위치하도록 설계된 웹 호스트도 있습니다. 두 개의 호스트가 있으면 웹 호스트에서 관리하는 프런트 엔드 트래픽에서 스크립트 호스트를 격리하는 데 도움이 됩니다.

## <a name="folder-structure"></a>폴더 구조
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Azure App Service에서 함수를 함수 앱에 배포하기 위한 프로젝트를 설정하는 경우에는, 이 폴더 구조를 사용자의 사이트 코드로 처리할 수 있습니다. 배포 시 패키지 설치 또는 코드 transpilation 수행을 위하여 지속적인 통합 및 배포 같은 기존 툴 또는 사용자 지정 배포 스크립트를 사용할 수 있습니다.

> [!NOTE]
> `host.json` 파일과 함수 폴더를 `wwwroot` 폴더에 직접 배포해야 합니다. 배포에 `wwwroot` 폴더를 포함하지 마세요. 그렇지 않으면 `wwwroot\wwwroot` 폴더가 만들어집니다. 
> 
> 

## <a id="fileupdate"></a> 함수 앱 파일을 업데이트하는 방법
Azure 포털에 기본 제공되는 함수 편집기를 사용하면 함수에 대한 코드 파일 및 *function.json* 파일을 업데이트할 수 있습니다. *package.json* 또는 *project.json* 또는 종속성 등의 다른 파일을 업로드하거나 업데이트하려면 다른 배포 방법을 사용해야 합니다.

함수 앱은 App Service를 기반으로 하므로 [표준 웹앱에 사용할 수 있는 배포 옵션](../app-service/app-service-deploy-local-git.md)을 함수 앱에 모두 사용할 수도 있습니다. 함수 앱 파일을 업로드하거나 업데이트하는 데 사용할 수 있는 방법이 몇 가지 입니다. 

#### <a name="to-use-app-service-editor"></a>App Service 편집기 사용하기
1. Azure Functions 포털에서 **플랫폼 기능**을 클릭합니다.
2. **개발 도구** 섹션에서 **App Service 편집기**를 클릭합니다.   
   App Service 편집기가 로드된 후에 *host.json* 파일과 *wwwroot* 하위의 함수 폴더를 볼 수 있습니다. 
5. 파일을 열어서 편집하거나, 배포 컴퓨터에서 끌어서 놓기로 파일을 업로드합니다.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>함수 앱의 SCM(Kudu) 끝점을 사용하려면
1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.
2. **디버그 콘솔 > CMD**를 클릭합니다.
3. `D:\home\site\wwwroot\`으로 이동하여 *host.json*을 업데이트하거나 `D:\home\site\wwwroot\<function_name>`로 이동하여 함수 파일을 업데이트합니다.
4. 파일 그리드에서 적절한 폴더로 업로드할 파일을 끌어서 놓습니다. 파일을 삭제할 수 있는 파일 표에는 두 가지 영역이 있습니다. *.zip* 파일의 경우 "여기에 끌어 와서 업로드하고 압축을 풉니다" 레이블이 있는 상자가 나타납니다. 다른 파일 형식의 경우 "압축" 상자 외부에 파일 표를 살펴봅니다.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>연속 배포를 사용하려면
[Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)항목의 지침을 따릅니다.

## <a name="parallel-execution"></a>병렬 실행
복수의 트리거 이벤트가 단일 스레드 함수 런타임이 해당 이벤트를 처리할 수 있는 속도보다 빨리 발생하면 런타임은 병렬 모드로 함수를 여러 번 호출할 수 있습니다.  함수 앱이 [소비 호스팅 계획](functions-scale.md#how-the-consumption-plan-works)을 사용하는 경우 함수 앱은 자동으로 확장할 수 있습니다.  앱이 소비 호스팅 계획 또는 일반 [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서 실행되는지 여부에 관계없이 함수 앱의 각 인스턴스는 여러 스레드를 사용하여 동시 함수 호출을 병렬로 처리할 수 있습니다.  각 함수 앱 인스턴스의 최대 동시 함수 호출 수는 함수 앱 내의 다른 함수에서 사용하는 리소스뿐만 아니라 사용 중인 트리거 유형에 따라 달라집니다.

## <a name="functions-runtime-versioning"></a>Functions 런타임 버전 관리

`FUNCTIONS_EXTENSION_VERSION` 앱 설정을 사용하여 Functions 런타임의 버전을 구성할 수 있습니다. 예를 들어 “”~1은 함수 앱이 주요 버전으로 1을 사용한다는 것을 나타냅니다. 함수 앱은 부 버전이 새로 릴리스될 때마다 업그레이드됩니다. 정확한 함수 앱 버전을 확인하는 방법을 비롯한 자세한 내용을 보려면 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

## <a name="repositories"></a>리포지토리
Azure Functions에 대한 코드는 공개 소스이며 GitHub 리포지토리에 저장됩니다.

* [Azure Functions 런타임](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure Functions 포털](https://github.com/projectkudu/AzureFunctionsPortal)
* [Azure Functions 템플릿](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>바인딩
지원되는 모든 바인딩을 포함하는 테이블입니다.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

바인딩에서 들어오는 오류와 함께 문제가 있습니까? [Azure Functions 바인딩 오류 코드](functions-bindings-error-pages.md) 설명서를 참조하세요.

## <a name="reporting-issues"></a>문제 보고
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions F# 개발자 참조](functions-reference-fsharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Azure Functions: Azure App Service](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) 팀 블로그 과정. Azure Functions 개발에 대한 기록

