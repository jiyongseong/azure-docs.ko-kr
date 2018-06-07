---
title: Azure Machine Learning 데이터 준비에서 사용 가능한 지원되는 데이터 대상 및 출력 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비에 사용 가능한 지원되는 대상 및 출력의 전체 목록을 제공합니다.
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
ms.openlocfilehash: 5451a5f525da845f97141a97241efb53ec42ff58
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="supported-data-exports-for-this-preview"></a>이 미리 보기에 대해 지원되는 데이터 내보내기 
여러 다른 형식으로 내보내는 것이 가능합니다. 이러한 형식을 이용하여 데이터 준비 결과를 Machine Learning 워크플로의 나머지 부분에 통합하기 전에 데이터 준비 중간 결과를 유지할 수 있습니다.

## <a name="types"></a>형식 
### <a name="csv-file"></a>CSV 파일 
쉼표로 구분된 값 파일을 저장소에 씁니다.

#### <a name="options"></a>옵션
- 줄 끝
- Null 값 대체
- 오류 값 대체 
- 구분 기호


### <a name="parquet"></a>Parquet 
데이터 집합을 Parquet으로 저장소에 기록합니다.

Parquet은 형식으로써 저장소에서 다양한 형태를 취할 수 있습니다. 작은 데이터 집합의 경우 단일 .parquet 파일이 사용되기도 합니다. 다양한 Python 라이브러리가 단일 .parquet 파일에 대한 읽기와 쓰기를 지원합니다. 

현재 Azure Machine Learning Workbench는 로컬 대화형 사용 중 Parquet에 쓰기를 위해 PyArrow Python 라이브러리를 사용합니다. 다시 말해서 단일 파일 parquet이 로컬 대화형 사용 중 지원되는 유일한 Parquet 출력 형식입니다.

Spark의 규모 확장 실행 중 Azure Machine Learning Workbench는 Spark의 Parquet 읽기 및 쓰기 기능을 사용합니다. Parquet에 대한 Apache Spark의 기본 출력 형식(현재 하나만 지원됨)은 Hive 데이터 집합과 구조가 비슷합니다. 다시 말해 폴더에는 각각 큰 데이터 집합의 작은 파티션인 다수의 .parquet 파일이 있습니다. 

#### <a name="caveats"></a>주의 사항 
Parquet은 형식으로써 비교적 신생이라 서로 다른 라이브러리 간에 일부 구현 불일치가 있습니다. 예를 들어, Spark는 Parquet에 쓸 때 열 이름으로 유효한 문자에 제한을 둡니다. PyArrow는 그렇지 않습니다. 다음 문자는 열 이름에 사용할 수 없습니다. 
- .
- ;
- {}
- ()
- \\n
- \\t
- =

>[!NOTE]
>- Spark와의 호환성을 위해 Parquet에 데이터를 쓸 때마다 열 이름에 있는 이러한 문자의 발생은 밑줄(_)로 대체됩니다.
>- 로컬 및 규모 확장 실행 간에 일관성을 유지하기 위해 앱, Python 또는 Spark를 통해 Parquet에 작성된 모든 데이터는 Spark 호환성을 보장하기 위해 열 이름이 삭제됩니다. Spark에서 Parquet 문자에 쓸 때 예상되는 열 이름을 유지하려면 열에서 유효하지 않은 집합을 제거한 다음 쓰기를 수행하십시오.



## <a name="locations"></a>위치 
### <a name="local"></a>Local 
로컬 하드 드라이브 또는 매핑된 네트워크 저장소 위치입니다.

### <a name="azure-blob-storage"></a>Azure Blob 저장소
Azure Blob 저장소에는 Azure 구독이 필요합니다.

