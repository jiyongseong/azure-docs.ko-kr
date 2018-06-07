---
title: Visual Studio로 논리 앱 관리 - Azure Logic Apps | Microsoft Docs
description: Visual Studio 클라우드 탐색기를 사용하여 Logic Apps 및 기타 Azure 자산 관리
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 7914bce6ca71b1b3f00c69fb6f33154f0f52dc7a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34362200"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Visual Studio로 논리 앱 관리

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 논리 앱을 만들고, 편집하고, 관리하고, 배포할 수 있지만, 소스 제어에 논리 앱을 추가하고 다른 버전을 게시하고 다른 배포 환경에 대한 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 템플릿을 만들려는 경우에도 Visual Studio를 사용할 수 있습니다. Visual Studio 클라우드 탐색기로 다른 Azure 리소스와 함께 논리 앱을 찾고 관리할 수 있습니다. 예를 들어 Azure Portal에서 이미 배포된 논리 앱을 열고, 다운로드하고, 편집하고, 실행하고, 실행 기록을 보고, 해제하고, 설정할 수 있습니다. Visual Studio에서 Azure Logic Apps를 작업하는 데 익숙하지 않다면 [Visual Studio로 논리 앱 만들기](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)를 참조하세요.

> [!IMPORTANT]
> Visual Studio에서 논리 앱을 배포하거나 게시하면 Azure Portal에서 해당 앱의 버전을 덮어씁니다. 따라서 Azure Portal에서 변경한 내용을 계속 유지하려면 다음에 Visual Studio에서 배포 또는 게시하기 전에 Azure Portal에서 [Visual Studio를 사용하여 논리 앱을 새로 고침](#refresh)해야 합니다.

<a name="requirements"></a>

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">무료 Azure 계정에 등록</a>합니다.

* 다음 도구가 없으면 다운로드하여 설치합니다. 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 또는 Visual Studio 2015 - Community Edition 이상</a>. 
  이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2017을 사용합니다.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Azure SDK(2.9.1 이상)</a> 및 <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Visual Studio 2017용 Azure Logic Apps 도구</a> 또는 <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015 버전</a> 
  
    Visual Studio Marketplace에서 직접 Azure Logic Apps 도구를 다운로드해 설치하거나 <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">Visual Studio 내에서 이 확장을 설치하는 방법</a>을 알아볼 수 있습니다. 
    설치를 완료하면 Visual Studio를 다시 시작해야 하는지 확인합니다.

* 포함된 Logic Apps 디자이너를 사용하는 동안 웹에 액세스

  디자이너는 Azure에서 리소스를 만들고 논리 앱의 커넥터에서 속성 및 데이터를 읽기 위해 인터넷 연결을 필요로 합니다. 
  예를 들어, Dynamics CRM Online 커넥터를 사용하는 경우 디자이너는 사용 가능한 사용자 지정 및 기본 속성에 대한 CRM 인스턴스를 확인합니다.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>논리 앱 찾기

Visual Studio에서 클라우드 탐색기를 사용하여 Azure 구독에 연결되고 Azure Portal에서 배포된 모든 논리 앱을 찾을 수 있습니다.

1. Visual Studio를 엽니다. **보기** 메뉴에서 **클라우드 탐색기**를 선택합니다.

2. 클라우드 탐색기에서 **계정 관리**를 선택합니다. 논리 앱과 연결된 Azure 구독을 선택하고 **적용**을 선택합니다. 예: 

   !["계정 관리" 선택](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. **리소스 그룹**으로 검색하는지 아니면 **리소스 종류**로 검색하는지에 따라 다음 단계를 수행합니다.

   * **리소스 그룹**: Azure 구독 아래에서 클라우드 탐색기가 해당 구독과 연결된 모든 리소스 그룹을 표시합니다. 
   논리 앱을 포함하고 있는 리소스 그룹을 확장한 다음, 논리 앱을 선택합니다.

   * **리소스 종류**: Azure 구독에서 **Logic Apps**를 확장합니다. 클라우드 탐색기가 구독과 연결된 모든 배포된 논리 앱을 표시하면 논리 앱을 선택합니다.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Visual Studio에서 열기

Visual Studio에서 Azure Portal을 통해 직접 또는 Visual Studio에서 Azure Resource Manager 프로젝트로 만들고 배포한 논리 앱을 열 수 있습니다.

1. 클라우드 탐색기를 열고 논리 앱을 찾습니다. 

2. 논리 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   이 예제는 리소스 종류별로 논리 앱을 표시하므로 논리 앱이 **Logic Apps** 섹션 아래에 표시됩니다.

  ![Azure Portal에서 배포된 논리 앱 열기](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Logic Apps 디자이너에서 논리 앱이 열린 후 디자이너의 맨 아래에서 **코드 보기**를 선택하면 기본 논리 앱 정의 구조를 검토할 수 있습니다. 
   논리 앱에 대한 배포 템플릿을 만들려면 해당 논리 앱에 대한 [Azure Resource Manager 템플릿을 다운로드하는 방법](#download-logic-app)을 살펴보세요. [Resource Manager 템플릿](../azure-resource-manager/resource-group-overview.md#template-deployment)에 대해 자세히 알아보세요.

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Azure에서 다운로드

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 논리 앱을 다운로드하여 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 템플릿으로 저장할 수 있습니다. 그런 다음, Visual Studio를 사용하여 로컬로 템플릿을 편집하고 다른 배포 환경에 적합하도록 사용자 지정할 수 있습니다. 논리 앱을 자동으로 다운로드하면 해당 정의가 [Resource Manager 템플릿](../azure-resource-manager/resource-group-overview.md#template-deployment) 내에서 *매개 변수화*되며, 템플릿도 JSON(JavaScript Object Notation)을 사용합니다.

1. Visual Studio에서 클라우드 탐색기를 열고, Azure에서 다운로드하려는 논리 앱을 찾아서 선택합니다.

2. 해당 앱의 바로 가기 메뉴에서 **논리 앱 편집기로 열기**를 선택합니다.

   논리 앱 디자이너가 열리고 논리 앱이 표시됩니다. 
   논리 앱의 기본 정 및 구조를 보려면 디자이너의 맨 아래에서 **코드 보기**를 선택합니다. 

3. 디자이너 도구 모음에서 **다운로드**를 선택합니다.

   !["다운로드" 선택](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. 위치를 묻는 메시지가 표시되면 해당 위치로 이동하여 논리 앱 정의에 대한 Resource Manager 템플릿을 JSON(.json) 파일 형식으로 저장합니다. 

논리 앱 정의는 Resource Manager 템플릿 내부의 `resources` 하위 섹션에 표시됩니다. 이제 Visual Studio를 사용하여 논리 앱 정의 및 Resource Manager 템플릿을 편집할 수 있습니다. 또한 템플릿을 Visual Studio 솔루션에 Azure Resource Manager 프로젝트로 추가할 수 있습니다. [Visual Studio의 논리 앱에 대한 Resource Manager 프로젝트](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)에 대해 알아보세요. 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Azure에서 새로 고침

Azure Portal에서 논리 앱을 편집하고 변경 내용을 유지하려면 Visual Studio에서 해당 변경 내용으로 앱 버전을 새로 고침해야 합니다. 

* Visual Studio의 논리 앱 디자이너 도구 모음에서 **새로 고침**을 선택합니다.

  또는

* Visual Studio 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **새로 고침**을 선택합니다. 

![업데이트로 논리 앱 새로 고침](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>논리 앱 업데이트 게시

Visual Studio에서 Azure로 논리 앱 업데이트를 배포할 준비가 완료되면 논리 앱 디자이너 도구 모음에서 **게시**를 선택합니다.

![업데이트된 논리 앱 게시](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>수동으로 논리 앱 실행

Azure에 배포된 논리 앱을 Visual Studio에서 수동으로 트리거할 수 있습니다. 논리 앱 디자이너 도구 모음에서 **트리거 실행**을 선택합니다.

![수동으로 논리 앱 실행](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>실행 기록 검토

논리 앱 실행 상태를 확인하고 문제를 진단하려면 Visual Studio에서 해당 실행에 대한 입력 및 출력 같은 세부 정보를 검토하면 됩니다.

1. 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **실행 기록 열기**를 선택합니다.

   ![실행 기록 열기](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. 특정 실행에 대한 세부 정보를 보려면 실행을 두 번 클릭합니다. 예: 

   ![구체적인 실행 기록](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > 속성별로 테이블을 정렬하려면 해당 속성의 열 헤더를 선택합니다. 

3. 입력 및 출력을 검토할 단계를 확장합니다. 예: 

   ![각 단계에 대한 입력 및 출력 보기](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>논리 앱 사용 또는 사용 안 함

논리 앱을 삭제하지 않고도 다음 번에 트리거 조건이 충족되더라도 트리거가 발생하지 않게 할 수 있습니다. 논리 앱을 사용하지 않도록 설정하면 Logic Apps 엔진이 논리 앱에 대한 이후 워크플로 인스턴스를 만들고 실행하지 못하게 차단됩니다.
클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **사용 안 함**을 선택합니다.

![논리 앱 사용 안 함](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

논리 앱을 다시 시작할 준비가 되면 논리 앱을 다시 활성화할 수 있습니다. 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **사용**을 선택합니다.

![논리 앱 사용](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>논리 앱 삭제

Azure Portal에서 논리 앱을 삭제하려면 클라우드 탐색기에서 논리 앱의 바로 가기 메뉴를 열고 **삭제**를 선택합니다.

![논리 앱 삭제](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Visual Studio를 사용하여 배포된 논리 앱을 관리하는 방법을 배웠습니다. 다음으로 배포에 대한 논리 앱 정의 사용자 지정에 대해 알아보겠습니다.

> [!div class="nextstepaction"]
> [JSON의 작성자 논리 앱 정의](../logic-apps/logic-apps-author-definitions.md)
