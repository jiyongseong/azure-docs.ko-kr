---
title: Azure Machine Learning Data Preparation을 사용한 데이터 흐름 변환 예제 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비에서 사용할 수 있는 일단의 데이터 흐름 변환 예제를 제공합니다.
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
ms.openlocfilehash: aa213a3b1a8949f0fca5e4bbb7ec5a6a775ae6ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>사용자 지정 데이터 흐름 변환 예제(Python) 
메뉴의 변환 이름은 **Transform Dataflow(스크립트)**입니다. 이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.

## <a name="transform-frame"></a>프레임 변환
### <a name="create-a-new-column-dynamically"></a>동적으로 새 열 만들기 
열(**city2**)을 동적으로 만들고 여러 다른 버전의 San Francisco를 기존 city 열 중 하나로 조정합니다.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>새 집계 추가
score 열에 대해 계산된 첫 번째 및 마지막 집계가 있는 새 프레임을 만듭니다. 이러한 집계는 **risk_category** 열로 그룹화됩니다.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>열 윈저화 
열의 이상값을 줄이기 위해 수식을 충족하도록 데이터를 다시 작성합니다.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>데이터 흐름 변환
### <a name="fill-down"></a>자동 채우기 

자동 채우기를 하려면 두 변환이 필요합니다. 다음 테이블과 같은 데이터를 가정합니다.

|시스템 상태         |City       |
|--------------|-----------|
|워싱턴    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |시애틀    |
|캘리포니아    |로스앤젤레스|
|              |샌디에이고  |
|              |산호세   |
|텍사스         |댈러스     |
|              |샌안토니오|
|              |휴스턴    |

1. 다음 코드를 사용하여 "Add Column(스크립트)" 변환을 만듭니다.
```python
    row['State'] if len(row['State']) > 0 else None
```

2. 다음 코드가 포함된 "Transform Data Flow(스크립트)" 변환을 만듭니다.
```python
    df = df.fillna( method='pad')
```

이제 데이터는 다음 테이블과 같습니다.

|시스템 상태         |newState         |City       |
|--------------|--------------|-----------|
|워싱턴    |워싱턴    |Redmond    |
|              |워싱턴    |Bellevue   |
|              |워싱턴    |Issaquah   |
|              |워싱턴    |시애틀    |
|캘리포니아    |캘리포니아    |로스앤젤레스|
|              |캘리포니아    |샌디에이고  |
|              |캘리포니아    |산호세   |
|텍사스         |텍사스         |댈러스     |
|              |텍사스         |샌안토니오|
|              |텍사스         |휴스턴    |


### <a name="min-max-normalization"></a>최소 최대 정규화
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```