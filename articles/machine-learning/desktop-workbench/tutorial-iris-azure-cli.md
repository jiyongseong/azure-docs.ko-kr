---
title: Azure Machine Learning 미리 보기 기능 - 명령줄 인터페이스에 대한 자습서 | Microsoft Docs
description: 이 자습서에서는 명령줄 인터페이스에서 종단 간 아이리스 분류를 완료하는 데 필요한 모든 단계를 안내합니다.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 10/15/2017
ms.openlocfilehash: 05238c27a5654ae24c619b52d769abbf90b940e7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>자습서: 명령줄 인터페이스를 사용하여 아이리스 분류
Azure Machine Learning 서비스(미리 보기)는 데이터를 준비하고, 실험을 개발하고, 클라우드 범위에서 모델을 배포할 수 있는 전문 데이터 과학자를 위한 종단 간 데이터 과학 및 고급 분석 통합 솔루션입니다.

이 자습서에서는 Azure Machine Learning 미리 보기 기능에서 CLI(명령줄 인터페이스) 도구를 사용하여 다음을 수행하는 방법에 대해 알아봅니다. 
> [!div class="checklist"]
> * 실험 계정 설정 및 작업 영역 만들기
> * 프로젝트 만들기
> * 여러 계산 대상에 실험 제출
> * 학습된 모델 승격 및 등록
> * 새 데이터의 점수를 매기는 웹 서비스 배포

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음이 필요합니다.
- Azure 구독에 대한 액세스 권한 및 해당 구독에 리소스를 만들 수 있는 권한. 
  
  Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- [빠른 시작: Azure Machine Learning 서비스 설치 및 시작](../service/quickstart-installation.md)의 설명에 따라 설치된 Azure Machine Learning Workbench 응용 프로그램. 

  >[!IMPORTANT]
  >이 문서에서 CLI를 사용하여 Azure Machine Learning 서비스 계정을 만들 것이므로 Azure Machine Learning 서비스 계정을 만들지 마십시오.
 
## <a name="getting-started"></a>시작
Azure Machine Learning CLI(명령줄 인터페이스)를 사용하면 종단 간 데이터 과학 워크플로에 필요한 모든 작업을 수행할 수 있습니다. CLI 도구는 다음과 같은 방법으로 액세스할 수 있습니다.

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>옵션 1. Azure ML Workbench 로그인 대화 상자에서 Azure ML CLI 시작
처음으로 Azure ML Workbench를 시작하여 로그인하는 경우와 실험 계정에 아직 액세스할 수 없는 경우에는 다음 화면이 표시됩니다.

![계정이 없음](media/tutorial-iris-azure-cli/no_account_found.png)

대화 상자에서 **명령줄 창** 링크를 클릭하여 명령줄 창을 시작합니다.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>옵션 2. Azure ML Workbench 앱에서 Azure ML CLI 시작
이미 실험 계정에 액세스할 수 있으면 성공적으로 로그인할 수 있습니다. 그런 다음, **파일** --> **명령 프롬프트 열기** 메뉴를 차례로 클릭하여 명령줄 창을 열면 됩니다.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>옵션 3. 임의의 명령줄 창에서 Azure ML CLI를 사용하도록 설정
모든 명령줄 창에서 Azure ML CLI를 사용하도록 설정할 수도 있습니다. 명령 창을 시작하고 다음 명령을 입력하면 됩니다.

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
변경 내용을 영구적으로 유지하려면 Windows의 경우 `SETX`를 사용하고, macOS의 경우 `setenv`를 사용합니다.

>[!TIP]
>앞의 환경 변수를 설정하여 즐겨찾는 터미널 창에서 Azure CLI를 사용할 수 있습니다.

## <a name="step-1-log-in-to-azure"></a>1단계. Azure에 로그인
첫 번째 단계는 AML Workbench 앱(파일 > 명령 프롬프트 열기)에서 CLI를 여는 것입니다. 이렇게 하면 올바른 Python 환경이 준비되고 ML CLI 명령을 사용할 수 있게 됩니다. 

이제 CLI에서 올바른 컨텍스트를 설정하여 Azure 리소스에 액세스하고 관리할 수 있습니다.
 
```azure-cli
# log in
$ az login

# list all subscriptions
$ az account list -o table

# set the current subscription
$ az account set -s <subscription id or name>
```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>2단계. 새 Azure Machine Learning 실험 계정 및 작업 영역 만들기

이 단계에서는 새 실험 계정과 새 작업 영역을 만듭니다. 실험 계정 및 작업 영역에 대한 자세한 내용은 [Azure Machine Learning 개념](overview-general-concepts.md)을 참조하세요.

