---
title: Azure API Management에서 제품을 만들고 게시하는 방법
description: Azure API Management에서 제품을 만들고 게시하는 방법에 대해 알아봅니다.
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: af1768a6555168b777e68f378d32a0b44e9b2c78
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-publish-a-product"></a>제품 만들기 및 게시  

Azure API Management에서 제품은 하나 이상의 API뿐만 아니라 사용 할당량 및 사용 약관을 포함합니다. 제품이 게시되면 개발자는 제품을 구독하고 제품의 API를 사용할 수 있습니다.  

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 제품 만들기 및 게시
> * 제품에 API 추가

![추가된 제품](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>필수 조건

+ 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
+ 또한, 다음 자습서를 완료합니다. [첫 번째 API 가져오기 및 게시](import-and-publish.md)

## <a name="create-and-publish-a-product"></a>제품 만들기 및 게시

1. 왼쪽 메뉴에서 **제품**을 클릭하여 **제품** 페이지를 표시합니다.
2. **+ 제품**을 클릭합니다.

    ![추가된 제품](media/api-management-howto-add-products/add-product.png)

    제품을 추가하는 경우 다음 정보를 제공해야 합니다. 

    |Name|설명|
    |---|---|
    |표시 이름|**개발자 포털**에 표시하려는 이름입니다.|
    |Name|제품에 대한 설명이 포함된 이름입니다.|
    |설명|**설명** 필드에서 용도, 제품을 통해 액세스할 수 있는 API, 기타 유용한 정보 등의 자세한 정보를 제공할 수 있습니다.|
    |시스템 상태|제품을 게시하려는 경우 **게시됨**을 누릅니다. 제품의 API를 호출하려면 먼저 제품을 게시해야 합니다. 기본적으로 새 제품은 게시되지 않으며 **관리자** 그룹에만 표시됩니다.|
    |승인 필요|관리자가 이 제품에 대한 구독을 검토하고 허용하거나 거부하도록 하려면 **구독 승인 필요** 를 선택합니다. 상자의 선택을 취소하면 구독 시도가 자동으로 승인됩니다. |
    |구독 수 제한|동시에 여러 개의 구독 수를 제한하려면 구독 제한을 입력합니다. |
    |약관|구독자가 제품을 사용하기 위해 허용해야 하는 제품의 사용 약관을 포함할 수 있습니다.|
    |API|제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. <br/> 제품 만들기 중에 기존 API를 추가할 수 있습니다. 제품 **설정** 페이지 또는 API를 만드는 동안 나중에 제품에 API를 추가할 수 있습니다.|<br/>개발자는 API에 액세스하는 제품을 먼저 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다.<br/> APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독한 상태가 됩니다.|

3. **만들기**를 클릭하여 새 제품을 만듭니다.

### <a name="add-more-configurations"></a>구성 더 추가

**설정** 탭을 선택하여 저장한 후 제품을 계속 구성할 수 있습니다. 

**구독** 탭에서 구독자를 보고 제품에 추가합니다.

**액세스 제어** 탭에서 개발자 또는 게스트를 위한 제품의 표시 여부를 설정합니다.

## <a name="add-apis"> </a>제품에 여러 API 추가

제품은 하나 이상의 API와 연결됩니다. 다양한 API를 포함하고 개발자 포털을 통해 개발자에게 제공할 수 있습니다. 제품 만들기 중에 기존 API를 추가할 수 있습니다. 제품 **설정** 페이지 또는 API를 만드는 동안 나중에 제품에 API를 추가할 수 있습니다.

개발자는 API에 액세스하는 제품을 먼저 구독해야 합니다. 구독할 경우 해당 제품의 모든 API에 적절한 구독 키를 받게 됩니다. APIM 인스턴스를 만든 경우 사용자는 이미 관리자이므로 기본적으로 모든 제품을 구독합니다.

### <a name="add-an-api-to-an-existing-product"></a>기존 제품에 API 추가

1. 제품을 선택합니다.
2. API 탭을 선택합니다.
3. **+API**를 클릭합니다.
4. API를 선택하고 **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 제품 만들기 및 게시
> * 제품에 API 추가

다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [빈 API 및 모의 API 응답 만들기](mock-api-responses.md)
