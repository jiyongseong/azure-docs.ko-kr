---
title: Azure Machine Learning 데이터 준비에서 Python 확장성 사용 | Microsoft Docs
description: 이 문서에서는 Python 코드를 사용하여 데이터 준비의 기능을 확장하는 방법에 대한 개요 및 몇 가지 자세한 예제를 제공합니다.
services: machine-learning
author: euangMS
ms.author: euang
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.openlocfilehash: 6363d39b2dfbd36ccebff6780e35caf58ca84dda
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="data-preparations-python-extensions"></a>데이터 준비 Python 확장
기본 제공 기능 사이의 격차를 해소하기 위해 Azure Machine Learning 데이터 준비에는 여러 수준의 확장성이 포함되어 있습니다. 이 문서에서는 Python 스크립트를 통해 확장성을 설명합니다. 

## <a name="custom-code-steps"></a>사용자 지정 코드 단계 
데이터 준비에는 사용자가 코드를 작성할 수는 다음과 같은 사용자 지정 단계가 있습니다.

* 열 추가
* 고급 필터
* 데이터 흐름 변환
* 파티션 변환

## <a name="code-block-types"></a>코드 블록 형식 
위 각 단계에 대해 두 가지 코드 블록 형식이 지원됩니다. 첫째, 있는 그대로 실행되는 기본 Python 식이 지원됩니다. 둘째, 제공한 코드에서 알려진 서명을 사용하여 특정 함수를 호출하는 Python 모듈이 지원됩니다.

예를 들어 다음 두 가지 방식으로 다른 열의 로그를 계산하는 새 열을 추가할 수 있습니다.

식 

```python    
    math.log(row["Score"])
```

모듈 
    
```python
def newvalue(row): 
        return math.log(row["Score"])
```


모듈 모드의 열 추가 변형은 행 변수를 허용하고 열에 대한 값을 반환하는 `newvalue`라는 함수를 찾으려고 합니다. 이 모듈에는 다른 함수, 가져오기 등을 사용하여 임의의 Python 코드 수량을 포함할 수 있습니다.

각 확장 지점에 대한 세부 정보는 다음 섹션에 설명되어 있습니다. 

## <a name="imports"></a>가져오기 
식 블록 형식을 사용하는 경우 여전히 코드에 **import** 문을 추가할 수 있습니다. 이러한 문은 코드 맨 위 줄에 그룹화되어야 합니다.

올바른 코드 

```python
import math 
import numpy 
math.log(row["Score"])
```
 

오류  

```python
import math  
math.log(row["Score"])  
import numpy
```
 
 
모듈 블록 형식을 사용하는 경우 **import** 문을 사용하기 위한 모든 일반 Python 규칙을 따를 수 있습니다. 

## <a name="default-imports"></a>기본 가져오기
다음 import는 코드에 항상 포함되며 사용할 수 있습니다. 다시 가져올 필요는 없습니다. 

```python
import math  
import numbers  
import datetime  
import re  
import pandas as pd  
import numpy as np  
import scipy as sp
```
  

## <a name="install-new-packages"></a>새 패키지 설치
기본적으로 설치되지 않는 패키지를 사용하려면 먼저 데이터 준비에서 사용되는 환경에 설치해야 합니다. 이 설치는 설치를 실행하려는 로컬 컴퓨터 및 모든 계산 대상에서 수행해야 합니다.

계산 대상에서 패키지를 설치하려면 프로젝트의 루트 아래에 있는 aml_config 폴더의 conda_dependencies.yml 파일을 수정해야 합니다.

### <a name="windows"></a>Windows 
Windows에서 해당 위치를 찾으려면 Python의 앱별 설치와 해당 스크립트 디렉터리를 찾습니다. 기본 위치는 다음과 같습니다.  

`C:\Users\<user>\AppData\Local\AmlWorkbench\Python\Scripts` 

그런 후 다음 명령 중 하나를 실행합니다. 

`conda install <libraryname>` 

또는 

`pip install <libraryname> `

