---
title: Azure Machine Learning 데이터 준비에 사용 가능한 지원되는 검사기 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비에서 사용할 수 있는 검사기의 전체 목록을 제공합니다.
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 5d5797ede15be0779873f0a023433f0a915dd74a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Azure Machine Learning 데이터 준비 미리 보기에 지원되는 검사기
이 문서에서는 이 미리 보기에서 사용할 수 있는 일단의 검사기에 대해 간략히 설명합니다.

## <a name="the-halo-effect"></a>Halo 효과 
일부 검사기는 Halo 효과를 지원합니다. 이 효과는 서로 다른 두 가지 색상을 사용하여 변환에서 변경된 결과를 시각적으로 즉시 보여 줍니다. 회색은 마지막 변환 이전의 값을 나타내고, 파란색은 현재 값을 나타냅니다. 이 효과는 옵션에서 사용/해제로 설정할 수 있습니다.

## <a name="graphical-filtering"></a>그래픽 필터링 
검사기 중 일부는 편집기로 사용하여 데이터 필터링을 지원합니다. 검사기를 편집기로 사용하여 그래픽 요소를 선택한 다음, 검사기 창의 오른쪽 위에 있는 도구 모음을 사용하여 선택한 값을 필터링하거나 출력합니다. 

## <a name="column-statistics"></a>열 통계
숫자 열의 경우 이 검사기는 열에 대한 다양한 통계를 제공합니다. 통계에 포함되는 측정값은 다음과 같습니다. 
- 최소
- 하한 사분위수
- 중앙값
- 상한 사분위수
- 최대
- 평균
- 표준 편차


### <a name="options"></a>옵션 
- 없음

## <a name="histogram"></a>히스토그램 
단일 숫자 열의 히스토그램을 계산하고 표시합니다. 기본 버킷 수는 Scott의 규칙을 사용하여 계산됩니다. 그러나 규칙은 옵션을 통해 재정의할 수 있습니다.

이 검사기는 Halo 효과를 지원합니다.


### <a name="options"></a>옵션
- 최소 버킷 수(기본 버킷팅이 선택된 경우에도 적용)
- 기본 버킷 수(Scott의 규칙) 
- Halo 표시
- 커널 밀도 점도 오버레이(가우스 커널) 
- 로그 눈금 간격 사용


### <a name="actions"></a>작업
이 검사기는 버킷을 통한 필터링을 지원하며, 단일 또는 다중 선택 버킷을 포함할 수 있습니다. 앞에서 설명한 대로 필터를 적용합니다.

## <a name="value-counts"></a>값 개수
이 검사기는 현재 선택된 열에 대한 값의 빈도 테이블을 제공합니다. 기본적으로 상위 6개 값이 표시됩니다. 그러나 이 제한을 임의의 수로 변경할 수 있습니다. 맨 위 대신 맨 아래의 개수부터 표시하도록 설정할 수도 있습니다. 이 검사기는 Halo 효과를 지원합니다.

### <a name="options"></a>옵션 
- 상위 값의 수
- 내림차순
- Null/오류 값 포함
- Halo 표시
- 로그 눈금 간격 사용


### <a name="actions"></a>작업 
이 검사기는 막대를 통한 필터링을 지원하며, 단일 또는 다중 선택 막대를 포함할 수 있습니다. 앞에서 설명한 대로 필터를 적용합니다.

## <a name="box-plot"></a>상자 그림 
숫자 열의 상자 수염 그림입니다.

### <a name="options"></a>옵션 
- 열별 그룹화

## <a name="scatter-plot"></a>산점도
두 개의 숫자 열에 대한 산점도입니다. 성능상의 이유로 데이터를 축소 샘플링합니다. 샘플 크기는 옵션에서 재정의할 수 있습니다.

### <a name="options"></a>옵션  
- X-축 열
- Y-축 열
- 샘플 크기
- 열별 그룹화


## <a name="time-series"></a>시계열
X-축에서 시간을 인식하는 선 그래프입니다.

### <a name="options"></a>옵션
- 날짜 열
- 숫자 열
- 샘플 크기


### <a name="actions"></a>작업
이 검사기는 그래프의 범위를 선택하기 위해 클릭한 다음 끌기 선택 방법을 통해 필터링할 수 있습니다. 선택이 완료되면 앞에서 설명한 대로 필터를 적용합니다.


## <a name="map"></a>Map 
위도 및 경도가 지정되었다고 가정하고 점으로 표시된 맵입니다. 위도를 먼저 선택해야 합니다.

### <a name="options"></a>옵션
- 위도 열
- 경도 열
- 클러스터링 설정
- 열별 그룹화


### <a name="actions"></a>작업
이 검사기는 맵에서 점 선택을 통한 필터링을 지원합니다. **Ctrl** 키를 누른 채 마우스를 클릭한 다음 끌어 점 주변으로 정사각형을 만듭니다. 그런 다음 앞에서 설명한 대로 필터를 적용합니다.

맵의 왼쪽에서 **E** 키를 눌러 가능한 점만 표시하도록 맵의 크기를 빠르게 조정할 수 있습니다.


## <a name="pattern-frequency"></a>패턴 빈도 

이 검사기는 선택한 문자열 열의 패턴 목록을 보여 줍니다. 패턴은 구문과 같은 정규식을 사용하여 표현됩니다. 패턴에 마우스를 가져가면 해당 패턴으로 표현된 값의 예제가 표시됩니다. 패턴과 함께 백분율의 대략적인 범위도 표시됩니다.

![패턴 검사기의 이미지](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>옵션
- 상위 값의 수
- 내림차순
- Halo 표시

### <a name="actions"></a>작업
이 검사기는 표시된 패턴을 기반으로 필터링을 지원합니다. **Ctrl** 키를 누른 후 패턴 검사기에서 채워진 막대를 선택합니다. 그런 다음 앞에서 설명한 대로 필터를 적용합니다. 사용자 작업 결과로 고급 필터 단계가 추가됩니다. 고급 필터 단계의 편집 옵션을 호출하여 생성된 Python 코드를 보고 수정할 수 있습니다.
