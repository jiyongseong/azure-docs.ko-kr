---
title: Azure Machine Learning 데이터 준비에서 새 열을 파생하는 Python 샘플 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비에서 새 열을 만드는 Python 코드 예제를 제공합니다.
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
ms.openlocfilehash: 02670fa4506237ac391e467e5c7a1187b1131ece
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-custom-column-transforms-python"></a>사용자 지정 열 변환 샘플(Python) 
메뉴에서 이 변환의 이름은 **Add Column(스크립트)**입니다.

이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.

## <a name="test-equivalence-and-replace-values"></a>등가 테스트 및 값 바꾸기 
Col1의 값이 4보다 작으면 새 열의 값은 1이어야 합니다. Col1의 값이 4보다 크면 새 열의 값은 2입니다. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>현재 날짜 및 시간 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>형식 캐스팅 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Null 여부 평가 
Col1에 null이 있으면 새 열을 **Bad**로 표시하고, 그렇지 않으면 **Good**으로 표시합니다. 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>새 계산 열 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Epoch 계산 
Unix Epoch 이후의 시간(초)입니다(Col1이 이미 날짜라고 가정). 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```

## <a name="hash-a-column-value-into-a-new-column"></a>열 값을 새 열에 해시
```python
    import hashlib
    hash(row["MyColumnToHashCol1"])

```




