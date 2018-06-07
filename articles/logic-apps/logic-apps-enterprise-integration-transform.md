---
title: 변환을 사용하여 XML 데이터 변환 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 SDK를 사용하여 Logic Apps에서 XML 데이터를 다른 형식으로 변환하기 위한 변환 또는 맵 만들기
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: add01429-21bc-4bab-8b23-bc76ba7d0bde
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: fd59b6b3f51adb538e774bc5bb089880ca22e97e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="enterprise-integration-with-xml-transforms"></a>XML 변환과 엔터프라이즈 통합
## <a name="overview"></a>개요
엔터프라이즈 통합 변환 커넥터에서는 데이터의 형식을 변환합니다. 예를 들어 YearMonthDay 형식의 현재 날짜가 포함된 들어오는 메시지가 있을 수 있습니다. 날짜를 MonthDayYear 형식으로 변경하는 데 사용할 수 있습니다.

## <a name="what-does-a-transform-do"></a>변환의 기능은 무엇인가요?
맵으로 알려진 변환은 원본 XML 스키마(입력)와 대상 XML 스키마(출력)로 구성됩니다. 다양한 기본 제공 함수를 사용하여 문자열 조작, 조건부 할당, 산술 식, 날짜 시간 포맷터, 루핑 구문 등을 비롯한 데이터를 조작하거나 제어할 수 있습니다.

## <a name="how-to-create-a-transform"></a>변환 생성 방법
Visual Studio [엔터프라이즈 통합 SDK](https://aka.ms/vsmapsandschemas)를 사용하여 변환/맵을 만들 수 있습니다. 변환을 만들고 테스트하는 작업을 완료한 경우 통합 계정으로 업로드합니다. 

## <a name="how-to-use-a-transform"></a>변환 사용 방법
변환/맵을 통합 계정에 업로드한 후에 논리 앱을 만드는 데 사용할 수 있습니다. 논리 앱은 논리 앱이 트리거될 때마다 (그리고 변환해야 하는 입력 콘텐츠가 있는 경우) 변환을 실행합니다.

**변환을 사용하는 단계는 다음과 같습니다**.

### <a name="prerequisites"></a>필수 조건

* 통합 계정을 만든 후 맵 추가  

지금까지 필수 구성 요소를 살펴보았습니다. 이제 논리 앱을 만들 차례입니다.  

1. 논리 앱을 만들고 맵을 포함하는 [통합 계정에 연결](../logic-apps/logic-apps-enterprise-integration-accounts.md "논리 앱에 통합 계정을 연결하는 방법 알아보기")합니다.
2. 논리 앱에 **요청** 트리거 추가  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-1.png)    
3. 먼저 **작업 추가** 를 선택하여 **변환 XML** 작업을 추가합니다.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-2.png)   
4. 사용하려는 작업에 대해 모든 작업을 필터링하기 위해 검색 상자에 *변환*이라는 단어를 입력합니다.  
   ![](./media/logic-apps-enterprise-integration-transforms/transform-3.png)  