### <a name="mac"></a>Mac 
Mac에서 해당 위치를 찾으려면 Python의 앱별 설치와 해당 스크립트 디렉터리를 찾습니다. 기본 위치는 다음과 같습니다. 

`/Users/<user>/Library/Caches/AmlWorkbench/Python/bin` 

그런 후 다음 명령 중 하나를 실행합니다. 

`./conda install <libraryname>`

또는 

`./pip install <libraryname>`

## <a name="use-custom-modules"></a>사용자 지정 모듈 사용
데이터 흐름 변환(스크립트)에서 다음과 같은 Python 코드를 작성합니다.

```python
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction1
df = ExtensionFunction1(df)
```

열 추가(스크립트)에서 Code Block Type = Module을 설정하고 다음과 같이 Python 코드를 작성합니다.

```python 
import sys
sys.path.append(*<absolute path to the directory containing UserModule.py>*)

from UserModule import ExtensionFunction2

def newvalue(row):
    return ExtensionFunction2(row)
```
다른 실행 컨텍스트(로컬, Docker Spark)의 경우 절대 경로로 적절한 위치를 가리킵니다. “os.getcwd() + relativePath”를 사용하여 해당 위치를 지정할 수도 있습니다.


## <a name="column-data"></a>열 데이터 
열 데이터는 점 표기법을 사용하거나 키-값 표기법을 사용하여 행에서 액세스할 수 있습니다. 공백이나 특수 문자가 포함된 열 이름은 점 표기법을 사용하여 액세스할 수 없습니다. `row` 변수는 항상 두 모드의 Python 확장(모듈 및 식)에서 정의해야 합니다. 

예 

```python
    row.ColumnA + row.ColumnB  
    row["ColumnA"] + row["ColumnB"]
```

## <a name="add-column"></a>열 추가 
### <a name="purpose"></a>목적
열 추가 확장 지점에서는 새 열을 계산하는 Python을 작성할 수 있습니다. 작성하는 코드는 전체 행에 액세스할 수 있습니다. 코드는 각 행마다 새로운 열 값을 반환해야 합니다. 

### <a name="how-to-use"></a>사용 방법
열 추가(스크립트) 블록을 사용하여 이 확장 지점을 추가할 수 있습니다. **열** 상황에 맞는 메뉴와 최상위 **변환** 메뉴에서 사용할 수 있습니다. 

### <a name="syntax"></a>구문
식

```python
    math.log(row["Score"])
```

모듈 

```python
def newvalue(row):  
     return math.log(row["Score"])
```
 

## <a name="advanced-filter"></a>고급 필터
### <a name="purpose"></a>목적 
고급 필터 확장 지점에서는 사용자 지정 필터를 작성할 수 있습니다. 전체 행에 액세스할 수 있으며 코드는 True(행 포함) 또는 False(행 제외)를 반환해야 합니다. 

### <a name="how-to-use"></a>사용 방법
고급 필터(스크립트) 블록을 사용하여 이 확장 지점을 추가할 수 있습니다. 최상위 **변환** 메뉴에서 사용할 수 있습니다. 

### <a name="syntax"></a>구문

식

```python
    row["Score"] > 95
```

모듈  

```python
def includerow(row):  
    return row["Score"] > 95
```
 

## <a name="transform-dataflow"></a>데이터 흐름 변환
### <a name="purpose"></a>목적 
데이터 흐름 변환 확장 지점에서는 데이터 흐름을 완전히 변환할 수 있습니다. 처리하는 모든 열 및 행이 포함된 Pandas 데이터 프레임에 액세스할 수 있습니다. 코드는 새 데이터를 포함하는 Pandas 데이터 프레임을 반환해야 합니다. 

>[!NOTE]
>Python에서 이 확장이 사용되는 경우 Pandas 데이터 프레임의 메모리로 모든 데이터가 로드되어야 합니다. 
>
>Spark에서는 모든 데이터가 단일 작업자 노드에 수집됩니다. 데이터가 매우 큰 경우 작업자는 메모리가 부족할 수 있습니다. 이 방법을 사용할 때는 주의하세요.

