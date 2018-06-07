---
title: Azure API Management에서 첫 번째 API 가져오기 및 게시 | Microsoft Docs
description: Azure API Management에서 첫 번째 API를 가져온 후 게시하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: a7a4391b436af4ffa303741397f0be7abf0186fb
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="import-and-publish-your-first-api"></a>첫 번째 API 가져오기 및 게시 

이 자습서에서는 http://conferenceapi.azurewebsites.net?format=json에 있는 "OpenAPI 사양" 백 엔드 API를 가져오는 방법을 보여줍니다. 이 백 엔드 API는 Microsoft에서 제공하고 Azure에서 호스트됩니다. 

백 엔드 API를 APIM(API Management)으로 가져오면 APIM API는 백 엔드 API의 외관이 됩니다. 백 엔드 API를 가져올 때 원본 API와 APIM API는 동일합니다. APIM을 사용하여 백 엔드 API를 건드리지 않고 필요에 따라 외관을 사용자 지정할 수 있습니다. 자세한 내용은 [API 변환 및 보호](transform-api.md)를 참조하세요. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 첫 번째 API 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

![새 API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>필수 조건

다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>백 엔드 API 가져오기 및 게시

이 섹션에서는 OpenAPI 사양 백 엔드 API를 가져오고 게시하는 방법을 보여 줍니다.
 
1. **API Management**에서 **API**를 선택합니다.
2. 목록에서 **OpenAPI 사양**을 선택합니다.

    ![API 만들기](./media/api-management-get-started/create-api.png)

    만드는 동안 또는 나중에 **설정** 탭으로 이동하여 API 값을 설정할 수 있습니다. 필드 옆에 있는 빨간색 별표는 필수 필드임을 나타냅니다.

    |설정|값|설명|
    |---|---|---|
    |**OpenAPI 사양**|http://conferenceapi.azurewebsites.net?format=json|API를 구현하는 서비스를 참조합니다. API 관리는 이 주소로 요청을 전달합니다.|
    |**표시 이름**|*데모 회의 API*|서비스 URL을 입력한 후 tab 키를 누르면 APIM은 json에 포함된 내용에 따라 이 필드를 채웁니다. <br/>이 이름은 개발자 포털에 표시됩니다.|
    |**Name**|*demo-conference-api*|API의 고유한 이름을 제공합니다. <br/>서비스 URL을 입력한 후 tab 키를 누르면 APIM은 json에 포함된 내용에 따라 이 필드를 채웁니다.|
    |**설명**|API에 대한 선택적 설명을 제공합니다.|서비스 URL을 입력한 후 tab 키를 누르면 APIM은 json에 포함된 내용에 따라 이 필드를 채웁니다.|
    |**URL 구성표**|*HTTPS*|API에 액세스하는 데 사용할 수 있는 프로토콜을 결정합니다. |
    |**API URL 접미사**|*conference*|접미사는 API Management 서비스의 기준 URL에 추가됩니다. API Management는 접미사를 사용하여 API를 구분하므로, 접미사는 지정된 게시자의 모든 API에 대해 고유해야 합니다.|
    |**제품**|*무제한*|제품은 하나 이상의 API와 연결됩니다. 다양한 API를 제품에 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. <br/>API를 제품(이 예제에서는 *Unlimited*)에 연결하여 API를 게시합니다. 이 새 API를 제품에 추가하려면 제품 이름을 입력합니다(나중에 **설정** 페이지에서 입력 가능). 이 단계는 여러 제품에 API 추가를 여러 번 반복할 수 있습니다.<br/>개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. <br/> APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 모든 제품을 구독한 상태가 됩니다.<br/> 기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품인 **Starter** 및 **Unlimited**와 함께 제공됩니다. |
    |API 버전 관리||버전 관리에 대한 자세한 내용은 [여러 버전의 API 게시](api-management-get-started-publish-versions.md)를 참조하세요.|
    
    >[!NOTE]
    > API를 게시하려면 API를 제품과 연결해야 합니다. **설정 페이지**에서 연결할 수 있습니다.
    
3. **만들기**를 선택합니다.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Azure Portal에서 새 APIM API 테스트

dAzure Portal에서 직접 작업을 호출할 수 있으며, 이 포털을 사용하면 편리한 방법으로 API의 작업을 보고 테스트할 수 있습니다.  
1. 이전 단계에서(**API** 탭에서) 만든 API를 선택합니다.
2. **테스트** 탭을 누릅니다.  ![API 테스트](./media/api-management-get-started/test-api.png)
3. **GetSpeakers**를 클릭합니다.
    페이지에는 쿼리 매개 변수에 대한 필드가 표시되지만 이 경우에는 아무 필드도 표시되지 않습니다. 페이지에는 헤더에 대한 필드도 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. 키가 자동으로 채워집니다.
4. **보내기**를 누릅니다.

    백 엔드는 **200 정상** 및 일부 데이터로 응답합니다.

## <a name="call-operation"> </a>개발자 포털에서 작업 호출

**개발자 포털**에서 작업을 호출하여 API를 테스트할 수도 있습니다. 

1. **데모 회의 API**를 선택합니다.
2. **GetSpeakers**를 클릭합니다.
    
    페이지에는 쿼리 매개 변수에 대한 필드가 표시되지만 이 경우에는 아무 필드도 표시되지 않습니다. 페이지에는 헤더에 대한 필드도 표시됩니다. 헤더 중 하나는 이 API와 연결된 제품의 구독 키에 대한 "Ocp-Apim-Subscription-Key"입니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 키가 자동으로 채워집니다.
3. **시도**를 누릅니다.
4. **보내기**를 누릅니다.
    
    작업이 호출되면 개발자 포털에 응답이 표시됩니다.  

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 첫 번째 API 가져오기
> * Azure Portal에서 API 테스트
> * 개발자 포털에서 API 테스트

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [제품 생성 및 게시](api-management-howto-add-products.md)
