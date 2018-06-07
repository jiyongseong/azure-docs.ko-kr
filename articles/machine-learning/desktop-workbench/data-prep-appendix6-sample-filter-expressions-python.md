---
title: Azure Machine Learning 데이터 준비에서 사용할 수 있는 필터 식 예제 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비로 가능한 필터링 식의 예제 집합을 제공합니다.
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
ms.openlocfilehash: 973c56b8b2821c8e3d63161e6a233243639c74f4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-filter-expressions-python"></a>필터 식 샘플(Python) 
이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.

## <a name="filter-with-equivalence-test"></a>등가 테스트를 사용하여 필터링
(숫자) Col2의 값이 4보다 큰 행만 필터링합니다. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>여러 열을 사용하여 필터링 
Col1에 **Good** 값을 포함하고 Col2에 (숫자) 값 1을 포함하는 행만 필터링합니다. 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Null에 대한 필터 테스트
Col1에 Null 값이 있는 행만 필터링합니다. 
```python
    pd.isnull(row["Col1"])
```
