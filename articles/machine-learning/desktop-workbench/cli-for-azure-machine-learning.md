---
title: 최상위 작업에 대한 CLI 설치 및 사용 - Azure Machine Learning
description: Azure Machine Learning에서 가장 일반적인 기계 학습 작업에 대한 CLI를 설치하고 사용하는 방법을 알아봅니다.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2018
ms.openlocfilehash: 45f96884bf6abe1c3868680be29b7f90cc50489d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="install-and-use-the-machine-learning-cli-for-top-tasks-in-azure-machine-learning"></a>Azure Machine Learning의 최상위 작업에 대한 기계 학습 CLI 설치 및 사용

Azure Machine Learning 서비스는 통합된 종단 간 데이터 과학 및 고급 분석 솔루션입니다. 전문 데이터 과학자들은 Azure Machine Learning 서비스를 사용하여 클라우드 규모로 데이터를 준비하고, 실험을 개발하고, 모델을 배포할 수 있습니다. 

Azure Machine Learning은 다음과 같은 일을 할 수 있는 CLI(명령줄 인터페이스)를 제공합니다.
+ 작업 영역 및 프로젝트 관리
+ 계산 대상 설정
+ 교육 실험 실행
+ 지난 실행에 대한 기록 및 메트릭 보기
+ 모델을 프로덕션 환경에 웹 서비스로 배포
+ 프로덕션 모델 및 서비스 관리

이 문서에서는 사용자 편의를 위해 가장 유용한 CLI 명령 중 일부를 제공합니다. 

![Azure Machine Learning CLI](media/cli-for-azure-machine-learning/flow.png)

## <a name="what-you-need-to-get-started"></a>시작에 필요한 항목

모델을 배포할 수 있는 Azure 구독이나 리소스 그룹에 대한 기여자 액세스 권한이 필요합니다. 또한 CLI를 실행하기 위해 Azure Machine Learning Workbench를 설치해야 합니다. 

>[!IMPORTANT]
>Azure Machine Learning 서비스와 함께 제공되는 CLI는 Azure 리소스 관리에 사용되는 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)와 다릅니다.

## <a name="get-and-start-cli"></a>CLI 얻기 및 시작

이 CLI를 얻으려면 다음 위치에서 다운로드할 수 있는 Azure Machine Learning Workbench를 설치하세요.
    + Windows - https://aka.ms/azureml-wb-msi 
    + MacOS - https://aka.ms/azureml-wb-dmg 

CLI를 시작하려면:
+ Azure Machine Learning Workbench의 **파일 -> 명령 프롬프트 열기**에서 CLI를 실행합니다.

## <a name="get-command-help"></a>명령 도움말 가져오기 

명령 뒤에 `--debug` 또는 `--help`를 사용하여(예: `az ml <xyz> --debug`. 여기서 `<xyz>`는 명령 이름) CLI에 대한 추가 정보를 가져올 수 있습니다. 예: 
```azurecli
az ml computetarget --debug 

az ml experiment --help
```

## <a name="common-cli-tasks-for-azure-machine-learning"></a>Azure Machine Learning에 대한 일반 CLI 작업 

