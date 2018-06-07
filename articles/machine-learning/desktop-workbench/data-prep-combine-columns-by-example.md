---
title: Azure Machine Learning Workbench를 사용하여 예제별 열 결합 변환
description: "'예제별 열 결합' 변환에 대한 참조 문서"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 469fefa48ea795a56b0e7525f99634c5149a780f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="combine-columns-by-example-transformation"></a>예제별 열 결합 변환
이 변환을 사용하여 여러 열의 값을 결합하여 새 열을 추가할 수 있습니다. 사용자는 이 변환을 수행하기 위해 구분 기호를 지정하거나 결합된 값 예제를 제공할 수 있습니다. 사용자가 결합 예제를 제공하면, **예제별 열 파생** 변환에서 사용된 것과 같은 **By-Example** 엔진을 사용하여 변환이 처리됩니다.

## <a name="how-to-perform-this-transformation"></a>이 변환을 수행하는 방법

이 변형을 수행하려면 다음 단계를 따릅니다.
1. 하나의 열으로 결합하려는 두 개 이상의 열을 선택합니다. 
2. **변환** 메뉴에서 **예제별 열 결합**을 선택합니다. 또는 선택한 열의 머리글을 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에서 **예제별 열 결합**을 선택합니다. 변환 편집기가 열리고 선택한 맨 오른쪽 열 옆에 새 열이 추가됩니다. 새 열에는 결합된 값이 기본 구분 기호로 구분되어 포함됩니다. 선택한 열은 열 머리글의 확인란으로 식별될 수 있습니다. 선택 영역에서 열을 추가 및 제거할 경우 해당 확인란을 사용하여 실행 취소할 수 있습니다.
3. 새로 만든 열에서 결합된 값을 업데이트할 수 있습니다. 업데이트된 값은 변환을 학습하는 예제로 사용됩니다.
4. 변환을 적용하려면 **확인**을 클릭합니다.

### <a name="transform-editor-advanced-mode"></a>변환 편집기: 고급 모드

고급 모드에서는 열을 결합하기 위한 보다 풍부한 환경을 제공합니다. 

**열 결합 기준**에서 **구분 기호**를 선택하면 **구분 기호** 텍스트 상자에서 문자열을 지정할 수 있습니다. **구분 기호** 텍스트 상자 밖을 눌러 데이터 표에서 결과를 미리 볼 수 있습니다. **확인**을 눌러 변환을 커밋합니다.

**열 결합 기준** 아래에서 **예제**를 선택하여 결합된 값 예제를 제공할 수 있습니다. 예를 들어 행을 승격하려면 표의 행을 두 번 클릭합니다. 승격된 행에 대해 예상되는 출력을 텍스트 상자에 입력합니다. **구분 기호** 텍스트 상자 밖을 눌러 데이터 표에서 결과를 미리 볼 수 있습니다. **확인**을 눌러 변환을 커밋합니다. 

사용자는 변환 편집기에 있는 링크를 클릭하여 **기본 모드**와 **고급 모드** 간을 전환할 수 있습니다.

### <a name="transform-editor-send-feedback"></a>변환 편집기: 피드백 보내기

**피드백 보내기** 링크를 클릭하면 사용자가 제공한 예제로 주석 상자가 미리 채워진 **피드백** 대화 상자가 열립니다. 사용자는 주석 상자의 내용을 검토하고 문제를 이해하는 데 필요한 자세한 정보를 제공해야 합니다. Microsoft와 데이터를 공유하지 않으려면 **피드백 보내기** 단추를 클릭하기 전에 미리 채워진 예제 데이터를 삭제해야 합니다. 

### <a name="editing-existing-transformation"></a>기존 변환 편집

사용자는 변환 단계의 **편집** 옵션을 선택하여 기존 **예제별 열 결합** 변환을 편집할 수 있습니다. **편집**을 클릭하면 변환 편집기가 **기본 모드**로 열립니다. 머리글의 링크를 클릭하여 **고급 모드**로 전환할 수 있습니다. 변환을 만드는 동안 제공된 모든 예제가 여기에 표시됩니다.

## <a name="example-using-separators"></a>구분 기호를 사용하는 예제

이 예제에서는 *Street*, *City*, *State* 및 *ZIP* 열을 결합하기 위해 쉼표+공백을 구분 기호로 사용합니다.

|Street|City|시스템 상태|ZIP|열|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th Way|REDMOND|WA|98052|16011 N.E. 36th Way, REDMOND, WA, 98052|
|16021 N.E. 36th Way|REDMOND|WA|98052|16021 N.E. 36th Way, REDMOND, WA, 98052|
|16031 N.E. 36th Way|REDMOND|WA|98052|16031 N.E. 36th Way, REDMOND, WA, 98052|
|16041 N.E. 36th Way|REDMOND|WA|98052|16041 N.E. 36th Way, REDMOND, WA, 98052|
|16051 N.E. 36th Way|REDMOND|WA|98052|16051 N.E. 36th Way, REDMOND, WA, 98052|
|16061 N.E. 36th Way|REDMOND|WA|98052|16061 N.E. 36th Way, REDMOND, WA, 98052|
|3460 157th Avenue NE|REDMOND|WA|98052|3460 157th Avenue NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th Avenue N.E.|REDMOND|WA|98052|3240 157th Avenue N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>예제별 방식을 사용하는 예제

**굵게** 표시한 값은 예제로 제공되었습니다.

|Date|월|Year|Hour|분|초|결합된 열|
|:----|:----|:----|:----|:----|:----|:----|
|13|Oct|2016|15|01|23|**13-Oct-2016 15:01:23 PDT**|
|16|Oct|2016|16|22|33|16-Oct-2016 15:01:33 PDT|
|17|Oct|2016|12|43|12|17-Oct-2016 15:01:12 PDT|
|12|Nov|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|Jan|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|Mar|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|Apr|2017|11|34|36|16-Apr-2016 15:01:36 PDT|

