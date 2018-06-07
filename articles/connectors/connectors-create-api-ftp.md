---
title: 논리 앱에서 FTP 커넥터를 사용하는 방법 알아보기 | Microsoft Docs
description: Azure 앱 서비스로 논리 앱을 만듭니다. FTP 서버에 연결하여 파일을 관리합니다. FTP 서버에서 파일 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 7719e0e90df3a384e870e2e839cb77a27d95ada8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-the-ftp-connector"></a>FTP 커넥터 시작
FTP 커넥터를 사용하여 FTP 서버에서 파일을 모니터링 및 관리하고 파일을 만듭니다. 

[커넥터](apis-list.md)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [지금 논리 앱을 만들어](../logic-apps/quickstart-create-first-logic-app-workflow.md) 시작할 수 있습니다.

## <a name="connect-to-ftp"></a>FTP에 연결
논리 앱에서 서비스에 액세스하려면 먼저 서비스에 대한 *연결*을 만들어야 합니다. [연결](connectors-overview.md)은 논리 앱과 다른 서비스 간의 연결을 제공합니다.  

### <a name="create-a-connection-to-ftp"></a>FTP에 대한 연결 만들기
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>FTP 트리거 사용
트리거는 논리 앱에 정의된 워크플로를 시작하는 데 사용할 수 있는 이벤트입니다. [트리거에 대해 자세히 알아보세요.](../logic-apps/logic-apps-overview.md#logic-app-concepts)  

> [!IMPORTANT]
> FTP 커넥터를 사용하려면 인터넷에서 액세스할 수 있고 PASSIVE 모드로 작동하도록 구성된 FTP 서버가 필요합니다. 또한 FTP 커넥터는 **암시적 FTPS(FTP over SSL)와 호환되지 않습니다**. FTP 커넥터는 명시적 FTPS(FTP over SSL)만 지원합니다.  
> 
> 

이 예제에서는 파일이 FTP 서버에 추가되거나 수정될 때 **FTP - 파일을 추가하거나 수정할 때** 트리거를 사용하여 논리 앱 워크플로를 시작하는 방법을 보여 줍니다. 엔터프라이즈 예에서는 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 FTP 폴더를 모니터링할 수 있습니다.  그런 다음 **파일 콘텐츠 가져오기**와 같은 FTP 커넥터 작업을 사용하여 추가 처리할 주문 및 주문 데이터베이스에 있는 저장소의 콘텐츠를 가져올 수 있습니다.

1. Logic Apps 디자이너의 검색 상자에 *ftp*를 입력한 후 **FTP - 파일을 추가하거나 수정할 때** 트리거를 선택합니다.   
   ![FTP 트리거 이미지 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **파일을 추가하거나 수정할 때** 컨트롤이 열립니다.  
   ![FTP 트리거 이미지 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. 컨트롤의 오른쪽에 있는 **...**를 선택합니다. 이렇게 하면 폴더 선택 컨트롤이 열립니다.  
   ![FTP 트리거 이미지 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. **>**(오른쪽 화살표)를 선택하고 새 파일 또는 수정된 파일을 모니터링할 폴더를 탐색하여 찾습니다. 해당 폴더를 선택하면 이제 폴더가 **폴더** 컨트롤에 표시됩니다.  
   ![FTP 트리거 이미지 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

이제, 논리 앱은 파일이 특정 FTP 폴더에서 수정되거나 만들어질 때 워크플로의 다른 트리거 및 작업의 실행을 시작하는 트리거로 구성되었습니다. 

> [!NOTE]
> 논리 앱이 작동하려면 하나 이상의 트리거와 동작이 있어야 합니다. 다음 섹션의 단계에 따라 동작을 추가합니다.  
> 
> 

## <a name="use-a-ftp-action"></a>FTP 작업 사용
작업은 논리 앱에 정의된 워크플로에 의해 수행되는 작업입니다. [작업에 대해 자세히 알아봅니다.](../logic-apps/logic-apps-overview.md#logic-app-concepts)  

이제 트리거를 추가했으므로 다음 단계에 따라 트리거가 찾은 새 파일 또는 수정된 파일의 콘텐츠를 가져올 작업을 추가합니다.    

1. **+ 새 단계**를 선택하여 FTP 서버에서 파일의 콘텐츠를 가져올 작업을 추가합니다.  
2. **동작 추가** 링크를 선택합니다.  
   ![FTP 작업 이미지 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. *FTP*를 입력하여 FTP와 관련된 모든 작업을 검색합니다.
4. FTP 폴더에서 새 파일 또는 수정된 파일을 찾을 때 수행할 작업으로 **FTP - 파일 콘텐츠 가져오기**를 선택합니다.      
   ![FTP 작업 이미지 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **파일 콘텐츠 가져오기** 컨트롤이 열립니다. **참고**: 논리 앱에 FTP 서버 계정에 액세스하기 위한 권한을 아직 부여하지 않았으면 이러한 권한을 부여하라는 메시지가 표시됩니다.  
   ![FTP 작업 이미지 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. **파일** 컨트롤(**FILE*** 아래에 흰색 영역)을 선택합니다. 여기에서 FTP 서버에서 찾은 새 파일 또는 수정된 파일의 다양한 속성을 사용할 수 있습니다.  
6. **파일 콘텐츠** 옵션을 선택합니다.  
   ![FTP 작업 이미지 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. 컨트롤이 업데이트되고 **FTP - 파일 콘텐츠 가져오기** 작업에서 FTP 서버에 있는 새 파일 또는 수정된 파일의 *파일 콘텐츠*를 가져오는 것이 표시됩니다.      
   ![FTP 작업 이미지 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. 작업을 저장한 후 파일을 FTP 폴더에 추가하여 워크플로를 테스트합니다.    

이제 FTP 서버에서 폴더를 모니터링하고 FTP 서버에서 새 파일 또는 수정된 파일을 찾으면 워크플로를 시작하는 트리거로 논리 앱이 구성되었습니다. 

또한 새 파일 또는 수정된 파일의 콘텐츠를 가져오는 작업으로도 논리 앱이 구성되었습니다.

이제 새 파일 또는 수정된 파일의 콘텐츠를 SQL 데이터베이스 테이블에 삽입하는 [SQL Server - 행 삽입](connectors-create-api-sqlazure.md) 작업과 같은 다른 작업을 추가할 수 있습니다.  

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/ftpconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
[논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)