5. **변환 XML** 작업을 선택합니다.   
6. 변환할 XML **콘텐츠** 를 추가합니다. HTTP 요청에서 수신한 XML 데이터를 **콘텐츠**로 사용할 수 있습니다. 이 예제에서는 논리 앱을 트리거한 HTTP 요청의 본문을 선택합니다.

   > [!NOTE]
   > **XML 변환**의 콘텐츠가 XML인지 확인합니다. 콘텐츠가 xml이 아니거나 base64로 인코딩된 경우 콘텐츠를 처리하는 식을 지정해야 합니다. 예를 들어 콘텐츠를 디코딩하기 위한 ```@base64ToBinary``` 또는 콘텐츠를 XML로 처리하기 위한 ```@xml```과 같은 [함수](logic-apps-workflow-definition-language.md#functions)를 사용할 수 있습니다.
 

7. 변환을 수행하는 데 사용하려는 **맵** 의 이름을 선택합니다. 맵이 이미 통합 계정에 있어야 합니다. 이전 단계에서 맵을 포함한 통합 계정에 논리 앱 액세스 권한을 이미 제공했습니다.      
   ![](./media/logic-apps-enterprise-integration-transforms/transform-4.png) 
8. 작업을 저장합니다.  
    ![](./media/logic-apps-enterprise-integration-transforms/transform-5.png) 

이 시점에서 맵의 설정이 완료됩니다. 실제 응용 프로그램에서는 SalesForce와 같은 LOB 응용 프로그램에 변환한 데이터를 저장할 수 있습니다. Salesforce에 변환의 출력을 보내는 작업을 쉽게 수행할 수 있습니다. 

이제 HTTP 끝점에 요청하여 변환을 테스트할 수 있습니다.  


## <a name="features-and-use-cases"></a>기능 및 사용 사례
* 맵에서 만든 변환은 이름과 주소를 한 문서에서 다른 문서로 복사하는 것과 같이 단순할 수 있습니다. 또는 기본 맵 작업을 사용하여 더 복잡한 변환을 만들 수 있습니다.  
* 문자열, 날짜 시간 함수 등 여러 맵 작업이나 함수를 바로 사용할 수 있습니다.  
* 스키마 간에 직접 데이터 복사를 수행할 수 있습니다. SDK에 포함된 매퍼에서 원본 스키마의 요소를 대상 스키마의 대응 항목과 연결하는 선을 그리기만 하면 됩니다.  
* 맵을 만들 때 만들어진 모든 관계와 링크를 보여 주는 맵의 그래픽 표현이 표시됩니다.
* 맵 테스트 기능을 사용하여 샘플 XML 메시지를 추가합니다. 한 번만 클릭하면 만든 맵을 테스트하고 생성된 출력을 확인할 수 있습니다.  
* 기존 데이터 업로드  
* XML 형식 지원을 포함합니다.

## <a name="advanced-features"></a>고급 기능

### <a name="reference-assembly-or-custom-code-from-maps"></a>맵의 사용자 지정 코드 또는 참조 어셈블리 
변환 작업은 외부 어셈블리를 참조하여 맵 또는 변환을 지원합니다. 이 기능을 사용하면 XSLT 맵에서 사용자 지정 .NET 코드를 직접 호출할 수 있습니다. 다음은 맵에서 어셈블리를 사용하기 위한 필수 구성 요소입니다.

* 맵에서 참조하는 맵과 어셈블리를 [통합 계정에 업로드](./logic-apps-enterprise-integration-maps.md)해야 합니다. 

  > [!NOTE]
  > 맵과 어셈블리는 특정 순서로 업로드해야 합니다. 어셈블리를 참조하는 맵을 업로드하기 전에 어셈블리를 업로드해야 합니다.

* 맵에는 다음 속성 및 어셈블리 코드에 대한 호출을 포함하는 CDATA 섹션도 있어야 합니다.

    * **name**은 사용자 지정 어셈블리 이름입니다.
    * **namespace**는 사용자 지정 코드가 포함된 어셈블리의 네임스페이스입니다.

  다음 예제에서는 "XslUtilitiesLib"라는 어셈블리를 참조하고 어셈블리에서 `circumreference` 메서드를 호출하는 맵을 보여줍니다.

  ````xml
  <?xml version="1.0" encoding="UTF-8"?>
  <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:msxsl="urn:schemas-microsoft-com:xslt" xmlns:user="urn:my-scripts">
  <msxsl:script language="C#" implements-prefix="user">
    <msxsl:assembly name="XsltHelperLib"/>
    <msxsl:using namespace="XsltHelpers"/>
    <![CDATA[public double circumference(int radius){ XsltHelper helper = new XsltHelper(); return helper.circumference(radius); }]]>
  </msxsl:script>
  <xsl:template match="data">
     <circles>
        <xsl:for-each select="circle">
            <circle>
                <xsl:copy-of select="node()"/>
                    <circumference>
                        <xsl:value-of select="user:circumference(radius)"/>
                    </circumference>
            </circle>
        </xsl:for-each>
     </circles>
    </xsl:template>
    </xsl:stylesheet>
  ````


### <a name="byte-order-mark"></a>바이트 순서 표시
기본적으로 변환의 응답은 BOM(바이트 순서 표시)으로 시작됩니다. 이 기능은 코드 보기 편집기에서 작업하는 동안에만 액세스 할 수 있습니다. 이 기능을 사용하지 않도록 설정하려면 `transformOptions` 속성에 대해 `disableByteOrderMark`를 지정합니다.

````json
"Transform_XML": {
    "inputs": {
        "content": "@{triggerBody()}",
        "integrationAccount": {
            "map": {
                "name": "TestMap"
            }
        },
        "transformOptions": "disableByteOrderMark"
    },
    "runAfter": {},
    "type": "Xslt"
}
````





## <a name="learn-more"></a>자세한 정보
* [엔터프라이즈 통합 팩에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-overview.md "엔터프라이즈 통합 팩에 대해 알아보기")  
* [맵에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-maps.md "엔터프라이즈 통합 맵에 대해 알아보기")  

