---
title: Azure Machine Learning 데이터 준비(Data Preparation)에서 사용 가능한 지원되는 데이터 원본 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비에서 사용 가능한 지원되는 데이터 원본의 전체 목록을 제공합니다.
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
ms.openlocfilehash: 7b42080ea4bf9a9e49f2695ab8746d9ead7348bd
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="supported-data-sources-for-azure-machine-learning-data-preparation"></a>Azure Machine Learning 데이터 준비에서 지원되는 데이터 원본 
이 문서에서는 Azure Machine Learning 데이터 준비에 대해 현재 지원되는 데이터 원본을 대략적으로 설명합니다.

이 릴리스의 지원되는 데이터 원본은 다음과 같습니다.

## <a name="types"></a>형식 

### <a name="sql-server"></a>SQL Server
온-프레미스 SQL Server 또는 Azure SQL Database에서 읽습니다.

#### <a name="options"></a>옵션
- 서버 주소
- 트러스트 서버(서버의 인증서가 유효하지 않은 경우에도 해당. 주의해서 사용)
- 인증 유형(Windows, Server)
- 사용자 이름
- 암호
- 연결할 데이터베이스
- SQL 쿼리

#### <a name="notes"></a>메모
- Sql-variant 열은 지원되지 않음
- 시간 열은 1970년 1월 1일까지 데이터베이스에서 시간을 추가하여 날짜/시간으로 변환됩니다.
- Spark 클러스터에서 실행될 때 모든 데이터 관련 열(date, datetime, datetime2, datetimeoffset)은 1583년 이전 날짜에 대해 잘못된 값으로 평가됩니다.
- 소수 열의 값은 10진수로 변환으로 인해 자릿수가 손실될 수 있습니다.

### <a name="directory-vs-file"></a>디렉터리 및 파일
단일 파일을 선택하고 데이터 준비로 읽어옵니다. 파일 형식을 구문 분석하여 다음 화면에 표시되는 파일 연결에 대한 기본 매개 변수를 확인합니다.

디렉터리 또는 디렉터리 내의 파일 집합(파일 선택기가 다중 선택인 경우)을 선택합니다. 두 방법 모두 파일을 단일 데이터 흐름으로 읽고, 서로에 추가합니다(필요한 경우 헤더가 제거됨).

지원되는 파일 형식은 다음과 같습니다.
- 구분된 파일(csv, tsv, txt 등)
- 고정 폭
- 일반 텍스트
- JSON 파일

### <a name="csv-file"></a>CSV 파일
저장소에서 쉼표로 구분된 값 파일을 읽습니다.

#### <a name="options"></a>옵션
- 구분 기호
- 주석
- 헤더
- 소수점 기호
- 파일 인코딩
- 줄 건너뛰기

### <a name="tsv-file"></a>TSV 파일
저장소에서 탭으로 구분된 값 파일을 읽습니다.

#### <a name="options"></a>옵션
- 주석
- 헤더
- 파일 인코딩
- 줄 건너뛰기

### <a name="excel-xlsxlsx"></a>Excel(.xls/.xlsx)
Excel 파일에서 시트 이름 또는 번호를 지정하여 한 번에 하나의 시트를 읽습니다.

#### <a name="options"></a>옵션
- 시트 이름 또는 번호
- 헤더
- 줄 건너뛰기

### <a name="json-file"></a>JSON 파일
저장소에서 JSON 파일을 읽습니다. 파일은 읽을 때 “평면화”됩니다.

#### <a name="options"></a>옵션
- 없음

### <a name="parquet"></a>Parquet
Parquet 데이터 집합(단일 파일 또는 폴더)을 읽습니다.

Parquet은 형식으로써 저장소에서 다양한 형태를 취할 수 있습니다. 작은 데이터 집합의 경우 단일 .parquet 파일이 사용되기도 합니다. 다양한 Python 라이브러리에서 단일 .parquet 파일로 읽기 또는 쓰기를 지원합니다. 현재 Azure Machine Learning 데이터 준비는 로컬 대화형 사용 중 Parquet을 읽기 위해 PyArrow Python 라이브러리를 사용합니다. Parquet 데이터 집합뿐만 아니라 단일 .parquet 파일(큰 데이터 집합의 일부가 아니고 일반적으로 작성된 경우)도 지원합니다.

Parquet 데이터 집합은 각각 큰 데이터 집합의 작은 파티션을 나타내는 둘 이상의 .parquet 파일의 컬렉션입니다. 데이터 집합은 일반적으로 폴더에 포함되고 Spark 및 Hive와 같은 플랫폼의 기본 parquet 출력 형식입니다.

>[!NOTE]
>여러 개의 .parquet 파일이 있는 폴더의 Parquet 데이터를 읽는 경우 읽을 디렉터리를 선택하고 **Parquet 데이터 집합** 옵션을 선택하는 것이 가장 안전합니다. 그러면 PyArrow는 개별 파일이 아니라 전체 폴더를 읽습니다. 이 경우 디스크에 Parquet을 저장하는 더 복잡한 방식의 읽기가 지원됩니다(예: 폴더 분할).

확장 실행은 Spark의 Parquet 읽기 기능을 사용하며 로컬 대화형과 유사하게 폴더 및 단일 파일을 지원합니다.

#### <a name="options"></a>옵션
- Parquet 데이터 집합. 이 옵션은 Azure Machine Learning 데이터 준비가 지정된 디렉터리를 확장하고 각 파일을 개별적으로 읽을지(비선택 모드) 또는 디렉터리를 전체 데이터 집합으로 취급할지를(선택 모드) 결정합니다. 선택 모드에서 PyArrow는 파일을 해석하는 가장 좋은 방법을 선택합니다.


## <a name="locations"></a>위치
### <a name="local"></a>Local
로컬 하드 드라이브 또는 매핑된 네트워크 저장소 위치입니다.

### <a name="sql-server"></a>SQL Server
온-프레미스 SQL Server 또는 Azure SQL Database.

### <a name="azure-blob-storage"></a>Azure Blob 저장소
Azure 구독이 필요한 Azure Blob Storage입니다.

