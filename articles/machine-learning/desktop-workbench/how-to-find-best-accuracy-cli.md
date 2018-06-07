---
title: Azure Machine Learning Workbench에서 가장 짧은 기간 동안 가장 높은 정확도의 실행 확인 | Microsoft Docs
description: Azure Machine Learning Workbench를 사용하여 CLI를 통해 최고의 정확도를 찾는 종단 간 사용 사례
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs, jmartens, jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: bd8888d911730831435b87d3a60b48a7797eea98
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="find-runs-with-the-best-accuracy-and-lowest-duration"></a>가장 짧은 기간 동안 가장 높은 정확도의 실행 확인
다수의 실행 중 한 가지 사용 사례는 최고 정확도의 실행을 찾는 것입니다. 한 가지 방법은 [JMESPath](http://jmespath.org/) 쿼리와 함께 CLI(명령줄 인터페이스 )를 사용하는 것입니다. Azure CLI에서 JMESPath를 사용하는 방법에 대한 자세한 내용은 [Azure CLI 2.0에서 JMESPath 쿼리 사용](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)을 참조하세요. 다음 예제에서는 정밀도 값이 0, 0.98, 1, 1인 네 개의 실행이 작성됩니다. `Threshold = .03`인 `[MaxAccuracy-Threshold, MaxAccuracy]` 범위에 있으면 실행이 필터링됩니다.

## <a name="sample-data"></a>샘플 데이터
`Accuracy` 값을 사용하는 기존 실행이 없는 경우 다음 단계에서 쿼리를 위한 실행을 생성합니다.

먼저 Azure Machine Learning Workbench에서 Python 파일을 만들고 이름을 `log_accuracy.py`로 지정한 후 다음 코드를 붙여 넣습니다.
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

다음으로 `run.py` 파일을 만들고 아래 코드를 붙여넣습니다.
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

마지막으로 Workbench에서 CLI를 열고 `python run.py` 명령을 실행하여 네 가지 실험을 제출합니다. 스크립트가 끝나면 `Run History` 탭에 네 가지 실행이 더 표시됩니다.

## <a name="query-the-run-history"></a>실행 기록 쿼리
첫 번째 명령은 최대 정확도 값을 찾습니다.
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

이 최대 정확도 값 `1`과 임계값 `0.03`을 사용하여 두 번째 명령은 `Accuracy`를 사용하여 실행을 필터링한 다음 `duration`을 기준으로 실행을 오름차순으로 정렬합니다.
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> 엄격한 상한값 확인을 원할 경우 쿼리 형식은 ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]``입니다.

Powershell을 사용하는 경우 다음 코드는 로컬 변수를 사용하여 임계값 및 최대 정확도를 저장합니다.
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>다음 단계
로깅에 대한 자세한 내용은 [Azure Machine Learning Workbench의 실행 기록 및 모델 메트릭을 사용하는 방법](how-to-use-run-history-model-metrics.md)을 참조하세요.    
