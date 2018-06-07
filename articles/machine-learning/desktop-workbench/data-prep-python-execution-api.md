---
title: Azure Machine Learning 데이터 준비 실행 API를 사용하는 방법에 대한 심층 가이드 | Microsoft Docs
description: 이 문서에서는 이전에 디자인한 데이터 원본 및 데이터 준비 패키지의 실행에 대한 자세한 정보를 제공합니다.
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
ms.openlocfilehash: 36814d238aabd12e7cc6947809c135130002eb46
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Python에서 데이터 원본 및 데이터 준비 패키지 실행

Python 내에서 Azure Machine Learning 데이터 원본 또는 Azure Machine Learning 데이터 준비 패키지를 사용하려면:

1. 프로젝트의 **데이터** 탭으로 이동합니다.

2. 적절한 원본을 마우스 오른쪽 단추로 클릭합니다.

3. **데이터 액세스 코드 파일 생성**을 선택합니다.

이 작업은 패키지를 실행하고 데이터 프레임을 반환하는 간단한 Python 스크립트를 만듭니다.

## <a name="data-sources"></a>데이터 원본

`azureml.dataprep.datasource` 모듈에는 데이터 원본을 실행하고 데이터 프레임을 반환하는 단일 함수가 포함되어 있습니다. `load_datasource(path, secrets=None, spark=None)`
- `path`는 데이터 원본(.dsource 파일)의 경로입니다.
- `secrets`은 키를 암호에 매핑하는 선택적 사전입니다.
- `spark`는 Spark 데이터 프레임을 반환할지 또는 Pandas 데이터 프레임을 반환할지를 지정하는 선택적 부울입니다. 기본적으로 Azure Machine Learning Workbench는 컨텍스트에 따라 런타임 시 반환할 데이터 프레임의 종류를 결정합니다.

## <a name="data-preparations-packages"></a>데이터 준비 패키지

`azureml.dataprep.package` 모듈에는 데이터 준비 패키지에서 데이터 흐름을 실행하는 세 가지 함수가 포함되어 있습니다.

### <a name="execution-functions"></a>실행 함수

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)`은 실행을 위해 지정된 데이터 흐름을 제출하지만 데이터 프레임을 반환하지 않습니다.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)`은 지정된 데이터 흐름을 실행하고 결과를 데이터 프레임으로 반환합니다.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)`는 메모리 내 데이터 원본을 기준으로 지정된 데이터 흐름을 실행하고 결과를 데이터 프레임으로 반환합니다. `user_config` 인수는 데이터 원본(.dsource 파일)의 절대 경로를 목록의 목록으로 표시하는 메모리 내 데이터 원본에 매핑하는 사전입니다.

### <a name="common-arguments"></a>공통 인수

- `package_path`는 데이터 준비 패키지(.dprep 파일)의 경로입니다.
- `dataflow_idx`는 실행할 패키지의 데이터 흐름에 대한 0부터 시작하는 인덱스입니다. 지정한 데이터 흐름이 다른 데이터 흐름 또는 데이터 원본을 참조하는 경우에도 실행됩니다.
- `secrets`은 키를 암호에 매핑하는 선택적 사전입니다.
- `spark`는 Spark 데이터 프레임을 반환할지 또는 Pandas 데이터 프레임을 반환할지를 지정하는 선택적 부울입니다. 기본적으로 Workbench는 컨텍스트에 따라 런타임 시 반환할 데이터 프레임의 종류를 결정합니다.
