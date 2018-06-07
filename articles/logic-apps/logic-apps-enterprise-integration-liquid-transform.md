---
title: "Liquid 변환을 사용하여 JSON 데이터 변환 - Azure Logic Apps | Microsoft Docs"
description: "Logic Apps 및 Liquid 템플릿을 사용하여 고급 JSON 변환에 대한 변환 또는 맵을 만듭니다."
services: logic-apps
documentationcenter: 
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 4bb003afd757faac675a9af8599a781247717a64
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="perform-advanced-json-transformations-with-a-liquid-template"></a>Liquid 템플릿을 사용하여 고급 JSON 변환 수행

Azure Logic Apps는 **작성** 또는 **구문 분석 JSON**과 같은 네이티브 데이터 조작 작업을 통한 기본 JSON 변환을 지원합니다. 고급 JSON 변환의 경우 논리 앱에서 Liquid 템플릿을 사용할 수 있습니다. 
[Liquid](https://shopify.github.io/liquid/)는 유연한 웹앱의 오픈 소스 템플릿 언어입니다.
 
이 문서에서는 반복, 제어 흐름, 변수 등 더 복잡한 JSON 변환을 지원하는 Liquid 맵 또는 템플릿을 사용하는 방법을 알아봅니다. 논리 앱에서 Liquid 변환을 수행하려면, 먼저 Liquid 맵을 사용하여 JSON 간의 매핑을 정의하고 해당 맵을 통합 계정에 저장해야 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 계정을 사용하여 시작](https://azure.microsoft.com/free/)할 수 있습니다. 그렇지 않으면 [종량제 구독에 등록](https://azure.microsoft.com/pricing/purchase-options/)할 수 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 기본 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)


## <a name="create-a-liquid-template-or-map-for-your-integration-account"></a>통합 계정에 대한 Liquid 템플릿 또는 맵 만들기

1. 이 예제에서 샘플 Liquid 템플릿을 만듭니다. Liquid 템플릿은 여기에 설명된 대로 JSON 입력을 변환하는 방법을 정의합니다.

   ``` json
   {%- assign deviceList = content.devices | Split: ', ' -%}
      {
        "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
        "firstNameUpperCase": "{{content.firstName | Upcase}}",
        "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
        "devices" : [
        {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
      }
   ```
   > [!NOTE]
   > Liquid 템플릿이 [필터](https://shopify.github.io/liquid/basics/introduction/#filters)를 사용하는 경우 해당 필터는 대문자로 시작해야 합니다. 

2. [Azure Portal](https://portal.azure.com)에 로그인합니다.

3. 기본 Azure 메뉴에서 **모든 리소스**를 선택합니다. 

4. 검색 상자에서 통합 계정을 찾고 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

5.  통합 계정 타일에서 **맵**을 선택합니다.

   ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

6. **추가**를 선택하고 맵에 이러한 세부 정보를 제공합니다.

   * **이름**: 맵의 이름이며, 이 예제에서는 "JsontoJsonTemplate"입니다.
   * **맵 형식**: 맵의 형식입니다. JSON부터 JSON 변환의 경우 **Liquid**를 선택해야 합니다.
   * **맵**: 변환에 사용할 기존 Liquid 템플릿이나 맵 파일이며 이 예제에서는 "SimpleJsonToJsonTemplate.liquid"입니다. 이 파일을 찾으려면 파일 선택기를 사용할 수 있습니다.

   ![Liquid 템플릿 추가](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>JSON 변환에 대한 Liquid 작업 추가

1. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)

2. 논리 앱에 [요청 트리거](../connectors/connectors-native-reqres.md#use-the-http-request-trigger)를 추가합니다.

3. **+ 다음 단계 > 작업 추가**를 선택합니다. 검색 창에서 *liquid*를 입력하고 **Liquid - JSON을 JSON으로 변환**을 선택합니다.

   ![Liquid 작업 찾기 및 선택](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. **콘텐츠** 상자에서 나타나는 동적 콘텐츠 목록 또는 매개 변수 목록의 **본문**을 선택합니다.
  
   ![본문 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. **맵** 목록에서 Liquid 템플릿을 선택합니다. 이 예제에서는 "JsonToJsonTemplate"입니다.

   ![맵 선택](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   목록이 비어 있으면 논리 앱이 통합 계정에 연결되어 있지 않을 수 있습니다. 
   Liquid 템플릿 또는 지도를 포함한 통합 계정에 논리 앱을 연결하려면 다음 단계를 따릅니다.

   1. 논리 앱 메뉴에서 **워크플로 설정**을 선택합니다.
   2. **통합 계정 선택** 목록에서 통합 계정을 선택하고 **저장**을 선택합니다.

   ![통합 계정에 논리 앱 연결](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>논리 앱 테스트

[Postman](https://www.getpostman.com/postman) 또는 유사한 도구의 논리 앱에 JSON 입력을 게시합니다. 논리 앱에서 변환된 JSON 출력은 이 예제와 같습니다.
  
![예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>더 많은 Liquid 작업 예제
Liquid는 JSON 변환으로만 제한되지 않습니다. 다음은 Liquid를 사용하는 다른 사용 가능한 변환 작업입니다.

* JSON을 텍스트로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   다음은 샘플 입력 및 출력입니다.
  
   ![JSON에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* XML에서 JSON으로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   다음은 샘플 입력 및 출력입니다.

   ![XML에서 JSON으로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* XML에서 텍스트로 변환
  
  이 예제에 사용되는 Liquid 템플릿은 다음과 같습니다.

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   다음은 샘플 입력 및 출력입니다.

   ![XML에서 텍스트로 예제 출력](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  
* [맵에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-maps.md "엔터프라이즈 통합 맵에 대해 알아보기")  

