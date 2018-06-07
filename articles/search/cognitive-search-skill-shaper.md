---
title: 쉐이퍼 인식 검색 기술(Azure Search) | Microsoft Docs
description: 구조화되지 않은 데이터에서 메타데이터 및 구조화된 정보를 추출하고 Azure Search 보강 파이프라인에서 복합 형식으로 구성합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 311f4bd67081de567763783a9d86540eda36d9f8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786772"
---
#   <a name="shaper-cognitive-skill"></a>쉐이퍼 인식 기술

**쉐이퍼** 기술은 복합 형식을 만들어 복합 필드(다중 파트 필드라고도 함)를 지원합니다. 복합 형식 필드에는 여러 파트가 있지만 Azure Search 인덱스에서 단일 항목으로 처리됩니다. 검색 시나리오에서 유용한 통합 필드의 예로 이름과 성을 하나의 필드로 결합하거나, 구/군/시와 시/도를 하나의 필드로 결합하거나, 이름과 생년월일을 하나의 필드로 결합하여 고유한 ID를 설정하는 것 등이 있습니다.

쉐이퍼 기술을 사용하면 기본적으로 구조를 만들고, 해당 구조의 멤버 이름을 정의하고, 각 멤버에게 값을 할당할 수 있습니다.

기본적으로 이 기술은 한 수준 깊이인 개체를 지원합니다. 더 복잡한 개체의 경우 여러 쉐이퍼 단계를 연결할 수 있습니다.

응답에서 출력 이름은 항상 “output”입니다. 내부적으로, 파이프라인은 아래 예제의 “analyzedText”와 같은 다른 이름을 “output”에 매핑할 수 있지만 쉐이퍼 기술 자체는 응답에 “output”을 반환합니다. 이는 보강된 문서를 디버그하고 이름 불일치를 확인하거나 사용자 지정 기술을 작성하고 응답을 직접 구조화하는 경우에 중요합니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>샘플 1: 복합 형식

각각 두 개의 멤버(*text* 및 *sentiment*)가 있는 *analyzedText*라는 구조를 만드는 시나리오를 가정해 보세요. Azure Search에서는 검색 가능한 다중 파트 필드를 복합 형식이라고 하며, 아직 기본적으로 지원되지 않습니다. 이 미리 보기에서는 쉐이퍼 기술을 사용하여 인덱스에 복합 형식의 필드를 생성할 수 있습니다. 

다음 예제에서는 멤버 이름을 입력으로 제공합니다. 출력 구조(Azure Search의 복합 필드)는 *targetName*을 통해 지정됩니다. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": analyzedText"
    }
  ]
}
```

### <a name="sample-input"></a>샘플 입력
이 쉐이퍼 기술에 사용 가능한 입력을 제공하는 JSON 문서는 다음과 같을 수 있습니다.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="sample-output"></a>샘플 출력
쉐이퍼 기술은 텍스트 및 *text* 및 *sentiment*의 결합된 요소를 사용하여 *analyzedText*라는 새 요소를 생성합니다. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="sample-2-input-consolidation"></a>샘플 2: 입력 통합

다른 예제에서는 파이프라인 처리의 각기 다른 단계에서 책 제목과 책의 여러 페이지에 있는 장 제목을 추출했다고 가정합니다. 이제 다양한 입력으로 구성된 단일 구조를 만들 수 있습니다.

이 시나리오에 대한 쉐이퍼 기술 정의는 다음 예제와 유사할 수 있습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*"
        }
    ],
    "outputs": [
        {
            "output": "titlesAndChapters",
            "targetName": "analyzedText"
        }
    ]
}
```

### <a name="sample-output"></a>샘플 출력
이 경우, 쉐이퍼는 모든 장 제목을 단순화하여 단일 배열을 만듭니다. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)

