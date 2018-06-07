---
title: 로컬로 Azure Functions 개발 및 실행 | Microsoft Docs
description: Azure Functions에서 실행하기 전에 로컬 컴퓨터에서 Azure Functions를 코딩 및 테스트하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 523ef25fe0d3227d526acbdee2c7cf2660fc4f25
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions를 로컬에서 코딩 및 테스트

[Azure Portal]에 Azure Functions 개발 및 테스트를 위한 도구가 완벽히 제공되지만 많은 개발자는 지역 개발 환경을 선호합니다. Azure Functions를 사용하면 원하는 코드 편집기와 로컬 개발 도구를 쉽게 사용하여 로컬 컴퓨터에서 함수를 개발하고 테스트 할 수 있습니다. 함수로 Azure에서 이벤트를 트리거할 수 있으며 로컬 컴퓨터에서 C# 및 JavaScript 함수를 디버깅할 수 있습니다. 

Visual Studio C# 개발자인 경우 Azure Functions은 [Visual Studio 2017과도 통합](functions-develop-vs.md)됩니다.

>[!IMPORTANT]  
> 로컬 개발과 포털 개발을 동일한 함수 앱에 혼합하지 않도록 합니다. 로컬 프로젝트에서 함수를 만들고 게시할 경우 포털에서 프로젝트 코드를 유지하거나 수정하려고 하지 않아야 합니다.

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions 핵심 도구 설치

[Azure Functions 핵심 도구]는 로컬 개발 컴퓨터에서 실행할 수 있는 Azure Functions 런타임의 로컬 버전입니다. 에뮬레이터 또는 시뮬레이터가 아닙니다. Azure에서 Functions를 작동하는 것과 동일한 런타임입니다. Azure Functions 핵심 도구에는 두 가지 버전이 있습니다.

