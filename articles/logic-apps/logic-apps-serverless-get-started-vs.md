---
title: Visual Studio에서 서버를 사용하지 않는 앱 빌드 | Microsoft Docs
description: Visual Studio에서 앱 만들기, 배포 및 관리에 대한 이 가이드를 사용하여 첫 번째 서버를 사용하지 않는 앱을 시작합니다.
keywords: ''
services: logic-apps
author: jeffhollan
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: d3cd509a0073e45bf910e9a74a279d678ff03b63
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Logic Apps 및 함수를 사용하여 Visual Studio에서 서버를 사용하지 않는 앱 빌드

Azure에서 서버를 사용하지 않는 도구 및 기능은 클라우드 응용 프로그램의 신속한 개발 및 배포를 허용합니다.  이 문서는 Visual Studio에서 서버를 사용하지 않는 응용 프로그램 작성을 시작하는 방법에 중점을 둡니다.  Azure에서 서버를 사용하지 않음 개요는 [이 문서에서 찾을 수 있습니다](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>모든 항목 준비

Visual Studio에서 서버를 사용하지 않는 응용 프로그램을 빌드하는 데 필요한 필수 구성 요소는 다음과 같습니다.

* [Visual Studio 2017](https://www.visualstudio.com/vs/) 또는 Visual Studio 2015 - Community, Professional 또는 Enterprise
* [Visual Studio용 Logic Apps 도구](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [최신 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 이상)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Functions를 로컬로 디버그할 [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools)
* 포함된 논리 앱 디자이너를 사용하는 경우 웹에 대한 액세스

## <a name="getting-started-with-a-deployment-template"></a>배포 템플릿 시작

Azure의 리소스 관리는 리소스 그룹 내에서 수행됩니다.  리소스 그룹은 리소스의 논리적 그룹화입니다.  리소스 그룹은 리소스의 컬렉션의 배포 및 관리를 허용합니다.  Azure에서 서버를 사용하지 않는 응용 프로그램의 경우 리소스 그룹에는 Azure Logic Apps 및 Azure Functions 모두가 포함되어 있습니다.  Visual Studio 내에서 리소스 그룹 프로젝트를 사용하여 단일 자산으로 전체 응용 프로그램을 개발, 관리 및 배포할 수 있습니다.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Visual Studio에서 리소스 그룹 프로젝트 만들기

1. Visual Studio에서 클릭하여 **새 프로젝트**를 추가합니다.
1. **클라우드** 범주에서 Azure **리소스 그룹** 프로젝트를 만들도록 선택합니다.  
 * 나열된 범주 또는 프로젝트가 표시되지 않는 경우 Visual Studio용 Azure SDK가 설치되었는지 확인합니다.
1. 프로젝트에 이름 및 위치를 지정하고 **확인**을 선택하여 템플릿을 선택하라는 메시지를 표시하는 Visual Studio를 만듭니다.  빈 값, 논리 앱 또는 다른 리소스로 시작하도록 선택할 수 있습니다.  그러나 이 경우 Azure 빠른 시작 템플릿을 사용하여 서버를 사용하지 않는 앱을 시작합니다.
1. **Azure 빠른 시작**의 템플릿을 표시하도록 선택합니다. ![Azure 빠른 시작 템플릿 선택][1]
1. 서버를 사용하지 않는 빠른 시작 템플릿(**101-logic-app-and-function-app**)을 선택하고 **확인**을 클릭합니다.

빠른 시작 템플릿은 리소스 그룹 프로젝트에서 배포 템플릿을 만듭니다.  템플릿은 Azure Functions를 호출하는 간단한 논리 앱을 포함하고 결과를 반환합니다.  솔루션 탐색기에서 `azuredeploy.json` 파일을 여는 경우 서버를 사용하지 않는 앱에 대한 리소스를 볼 수 있습니다.

## <a name="deploying-the-serverless-application"></a>서버를 사용하지 않는 응용 프로그램 배포

Visual Studio에서 논리 앱 비주얼 디자이너를 열려면 미리 배포된 Azure 리소스 그룹이 있어야 합니다.  이렇게 하면 디자이너는 논리 앱에 리소스에 대한 연결 및 서비스를 만들고 사용할 수 있습니다.  시작하려면 단순히 만든 솔루션을 배포해야 합니다.

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포**를 선택하고 **새** 배포를 만듭니다. ![새 리소스 배포 선택][2]
1. 유효한 Azure 구독 및 리소스 그룹을 선택합니다.
1. 솔루션을 **배포**하도록 선택합니다.
1. 논리 앱과 Azure 함수 앱의 이름을 입력합니다.  Azure 함수 이름은 전역적으로 고유해야 합니다.

서버를 사용하지 않는 솔루션이 지정된 리소스 그룹으로 배포됩니다.  Visual Studio에서 **출력**을 보면 배포의 상태를 볼 수 있습니다.

## <a name="editing-the-logic-app-in-visual-studio"></a>Visual Studio에서 논리 앱 편집

솔루션을 모든 리소스 그룹에 배포한 후 비주얼 디자이너를 사용하여 논리 앱을 편집하고 변경할 수 있습니다.

1. 솔루션 탐색기에서 `azuredeploy.json` 파일을 마우스 오른쪽 단추로 클릭하고 **Logic Apps 디자이너로 열기**를 선택합니다.
1. 솔루션이 배포된 **리소스 그룹** 및 **위치**를 선택하고 **확인**을 선택합니다.

이제 논리 앱 비주얼 디자이너가 Visual Studio와 함께 표시됩니다.  단계 추가, 워크플로 수정 및 변경 내용 저장을 계속할 수 있습니다.  또한 Visual Studio에서 논리 앱을 만들 수도 있습니다.  템플릿 탐색기에서 **리소스**를 마우스 오른쪽 단추로 클릭하면 **논리 앱**을 프로젝트에 추가하도록 선택할 수 있습니다.  빈 논리 앱이 리소스 그룹으로 미리 배포 없이 비주얼 디자이너에 로드됩니다.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>배포된 논리 앱에 대한 실행 기록 관리 및 보기

Azure에 배포된 논리 앱에 대한 실행 기록을 관리하고 볼 수도 있습니다.  Visual Studio에서 **클라우드 탐색기** 도구를 여는 경우 논리 앱을 마우스 오른쪽 단추로 클릭하고 속성 편집, 비활성화, 보기 또는 실행 기록 보기를 선택할 수 있습니다.  또한 편집을 클릭하면 Visual Studio 리소스 그룹 프로젝트에 게시된 논리 앱을 다운로드할 수 있습니다.  즉, Azure Portal에서 논리 앱 빌드를 시작한 경우에도 여전히 가져와서 Visual Studio에서 관리할 수 있습니다.

## <a name="developing-an-azure-function-in-visual-studio"></a>Visual Studio에서 Azure 함수 개발

배포 템플릿은 `azuredeploy.json` 변수 배포에 지정된 git 리포지토리에 대한 솔루션에 포함된 모든 Azure Functions를 배포합니다.  솔루션 내에서 함수 프로젝트를 작성하고 원본 제어(GitHub, Visual Studio Team Services 등)로 확인하고 `repo` 변수를 업데이트하는 경우 템플릿은 Azure 함수를 배포합니다.

### <a name="creating-an-azure-function-project"></a>Azure 함수 프로젝트 만들기

JavaScript, Python, F #, Bash, Batch 또는 PowerShell을 사용하는 경우 [함수 CLI의 단계](../azure-functions/functions-run-local.md)를 따라 프로젝트를 만듭니다.  C#에서 함수를 개발하는 경우 Azure 함수에 대한 현재 솔루션에서 [C# 클래스 라이브러리](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [서버를 사용하지 않는 소셜 대시보드를 빌드하는 방법 알아보기](logic-apps-scenario-social-serverless.md)
* [Visual Studio 클라우드 탐색기에서 논리 앱 관리](manage-logic-apps-with-visual-studio.md)
* [논리 앱 워크플로 정의 언어](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
