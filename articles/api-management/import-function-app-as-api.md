---
title: Azure Portal에서 함수 앱을 API로 가져오기 | Microsoft Docs
description: 이 자습서에서는 APIM(API Management)을 사용하여 함수 앱을 API로 가져오는 방법을 보여 줍니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 1962a4aac8e2d15caf4ec33998da1985d3b8a9af
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="import-a-function-app-as-an-api"></a>API로 함수 앱 가져오기

이 문서에서는 함수 앱을 API로 가져오는 방법을 보여 줍니다. 또한 APIM API를 테스트하는 방법도 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * API로 함수 앱 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

## <a name="prerequisites"></a>필수 조건

+ 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
+ 구독에 함수 앱이 있는지 확인합니다. 자세한 내용은 [함수 앱 만들기](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)를 참조하세요.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>백 엔드 API 가져오기 및 게시

1. **API Management**에서 **API**를 선택합니다.
2. **새 API 추가** 목록에서 **함수 앱**을 선택합니다.

    ![함수 앱](./media/import-function-app-as-api/function-app-api.png)
3. **찾아보기**를 눌러 구독의 함수 앱 목록을 표시합니다.
4. 앱을 선택합니다. APIM은 선택한 앱과 연결된 swagger를 찾아서 페치하고 가져옵니다. 
5. API URL 접미사를 추가합니다. 접미사는 이 APIM 인스턴스에서 이 특정 API를 식별하는 이름입니다. 이 APIM 인스턴스 내에서 고유해야 합니다.
6. API를 제품에 연결하여 API를 게시합니다. 이 경우 "*Unlimited*" 제품이 사용됩니다.  API를 게시하고 개발자가 사용할 수 있게 하려면 제품에 추가합니다. API를 만드는 동안 이 작업을 수행할 수도 있고 나중에 설정할 수도 있습니다.

    제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.

    기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품과 함께 제공됩니다.

    * **Starter**
    * **무제한**   
7. **만들기**를 선택합니다.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal에서 새 APIM API 테스트

dAzure Portal에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다.  

1. 이전 단계에서 만든 API를 선택합니다.
2. **테스트** 탭을 누릅니다.
3. 작업을 선택합니다.

    페이지에 쿼리 매개 변수에 대한 필드와 헤더 필드가 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다. 
1. **보내기**를 누릅니다.

    백 엔드는 **200 정상** 및 일부 데이터로 응답합니다.

## <a name="call-operation"> </a>개발자 포털에서 작업 호출

**개발자 포털**에서 작업을 호출하여 API를 테스트할 수도 있습니다. 

1. "백 엔드 API 가져오기 및 게시" 단계에서 만든 API를 선택합니다.
2. **개발자 포털**을 누릅니다.

    "개발자 포털" 사이트가 열립니다.
3. 만든 **API**를 선택합니다.
4. 테스트할 작업을 클릭합니다.
5. **시도**를 누릅니다.
6. **보내기**를 누릅니다.
    
    작업 호출 후에는 개발자 포털에 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)