> [!NOTE]
> 실험 계정에는 실험 실행 출력을 저장하는 데 사용되는 저장소 계정이 필요합니다. 저장소 계정 이름은 연결되는 URL이 포함되므로 Azure에서 전역적으로 고유해야 합니다. 기존 저장소 계정을 지정하지 않으면 실험 계정 이름이 새 저장소 계정을 만드는 데 사용됩니다. 고유한 이름을 사용했는지 확인합니다. 그렇지 않으면 _"\<storage_account_name>이라는 저장소 계정이 이미 사용되었습니다."_라는 오류 메시지가 표시됩니다. 또는 `--storage` 인수를 사용하여 기존 저장소 계정을 제공할 수 있습니다.

```azure-cli
# create a resource group 
$ az group create --name <resource group name> --location <supported Azure region>

# create a new experimentation account with a new storage account
$ az ml account experimentation create --name <experimentation account name> --resource-group <resource group name>

# create a new experimentation account with an existing storage account
$ az ml account experimentation create --name <experimentation account name>  --resource-group <resource group name> --storage <storage account Azure Resource ID>

# create a workspace in the experimentation account
az ml workspace create --name <workspace name> --account <experimentation account name> --resource-group <resource group name>
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>2.a단계(선택 사항) 공동 작업자와 작업 영역 공유
여기서는 작업 영역에 대한 액세스를 공동 작업자와 공유하는 방법을 살펴볼 수 있습니다. 실험 계정 또는 프로젝트에 대한 액세스를 공유하는 단계는 동일합니다. Azure 리소스 ID를 가져오는 방법만 업데이트해야 합니다.

```azure-cli
# find the workspace Azure Resource ID
$az ml workspace show --name <workspace name> --account <experimentation account name> --resource-group <resource group name>

# add Bob to this workspace as a new owner
$az role assignment create --assignee bob@contoso.com --role owner --scope <workspace Azure Resource ID>
```

> [!TIP]
> 위 명령의 `bob@contoso.com`은 현재 구독이 속하는 디렉터리의 유효한 Azure AD identity여야 합니다.

## <a name="step-3-create-a-new-project"></a>3단계. 새 프로젝트 만들기
다음 단계는 새 프로젝트를 만드는 것입니다. 새 프로젝트를 시작하는 데는 여러 가지 방법이 있습니다.

### <a name="create-a-new-blank-project"></a>비어 있는 새 프로젝트 만들기

```azure-cli
# create a new project
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path>
```

### <a name="create-a-new-project-with-a-default-project-template"></a>기본 프로젝트 템플릿을 사용하여 새 프로젝트 만들기
기본 템플릿을 사용하여 새 프로젝트를 만들 수 있습니다.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>클라우드 Git 리포지토리와 연결된 새 프로젝트 만들기
VSTS(Visual Studio Team Service) Git 리포지토리와 연결된 새 프로젝트를 만들 수 있습니다. 실험이 제출될 때마다 전체 프로젝트 폴더의 스냅숏이 원격 Git 리포지토리로 커밋됩니다. 자세한 내용은 [Azure Machine Learning Workbench 프로젝트에서 Git 리포지토리 사용](using-git-ml-project.md)을 참조하세요.

> [!NOTE]
> Azure Machine Learning은 VSTS에서 만들어진 빈 Git 리포지토리만 지원합니다.

```azure-cli
$ az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --repo <VSTS repo URL>
```
> [!TIP]
> "리포지토리 URL이 유효하지 않거나 사용자에게 액세스 권한이 없음"이라는 오류가 발생하는 경우, VSTS(_보안_, _개인 액세스 토큰 추가_ 메뉴)에서 보안 토큰을 만들고, 프로젝트를 만들 때 `--vststoken` 인수를 사용할 수 있습니다. 

### <a name="sample_create"></a>샘플에서 새 프로젝트 만들기
이 예제에서는 샘플 프로젝트를 템플릿으로 사용하여 새 프로젝트를 만듭니다.

```azure-cli
# List the project samples, find the Classifying Iris sample
$ az ml project sample list

# Create a new project from the sample
az ml project create --name <project name> --workspace <workspace name> --account <experimentation account name> --resource-group <resource group name> --path <local folder path> --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```
프로젝트를 만든 후 `cd` 명령을 사용하여 프로젝트 디렉터리로 이동합니다.

## <a name="step-4-run-the-training-experiment"></a>4 단계 학습 실험 실행 
아래 단계에서는 아이리스 샘플이 포함된 프로젝트가 있다고 가정합니다([온라인 샘플에서 새 프로젝트 만들기](#sample_create) 참조).

### <a name="prepare-your-environment"></a>환경 준비 
아이리스 샘플의 경우 matplotlib을 설치해야 합니다.

```azure-cli
$ pip install matplotlib
```

###  <a name="submit-the-experiment"></a>실험 제출

```azure-cli
# Execute the file
$ az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>내림차순 정규화 속도로 실험 반복
몇 가지 창의적인 기능을 사용하면 다양한 정규화 속도로 실험을 제출하는 Python 스크립트를 쉽게 준비할 수 있습니다. (올바른 프로젝트 경로를 가리키도록 파일을 편집해야 할 수도 있습니다.)

