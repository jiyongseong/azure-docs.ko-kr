---
title: 지속성 함수 확장 및 샘플 설치 - Azure
description: Azure Functions, 포털 개발 또는 Visual Studio 개발을 위한 지속성 함수 확장을 설치하는 방법에 대해 알아봅니다.
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
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 4dd4bbb9c382b772f8f60b259844e7e471ec73e3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>지속성 함수 확장 및 샘플 설치(Azure Functions)

Azure Functions에 대한 [지속성 함수](durable-functions-overview.md) 확장이 NuGet 패키지 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)에서 제공됩니다. 이 문서에서는 다음 개발 환경을 위한 패키지 및 샘플 집합을 설치하는 방법을 보여 줍니다.

* Visual Studio 2017(C#에 권장) 
* Visual Studio Code(JavaScript에 권장)
* Azure portal

## <a name="visual-studio-2017"></a>Visual Studio 2017

현재 Visual Studio는 지속성 함수를 사용하는 앱을 개발하기 위한 최적의 환경을 제공합니다.  사용자 함수를 로컬로 실행할 수 있으며 Azure에도 게시할 수도 있습니다. 빈 프로젝트 또는 샘플 함수 집합으로 시작할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

* [최신 버전의 Visual Studio](https://www.visualstudio.com/downloads/)(버전 15.3 이상)를 설치합니다. 설정 옵션에 **Azure 배포** 워크로드를 포함합니다.

### <a name="start-with-sample-functions"></a>샘플 함수로 시작 

1. [Visual Studio용 샘플 앱 .zip 파일](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip)을 다운로드합니다. 샘플 프로젝트에 이미 있으므로 NuGet 참조를 추가할 필요는 없습니다.
2. [Azure Storage 에뮬레이터](https://docs.microsoft.com/azure/storage/storage-use-emulator) 버전 5.2 이상을 설치 및 실행합니다. 또는 실제 Azure Storage 연결 문자열로 *local.appsettings.json* 파일을 업데이트할 수 있습니다.
3. Visual Studio 2017에서 프로젝트를 엽니다. 
4. 샘플을 실행하는 방법에 대한 지침은 [함수 체이닝 - Hello 시퀀스 샘플](durable-functions-sequence.md)로 시작하세요. 샘플을 로컬로 실행하거나 Azure에 게시할 수 있습니다.

### <a name="start-with-an-empty-project"></a>빈 프로젝트로 시작
 
샘플을 시작할 때와 같은 지침을 따르지만 *.zip* 파일을 다운로드하는 대신 다음 단계를 수행합니다.

1. 함수 앱 프로젝트를 만듭니다.
2. *패키지 관리*를 사용하는 다음 NuGet 패키지 참조를 검색하고 Microsoft.Azure.WebJobs.Extensions.DurableTask v1.4.0 프로젝트에 추가합니다(이 패키지를 검색하려면 *시험판 포함* 선택).
   
## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code는 주요 플랫폼인 Windows, macOS 및 Linux를 포함하는 로컬 개발 환경을 제공합니다.  사용자 함수를 로컬로 실행할 수 있으며 Azure에도 게시할 수도 있습니다. 빈 프로젝트 또는 샘플 함수 집합으로 시작할 수 있습니다.

### <a name="prerequisites"></a>필수 조건

* 최신 버전의 [Visual Studio Code](https://code.visualstudio.com/Download)를 설치합니다. 

* [Azure Functions를 로컬로 코딩 및 테스트](https://docs.microsoft.com/azure/azure-functions/functions-run-local)의 "Azure Functions Core Tools 설치"에 제공되는 지침을 따릅니다.

    >[!IMPORTANT]
    > Azure Functions Cross Platform Tools가 이미 있는 경우 사용 가능한 최신 버전으로 업데이트합니다.

    >[!IMPORTANT]
    >JavaScript의 지속형 함수에는 2.x 버전의 Azure Functions Core Tools가 필요합니다.

*  Windows 컴퓨터에서 작업하는 경우 [Azure Storage 에뮬레이터](https://docs.microsoft.com/azure/storage/storage-use-emulator) 버전 5.2 이상을 설치 및 실행합니다. 또는 실제 Azure Storage 연결로 *local.appsettings.json* 파일을 업데이트할 수 있습니다. 


### <a name="start-with-sample-functions"></a>샘플 함수로 시작

#### <a name="c"></a>C#

1. [지속성 함수 리포지토리](https://github.com/Azure/azure-functions-durable-extension.git)를 복제합니다.
2. 컴퓨터에서 [C# 스크립트 샘플 폴더](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx)로 이동합니다. 
3. 명령 프롬프트/터미널 창에서 다음을 실행하여 Azure Functions Durable Extension을 설치합니다.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. 명령 프롬프트/터미널 창에서 다음을 실행하여 Azure Functions Twilio Extension을 설치합니다.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.Twilio -v 3.0.0-beta5
    ```
5. Azure Storage 에뮬레이터를 실행하거나 *local.appsettings.json* 파일을 실제 Azure Storage 연결 문자열로 업데이트합니다.
6. Visual Studio Code에서 프로젝트를 엽니다. 
7. 샘플을 실행하는 방법에 대한 지침은 [함수 체이닝 - Hello 시퀀스 샘플](durable-functions-sequence.md)로 시작하세요. 샘플을 로컬로 실행하거나 Azure에 게시할 수 있습니다.
8. 명령 프롬프트/터미널에서 다음 명령을 실행하여 프로젝트를 시작합니다.
    ```bash
    func host start
    ```

#### <a name="javascript-functions-v2-only"></a>JavaScript(Functions v2만 해당)

1. [지속성 함수 리포지토리](https://github.com/Azure/azure-functions-durable-extension.git)를 복제합니다.
2. 컴퓨터에서 [JavaScript 샘플 폴더](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/javascript)로 이동합니다. 
3. 명령 프롬프트/터미널 창에서 다음을 실행하여 Azure Functions Durable Extension을 설치합니다.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
4. 명령 프롬프트/터미널 창에서 다음을 실행하여 npm 패키지를 복원합니다.
    
    ```bash
    npm install
    ``` 
5. 실제 Azure Storage 연결 문자열로 *local.appsettings.json* 파일을 업데이트합니다.
6. Visual Studio Code에서 프로젝트를 엽니다. 
7. 샘플을 실행하는 방법에 대한 지침은 [함수 체이닝 - Hello 시퀀스 샘플](durable-functions-sequence.md)로 시작하세요. 샘플을 로컬로 실행하거나 Azure에 게시할 수 있습니다.
8. 명령 프롬프트/터미널에서 다음 명령을 실행하여 프로젝트를 시작합니다.
    ```bash
    func host start
    ```

### <a name="start-with-an-empty-project"></a>빈 프로젝트로 시작
 
1. 명령 프롬프트/터미널에서 함수 앱을 호스트할 폴더로 이동합니다.
2. 명령 프롬프트/터미널 창에서 다음을 실행하여 Azure Functions Durable Extension을 설치합니다.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.4.0
    ```
3. 다음 명령을 실행하여 함수 앱 프로젝트를 만듭니다.

    ```bash
    func init
    ``` 
4. Azure Storage 에뮬레이터를 실행하거나 *local.appsettings.json* 파일을 실제 Azure Storage 연결 문자열로 업데이트합니다.
5. 다음으로 다음 명령을 실행하여 새 함수를 만든 후 마법사 단계를 따릅니다.

    ```bash
    func new
    ```
    >[!IMPORTANT]
    > 현재 지속성 함수 템플릿을 사용할 수 없지만 지원되는 옵션 중 하나로 시작한 후 코드를 수정할 수 있습니다. [오케스트레이션 클라이언트](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/HttpStart), [오케스트레이션 트리거](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence) 및 [작업 트리거](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/csx/E1_HelloSequence)에 대한 샘플을 참조하세요.

6. Visual Studio Code에서 프로젝트 폴더를 열고 템플릿 코드를 수정하여 계속 진행합니다. 
7. 명령 프롬프트/터미널에서 다음 명령을 실행하여 프로젝트를 시작합니다.
    ```bash
    func host start
    ```

## <a name="azure-portal"></a>Azure portal

원하는 경우 지속성 함수 개발을 위해 Azure Portal을 사용할 수 있습니다.

   > [!NOTE]
   > JavaScript의 지속형 함수는 아직 포털에서 사용할 수 없습니다.

### <a name="create-an-orchestrator-function"></a>오케스트레이터 함수 만들기

1. [functions.azure.com](https://functions.azure.com/signin)에서 새 함수 앱을 만듭니다.

2. [2.0 런타임 버전을 사용](set-runtime-version.md)하도록 함수 앱을 구성합니다.

   지속성 함수 확장은 1.X 런타임 및 2.0 런타임 모두에서 작동하지만 Azure Portal 템플릿은 2.0 런타임을 대상으로 하는 경우에만 사용할 수 있습니다.

3. **"사용자 고유의 사용자 지정 함수 만들기"** 를 선택하여 새 함수를 만듭니다.

4. **언어**를 **C#** 으로, **시나리오**를 **지속성 함수**로 변경한 후 **Durable Functions Http Starter - C#** 템플릿을 선택합니다.

5. **Extensions not installed(설치되지 않은 확장)** 아래에서 **설치**를 클릭하여 NuGet.org에서 확장을 다운로드합니다. 

6. 설치가 완료된 후 **Durable Functions Http Starter - C#** 템플릿으 선택하여 오케스트레이션 클라이언트 함수 – **"HttpStart"** 를 만듭니다.

7. 이제는 **Durable Functions Orchestrator - C#** 템플릿에서 오케스트레이션 함수 **"HelloSequence"** 를 만듭니다.

8. 마지막 함수는 **Durable Functions Activity - C#** 템플릿에서 **"Hello"** 로 지칭됩니다.

9. **"HttpStart"** 함수로 이동한 후 해당 URL을 복사합니다.

10. Postman 또는 cURL을 사용하여 지속성 함수를 호출합니다. 테스트하기 전에 URL에서 **{functionName}** 을 오케스트레이터 함수 이름 **HelloSequence**로 바꿉니다.  데이터는 필요하지 않으며 POST 동사만 사용합니다. 

    ```bash
    curl -X POST https://{your function app name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

11. 그런 다음 **"statusQueryGetUri"** 끝점을 호출합니다. 그러면 지속성 함수의 현재 상태가 표시됩니다.

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

12. 상태가 **"Completed"** 가 될 때까지 **"statusQueryGetUri"** 끝점을 계속 호출합니다. 

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

축하합니다! 첫 번째 지속성 함수가 Azure Portal에서 작동되고 실행됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [함수 체이닝 샘플 실행](durable-functions-sequence.md)
