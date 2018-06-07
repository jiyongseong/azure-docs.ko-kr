---
title: 텍스트 분할 인식 검색 기술(Azure Search) | Microsoft Docs
description: Azure Search 보강 파이프라인의 길이에 따라 텍스트를 텍스트의 페이지나 청크로 나눕니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: dbb9261cfce0a8437cfe76121fa16aa87c4b3393
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786802"
---
#   <a name="text-split-cognitive-skill"></a>텍스트 분할 인식 기술

**분할 텍스트** 기술은 텍스트를 텍스트의 청크로 나눕니다. 텍스트를 문장으로 또는 특정 길이의 페이지로 나눌 것인지 여부를 지정할 수 있습니다. 이 기술은 다른 기술 다운스트림에서 최대 텍스트 길이 요구 사항이 있는 경우 특히 유용합니다. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| textSplitMode      | "페이지" 또는 "문장" 중 하나 | 
| maximumPageLength | TextSplitMode가 "페이지"로 설정되는 경우 이는 `String.Length`에 의해 측정된 대로 최대 페이지 길이를 참조합니다. 최소값은 100입니다. | 
| defaultLanguageCode   | (선택 사항) 다음 언어 코드 `da, de, en, es, fi, fr, it, ko, pt` 중 하나입니다. 기본값은 영어(en)입니다. 고려할 사항은 다음과 같습니다.<ul><li>languagecode-countrycode 형식을 전달하는 경우 형식의 languagecode 부분만 사용됩니다.</li><li>언어가 이전 목록에 없는 경우 분할 기술은 문자를 경계로 텍스트를 나눕니다.</li><li>언어 코드를 제공하면 중국어, 일본어, 한국어 등 비공간 언어에 대해 단어를 반으로 줄이지 못하게 하는 데 유용합니다.</li></ul>  |


## <a name="skill-inputs"></a>기술 입력

| 매개 변수 이름       | 설명      |
|----------------------|------------------|
| text  | 하위 문자열로 분할될 텍스트입니다. |
| languageCode  | (선택 사항) 문서에 대한 언어 코드입니다.  |

## <a name="skill-outputs"></a>기술 출력 

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| textItems | 추출된 하위 문자열의 배열입니다. |


##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>샘플 출력

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>오류 사례
언어가 지원되지 않는 경우 경고가 생성되고 텍스트가 문자를 경계로 분할됩니다.

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
