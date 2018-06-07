---
title: Azure Machine Learning 데이터 준비로 가능한 예제 대상/출력 | Microsoft Docs
description: 이 문서는 Azure Machine Learning 데이터 준비를 통한 사용자 지정 데이터 대상/출력의 예제 집합을 제공합니다.
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
ms.openlocfilehash: a9476b9ac3a5cc1f82ab1c09678972fb6b78b894
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="sample-of-destination-connections-python"></a>대상 연결 샘플(Python) 
이 부록을 읽기 전에 [Python 확장성 개요](data-prep-python-extensibility-overview.md)를 참조하세요.


## <a name="write-to-excel"></a>Excel에 쓰기 


Excel에 쓰려면 추가 라이브러리가 필요합니다. 새 라이브러리 추가는 확장성 개요에 설명되어 있습니다. `openpyxl`은 추가해야 하는 라이브러리입니다.

Excel에 쓰기 전에 일부 다른 변경이 필요할 수도 있습니다. 데이터 준비에 사용되는 일부 데이터 유형은 일부 대상 형식에서 지원되지 않습니다. 예를 들어 "Error" 개체가 있으면 Excel로 정확하게 직렬화하지 않습니다. 따라서 Excel에 쓰기 전에 모든 열의 오류를 제거하는 "오류 값 바꾸기"변환이 필요합니다.

이전 작업이 모두 완료되면 다음 줄이 Excel 문서의 단일 시트에 데이터 테이블을 씁니다. 데이터 흐름 변환(스크립트) 변환을 추가합니다. 그런 다음 식 섹션에 다음 코드를 입력합니다.


### <a name="on-windows"></a>Windows  
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>MacOS/OS X에서 ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
