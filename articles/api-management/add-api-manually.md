---
title: Azure Portal을 사용하여 수동으로 API 추가 | Microsoft Docs
description: 이 자습서에서는 APIM(API Management)을 사용하여 API를 수동으로 추가하는 방법을 보여 줍니다.
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
ms.openlocfilehash: ef7cfa0f30eaaa426c312b21ce0a73aa4409d2ec
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="add-an-api-manually"></a>API를 수동으로 추가 

이 문서의 단계에서는 Azure Portal을 사용하여 APIM(API Management) 인스턴스에 수동으로 API를 추가하는 방법을 보여 줍니다. 빈 API를 만들고 수동으로 정의하려는 일반적인 시나리오는 모의 API를 만들려는 경우입니다. 모의 API 만들기에 대한 자세한 내용은 [모의 API 응답 만들기](mock-api-responses.md)를 참조하세요.

기존 API를 가져오려는 경우 [관련 항목](#related-topics) 섹션을 참조하세요.

이 문서에서는 빈 API를 만들고 [httpbin.org](http://httpbin.org)(공용 테스트 서비스)를 백 엔드 API로 지정합니다.

## <a name="prerequisites"></a>필수 조건

다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>API 만들기

1. **API Management**에서 **API**를 선택합니다.
2. 왼쪽 메뉴에서 **+API 추가**를 선택합니다.
3. 목록에서 **빈 API**를 선택합니다.

    ![빈 API](media/add-api-manually/blank-api.png)
4. API에 대한 설정을 입력합니다.

    ![설정](media/add-api-manually/settings.png)

    |**Name**|**값**|**설명**|
    |---|---|---|
    |**표시 이름**|*빈 API*" |이 이름은 개발자 포털에 표시됩니다.|
    |**웹 서비스 URL**(선택 사항)| "*http://httpbin.org*"| 모의 API를 만들려는 경우에는 아무 것도 입력할 필요가 없습니다. <br/>이 예에서는 [http://httpbin.org](http://httpbin.org)를 입력합니다. 이것은 공용 테스트 서비스입니다. <br/>자동으로 백 엔드에 매핑되는 API를 가져오려는 경우 [관련 항목](#related-topics) 섹션의 항목 중 하나를 참조하세요.|
    |**URL 구성표**|"*HTTPS*"|이 경우 백 엔드에 비보안 HTTP 액세스가 있더라도 백 엔드에 대해 보안 HTTPS APIM 액세스를 지정합니다. <br/>이러한 종류의 시나리오(HTTPS-HTTP)를 HTTPS 종단이라고 합니다. 가상 네트워크 내에 API가 있는 경우(HTTPS가 사용되지 않더라도 액세스의 보안이 유지된다는 것을 아는 경우) 이 작업을 수행할 수 있습니다. <br/>일부 CPU 주기를 줄이기 위해 "HTTPS 종단"을 사용하려고 할 수도 있습니다.|
    |**URL 접미사**|"*hbin*"| 접미사는 이 APIM 인스턴스에서 이 특정 API를 식별하는 이름입니다. 이 APIM 인스턴스 내에서 고유해야 합니다.|
    |**제품**|"*Unlimited*" |API를 제품에 연결하여 API를 게시합니다. API를 게시하고 개발자가 사용할 수 있게 하려면 제품에 추가합니다. API를 만드는 동안 이 작업을 수행할 수도 있고 나중에 설정할 수도 있습니다.<br/><br/>제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. <br/>개발자는 API에 액세스하려면 먼저 제품을 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.<br/><br/> 기본적으로 각 API Management 인스턴스는 두 개의 샘플 제품인 **Starter** 및 **Unlimited**와 함께 제공됩니다.| 
5. **만들기**를 선택합니다.

이때 백 엔드 API의 작업에 매핑되는 APIM의 작업은 없습니다. APIM을 통해서가 아니라 백 엔드를 통해 노출되는 작업을 호출하는 경우 **404**가 표시됩니다. 

>[!NOTE] 
> 기본적으로 추가한 API가 일부 백 엔드 서비스에 연결되더라도 허용 목록에 추가할 때까지 APIM는 어떤 작업도 공개하지 않습니다. 백 엔드 서비스의 작업을 허용 목록에 추가하려면 백 엔드 작업에 매핑되는 APIM 작업을 만듭니다.
>

## <a name="add-and-test-an-operation"></a>작업 추가 및 테스트

이 섹션에서는 백 엔드 "http://httpbin.org/get" 작업에 매핑하기 위해 "/get" 작업을 추가하는 방법을 보여줍니다.

### <a name="add-the-operation"></a>작업 추가

1. 이전 단계에서 만든 API를 선택합니다.
2. **+ 작업 추가**를 클릭합니다.
3. **URL**에서 **GET**을 선택하고 리소스에 "*/get*"을 입력합니다.
4. **표시 이름**으로 "*FetchData*"를 입력합니다.
5. **저장**을 선택합니다.

### <a name="test-the-operation"></a>작업 테스트

Azure Portal에서 작업을 테스트합니다. 또는 **개발자 포털**에서 테스트할 수도 있습니다.

1. **테스트** 탭을 선택합니다.
2. **FetchData**를 선택합니다.
3. **보내기**를 누릅니다.

"http://httpbin.org/get" 작업이 생성하는 응답이 표시됩니다. 작업을 변환하려면 [API 변환 및 보호](transform-api.md)를 참조하세요.

## <a name="add-and-test-a-parameterized-operation"></a>매개 변수가 있는 작업 추가 및 테스트

이 섹션에서는 매개 변수를 사용하는 작업을 추가하는 방법을 보여 줍니다. 이 예에서는 작업을 "http://httpbin.org/status/200"에 매핑합니다.

### <a name="add-the-operation"></a>작업 추가

1. 이전 단계에서 만든 API를 선택합니다.
2. **+ 작업 추가**를 클릭합니다.
3. **URL**에서 **GET**을 선택하고 리소스에 "*/status/{code}*"를 입력합니다. 필요에 따라 이 매개 변수와 관련된 몇 가지 정보를 제공할 수 있습니다. 예를 들어 **형식**에 대해 "*Number*"를 입력하고 **값**에 대해 “*200*”(기본값)을 입력합니다.
4. **표시 이름**으로 "GetStatus"를 입력합니다.
5. **저장**을 선택합니다.

### <a name="test-the-operation"></a>작업 테스트 

Azure Portal에서 작업을 테스트합니다.  또는 **개발자 포털**에서 테스트할 수도 있습니다.

1. **테스트** 탭을 선택합니다.
2. **GetStatus**를 선택합니다. 기본적으로 코드 값은 "*200*"으로 설정됩니다. 이 값을 변경하여 다른 값을 테스트할 수 있습니다. 예를 들어 "*418*"을 입력합니다.
3. **보내기**를 누릅니다.

    "http://httpbin.org/status/200" 작업이 생성하는 응답이 표시됩니다. 작업을 변환하려면 [API 변환 및 보호](transform-api.md)를 참조하세요.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [게시된 API 변환 및 보호](transform-api.md)
