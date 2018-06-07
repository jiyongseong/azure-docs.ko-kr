---
title: Azure Data Lake Analytics에서 U-SQL 인식 기능 사용 | Microsoft Docs
description: U-SQL에서 인텔리전스 인식 기능을 사용하는 방법에 알아봅니다.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: cd06e1ae56efdfdcfcd4fec5b2c17ee843d9e9dd
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311117"
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>자습서: U-SQL의 인식 기능 시작

## <a name="overview"></a>개요
U-SQL에 대한 인식 기능은 개발자가 빅 데이터 프로그램에서 인텔리전스를 사용하도록 합니다. 

다음과 같은 인식 기능을 사용할 수 있습니다.
* 이미징: 얼굴 감지
* 이미징: 감정 감지
* 이미징: 개체 감지(태그 지정)
* 이미징: OCR(광학 문자 인식)
* 텍스트: 핵심 구 추출
* 텍스트: 감정 분석

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>U-SQL 스크립트에 인식 기능을 사용하는 방법

전체 프로세스는 간단합니다.

* `REFERENCE ASSEMBLY` 문을 사용하여 U-SQL 스크립트에 대한 인식 기능을 사용합니다.
* Cognitive UDO를 사용하여 입력 행 집합에서 `PROCESS`를 사용하고, 출력 행 집합을 생성합니다.

### <a name="detecting-objects-in-images"></a>이미지에서 개체를 감지합니다.

다음 예제는 인식 기능을 사용하여 이미지에 있는 개체를 감지하는 방법을 보여줍니다.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
더 많은 예제는 **다음 단계** 섹션의 **U-SQL/인식 샘플**을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [U-SQL/인식 샘플](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