### <a name="how-to-use"></a>사용 방법 
데이터 흐름 변환(스크립트) 블록을 사용하여 이 확장 지점을 추가할 수 있습니다. 최상위 **변환** 메뉴에서 사용할 수 있습니다. 
### <a name="syntax"></a>구문 

식

```python
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

모듈 

```python
def transform(df):  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()  
    return df
```
  

## <a name="transform-partition"></a>파티션 변환  
### <a name="purpose"></a>목적 
파티션 변환 확장 지점에서는 데이터 흐름의 파티션을 변환할 수 있습니다. 해당 파티션에 대한 모든 열 및 행이 포함된 Pandas 데이터 프레임에 액세스할 수 있습니다. 코드는 새 데이터를 포함하는 Pandas 데이터 프레임을 반환해야 합니다. 

>[!NOTE]
>Python에서는 데이터 크기에 따라 단일 파티션 또는 여러 파티션이 생성될 수 있습니다. Spark에서는 지정된 작업자 노드는 파티션에 대한 데이터를 보유하는 데이터 프레임을 사용하게 됩니다. 두 경우 모두 전체 데이터 집합에 액세스할 수 있다고 가정할 수는 없습니다. 


### <a name="how-to-use"></a>사용 방법
파티션 변환(스크립트) 블록을 사용하여 이 확장 지점을 추가할 수 있습니다. 최상위 **변환** 메뉴에서 사용할 수 있습니다. 

### <a name="syntax"></a>구문 

식 

```python
    df['partition-id'] = index  
    df['index-column'] = range(1, len(df) + 1)  
    df = df.reset_index()
```
 

모듈 

```python
def transform(df, index):
    df['partition-id'] = index
    df['index-column'] = range(1, len(df) + 1)
    df = df.reset_index()
    return df
```


## <a name="datapreperror"></a>DataPrepError  
### <a name="error-values"></a>오류 값  
데이터 준비에는 오류 값 개념이 존재합니다. 

사용자 지정 Python 코드에 오류 값이 발생할 수 있습니다. `DataPrepError`라는 Python 클래스의 인스턴스입니다. 이 클래스는 Python 예외를 래핑하며 몇 가지 속성을 갖습니다. 속성은 원래 값이 처리될 때 발생한 오류와 원래 값에 대한 정보를 포함합니다. 


### <a name="datapreperror-class-definition"></a>DataPrepError 클래스 정의
```python 
class DataPrepError(Exception): 
    def __bool__(self): 
        return False 
``` 
일반적으로 데이터 준비 Python 프레임워크의 DataPrepError 생성은 다음과 같습니다. 
```python 
DataPrepError({ 
   'message':'Cannot convert to numeric value', 
   'originalValue': value, 
   'exceptionMessage': e.args[0], 
   '__errorCode__':'Microsoft.DPrep.ErrorValues.InvalidNumericType' 
}) 
``` 
#### <a name="how-to-use"></a>사용 방법 
확장 지점에서 Python을 실행하여, 이전의 생성 방법을 사용하여 DataPrepError를 반환 값으로 생성할 수 있습니다. 확장 지점에 데이터가 처리될 때 DataPrepError가 발생할 가능성이 훨씬 더 높습니다. 이 시점에서 사용자 지정 Python 코드는 DataPrepError를 유효한 데이터 형식으로 처리해야 합니다.

#### <a name="syntax"></a>구문 
식 
```python 
    if (isinstance(row["Score"], DataPrepError)): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
```python 
    if (hasattr(row["Score"], "originalValue")): 
        row["Score"].originalValue 
    else: 
        row["Score"] 
``` 
모듈 
```python 
def newvalue(row): 
    if (isinstance(row["Score"], DataPrepError)): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
``` 
```python 
def newvalue(row): 
    if (hasattr(row["Score"], "originalValue")): 
        return row["Score"].originalValue 
    else: 
        return row["Score"] 
```  
