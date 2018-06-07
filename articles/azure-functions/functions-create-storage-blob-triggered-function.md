---
title: Azure에서 Blob Storage에 의해 트리거되는 함수 만들기 | Microsoft Docs
description: Azure Functions를 사용하여 Azure Blob Storage에 추가된 항목에 의해 호출되는 서버를 사용하지 않는 함수를 만듭니다.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 928ea1dbb68206e128f0593ba15cb48935ab1ccf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Azure Blob Storage에 의해 트리거되는 함수 만들기

파일이 업로드되거나 Azure Blob Storage에서 업데이트될 때 트리거되는 함수를 만드는 방법을 알아봅니다.

![로그에서 메시지 보기.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>필수 조건

+ [Microsoft Azure Storage 탐색기](http://storageexplorer.com/)를 다운로드하고 설치합니다.
+ Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

## <a name="create-an-azure-function-app"></a>Azure Function 앱 만들기

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![함수 앱을 성공적으로 만들었습니다.](./media/functions-create-first-azure-function/function-app-create-success.png)

다음으로 새 함수 앱에서 함수를 만듭니다.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Blob Storage 트리거 함수 만들기

1. 함수 앱을 확장한 후 **함수** 옆의 **+** 단추를 클릭합니다. 함수 앱에서 첫 번째 함수이면 **사용자 지정 함수**를 선택합니다. 그러면 함수 템플릿의 전체 집합이 표시됩니다.

    ![Azure Portal에서 함수 빨리 시작하기 페이지](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

2. 검색 필드에 `blob`를 입력한 다음 Blob Storage 트리거 템플릿에서 원하는 언어를 선택합니다.

    ![Blob Storage 트리거 템플릿을 선택합니다.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
 
3. 이미지 아래의 표에 지정된 설정을 사용합니다.

    ![Blob Storage 트리거 함수 만들기.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | 설정 | 제안 값 | 설명 |
    |---|---|---|
    | **Name** | 함수 앱에서 고유 | 이 Blob 트리거 함수의 이름입니다. |
    | **Path**   | samples-workitems/{name}    | 모니터링되는 Blob Storage의 위치입니다. 바인딩에 _name_ 매개 변수로 전달되는 Blob의 파일 이름입니다.  |
    | **Storage 계정 연결** | AzureWebJobsStorage | 함수 앱에 이미 사용된 저장소 계정 연결을 사용하거나 새로 만들 수 있습니다.  |

3. **만들기**를 클릭하여 사용자의 함수를 만듭니다.

다음으로 Azure Storage 계정에 연결하고 **samples-workitems** 컨테이너를 만듭니다.

## <a name="create-the-container"></a>컨테이너 만들기

1. 함수에서 **통합**을 클릭하고 **설명서**를 확장하여 **계정 이름** 및 **계정 키**를 모두 복사합니다. 이러한 자격 증명을 사용하여 저장소 계정에 연결합니다. 저장소 계정에 이미 연결된 경우 4단계로 건너뜁니다.

    ![Storage 계정 연결 자격 증명 가져오기.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. [Microsoft Azure Storage 탐색기](http://storageexplorer.com/) 도구를 실행하고 왼쪽의 연결 아이콘을 클릭하고 **저장소 계정 이름 및 키 사용**을 선택하고 **다음**을 클릭합니다.

    ![Storage 계정 탐색기 도구 실행.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. 1단계에서 **계정 이름** 및 **계정 키**를 입력하고 **다음**을 클릭한 후 **연결**을 클릭합니다. 

    ![저장소 자격 증명 입력 및 연결.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. 연결된 저장소 계정을 확장하고 **Blob 컨테이너**를 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 클릭하고 `samples-workitems`를 입력한 후 Enter 키를 누릅니다.

    ![저장소 큐 만들기.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

이제 Blob 컨테이너가 있고 컨테이너에 파일을 업로드하여 함수를 테스트할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

1. Azure Portal로 돌아가서 함수를 찾은 후 페이지 맨 아래에 있는 **로그**를 확장하고 로그 스트리밍이 일시 중지되지 않았는지 확인합니다.

1. Storage 탐색기에서 저장소 계정, **Blob 컨테이너** 및 **samples-workitems**를 확장합니다. **업로드**를 클릭한 후 **파일 업로드...**를 클릭합니다.

    ![Blob 컨테이너에 파일 업로드.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. **파일 업로드** 대화 상자에서 **파일** 필드를 클릭합니다. 로컬 컴퓨터에서 이미지 파일과 같은 파일을 찾아 선택하고 **열기** 및 **업로드**를 차례로 클릭합니다.

1. 함수 로그로 돌아가 Blob를 읽었는지 확인합니다.

   ![로그에서 메시지 보기.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > 함수 앱이 기본 소비 계획에서 실행될 때 추가 또는 업데이트되는 Blob과 트리거되는 함수 사이에 최대 몇 분의 지연이 있을 수 있습니다. Blob 트리거 함수에서 대기 시간을 줄여야 하는 경우 App Service 계획에서 함수 앱을 실행하는 것이 좋습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

Blob Storage에서 Blob이 추가 또는 업데이트될 때 실행되는 함수를 만들었습니다. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Blob Storage 트리거에 대한 자세한 내용은 [Azure Functions Blob Storage 바인딩](functions-bindings-storage-blob.md)을 참조하세요.
