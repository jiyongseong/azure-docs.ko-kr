---
title: 논리 앱 정의를 위한 JSON 만들기, 편집 또는 확장 - Azure Logic Apps | Microsoft Docs
description: JSON에서 논리 앱 정의 작성 및 사용자 지정
author: ecfan
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2018
ms.author: estfan; LADocs
ms.openlocfilehash: f37f600d001b110775d8ca0e78950e3b8743df82
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="create-edit-or-customize-json-for-logic-app-definitions"></a>논리 앱 정의를 위한 JSON 만들기, 편집 또는 사용자 지정

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 자동화된 워크플로를 사용하여 엔터프라이즈 통합 솔루션을 만들 때 기본 논리 앱 정의는 해당 설명 및 유효성 검사에 대해 간단하고 선언적 JSON(JavaScript Object Notation)과 함께 [WDL(워크플로 정의 언어) 스키마](../logic-apps/logic-apps-workflow-definition-language.md)를 사용합니다. 이러한 형식 덕분에 코드에 대해 잘 몰라도 논리 앱 정의를 더 쉽게 읽고 이해할 수 있습니다. 논리 앱의 만들기 및 배포를 자동화하려는 경우 논리 앱 정의를 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-overview.md#template-deployment) 내 [Azure 리소스](../azure-resource-manager/resource-group-overview.md)로 포함할 수 있습니다. 그런 다음, [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp), [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 또는 [Azure Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/)를 사용하여 논리 앱을 생성, 관리 및 배포할 수 있습니다.

JSON에서 논리 앱 정의를 사용하려면 Azure Portal 또는 Visual Studio에서 작업하는 경우 코드 보기 편집기를 열거나 원하는 편집기에로 정의를 복사합니다. 논리 앱을 처음 사용하는 경우 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 검토하세요.

> [!NOTE]
> 논리 앱 정의에서 매개 변수 및 여러 트리거를 정의하는 것처럼 일부 Azure Logic Apps 기능은 Logic Apps 디자이너가 아닌 JSON에서만 사용할 수 있습니다. 따라서 이러한 작업의 경우 코드 보기 또는 다른 편집기에서 작업해야 합니다.

## <a name="edit-json---azure-portal"></a>JSON 편집 - Azure Portal

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에서 “논리 앱”을 찾은 다음, 결과에서 사용자의 논리 앱을 선택합니다.

3. 논리 앱 메뉴의 **개발 도구**에서 **Logic Apps 코드 보기**를 선택합니다.

   코드 보기 편집기가 열리고 논리 앱 정의가 JSON 형식으로 표시됩니다.

## <a name="edit-json---visual-studio"></a>JSON 편집 - Visual Studio

Visual Studio에서 논리 앱 정의를 작업하려면 [필요한 도구가 설치되었는지](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) 확인해야 합니다. Visual Studio를 사용하여 논리 앱을 만들려면 [빠른 시작: Azure Logic Apps - Visual Studio를 사용하여 작업 및 프로세스 자동화](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)를 검토합니다.

Visual Studio에서 생성되어 직접 Azure Portal에서 또는 Visual Studio에서 Azure Resource Manager 프로젝트로 배포된 논리 앱을 열 수 있습니다.

1. Visual Studio 솔루션 또는 논리 앱이 포함된 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 프로젝트를 엽니다.

