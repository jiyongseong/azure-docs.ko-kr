---
title: Azure 미디어 인텔리전스 | Microsoft Docs
description: Azure Media Services를 사용하면 AudioAnalyzerPreset 및 VideoAnalyzerPreset을 사용하여 오디오 및 비디오 콘텐츠를 분석할 수 있습니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: 804a418f6ee88974d6e74a2c18bc5d01b6adf838
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="media-intelligence"></a>미디어 인텔리전스

Azure Media Services REST v3 API를 사용하면 오디오 및 비디오 콘텐츠를 분석할 수 있습니다. 콘텐츠를 분석하려면 **Transform**을 만들고 **AudioAnalyzerPreset** 또는 **VideoAnalyzerPreset** 사전 설정 중 하나를 사용하는 **Job**을 제출합니다. 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset**를 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다. 출력에는 오디오 대본의 VTT 파일과 JSON 파일(모든 인사이트 포함)이 포함됩니다. 이 사전 설정은 입력 파일의 언어를 [BCP47](https://tools.ietf.org/html/bcp47) 문자열의 형태로 지정하는 속성을 허용합니다. 오디오 인사이트는 다음 정보를 포함합니다.

* 오디오 전사 – 타임 스탬프가 있는 음성의 대본입니다. 여러 언어 지원
* 화자 인덱싱 – 화자와 해당 음성의 매핑입니다.
* 음성 감정 분석 - 오디오 전사에 대해 수행한 감정 분석 결과입니다.
* 키워드 – 오디오 전사에서 추출한 키워드입니다.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset**을 사용하면 비디오 파일에서 여러 개의 오디오 및 비디오 인사이트를 추출할 수 있습니다. 출력에는 JSON 파일(모든 인사이트 포함), 비디오 대본의 VTT 파일 및 썸네일 컬렉션이 포함됩니다. 이 사전 설정은 [BCP47](https://tools.ietf.org/html/bcp47) 문자열(비디오의 언어를 나타냄)을 속성으로 수락합니다. 비디오 인사이트에는 앞서 언급한 모든 오디오 인사이트와 다음과 같은 추가 항목이 포함됩니다.

* 얼굴 추적 - 비디오에 얼굴이 있는 시간입니다. 얼굴마다 얼굴 ID와 해당 썸네일 컬렉션이 있습니다.
* 시각적 텍스트 - 광학 문자 인식을 통해 감지된 텍스트입니다. 텍스트에는 타임스탬프가 지정되며 키워드(오디오 대본과 함께)를 추출하는 데 사용되기도 합니다.
* 키 프레임 – 비디오에서 추출한 키 프레임 컬렉션입니다.
* 시각적 콘텐츠 조정 – 본질적으로 성인용 또는 선정적이라는 플래그가 지정된 비디오의 일부분입니다.
* 주석 – 미리 정의된 개체 모델을 기반으로 비디오에 주석을 추가한 결과입니다.

##  <a name="insightsjson-elements"></a>insights.json 요소

비디오 또는 오디오에서 찾은 모든 인사이트가 포함된 JSON 파일(insights.json)이 출력에 포함됩니다. json에는 다음 요소가 포함될 수 있습니다.

### <a name="transcript"></a>대본

|Name|설명|
|---|---|
|id|줄 ID입니다.|
|text|자체 대본입니다.|
|언어|대본 언어입니다. 각 줄마다 다른 언어가 사용될 수 있는 대본을 지원하기 위한 요소입니다.|
|인스턴스|이 줄이 나타나는 시간 범위 목록입니다. 인스턴스가 대본이면 인스턴스는 하나만 있습니다.|

예:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|Name|설명|
|---|---|
|id|OCR 줄 ID입니다.|
|text|OCR 텍스트입니다.|
|신뢰도|인식 신뢰도입니다.|
|언어|OCR 언어입니다.|
|인스턴스|이 OCR이 표시된 시간 범위 목록(동일한 OCR이 여러 번 나타날 수 있음)입니다.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="keywords"></a>키워드

|Name|설명|
|---|---|
|id|키워드 ID입니다.|
|text|키워드 텍스트입니다.|
|신뢰도|키워드의 인식 신뢰도입니다.|
|언어|키워드 언어(번역된 경우)입니다.|
|인스턴스|키워드가 표시되는 시간 범위 목록(키워드가 여러 번 나타날 수 있음)입니다.|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

### <a name="faces"></a>얼굴

|Name|설명|
|---|---|
|id|얼굴 ID입니다.|
|이름|얼굴 이름입니다. ‘Unknown #0’, 확인된 유명인 또는 고객 교육을 받은 사람일 수 있습니다.|
|신뢰도|얼굴 인식 신뢰도입니다.|
|description|유명인인 경우 설명입니다("Satya Nadella의 출생지는...."). |
|thumbnalId|얼굴 썸네일의 ID입니다.|
|knownPersonId|알려진 사람인 경우 내부 ID입니다.|
|referenceId|Bing 유명인의 경우 Bing ID입니다.|
|referenceType|현재는 그냥 Bing입니다.|
|title|유명인인 경우 직함(예: "Microsoft CEO")입니다.|
|imageUrl|유명인인 경우 이미지 URL입니다.|
|인스턴스|지정된 시간 범위 내에 얼굴이 나타난 인스턴스입니다. 인스턴스마다 thumbnailsId가 있습니다. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="labels"></a>레이블

|Name|설명|
|---|---|
|id|레이블 ID입니다.|
|이름|레이블 이름(예: '컴퓨터', 'TV')입니다.|
|언어|레이블 이름 언어(번역된 경우)입니다. BCP-47|
|인스턴스|레이블이 나타나는 시간 범위 목록(레이블은 여러 번 나타날 수 있음)입니다. 인스턴스마다 신뢰도 필드가 있습니다. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="shots"></a>샷

|Name|설명|
|---|---|
|id|샷 ID입니다.|
|keyFrames|샷 내의 키 프레임 목록(각각 ID와 인스턴스 시간 범위 목록이 있음)입니다.|
|인스턴스|이 샷의 시간 범위 목록(샷에는 인스턴스가 하나만 있음)입니다.|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="audioeffects"></a>audioEffects

|Name|설명|
|---|---|
|id|오디오 효과 ID입니다.|
|형식|오디오 효과 유형(예: 박수, 음성, 음소거)입니다.|
|인스턴스|이 오디오 효과가 나타나는 시간 범위 목록입니다.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```


### <a name="sentiments"></a>감정

감정은 sentimentType 필드(긍정적/중립/부정적)로 집계됩니다. 예: 0-0.1, 0.1-0.2.

|Name|설명|
|---|---|
|id|감정 ID입니다.|
|averageScore |해당 감정 유형의 모든 인스턴스의 모든 점수 평균 – 긍정적/중립/부정적|
|인스턴스|이 감정이 나타나는 시간 범위 목록입니다.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure Media Services로 비디오 분석](analyze-videos-tutorial-with-api.md)
