---
title: "Azure API Management에서 캐싱을 추가하여 성능 향상 | Microsoft Docs"
description: "대기 시간, 대역폭 사용 및 API Management 서비스 호출에 대한 웹 서비스 부하를 개선하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 7458ad6e0a864d742f74ce743ce3179594113c00
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2017
---
# <a name="add-caching-to-improve-performance-in-azure-api-management"></a>Azure API Management에서 캐싱을 추가하여 성능 향상
응답 캐싱을 위해 API Management의 작업을 구성할 수 있습니다. 응답 캐싱은 그다지 사용되지 않는 데이터에 대한 API 대기 시간, 대역폭 사용량 및 웹 서비스 부하를 상당히 줄일 수 있습니다.
 
캐싱에 대한 자세한 내용은 [API Management 캐싱 정책](api-management-caching-policies.md)과 [Azure API Management의 사용자 지정 캐싱](api-management-sample-cache-by-key.md)을 참조하세요.

![캐시 정책](media/api-management-howto-cache/cache-policies.png)

학습할 내용:

> [!div class="checklist"]
> * API에 대한 응답 캐싱 추가
> * 실행 중인 캐싱 확인

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

+ [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ [API 제품 가져오기 및 게시](import-and-publish.md)

## <a name="caching-policies"> </a>캐싱 정책 추가

이 예제에 나와 있는 캐싱 정책을 사용하면 **GetSpeakers** 작업에 대한 첫 번째 요청이 백 엔드 서비스의 응답을 반환합니다. 이 응답은 지정된 헤더 및 쿼리 문자열 매개 변수를 통해 캐시 및 입력됩니다. 일치하는 매개 변수를 사용하는, 작업에 대한 후속 호출은 캐시 기간 간격이 만료될 때까지 캐시된 응답을 반환합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. APIM 인스턴스로 이동합니다.
3. **API** 탭을 선택합니다.
4. API 목록에서 **Demo Conference API**를 선택합니다.
5. **GetSpeakers**를 선택합니다.
6. 화면 맨 위에서 **디자인** 탭을 선택합니다.
7. **인바운드 처리** 창에서 삼각형(연필 옆에 있는)을 클릭합니다.

    ![코드 편집기](media/api-management-howto-cache/code-editor.png)
8. **코드 편집기**를 선택합니다.
9. **inbound** 요소에 다음 정책을 추가합니다.

        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
            <vary-by-header>Accept</vary-by-header>
            <vary-by-header>Accept-Charset</vary-by-header>
            <vary-by-header>Authorization</vary-by-header>
        </cache-lookup>

10. **outbound** 요소에 다음 정책을 추가합니다.

        <cache-store caching-mode="cache-on" duration="20" />

    **기간** 은 캐싱된 응답의 만료 간격을 지정합니다. 이 예제에서는 간격이 **20**초입니다.

## <a name="test-operation"> </a>작업 호출 및 캐싱 테스트
실행 중인 캐싱을 확인하려면 개발자 포털에서 작업을 호출합니다.

1. Azure Portal에서 APIM 인스턴스로 이동합니다.
2. **API** 탭을 선택합니다.
3. 캐싱 정책을 추가할 API를 선택합니다.
4. **GetSpeakers** 작업을 선택합니다.
5. 맨 위 오른쪽 메뉴에 **테스트** 탭을 클릭합니다.
6. **보내기**를 누릅니다.

## <a name="next-steps"> </a>다음 단계
* 캐싱 정책에 대한 자세한 내용은 [API Management 정책 참조][API Management policy reference]의 [캐싱 정책][Caching policies]을 참조하세요.
* 정책 식을 사용하여 키별 캐싱 항목에 대한 자세한 내용은 [Azure API Management에서 사용자 지정 캐싱](api-management-sample-cache-by-key.md)을 참조하세요.

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Create an API Management service instance]: get-started-create-service-instance.md

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps
