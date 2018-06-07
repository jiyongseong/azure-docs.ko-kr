---
title: Logic Apps에 Azure Blob Storage 커넥터 추가 | Microsoft Docs
description: 시작하여 논리 앱에서 Azure Blob Storage 커넥터 구성
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: b5dc3f75-6bea-420b-b250-183668d2848d
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/02/2017
ms.author: estfan; ladocs
ms.openlocfilehash: 7aaff2ac78201c4484105c6cacc5f0fef19ca7b5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="use-the-azure-blob-storage-connector-in-a-logic-app"></a>논리 앱에서 Azure Blob Storage 커넥터 사용
Azure Blob Storage 커넥터를 사용하여 논리 앱 내의 저장소 계정에서 blob을 업로드, 업데이트, 가져오기 및 삭제할 수 있습니다.  

Azure Blob 저장소를 사용하여 다음과 같은 작업을 수행합니다.

* 새 프로젝트를 업로드하거나 최근에 업데이트한 파일을 가져와 워크플로를 작성합니다.
* 파일 메타데이터 가져오기, 파일 삭제, 파일 복사 및 삭제를 위한 작업을 사용합니다. 예를 들어 도구가 Azure 웹 사이트에서 업데이트되면(트리거) Blob 저장소에서 파일을 업데이트합니다(작업). 

이 항목에서는 논리 앱에서 Blob 저장소 커넥터를 사용하는 방법을 보여 줍니다.

Logic Apps에 대해 자세히 알아보려면 [논리 앱이란 무엇인가요?](../logic-apps/logic-apps-overview.md) 및 [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="connect-to-azure-blob-storage"></a>Azure Blob 저장소에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. 연결은 논리 앱과 다른 서비스 간의 연결을 제공합니다. 예를 들어 저장소 계정에 연결하려면 먼저 Blob 저장소 *연결*을 만듭니다. 연결을 만들려면 연결하려는 서비스에 액세스할 때 일반적으로 사용하는 자격 증명을 입력합니다. 따라서 Azure 저장소를 사용하는 경우 저장소 계정에 대한 자격 증명을 입력하여 연결을 만듭니다. 

#### <a name="create-the-connection"></a>연결 만들기
> [!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="use-a-trigger"></a>트리거 사용
이 연결에는 트리거가 필요하지 않습니다. 다른 트리거(되풀이 트리거, HTTP 웹후크 트리거, 다른 커넥터와 함께 사용할 수 있는 트리거 포함)를 사용하여 논리 앱을 시작합니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)에서 예제를 제공하고 있습니다.

## <a name="use-an-action"></a>작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다.

1. 더하기 기호를 선택합니다. **작업 추가**, **조건 추가** 또는 **자세히** 옵션 중 하나 등 몇 가지가 표시됩니다.
   
    ![](./media/connectors-create-api-azureblobstorage/add-action.png)
2. **작업 추가**를 선택합니다.
3. 사용 가능한 모든 작업의 목록을 표시하려면 텍스트 상자에 "blob"을 입력합니다.
   
    ![](./media/connectors-create-api-azureblobstorage/actions.png) 
4. 이 예제에서는 **AzureBlob - 경로를 사용하여 파일 메타데이터 가져오기**를 선택합니다. 연결이 이미 있는 경우 **...** (선택기 표시) 단추를 선택하여 파일을 선택합니다.
   
    ![](./media/connectors-create-api-azureblobstorage/sample-file.png)
   
    연결 정보를 묻는 메시지가 표시되면 연결을 만들기 위한 세부 정보를 입력합니다. 이 항목의 [연결 만들기](connectors-create-api-azureblobstorage.md#create-the-connection)에서는 이러한 속성에 대해 설명합니다. 
   
   > [!NOTE]
   > 이 예제에서는 파일의 메타 데이터를 가져옵니다. 메타데이터를 보려면 다른 커넥터를 사용하여 새 파일을 만드는 다른 작업을 추가합니다. 예를 들어 메타데이터에 따라 새 "test" 파일을 만드는 OneDrive 작업을 추가합니다. 


5. 변경 내용을 **저장**합니다(도구 모음 왼쪽 위 모서리). 논리 앱이 저장되며 이 논리 앱이 사용 상태로 자동 설정될 수 있습니다.

> [!TIP]
> [저장소 탐색기](http://storageexplorer.com/)는 여러 Storage 계정을 관리하는 유용한 도구입니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/azureblobconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md) [API 목록](apis-list.md)에서 Logic Apps의 사용 가능한 다른 커넥터를 확인하세요.