이 섹션에서는 다음을 포함하여 CLI로 수행할 수 있는 가장 일반적인 작업에 대해 알아봅니다.
+ [계산 대상 설정](#target)
+ [원격으로 실행할 작업 제출](#jobs)
+ [Jupyter 노트북 작업](#jupyter)
+ [실행 기록과 상호 작용](#history)
+ [작동하도록 환경 구성](#o16n)

<a name="target"></a>

### <a name="set-up-a-compute-target"></a>계산 대상 설정

다음을 포함한 여러 대상에서 기계 학습 모델을 계산할 수 있습니다.
+ 로컬 모드
+ 데이터 과학 VM(DSVM)
+ HDInsight 클러스터

데이터 과학 VM 대상을 연결하려면:
```azurecli
az ml computetarget attach remotedocker -n <target name> -a <ip address or FQDN> -u <username> -w <password>
``` 

HDInsight 대상을 연결하려면:
```azurecli
az ml computetarget attach cluster -n <target name> -a <cluster name, e.g. myhdicluster-ssh.azurehdinsight.net> -u <ssh username> -w <ssh password>
```

**aml_config** 폴더 내에서 conda 종속성을 변경할 수 있습니다. 

또한 PySpark, Python 또는 GPU DSVM의 Python를 작동할 수 있습니다. 

Python 작동 모드를 정의하려면:
+ Python은 `<target name>.runconfig`에서 `Framework:Python` 추가 

+ PySpark은 `<target name>.runconfig`에서 `Framework:PySpark` 추가 

+ GPU DSVM의 Python은
    1. `<target name>.runconfig`에서 `Framework:Python` 추가 

    1. `<target name>.compute`에서 `baseDockerImage: microsoft/mmlspark:plus-gpu-0.9.9 and nvidiaDocker:true` 추가

계산 대상을 준비하려면:
```azurecli
az ml experiment prepare -c <target name>
```

>[!TIP]
>구독을 표시하려면:<br/>
>`az account show`<br/>
>
>구독을 설정하려면:<br/>
>`az account set –s "my subscription name" `

<a name="jobs"></a>

### <a name="submit-remote-jobs"></a>원격 작업 제출

원격 대상에 작업을 제출하려면:
```azurecli
az ml experiment submit -c <target name> myscript.py
```

<a name="jupyter"></a>

### <a name="work-with-jupyter-notebooks"></a>Jupyter 노트북 작업

Jupyter 노트북을 시작하려면:
```azurecli
az ml notebook start
```

이 명령은 로컬 호스트에서 Jupyter 노트북을 시작합니다. 커널 Python 3를 선택하여 로컬로 작업하거나 커널 `<target name>`을 선택하여 원격 VM에서 작업할 수 있습니다.

<a name="history"></a>

### <a name="interact-with-and-explore-the-run-history"></a>실행 기록과 상호 작용 및 탐색

실행 기록을 나열하려면:
```azurecli
az ml history list -o table
```

완료된 실행을 모두 나열하려면:
```azurecli
az ml history list --query "[?status=='Completed']" -o table
```

가장 정확도가 높은 실행을 찾으려면:
```azurecli
az ml history list --query "@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy"
```

또한 각 실행에서 생성된 파일을 다운로드할 수 있습니다. 

출력 폴더에 저장된 모델을 승격하려면:
```azurecli
az ml history promote -r <run id> -ap outputs/model.pkl -n <model name>
```

해당 모델을 다운로드하려면:
```azurecli
az ml asset download -l assets/model.pkl.link -d <model folder path>
```

<a name="o16n"></a>

### <a name="configure-your-environment-to-operationalize"></a>작동하도록 환경 구성

작동 환경을 설정하려면 다음 항목을 만들어야 합니다.

> [!div class="checklist"]
> * 리소스 그룹 
> * 저장소 계정
> * ACR(Azure Container Registry)
> * Application Insights 계정
> * ACS(Azure Container Service) 클러스터의 Kubernetes 배포


Docker 컨테이너에서 테스트할 로컬 배포를 설정하려면:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name>
```

Kubernetes를 사용하여 ACS 클러스터를 설정하려면:
```azurecli
az ml env setup -l <region, e.g. eastus2> -n <env name> -g <resource group name> --cluster
```

배포 상태를 모니터링하려면:
```azurecli
az ml env show -n <environment name> -g <resource group name>
```

사용할 환경을 설정하려면:
```azurecli
az ml env set -n <environment name> -g <resource group name>
```

## <a name="next-steps"></a>다음 단계

다음 문서 중 하나를 시작합니다. 
+ [Azure Machine Learning을 설치하고 시작](../service/quickstart-installation.md)
+ [아이리스 데이터 분류 자습서: 1부](tutorial-classifying-iris-part-1.md)

다음 문서 중 하나를 자세히 살펴봅니다.
+ [모델을 관리하기 위한 CLI 명령](model-management-cli-reference.md)
