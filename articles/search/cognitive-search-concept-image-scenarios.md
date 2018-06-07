---
title: Azure Search에서 이미지의 텍스트 처리 및 추출 | Microsoft Docs
description: Azure Search의 인식 검색 파이프라인에 있는 이미지에서 텍스트 및 기타 정보를 처리하고 추출합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 77fbd69aad6c78ecd5c933d8017c980afaa661a3
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367276"
---
#  <a name="how-to-process-and-extract-information-from-images-in-cognitive-search-scenarios"></a>인식 검색 시나리오의 이미지에서 정보를 처리하고 추출하는 방법

인식 검색에는 이미지 및 이미지 파일에서 작동하는 몇 가지 기능이 있습니다. 문서를 해독하는 동안 *imageAction* 매개 변수를 사용하여 정지 신호에서 단어 "STOP"과 같은 영숫자 텍스트를 포함하는 사진이나 그림에서 텍스트를 추출할 수 있습니다. 기타 시나리오에는 민들레 사진의 "민들레" 또는 "노란색"과 같은 이미지의 텍스트 표현을 생성하는 방법이 포함됩니다. 또한 해당 크기와 같은 이미지에 대한 메타데이터를 추출할 수 있습니다.

이 아티클에서는 이미지 처리를 자세히 설명하고 인식 검색 파이프라인에 있는 이미지로 작업하는 방법에 대한 지침을 제공합니다.

<a name="get-normalized-images"></a>

## <a name="get-normalized-images"></a>정규화된 이미지 가져오기

문서 해독의 일환으로 이미지 파일이나 파일에 포함된 이미지를 처리하기 위한 새로운 인덱서 구성 매개 변수 집합이 있습니다. 이러한 매개 변수는 추가 다운스트림 처리를 위해 이미지를 정규화하는 데 사용됩니다. 이미지를 정규화하면 보다 일관되게 만들 수 있습니다. 큰 이미지는 사용 가능하도록 최대 높이 및 너비로 크기가 조정됩니다. 방향에 메타데이터를 제공하는 이미지의 경우 세로 로드를 위해 이미지 회전이 조정됩니다. 메타데이터 조정은 각 이미지에 대해 만든 복합 형식으로 캡처됩니다. 

이미지 정규화를 해제할 수 없습니다. 이미지에서 기술을 반복하려면 정규화된 이미지를 사용하는 것이 좋습니다.