2. 기본적으로 [Resource Manager 템플릿](../azure-resource-manager/resource-group-overview.md#template-deployment)에 표시되는 **LogicApp.json**이라 명명된 논리 앱의 정의를 찾아서 엽니다. 배포를 위해 이 템플릿을 다른 환경으로 사용 및 사용자 지정할 수 있습니다.

3. 논리 앱 정의 및 템플릿에 대한 바로 가기 메뉴를 엽니다. **Logic Apps 디자이너를 사용하여 열기**를 선택합니다.

   ![Visual Studio 솔루션에서 논리 앱 열기](./media/logic-apps-author-definitions/open-logic-app-designer.png)

4. 디자이너의 맨 아래에서 **코드 보기**를 선택합니다. 

   코드 보기 편집기가 열리고 논리 앱 정의가 JSON 형식으로 표시됩니다.

5. 디자이너 보기로 돌아가려면 코드 보기 편집기의 맨 아래에서 **디자인**을 선택합니다.

## <a name="parameters"></a>매개 변수

매개 변수를 사용하면 논리 앱 전체에서 값을 다시 사용할 수 있어서 자주 변경되는 값을 대체할 때 유용합니다. 예를 들어 여러 위치에서 사용할 이메일 주소가 있는 경우 해당 이메일 주소를 매개 변수로 정의해야 합니다. 

매개 변수는 다양한 환경에서 매개 변수를 재정의해야 하는 경우에도 유용합니다. [배포에 필요한 매개 변수](#deployment-parameters) 및 [Azure Logic Apps용 REST API 설명서](https://docs.microsoft.com/rest/api/logic)에 대해 자세히 알아보세요.

> [!NOTE]
> 매개 변수는 코드 보기에서만 사용할 수 있습니다.

[첫 번째 예제 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)에서는 웹 사이트의 RSS 피드에 새 게시물이 나타나면 이메일을 보내는 워크플로를 만들었습니다. 피드의 URL은 하드 코드되어 있기 때문에 이 예에서는 피드의 URL을 더 쉽게 변경할 수 있도록 쿼리 값을 매개 변수로 바꾸는 방법을 보여줍니다.

1. 코드 보기에서 `parameters : {}` 개체를 찾고 `currentFeedUrl` 개체를 추가합니다.

   ``` json
     "currentFeedUrl" : {
      "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
   }
   ```

2. `When_a_feed-item_is_published` 동작에서 `queries` 섹션을 찾고 쿼리 값을 `"feedUrl": "#@{parameters('currentFeedUrl')}"`로 바꿉니다. 

   **이전**
   ``` json
   }
      "queries": {
          "feedUrl": "https://s.ch9.ms/Feeds/RSS"
       }
   },   
   ```

   **이후**
   ``` json
   }
      "queries": {
          "feedUrl": "#@{parameters('currentFeedUrl')}"
       }
   },   
   ```

   두 개 이상의 문자열을 조인하기 위해 `concat` 함수를 사용할 수도 있습니다. 
   예를 들어 `"@concat('#',parameters('currentFeedUrl'))"`은 이전 예제와 동일하게 작동합니다.

3.  완료하면 **저장**을 선택합니다. 

이제 `currentFeedURL`개 개체를 통해 다른 URL을 전달하여 웹 사이트의 RSS 피드를 변경할 수 있습니다.

<a name="deployment-parameters"></a>

## <a name="deployment-parameters-for-different-environments"></a>다른 환경에 대한 배포 매개 변수

일반적으로 배포 수명 주기에는 개발, 스테이징 및 프로덕션이 포함됩니다. 예를 들어, 이러한 모든 환경에서 동일한 논리 앱 정의를 사용하지만 다른 데이터베이스를 사용할 수 있습니다. 마찬가지로 고가용성을 위해 여러 지역에서 동일한 정의를 사용하지만 각 논리 앱 인스턴스에는 해당 지역의 데이터베이스를 사용하는 것이 좋습니다. 

> [!NOTE] 
> 이 시나리오는 *런타임*에 매개 변수를 사용하는 것과 다르며 대신 `trigger()` 함수를 사용해야 합니다.

기본 정의는 다음과 같습니다.

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```
논리 앱에 대한 실제 `PUT` 요청에서 매개 변수 `uri`를 제공할 수 있습니다. 각 환경에서 `connection` 매개 변수에 대해 다른 값을 제공할 수 있습니다. 기본값이 더 이상 존재하지 않으므로 논리 앱 페이로드에 이 매개 변수가 필요합니다.

``` json
{
    "properties": {},
        "definition": {
          /// Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

자세히 알아보려면 [Azure Logic Apps용 REST API 설명서](https://docs.microsoft.com/rest/api/logic/)를 참조하세요.

## <a name="process-strings-with-functions"></a>함수로 문자열 처리

Logic Apps에는 문자열 작업을 위한 다양한 함수가 있습니다. 예를 들어 주문에 포함된 회사 이름을 다른 시스템에 전달한다고 가정하겠습니다. 하지만 문자 인코딩의 적절한 처리에 대한 확신이 없습니다. 문자열에 base64 인코딩을 수행할 수 있지만, URL의 이스케이프를 방지하기 위해 여러 문자를 바꿀 수 있습니다. 또한 앞쪽의 5자는 사용하지 않기 때문에 회사 이름의 하위 문자열만 필요합니다. 

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

아래 단계에서는 이 예제가 내부에서 외부로 작업하면서 이 문자열을 처리하는 방법을 설명합니다.

``` 
"uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. 총 문자 수를 얻기 위해 회사 이름의 [`length()`](../logic-apps/logic-apps-workflow-definition-language.md)를 구합니다.

2. 짧은 문자열을 구하려면 `5`를 뺍니다.

3. 이제 [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)을 구합니다. 인덱스 `5`에서 시작하여 문자열의 나머지로 이동합니다.

4. 이 하위 문자열을 [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) 문자열로 변환합니다.

5. 이제 모든 `+` 문자를 `-` 문자로 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)합니다.

6. 마지막으로 모든 `/` 문자를 `_` 문자로 [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md)합니다.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>목록 항목을 속성 값에 매핑한 다음, 맵을 매개 변수로 사용

속성 값을 기반으로 다른 결과를 얻으려면, 각 속성 값을 결과와 일치시키는 맵을 만든 다음, 이 맵을 매개 변수로 사용합니다. 

예를 들어,이 워크플로는 일부 범주를 매개 변수로 정의하고 이 범주를 특정 URL과 일치시키는 맵을 정의합니다. 우선, 워크플로에서 문서의 목록을 가져옵니다. 그런 다음, 워크플로에서 맵을 사용하여 각 문서의 범주와 일치하는 URL을 찾습니다.

*   [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) 함수는 해당 범주가 알려진 정의된 범주와 일치하는지 확인합니다.

*   일치하는 범주를 확인한 후에는 대괄호`parameters[...]`를 사용하여 맵에서 항목을 끌어옵니다.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Date 함수로 데이터 가져오기

*트리거*를 기본적으로 지원하지 않는 데이터 소스에서 데이터를 가져오려면, 시간과 날짜를 대신 처리하기 위해 Date 함수를 사용합니다. 예를 들어 이 식은 내부에서 외부로 작업하면서 워크플로 단계가 얼마나 소요되는지를 구합니다.

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. `order` 작업에서 `startTime`을 추출합니다. 
2. `utcNow()`로 현재 시간을 구합니다.
3. 1초를 뺍니다.

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md) 

   `minutes` 또는 `hours`와 같은 다른 시간 단위를 사용할 수 있습니다. 

3. 이제 두 값을 비교할 수 있습니다. 

   첫 번째 값이 두 번째 값보다 작은 경우에는 명령이 처음으로 배치된 후 1초 이상의 시간이 경과된 것입니다.

날짜 형식을 지정하기 위해 문자열 포맷터를 사용할 수 있습니다. 예를 들어 RFC1123을 얻으려면 [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)를 사용합니다. [날짜 형식 지정](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 자세히 알아보세요.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```


## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Azure Logic Apps에 대한 워크플로 정의 언어 스키마](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 자세히 알아보기
* [Azure Logic Apps의 워크플로 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md)에 대해 자세히 알아보기