+ [버전 1.x](#v1): 런타임 버전 1.x를 지원합니다. 이 버전은 Windows 컴퓨터에서만 지원되며 [npm 패키지](https://docs.npmjs.com/getting-started/what-is-npm)에서 설치됩니다.
+ [버전 2.x](#v2): 런타임 버전 2.x를 지원합니다. 이 버전은 [Windows](#windows-npm), [macOS](#brew) 및 [Linux](#linux)를 지원합니다. 설치에 플랫폼별 패키지 관리자 또는 npm을 사용합니다. 

### <a name="v1"></a>버전 1.x

원래 버전의 도구는 Functions 1.x 런타임을 사용합니다. 이 버전은 .NET Framework(4.7.1)를 사용하며 Windows 컴퓨터에서만 지원됩니다. 버전 1.x 도구를 설치하려면 먼저 npm이 포함된 [NodeJS를 설치](https://docs.npmjs.com/getting-started/installing-node)해야 합니다.

다음 명령을 사용하여 버전 1.x 도구를 설치합니다.

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>버전 2.x

>[!NOTE]
> Azure Functions 런타임 2.0은 미리 보기 상태이며, 현재 Azure Functions의 일부 기능은 지원되지 않습니다. 자세한 내용은 [Azure Functions 버전](functions-versions.md)을 참조하세요. 

버전 2.x 도구는 .NET Core를 기반으로 하는 Azure Functions 런타임 2.x를 사용합니다. 이 버전은 [Windows](#windows-npm), [macOS](#brew) 및 [Linux](#linux)를 포함하여 .NET Core 2.x에서 지원하는 모든 플랫폼에서 지원됩니다.

#### <a name="windows-npm"></a>Windows

다음 단계에서는 npm을 사용하여 Windows에 핵심 도구를 설치합니다. [Chocolatey](https://chocolatey.org/)를 사용할 수도 있습니다. 자세한 내용은 [핵심 도구 추가 정보](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)를 참조하세요.

1. [Windows용 .NET Core 2.0](https://www.microsoft.com/net/download/windows)을 설치합니다.

2. [Node.js](npm 포함)를 설치합니다. 버전 2.x 도구의 경우 Node.js 8.5 이상 버전만 지원됩니다.

3. 다음과 같이 핵심 도구 패키지를 설치합니다.

  ```bash
  npm install -g azure-functions-core-tools@core
  ```

#### <a name="brew"></a>Homebrew가 있는 MacOS

다음 단계에서는 Homebrew를 사용하여 macOS에 핵심 도구를 설치합니다.

1. [macOS용 .NET Core 2.0](https://www.microsoft.com/net/download/macos)을 설치합니다.

1. 아직 설치되지 않은 경우 [Homebrew](https://brew.sh/)를 설치합니다.

2. 다음과 같이 핵심 도구 패키지를 설치합니다.

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a>APT가 있는 Linux(Debian/Ubuntu)

다음 단계에서는 [APT](https://wiki.debian.org/Apt)를 사용하여 Ubuntu/Debian Linux 배포판에 핵심 도구를 설치합니다. 다른 Linux 배포판의 경우 [핵심 도구 추가 정보](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux)를 참조하세요.

1. [Linux용 .NET Core 2.0](https://www.microsoft.com/net/download/linux)을 설치합니다.

1. Microsoft 제품 키를 신뢰할 수 있는 키로 등록합니다.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
  ```

2.  패키지 피드를 설정하고, 다음 명령의 `<version>`을 다음 표의 해당 버전 이름으로 바꿉니다.

  ```bash
  sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-<version>-prod <version> main" > /etc/apt/sources.list.d/dotnetdev.list'
  sudo apt-get update
  ```

  | Linux 배포 | `<version>` |
  | --------------- | ----------- |
  | Ubuntu 17.10    | `artful`    |
  | Ubuntu 17.04    | `zesty`     |
  | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

3. 다음과 같이 핵심 도구 패키지를 설치합니다.

  ```bash
  sudo apt-get install azure-functions-core-tools
  ```

## <a name="run-azure-functions-core-tools"></a>Azure Functions 핵심 도구 실행
 
Azure Functions 핵심 도구는 다음 명령 별칭을 추가합니다.
* **func**
* **azfun**
* **azurefunctions**

이러한 별칭은 예제에서 `func`가 표시된 곳에 사용할 수 있습니다.

```
func init MyFunctionProj
```

## <a name="create-a-local-functions-project"></a>로컬 Functions 프로젝트 만들기

로컬로 실행하는 경우 Functions 프로젝트는 [host.json](functions-host-json.md) 및 [local.settings.json](#local-settings-file) 파일이 있는 디렉터리입니다. 이 디렉터리는 Azure의 함수 앱에 해당합니다. Azure Functions 폴더 구조에 대한 자세한 내용은 [Azure Functions 개발자 가이드](functions-reference.md#folder-structure)를 참조하세요.

터미널 창이나 명령 프롬프트에서 다음 명령을 실행하여 프로젝트 및 로컬 Git 리포지토리를 만듭니다.

```
func init MyFunctionProj
```

출력은 다음 예제와 유사합니다.

```
Writing .gitignore
Writing host.json
Writing local.settings.json
Created launch.json
Initialized empty Git repository in D:/Code/Playground/MyFunctionProj/.git/
```

로컬 Git 리포지토리 없이 프로젝트를 만들려면 `--no-source-control [-n]` 옵션을 사용합니다.

## <a name="register-extensions"></a>확장 등록

Azure Functions 런타임의 2.x 버전에서는 함수 앱에서 사용하는 [바인딩 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/README.md)을 명시적으로 등록해야 합니다. 

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md#register-binding-extensions)을 참조하세요.

## <a name="local-settings-file"></a>로컬 설정 파일

local.settings.json 파일은 앱 설정, 연결 문자열 및 Azure Functions 핵심 도구에 대한 설정을 저장합니다. 구조는 다음과 같습니다.

```json
{
  "IsEncrypted": false,   
  "Values": {
    "AzureWebJobsStorage": "<connection string>", 
    "AzureWebJobsDashboard": "<connection string>" 
  },
  "Host": {
    "LocalHttpPort": 7071, 
    "CORS": "*" 
  },
  "ConnectionStrings": {
    "SQLConnectionString": "Value"
  }
}
```
| 설정      | 설명                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | **true**로 설정하면 모든 값은 로컬 컴퓨터 키를 사용하여 암호화됩니다. `func settings` 명령과 함께 사용됩니다. 기본값은 **false**입니다. |
| **값** | 로컬에서 실행될 때 사용되는 응용 프로그램 설정의 컬렉션입니다. **AzureWebJobsStorage** 및 **AzureWebJobsDashboard**는 예입니다. 전체 목록은 [app settings reference](functions-app-settings.md)를 참조하세요. 많은 트리거와 바인딩에는 Blob 저장소 트리거에 대한 **Connection**과 같은 앱 설정을 참조하는 속성이 있습니다. 이러한 속성의 경우 **Values** 배열에 정의된 응용 프로그램 설정이 필요합니다. 또한 백분율 기호로 값을 래핑하여(예: `%AppSettingName%`) 앱 설정 이름으로 설정한 모든 바인딩 속성에도 적용됩니다. |
| **호스트** | 이 섹션의 설정은 로컬에서 실행할 때 Functions 호스트 프로세스를 사용자 지정합니다. | 
| **LocalHttpPort** | 로컬 Functions 호스트(`func host start` 및 `func run`)를 실행할 때 사용되는 기본 포트를 설정합니다. `--port` 명령줄 옵션이 이 값보다 우선합니다. |
| **CORS** | [CORS(원본 간 리소스 공유)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)에 허용된 원본을 정의합니다. 원본은 공백 없이 쉼표로 구분된 목록으로 제공됩니다. 와일드카드 값(\*)이 지원되므로 모든 원본에서 요청할 수 있습니다. |
| **ConnectionStrings** | 함수에 대한 데이터베이스 연결 문자열을 포함합니다. 이 개체의 연결 문자열은 공급자 유형이 **System.Data.SqlClient**인 환경에 추가됩니다.  | 

이러한 설정은 코드에서 환경 변수로 읽을 수도 있습니다. 자세한 내용은 다음 언어별 참조 항목의 Environment 변수 섹션을 참조하세요.

+ [미리 컴파일된 C#](functions-dotnet-class-library.md#environment-variables)
+ [C# 스크립트(.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

local.settings.json 파일의 설정은 로컬에서 실행할 때 Functions 도구에서만 사용됩니다. 기본적으로 이러한 설정은 프로젝트가 Azure에 게시될 때 자동으로 마이그레이션되지 않습니다. [게시할 때](#publish) `--publish-local-settings` 스위치를 사용하여 이러한 설정이 Azure의 함수 앱에 추가되었는지 확인합니다.

**AzureWebJobsStorage**에 유효한 저장소 연결 문자열이 설정되어 있지 않으면 다음 오류 메시지가 표시됩니다.  

>local.settings.json에 AzureWebJobsStorage 값이 없습니다. 이 값은 HTTP 이외의 모든 트리거에 필요합니다. 'func azure functionapp fetch-app-settings <functionAppName>'를 실행하거나 local.settings.json에서 연결 문자열을 지정할 수 있습니다. 
  
[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

### <a name="configure-app-settings"></a>앱 설정 구성

연결 문자열 값을 설정하려면 다음 옵션 중 하나를 수행합니다.
* [Azure Storage 탐색기](http://storageexplorer.com/)에서 연결 문자열을 입력합니다.
* 다음 중 하나의 명령을 사용합니다.

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```
    두 명령을 사용하려면 모두 Azure에 먼저 로그인해야 합니다.

<a name="create-func"></a>
## <a name="create-a-function"></a>함수 만들기

함수를 만들려면 다음 명령을 실행합니다.

```
func new
``` 
`func new`는 다음 선택적 인수를 지원합니다.

| 인수     | 설명                            |
| ------------ | -------------------------------------- |
| **`--language -l`** | C#, F# 또는 JavaScript와 같은 템플릿 프로그래밍 언어 |
| **`--template -t`** | 템플릿 이름 |
| **`--name -n`** | 함수 이름 |

예를 들어 JavaScript HTTP 트리거를 만들려면 다음을 실행합니다.

```
func new --language JavaScript --template "Http Trigger" --name MyHttpTrigger
```

큐 트리거 함수를 만들려면 다음을 실행합니다.

```
func new --language JavaScript --template "Queue Trigger" --name QueueTriggerJS
```
<a name="start"></a>
## <a name="run-functions-locally"></a>로컬로 함수 실행

Functions 프로젝트를 실행하려면 Functions 호스트를 실행합니다. 이 호스트는 프로젝트의 모든 함수에 대한 트리거를 활성화합니다.

```
func host start
```

`func host start`는 다음 옵션을 지원합니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | 수신 대기할 로컬 포트입니다. 기본값: 7071 |
| **`--debug <type>`** | 옵션은 `VSCode` 및 `VS`입니다. |
| **`--cors`** | CORS 원본의 공백 없이 쉼표로 구분된 목록입니다. |
| **`--nodeDebugPort -n`** | 사용할 노드 디버거의 포트입니다. 기본값: launch.json 값 또는 5858 |
| **`--debugLevel -d`** | 콘솔 추적 수준입니다(해제, 자세한 정보 표시, 정보, 경고 또는 오류). 기본값: 정보|
| **`--timeout -t`** | Functions 호스트를 시작할 제한 시간(초)입니다. 기본값: 20초|
| **`--useHttps`** | http://localhost:{port}가 아닌 https://localhost:{port}에 바인딩합니다. 기본적으로 이 옵션은 사용자 컴퓨터에 신뢰할 수 있는 인증서를 만듭니다.|
| **`--pause-on-error`** | 프로세스를 종료하기 전에 추가 입력에 대해 일시 중지합니다. IDE(통합 개발 환경)에서 Azure Functions 핵심 도구를 시작할 때 유용합니다.|

Functions 호스트가 시작되면 HTTP 트리거 함수의 URL이 출력됩니다.

```
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="debug-in-vs-code-or-visual-studio"></a>VS 코드 또는 Visual Studio에서 디버그

디버거를 연결하려면 `--debug` 인수를 전달합니다. JavaScript 함수를 디버그하려면 Visual Studio Code를 사용합니다. C# 함수의 경우 Visual Studio를 사용합니다.

C# 함수를 디버그하려면 `--debug vs`를 사용합니다. [Azure Functions Visual Studio 2017 도구](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/)를 사용할 수도 있습니다. 

호스트를 시작하고 JavaScript 디버깅을 설정하려면 다음을 실행합니다.

```
func host start --debug vscode
```

> [!IMPORTANT]
> 디버깅에서는 Node.js 8.x만 지원됩니다. Node.js 9.x는 지원되지 않습니다. 

그런 다음 Visual Studio Code의 **디버그** 뷰에서 **Azure Functions에 연결**을 선택합니다. 중단점을 연결하고, 변수를 검사하고, 코드를 단계별로 실행할 수 있습니다.

![Visual Studio Code를 사용한 JavaScript 디버깅](./media/functions-run-local/vscode-javascript-debugging.png)

### <a name="passing-test-data-to-a-function"></a>테스트 데이터를 함수에 전달

로컬로 함수를 테스트하려면 [Functions 호스트를 시작](#start)하고 HTTP 요청을 사용하여 로컬 서버에서 끝점을 호출합니다. 호출하는 끝점은 함수의 형식에 따라 달라집니다. 

>[!NOTE]  
> 이 항목의 예제에서는 cURL 도구를 사용하여 터미널 또는 명령 프롬프트의 HTTP 요청을 보냅니다. 로컬 서버에 HTTP 요청을 보내도록 선택한 도구를 사용할 수 있습니다. cURL 도구는 Linux 기반 시스템에서 기본적으로 사용할 수 있습니다. Windows에서는 먼저 [cURL 도구](https://curl.haxx.se/)를 다운로드한 후 설치해야 합니다.

함수를 테스트하는 방법에 대한 일반적인 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요.

#### <a name="http-and-webhook-triggered-functions"></a>HTTP 및 웹후크 트리거된 함수

다음 끝점을 호출하여 HTTP 및 웹후크 트리거된 함수를 로컬로 실행합니다.

    http://localhost:{port}/api/{function_name}

Functions 호스트가 수신 대기 중인 동일한 서버 이름 및 포트를 사용하도록 합니다. Function 호스트를 시작할 때 생성된 출력에서 이 항목을 확인합니다. 트리거에서 지원하는 HTTP 메서드를 사용하여 이 URL을 호출할 수 있습니다. 

다음 cURL 명령은 _이름_ 매개 변수를 쿼리 문자열에 전달한 GET 요청에서 `MyHttpTrigger` 빠른 시작 함수를 트리거합니다. 

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```
다음 예제는 요청 본문에서 _이름_을 전달하는 POST 요청에서 호출되는 동일한 함수입니다.

```
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

쿼리 문자열에서 데이터를 전달하는 브라우저에서 GET 요청을 수행할 수 있습니다. 다른 모든 HTTP 메서드에서 cURL, Fiddler, Postman 또는 비슷한 HTTP 테스트 도구를 사용해야 합니다.  

#### <a name="non-http-triggered-functions"></a>HTTP가 아닌 트리거된 함수
HTTP 트리거와 웹후크가 아닌 다른 모든 종류의 함수에서 관리 끝점을 호출하여 로컬로 함수를 테스트할 수 있습니다. 로컬 서버에서 HTTP POST 요청으로 이 끝점을 호출하면 함수를 트리거합니다. 필요에 따라 POST 요청의 본문에서 실행에 테스트 데이터를 전달할 수 있습니다. 이 기능은 Azure Portal에서 **테스트** 탭과 비슷합니다.  

다음 관리자 끝점을 호출하여 HTTP가 아닌 함수를 트리거합니다.

    http://localhost:{port}/admin/functions/{function_name}

함수의 관리자 끝점에 테스트 데이터를 전달하려면 POST 요청 메시지의 본문에서 데이터를 제공해야 합니다. 메시지 본문에는 다음 JSON 형식이 필요합니다.

```JSON
{
    "input": "<trigger_input>"
}
```` 
`<trigger_input>` 값에는 함수에 필요한 형식의 데이터가 포함됩니다. 다음 cURL 예제는 `QueueTriggerJS` 함수에 대한 POST 요청입니다. 이 경우에 입력은 큐에 위치해야 하는 메시지에 해당하는 문자열입니다.      

```
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>1.x 버전에서 `func run` 명령 사용

>[!IMPORTANT]  
> `func run` 명령은 도구의 2.x 버전에서 지원되지 않습니다. 자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md) 항목을 참조하세요.

`func run <FunctionName>`을 사용하여 함수를 직접 호출하고 함수에 대한 입력 데이터를 제공할 수도 있습니다. 이 명령은 Azure Portal에서 **테스트** 탭을 사용하여 함수를 실행하는 것과 비슷합니다. 

`func run`은 다음 옵션을 지원합니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | 인라인 콘텐츠입니다. |
| **`--debug -d`** | 함수를 실행하기 전에 호스트 프로세스에 디버거를 연결합니다.|
| **`--timeout -t`** | 로컬 Functions 호스트가 준비될 때까지의 대기 시간(초)입니다.|
| **`--file -f`** | 콘텐츠로 사용할 파일 이름입니다.|
| **`--no-interactive`** | 입력에 대한 메시지를 표시하지 않습니다. 자동화 시나리오에 유용합니다.|

예를 들어 HTTP 트리거 함수를 호출하고 콘텐츠 본문을 전달하려면 다음 명령을 실행합니다.

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

### <a name="viewing-log-files-locally"></a>로컬로 로그 파일 보기

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Azure에 게시

Azure의 함수 앱에 Functions 프로젝트를 게시하려면 `publish` 명령을 사용합니다.

```
func azure functionapp publish <FunctionAppName>
```

다음 옵션을 사용할 수 있습니다.

| 옵션     | 설명                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  local.settings.json의 설정을 Azure에 게시하고, 설정이 이미 있는 경우 덮어쓸지 묻습니다.|
| **`--overwrite-settings -y`** | `-i`와 함께 사용해야 합니다. Azure의 AppSettings을 로컬 값으로 덮어씁니다(서로 다른 경우). 기본값은 프롬프트입니다.|

이 명령은 Azure에서 기존 함수 앱에 게시합니다. `<FunctionAppName>`이 구독에 없으면 오류가 발생합니다. Azure CLI를 사용하여 명령 프롬프트 또는 터미널 창에서 함수 앱을 만드는 방법을 알아보려면 [서버를 사용하지 않고 실행하기 위한 함수 앱 만들기](./scripts/functions-cli-create-serverless.md)를 참조하세요.

`publish` 명령은 Functions 프로젝트 디렉터리의 콘텐츠를 업로드합니다. 파일을 로컬로 삭제하는 경우 `publish` 명령은 Azure에서 해당 파일을 삭제하지 않습니다. [Azure Portal]에서 [Kudu 도구](functions-how-to-use-azure-function-app-settings.md#kudu)를 사용하여 Azure에서 파일을 삭제할 수 있습니다.  

>[!IMPORTANT]  
> Azure에서 함수 앱을 만드는 경우 기본적으로 Function 런타임 버전 1.x가 사용됩니다. 함수 앱이 런타임 버전 2.x를 사용하도록 하려면 응용 프로그램 설정 `FUNCTIONS_EXTENSION_VERSION=beta`를 추가합니다.  
이 설정을 함수 앱에 추가하려면 다음 Azure CLI 코드를 사용합니다. 
```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>다음 단계

Azure Functions 핵심 도구는 [오픈 소스이며 GitHub에서 호스팅](https://github.com/azure/azure-functions-cli)됩니다.  
버그 또는 기능 요청을 제출하려면 [GitHub 문제를 개설](https://github.com/azure/azure-functions-cli/issues)합니다. 

<!-- LINKS -->

[Azure Functions 핵심 도구]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