| 구성 매개 변수 | 설명 |
|--------------------|-------------|
| imageAction   | 포함된 이미지 또는 이미지 파일이 있을 때 아무 작업도 수행하지 않아야 하는 경우 "없음"으로 설정합니다. <br/>정규화된 이미지의 배열을 문서 해독의 일부로 생성하려 "generateNormalizedImages"로 설정합니다. 이러한 이미지는 *normalized_images* 필드에 노출됩니다. <br/>기본값은 "없음"입니다. 이 구성은 "dataToExtract"가 "contentAndMetadata"로 설정된 경우 Blob 데이터 원본에만 관련됩니다. |
|  normalizedImageMaxWidth | 정규화된 이미지의 최대 너비(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다.|
|  normalizedImageMaxHeight | 정규화된 이미지의 최대 높이(픽셀 단위)가 생성되었습니다. 기본값은 2000입니다.|

> [!NOTE]
> *imageAction*을 "없음" 이외의 값으로 설정하는 경우 *parsingMode* 속성을 "기본값" 이외의 값으로 설정할 수 없습니다.  인덱서 구성에서 이러한 두 속성 중 하나를 기본이 아닌 값으로 설정할 수 있습니다.

**parsingMode** 매개 변수를 `json`(각 blob을 단일 문서로 인덱싱) 또는 `jsonArray`(blob에 JSON 배열이 포함되어 있고 각 배열 요소를 별도 문서로 처리해야 하는 경우)로 설정합니다.

정규화된 이미지의 최대 너비와 높이에 대한 2000 픽셀이라는 기본값은 [OCR 기술](cognitive-search-skill-ocr.md) 및 [이미지 분석 기술](cognitive-search-skill-image-analysis.md)에서 지원하는 최대 크기에 기반합니다. 최대 한도를 늘리면 더 큰 이미지를 처리하는 데 실패할 수 있습니다.


다음과 같이 [인덱서 정의](ref-create-indexer.md)에서 imageAction을 지정합니다.

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":
  {
    "configuration": 
    {
        "dataToExtract": "contentAndMetadata",
        "imageAction": "generateNormalizedImages"
    }
  }
}
```

*imageAction*을 "generateNormalizedImages"로 설정한 경우 새 *normalized_images* 필드에는 이미지의 배열이 포함됩니다. 각 이미지는 다음 멤버가 포함된 복합 형식입니다.

| 이미지 멤버       | 설명                             |
|--------------------|-----------------------------------------|
| 데이터               | JPEG 형식의 BASE64 인코딩된 정규화된 이미지 문자열입니다.   |
| width              | 픽셀 단위로 정규화된 이미지의 너비입니다. |
| height             | 픽셀 단위로 정규화된 이미지의 높이입니다. |
| originalWidth      | 정규화 이전에 이미지의 원래 너비입니다. |
| originalHeight      | 정규화 이전에 이미지의 원래 높이입니다. |
| rotationFromOriginal |  정규화된 이미지를 만들기 위해 발생한 시계 반대 방향 회전입니다. 0도에서 360도 사이의 값입니다. 이 단계에서는 카메라 또는 스캐너에서 생성하는 이미지의 메타데이터를 읽습니다. 일반적으로 90도의 배수입니다. |
| contentOffset |이미지가 추출된 콘텐츠 필드 내의 문자 오프셋입니다. 이 필드는 포함된 이미지가 있는 파일에만 적용됩니다. |

 *normalized_images* 샘플 값:
```json
[
  {
    "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
    "width": 500,
    "height": 300,
    "originalWidth": 5000,  
    "originalHeight": 3000,
    "rotationFromOriginal": 90,
    "contentOffset": 500  
  }
]
```

## <a name="image-related-skills"></a>이미지 관련 기술

입력으로 이미지를 사용하는 [OCR](cognitive-search-skill-ocr.md) 및 [이미지 분석](cognitive-search-skill-image-analysis.md)이라는 두 개의 기본 제공 인식 기술이 있습니다. 

현재 이러한 기술은 문서 해독 단계에서 생성된 이미지에서만 작동합니다. 따라서 지원되는 입력은 `"/document/normalized_images"`입니다.

### <a name="image-analysis-skill"></a>이미지 분석 기술

[이미지 분석 기술](cognitive-search-skill-image-analysis.md)은 이미지 콘텐츠를 기준으로 다양한 시각적 기능 집합을 추출합니다. 예를 들어 이미지에서 캡션을 생성하거나, 태그를 생성하거나, 유명인과 랜드마크를 식별할 수 있습니다.

### <a name="ocr-skill"></a>OCR 기술

[OCR 기술](cognitive-search-skill-ocr.md)은 JPG, PNG 및 비트맵 이미지와 같은 파일에서 텍스트를 추출합니다. 레이아웃 정보뿐만 아니라 텍스트도 추출할 수 있습니다. 레이아웃 정보는 식별된 문자열 각각에 대한 경계 상자를 제공합니다.

OCR 기술을 사용하면 이미지에서 텍스트를 검색하는 데 사용할 알고리즘을 선택할 수 있습니다. 현재 인쇄된 텍스트에 하나, 필기된 텍스트에 하나 등 두 개의 알고리즘을 지원합니다.

## <a name="embedded-image-scenario"></a>포함된 이미지 시나리오

일반적인 시나리오에서는 다음 단계를 수행하여 텍스트와 이미지 원본 텍스트를 비롯한 모든 파일 콘텐츠를 포함하는 단일 문자열을 만듭니다.  

1. [normalized_images 추출](#get-normalized-images)
1. `"/document/normalized_images"`을 입력으로 사용하여 OCR 기술 실행
1. 파일에서 추출된 원시 텍스트와 해당 이미지의 텍스트 표현을 병합합니다. [텍스트 병합](cognitive-search-skill-textmerger.md) 기술을 사용하여 큰 단일 문자열에 두 텍스트 청크를 통합할 수 있습니다.

다음 예제 기술 집합은 문서의 텍스트 콘텐츠를 포함하는 *merged_text* 필드를 만듭니다. 또한 포함된 이미지 각각에서 OCRed 텍스트를 포함합니다. 

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

merged_text 필드가 있으므로 인덱서 정의에서 검색 가능한 필드로 매핑할 수 있습니다. 이미지의 텍스트를 포함하여 파일의 모든 콘텐츠를 검색할 수 있습니다.

## <a name="visualize-bounding-boxes-of-extracted-text"></a>추출된 텍스트의 경계 상자 시각화

또 다른 일반적인 시나리오는 검색 결과 레이아웃 정보를 시각화하는 것입니다. 예를 들어 이미지에서 발견한 텍스트의 조각을 검색 결과의 일부로 강조 표시하는 것이 좋습니다.

정규화된 이미지에서 OCR 단계를 수행했으므로 레이아웃 좌표는 정규화된 이미지 공간에 위치합니다. 정규화된 이미지를 표시할 때 좌표의 존재는 일반적으로 문제가 되지 않지만 원래 이미지를 표시하려는 경우에는 문제가 될 수 있습니다. 이 경우에 레이아웃의 각 좌표 지점을 원래 이미지 좌표계로 변환합니다. 

정규화된 좌표를 원래 좌표 공간으로 변환하는 경우 도우미로 다음 알고리즘을 사용할 수 있습니다.

```csharp
        /// <summary>
        ///  Converts a point in the normalized coordinate space to the original coordinate space.
        ///  This method assumes the rotation angles are multiples of 90 degrees.
        /// </summary>
        public static Point GetOriginalCoordinates(Point normalized,
                                    int originalWidth,
                                    int originalHeight,
                                    int width,
                                    int height,
                                    double rotationFromOriginal)
        {
            Point original = new Point();
            double angle = rotationFromOriginal % 360;

            if (angle == 0 )
            {
                original.X = normalized.X;
                original.Y = normalized.Y;
            } else if (angle == 90)
            {
                original.X = normalized.Y;
                original.Y = (width - normalized.X);
            } else if (angle == 180)
            {
                original.X = (width -  normalized.X);
                original.Y = (height - normalized.Y);
            } else if (angle == 270)
            {
                original.X = height - normalized.Y;
                original.Y = normalized.X;
            }

            double scalingFactor = (angle % 180 == 0) ? originalHeight / height : originalHeight / width;
            original.X = (int) (original.X * scalingFactor);
            original.Y = (int)(original.Y * scalingFactor);

            return original;
        }
```

## <a name="see-also"></a>참고 항목
+ [인덱서 만들기(REST)](ref-create-indexer.md)
+ [이미지 기술 분석](cognitive-search-skill-image-analysis.md)
+ [OCR 기술](cognitive-search-skill-ocr.md)
+ [텍스트 병합 기술](cognitive-search-skill-textmerger.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
