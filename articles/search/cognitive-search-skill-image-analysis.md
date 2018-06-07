---
title: 이미지 분석 인식 검색 기술(Azure Search) | Microsoft Docs
description: Azure Search 보강 파이프라인의 이미지 분석 인식 기술을 사용하여 이미지 분석을 통해 의미 체계 텍스트를 추출합니다.
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
ms.openlocfilehash: 271f3231078b8842b040509ccf0406ed2415e5ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363947"
---
#   <a name="image-analysis-cognitive-skill"></a>이미지 분석 인식 기술

**이미지 분석** 기술은 이미지 콘텐츠를 기준으로 다양한 시각적 기능 집합을 추출합니다. 예를 들어, 이미지에서 캡션을 생성하거나, 태그를 생성하거나, 유명인과 랜드마크를 식별할 수 있습니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| defaultLanguageCode   |  반환할 언어를 나타내는 문자열입니다. 서비스는 지정된 언어로 인식 결과를 반환합니다. 이 매개 변수를 지정하지 않을 경우 기본값은 “en”입니다. <br/><br/>지원되는 언어는 다음과 같습니다. <br/>*en* - 영어(기본값) <br/> *zh* - 중국어 간체|
|visualFeatures |   반환할 시각적 기능 유형을 나타내는 문자열 배열입니다. 유효한 시각적 기능 유형은 다음과 같습니다.  <ul><li> ‘범주’ - Cognitive Services [설명서](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy)에 정의된 분류에 따라 이미지 콘텐츠를 분류합니다.</li><li> ‘태그’ - 이미지 콘텐츠와 관련된 단어의 자세한 목록으로 이미지에 태그를 지정합니다.</li><li>‘설명’ - 완전한 문장으로 이미지 콘텐츠를 설명합니다.</li><li>‘얼굴’ - 얼굴이 있는지 여부를 검색합니다. 얼굴이 있으면 좌표, 성별 및 나이를 생성합니다.</li><li> ‘이미지 유형’ - 이미지가 클립 아트인지 또는 선 그리기인지 검색합니다.</li><li>   ‘색’ - 강조 색, 기준 색, 이미지가 흑백인지 여부를 결정합니다.</li><li>‘성인’ - 이미지가 본질적으로 음란물(노출 또는 성적 행위 묘사)인지 여부를 검색합니다. 성적으로 노골적인 콘텐츠도 검색됩니다.</li></ul> 시각적 기능의 이름은 대/소문자를 구분합니다.|
| 세부 정보   | 반환할 도메인 특정 세부 정보를 나타내는 문자열 배열입니다. 유효한 시각적 기능 유형은 다음과 같습니다. <ul><li>‘유명인’ - 이미지에서 검색된 경우 유명인을 식별합니다.</li><li>‘랜드마크’ - 이미지에서 검색된 경우 랜드마크를 식별합니다.</li></ul>
 |

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                                          |
|---------------|------------------------------------------------------|
| 이미지         | 복합 형식입니다. ```imageAction```이 ```generateNormalizedImages```로 설정된 경우 현재 Azure Blob 인덱서에서 생성한 "/document/normalized_images" 필드만 사용합니다. 자세한 내용은 [샘플](#sample-output)을 참조하세요.|



##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
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
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
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
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceRectangle": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceRectangle": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>오류 사례
다음 오류 사례에서는 요소가 추출되지 않습니다.

| 오류 코드 | 설명 |
|------------|-------------|
| NotSupportedLanguage | 제공한 언어가 지원되지 않습니다. |
| InvalidImageUrl | 이미지 URL의 형식이 잘못되었거나 액세스할 수 없습니다.|
| InvalidImageFormat | 입력 데이터가 유효한 이미지가 아닙니다. |
| InvalidImageSize | 입력 이미지가 너무 큽니다. |
| NotSupportedVisualFeature  | 지정한 기능 유형이 잘못되었습니다. |
| NotSupportedImage | 지원되지 않는 이미지(예: 아동 음란물)입니다. |
| InvalidDetails | 지원되지 않는 도메인 특정 모델입니다. |

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱서 만들기(REST)](ref-create-indexer.md)