```azure-cli
$ python run.py
```

## <a name="step-5-view-run-history"></a>5단계. 실행 기록 보기
다음 명령은 실행된 이전 실행을 모두 나열합니다. 

```azure-cli
$ az ml history list -o table
```
위의 명령을 실행하면 이 프로젝트에 속한 모든 실행의 목록이 표시됩니다. 정확도 및 정규화 속도 메트릭도 나열되어 있음을 알 수 있습니다. 이렇게 하면 목록에서 최상의 실행을 쉽게 파악할 수 있습니다.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>5.a단계 지정된 실행으로 만든 첨부 파일 보기 
특정 실행과 관련된 첨부 파일을 보려면 실행 기록의 info 명령을 사용할 수 있습니다. 앞의 목록에서 특정 실행의 실행 ID를 찾습니다.

```azure-cli
$ az ml history info --run <run id> --artifact driver_log
```

실행에서 아티팩트를 다운로드하려면 다음 명령을 사용할 수 있습니다.

```azure-cli
# Stream a given attachment 
$ az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>6단계. 실행의 아티팩트 승격 
실행 중 하나에 더 나은 AUC가 있으므로 이를 사용하여 프로덕션 환경에 배포할 점수 매기기 웹 서비스를 만듭니다. 이렇게 하려면 먼저 아티팩트를 자산으로 승격해야 합니다.

```azure-cli
$ az ml history promote --run <run id> --artifact-path outputs/model.pkl --name model.pkl
```

이렇게 하면 `model.pkl.link` 파일을 포함하는 프로젝트 디렉터리에 `assets` 폴더가 만들어집니다. 이 링크 파일은 승격된 자산을 참조하는 데 사용됩니다.

## <a name="step-7-download-the-files-to-be-operationalized"></a>7단계. 조작 가능한 파일 다운로드
승격된 모델을 사용하여 예측 웹 서비스를 만들 수 있도록 승격된 모델을 다운로드합니다. 

```azure-cli
$ az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-set-up-your-model-management-environment"></a>8단계: 모델 관리 환경 설정 
웹 서비스를 배포할 환경을 만듭니다. Docker를 사용하여 로컬 컴퓨터에서 웹 서비스를 실행할 수 있습니다. 또는 대규모 작업을 위해 ACS 클러스터에 배포합니다. 

```azure-cli
# Create new local operationalization environment
$ az ml env setup -l <supported Azure region> -n <env name>
# Once setup is complete, set your environment for current context
$ az ml env set -g <resource group name> -n <env name>
```

## <a name="step-9-create-a-model-management-account"></a>9단계. 모델 관리 계정 만들기 
모델 관리 계정은 프로덕션 환경에서 모델을 배포하고 추적하는 데 필요합니다. 

```azure-cli
$ az ml account modelmanagement create -n <model management account name> -g <resource group name> -l <supported Azure region>
```

## <a name="step-10-create-a-web-service"></a>10단계. 웹 서비스 만들기
배포한 모델을 사용하여 예측을 반환하는 웹 서비스를 만듭니다. 

```azure-cli
$ az ml service create realtime -m asset_download/model.pkl -f score_iris.py -r python –n <web service name>
```

## <a name="step-11-run-the-web-service"></a>11단계. 웹 서비스 실행
이전 단계의 결과에서 웹 서비스 ID를 사용하여 웹 서비스를 호출하고 테스트합니다. 

```azure-cli
# Get web service usage infomration
$ az ml service usage realtime -i <web service id>

# Call the web service with the run command:
$ az ml service run realtime -i <web service id> -d <input data>
```

## <a name="step-12-deleting-all-the-resources"></a>12단계. 모든 리소스 삭제 
이 자습서를 계속 진행하지 않는다면 만든 모든 리소스를 삭제하여 이 자습서를 완료하겠습니다. 

이렇게 하려면 리소스를 보유하는 리소스 그룹을 삭제합니다. 

```azure-cli
az group delete --name <resource group name>
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Machine Learning을 사용하여 다음을 수행하는 과정을 알아보았습니다. 
> [!div class="checklist"]
> * 실험 계정 설정 및 작업 영역 만들기
> * 프로젝트 만들기
> * 여러 계산 대상에 실험 제출
> * 학습된 모델 승격 및 등록
> * 모델 관리용 모델 관리 계정 만들기
> * 웹 서비스 배포 환경을 만듭니다.
> * 웹 서비스 배포 및 새 데이터로 점수 매기기
