---
title: Azure Machine Learning 데이터 준비에 대한 실행 및 데이터 환경의 지원되는 조합 | Microsoft Docs
description: 이 문서에서는 Azure Machine Learning 데이터 준비에 대한 서로 다른 런타임 및 데이터 원본의 지원되는 조합의 전체 목록을 제공합니다.
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
ms.openlocfilehash: bdd1c51c915787d9e9522f6691ae0ff06d546484
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="supported-matrix-for-this-release"></a>이 릴리스에 지원되는 행렬 
코드가 Azure Machine Learning 데이터 원본을 사용하여 데이터를 로드하거나 Azure Machine Learning 데이터 준비에서 Pandas 또는 Spark 데이터 프레임을 가져올 때 다음과 같은 실험 계산 환경 및 데이터 위치의 조합이 지원됩니다.

|     |로컬 파일  |Azure Blob 저장소  |SQL Server 데이터베이스 \***  |
|---------|---------|---------|---------|---------|
|로컬 Python    |     지원됨    |지원되지 않음         | 지원되지 않음        |         |
|Docker(Linux VM) Python     |프로젝트 파일에서만 지원됨*         | 지원되지 않음        |        지원되지 않음 |         |
|Docker(Linux VM) PySpark     |프로젝트 파일에서만 지원됨*     |지원됨         | 지원됨 \*\*        |         |
|Azure 데이터 과학 Virtual Machine Python     |프로젝트 파일에서만 지원됨*         |지원되지 않음         |지원되지 않음         |         |
|Azure 데이터 과학 Virtual Machine PySPark     | 프로젝트 파일에서만 지원됨*        |지원되지 않음         |지원되지 않음         |         |
|Azure HDInsight PySpark     | 지원되지 않음        |지원됨         |지원됨 \*\*         |         |
|Azure HDInsight Python     | 지원되지 않음        | 지원되지 않음        | 지원되지 않음        |         |

현재 계산 대상에 대해 Azure Data Lake Store를 지원하지 않습니다.

*로컬 파일 경로를 사용하는 경우 프로젝트 내의 파일이 계산 환경으로 복사되어 읽혀집니다. 프로젝트 외부 파일은 복사되지 않고 경로가 더 이상 계산 환경에서 확인되지 않습니다. 코드가 로컬로 실행될 때 로컬 파일을 사용할 수 있도록 데이터 원본 대체를 사용하는 것을 고려하세요. 그런 다음 다른 실행 구성에 대한 Azure Storage Blob으로 전환합니다. 또한 데이터 원본에 대한 샘플링 지원을 사용하여 특정 실행 구성에서만 큰 데이터에 대한 실행을 관리할 수 있습니다.

\*\* Maven JDBC SQL Server 드라이버 6.2.1 사용 이 패키지(또는 호환 가능한 제품)가 계산 환경의 spark_dependencies.yml 파일에 포함되어 있어야 합니다.

\***계산 환경에서 데이터베이스에 연결할 수 있다면 Azure SQL Database 또는 SQL Server를 지원합니다. 
