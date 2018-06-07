---
title: Azure Portal을 사용하여 OpenAPI 사양 가져오기 | Microsoft Docs
description: API Management를 사용하여 OpenAPI 사양을 가져오는 방법을 알아봅니다.
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
ms.openlocfilehash: f5132215b1fda93c62c1fbea46c3266fcc44ec46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="import-an-openapi-specification"></a>OpenAPI 사양 가져오기

이 문서에서는 http://conferenceapi.azurewebsites.net?format=json에 있는 "OpenAPI 사양" 백 엔드 API를 가져오는 방법을 보여줍니다. 이 백 엔드 API는 Microsoft에서 제공하고 Azure에서 호스트됩니다. 또한 APIM API를 테스트하는 방법도 설명합니다.

> [!IMPORTANT]
> OpenAPI 가져오기와 관련된 중요한 정보 및 팁은 이 [문서](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/)를 참조하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * "OpenAPI 사양" 백 엔드 API 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

## <a name="prerequisites"></a>필수 조건

다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>백 엔드 API 가져오기 및 게시

1. **API Management**에서 **API**를 선택합니다.
2. **새 API 추가** 목록에서 **OpenAPI 사양**을 선택합니다.
    ![OpenAPI 사양](./media/import-api-from-oas/oas-api.png)
3. 적절한 설정을 입력합니다. 만드는 동안 모든 API 값을 설정할 수 있습니다. 또는 **설정** 탭으로 이동하여 일부를 나중에 설정할 수 있습니다. <br/> **탭** 키를 누르면 일부(또는 모든) 필드가 지정된 백 엔드 서비스의 정보로 채워집니다.

    ![API 만들기](./media/api-management-get-started/create-api.png)

    |설정|값|설명|
    |---|---|---|
    |**OpenAPI 사양**|http://conferenceapi.azurewebsites.net?format=json|API를 구현하는 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.|
    |**표시 이름**|*데모 회의 API*|서비스 URL을 입력한 후 tab 키를 누르면 APIM은 json에 포함된 내용에 따라 이 필드를 채웁니다. <br/>이 이름은 개발자 포털에 표시됩니다.|
    |**Name**|*demo-conference-api*|API의 고유한 이름을 제공합니다. <br/>서비스 URL을 입력한 후 tab 키를 누르면 APIM은 json에 포함된 내용에 따라 이 필드를 채웁니다.|
    |**설명**|API에 대한 선택적 설명을 제공합니다.|서비스 URL을 입력한 후 tab 키를 누르면 APIM은 json에 포함된 내용에 따라 이 필드를 채웁니다.|
    |**API URL 접미사**|*conference*|접미사는 API Management 서비스의 기준 URL에 추가됩니다. API Management는 접미사를 사용하여 API를 구분하므로, 접미사는 지정된 게시자의 모든 API에 대해 고유해야 합니다.|
    |**URL 구성표**|*HTTPS*|API에 액세스하는 데 사용할 수 있는 프로토콜을 결정합니다. |
    |**제품**|*무제한*| API를 제품에 연결하여 API를 게시합니다. 선택적으로 이 새로운 API를 제품에 추가하려면 제품 이름을 입력합니다. 이 단계는 여러 제품에 API 추가를 여러 번 반복할 수 있습니다.<br/>제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.<br/> 기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품인 **Starter** 및 **Unlimited**와 함께 제공됩니다. |

4. **만들기**를 선택합니다.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal에서 새 APIM API 테스트

dAzure Portal에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다.

1. 이전 단계에서 만든 API를 선택합니다.
2. **테스트** 탭을 누릅니다.

    ![API 테스트](./media/api-management-get-started/test-api.png)
1. **GetSpeakers**를 클릭합니다.

    페이지에는 쿼리 매개 변수에 대한 필드가 표시되지만 이 경우에는 아무 필드도 표시되지 않습니다. 페이지에는 헤더에 대한 필드도 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다.
4. **보내기**를 누릅니다.

    백 엔드는 **200 정상** 및 일부 데이터로 응답합니다.

## <a name="call-operation"> </a>개발자 포털에서 작업 호출

**개발자 포털**에서 작업을 호출하여 API를 테스트할 수도 있습니다.

1. "백 엔드 API 가져오기 및 게시" 단계에서 만든 API를 선택합니다.
2. **개발자 포털**을 누릅니다.

    ![개발자 포털에서 테스트](./media/api-management-get-started/developer-portal.png)

    "개발자 포털" 사이트가 열립니다.
3. **API**를 선택합니다.
4. **데모 회의 API**를 선택합니다.
5. **GetSpeakers**를 클릭합니다.

    페이지에는 쿼리 매개 변수에 대한 필드가 표시되지만 이 경우에는 아무 필드도 표시되지 않습니다. 페이지에는 헤더에 대한 필드도 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다.
6. **시도**를 누릅니다.
7. **보내기**를 누릅니다.

    작업 호출 후에는 개발자 포털에 **응답 상태**, **응답 헤더**, **응답 콘텐츠**가 표시됩니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
