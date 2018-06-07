---
title: OCR 인식 검색 기술(Azure Search) | Microsoft Docs
description: Azure Search 보강 파이프라인의 이미지 파일에서 텍스트를 추출합니다.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 48253b68a329d17f213369e8e4ee2e06bdf17992
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365827"
---
# <a name="ocr-cognitive-skill"></a>OCR 인식 기술

**OCR** 기술은 이미지 파일에서 텍스트를 추출합니다. 지원 파일 형식은 다음과 같습니다.

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF


## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| detectOrientation | 이미지 방향의 자동 검색을 사용합니다. <br/> 유효한 값: true / false.|
|defaultLanguageCode |  입력 텍스트의 언어 코드입니다. 지원되는 언어는 `ar, cs, da, de, en, es, fi, fr, he, hu, it, ko, pt-br, pt`을 포함합니다.  언어 코드가 지정되지 않았거나 null 이면 언어는 자동 검색됩니다.|
| textExtractionAlgorithm | "인쇄" 또는 "필기"입니다. "필기" 텍스트 인식 OCR 알고리즘은 현재 미리 보기 및 영어가 지원됩니다. |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                                          |
|---------------|------------------------------------------------------|
| 이미지         | 복합 형식입니다. ```imageAction```이 ```generateNormalizedImages```로 설정된 경우 현재 Azure Blob 인덱서에서 생성한 "/document/normalized_images" 필드만 사용합니다. 자세한 내용은 [샘플](#sample-output)을 참조하세요.|


## <a name="skill-outputs"></a>기술 출력
| 출력 이름     | 설명                   |
|---------------|-------------------------------|
| text          | 이미지에서 추출된 일반 텍스트입니다.   |
| layoutText    | 추출된 텍스트와 함께 텍스트를 찾을 수 있는 위치를 설명하는 복합 유형입니다.|


## <a name="sample-definition"></a>샘플 정의

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image."
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>샘플 텍스트와 layoutText 출력

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>샘플: 문서의 콘텐츠와 포함된 이미지에서 추출된 텍스트를 병합합니다.

텍스트 병합기에 대한 일반적인 사용 사례는 이미지의 텍스트 표현(이미지의 캡션 또는 OCR 기술에서의 텍스트)을 문서의 콘텐츠 필드에 병합하는 기능입니다. 

다음 기술 집합 예제는 *merged_text* 필드를 만들어 문서의 텍스트 콘텐츠 뿐만 아니라 해당 문서에 포함된 각 이미지의 OCR된 텍스트를 포함합니다. 

#### <a name="request-body-syntax"></a>요청 본문 구문
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
위의 기술 집합 예제는 정규화 이미지 필드가 있다고 가정합니다. 이 필드를 생성하려면 인덱서 정의에서 *imageAction* 구성을 아래와 같이 *generateNormalizedImages*로 설정합니다.

```json
{  
   //...rest of your indexer definition goes here ... 
  "parameters":{  
      "configuration":{  
         "dataToExtract":"contentAndMetadata",
         "imageAction":"generateNormalizedImages"
      }
   }
}
```

## <a name="see-also"></a>참고 항목
+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [TextMerger 기술](cognitive-search-skill-textmerger.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱서 만들기(REST)](ref-create-indexer.md)