---
title: Azure Machine Learning Workbench 프로젝트와 함께 Git 리포지토리 사용 | Microsoft Docs
description: 이 문서는 Azure Machine Learning Workbench 프로젝트와 함께 Git 리포지토리를 사용하는 방법을 설명합니다.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 6d244e990552ac4899361360efb44be140b06fe8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Machine Learning Workbench 프로젝트와 함께 Git 리포지토리 사용
Azure Machine Learning Workbench가 Git을 사용하여 데이터 과학 실험에서 버전 제어 기능을 제공하고 재현 가능성을 보장하는 방법을 알아봅니다. 프로젝트를 클라우드 Git 리포지토리(repo)와 연결하는 방법도 알아봅니다.

Machine Learning Workbench는 Git 통합을 위해 설계되었습니다. 새 프로젝트를 만들 때 프로젝트 폴더는 자동으로 로컬 Git 리포지토리로 "Git 초기화"됩니다. AzureMLHistory/\<프로젝트 GUID\>라는 분기와 함께 두 번째 숨겨진 로컬 Git 리포지토리도 만들어집니다. 분기는 각 실행에 대한 프로젝트 폴더 변경을 추적합니다. 

Azure Machine Learning 프로젝트를 Git 리포지토리와 연결하면 로컬 및 원격으로 자동 버전 제어가 가능합니다. Git 리포지토리는 Team Services(Visual Studio Team Services)에서 호스트됩니다. Machine Learning 프로젝트가 Git 리포지토리와 연결되어 있기 때문에 원격 리포지토리에 액세스할 수 있는 사람은 최신 소스 코드를 다른 컴퓨터에 다운로드할 수 있습니다(로밍).  

> [!NOTE]
> Team Services에는 Azure Machine Learning 실험 서비스와 독립적인 자체 ACL(액세스 제어 목록)이 있습니다. 사용자 액세스는 Git 리포지토리와 Machine Learning 작업 영역 또는 프로젝트 사이에 다를 수 있습니다. 액세스를 관리해야 할 수도 있습니다. 
> 
> 팀 멤버에게 Machine Learning 프로젝트에 대한 코드 수준의 액세스를 부여하거나 작업 영역만 공유하려는 경우 사용자에게 Team Services Git 리포지토리에 액세스할 수 있는 올바른 권한을 부여해야 합니다. 

Git으로 버전 제어를 관리하려면 마스터 분기를 사용하거나 리포지토리에 다른 분기를 만들 수 있습니다. 로컬 Git 리포지토리를 사용할 수 있고, 프로비전된 경우 원격 Git 리포지토리로 푸시할 수도 있습니다.

이 다이어그램에서는 Team Services Git 리포지토리와 Machine Learning 프로젝트 사이의 관계를 보여 줍니다.

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

원격 Git 리포지토리를 사용하려면 다음 섹션에서 설명하는 단계를 완료합니다.

> [!NOTE]
> 현재 Azure Machine Learning은 Team Services 계정에서만 Git 리포지토리를 지원합니다. GitHub와 같은 일반 Git 리포지토리에 대한 지원은 향후 지원될 예정입니다.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>1단계. Machine Learning 실험 계정 만들기
Machine Learning 실험 계정을 만들고 Azure Machine Learning Workbench 앱을 설치합니다. 자세한 내용은 [설치 및 만들기 빠른 시작](../service/quickstart-installation.md)을 참조하세요.

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>2단계. 팀 프로젝트를 만들거나 기존 팀 프로젝트 사용
[Azure Portal](https://portal.azure.com/)에서 새 팀 프로젝트를 만듭니다.
1. **+**를 선택합니다.
2. **팀 프로젝트**를 검색합니다.
3. 필요한 정보를 입력합니다.
    - **이름**: 팀 이름입니다.
    - **버전 제어**: **Git**을 선택합니다.
    - **구독**: Machine Learning Experimentation 계정이 있는 구독을 선택합니다.
    - **위치**: 이상적으로는 Machine Learning 실험 리소스와 가까운 지역을 선택합니다.
4. **만들기**를 선택합니다. 

![Azure Portal에서 팀 프로젝트 만들기](media/using-git-ml-project/create_vsts_team.png)

Azure Machine Learning Workbench에 액세스할 때 사용하는 Azure AD(Azure Active Directory) 계정을 사용하여 로그인합니다. 그렇지 않으면 시스템이 Azure AD 자격 증명을 사용하여 Machine Learning Workbench에 액세스할 수 없습니다. 명령줄을 사용하여 Machine Learning 프로젝트를 만들고 Git 리포지토리에 액세스하기 위해 개인 액세스 토큰을 제공하는 경우는 예외입니다. 이 내용은 문서 뒷부분에서 좀 더 자세히 설명합니다.

만든 팀 프로젝트로 바로 이동하려면 URL, https://\<팀 프로젝트 이름\>.visualstudio.com을 사용합니다.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>3단계. Machine Learning 프로젝트와 Git 리포지토리 설정

Machine Learning 프로젝트를 설정하는 옵션은 두 가지입니다.
- 원격 Git 리포지토리가 있는 Machine Learning 프로젝트 만들기
- 기존 Machine Learning 프로젝트를 Team Services Git 리포지토리와 연결하기

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>원격 Git 리포지토리가 있는 Machine Learning 프로젝트 만들기
Machine Learning Workbench를 열고 새 프로젝트를 만듭니다. **Git 리포지토리** 상자에 2단계의 Team Services Git 리포지토리 URL을 입력합니다. 일반적으로: https://\<Team Services 계정 이름\>.visualstudio.com/_git/\<프로젝트 이름\>과 같은 모양입니다.

![Git 리포지토리가 있는 Machine Learning 프로젝트 만들기](media/using-git-ml-project/create_project_with_git_rep.png)

Azure CLI(Azure 명령줄 인터페이스)를 사용하여 프로젝트를 만들 수도 있습니다. 개인용 액세스 토큰을 입력하는 옵션이 있습니다. Machine Learning은 Azure AD 자격 증명을 사용하는 대신 이 토큰을 사용하여 Git 리포지토리에 액세스할 수 있습니다.

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> 빈 프로젝트 템플릿을 선택한 경우 사용하기로 선택한 Git 리포지토리에 마스터 분기가 이미 있을 수도 있습니다. Machine Learning은 단순히 master 분기를 로컬로 복제합니다. aml_config 폴더와 다른 프로젝트 메타데이터 파일을 로컬 프로젝트 폴더에 추가합니다. 
>
> 다른 프로젝트 템플릿을 선택하면 Git 리포지토리에 master 분기가 있을 수 *없습니다*. 그런 경우 오류가 표시됩니다. 대안은 `az ml project create` 명령을 `--force` 스위치와 함께 사용하여 프로젝트를 만드는 것입니다. 이렇게 하면 원래 master 분기의 파일이 삭제되고 선택한 템플릿의 새 파일로 대체됩니다.

원격 리포지토리 통합이 가능한 새로운 Machine Learning 프로젝트가 만들어집니다. 프로젝트 폴더는 항상 로컬 Git 리포지토리로 Git 초기화됩니다. Git 원격이 원격 Team Services Git 리포지토리로 설정되어 커밋을 원격 Git 리포지토리로 푸시할 수 있습니다.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>기존 Machine Learning 프로젝트를 Team Services Git 리포지토리와 연결하기
Team Services Git 리포지토리 없이 Machine Learning 프로젝트를 만들고 실행 기록 스냅숏을 로컬 Git 리포지토리에 의존할 수 있습니다. 다음 명령을 사용하면 나중에 Team Services Git 리포지토리를 기존 Machine Learning 프로젝트와 연결할 수 있습니다.

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> 연결된 Git 리포지토리가 없는 Machine Learning 프로젝트에서만 리포지토리 업데이트 작업을 수행할 수 있습니다. 또한 Git 리포지토리를 Machine Learning과 연결한 후에는 제거할 수 없습니다.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>4단계. Git 리포지토리에 있는 프로젝트 스냅숏 캡처
프로젝트에서 몇 가지 스크립트를 실행하고 실행 사이에 몇 가지를 변경합니다. 이 작업은 데스크톱 앱이나 Azure CLI에서 `az ml experiment submit` 명령을 사용하여 수행할 수 있습니다. 자세한 내용은 [아이리스 분류 자습서](tutorial-classifying-iris-part-1.md)를 참조하세요. 각 실행에 대해 프로젝트 폴더의 어느 파일에서든 변경 사항이 있는 경우 전체 프로젝트 폴더의 스냅숏이 커밋되고 AzureMLHistory/\<프로젝트 GUID\>라는 분기 아래 원격 Git 리포지토리로 푸시됩니다. AzureMLHistory/\<project GUID\> 분기를 비롯하여 분기 및 커밋을 보려면, Team Services Git 리포지토리 URL로 이동합니다. 

> [!NOTE] 
> 스냅숏은 스크립트 실행 전에만 커밋됩니다. 현재는 데이터 준비 실행 또는 Notebook 셀 실행이 스냅숏을 트리거하지 않습니다.

![실행 기록 분기](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Git 명령을 사용하여 기록 분기에서 작업하지 않는 것이 좋습니다. 그러면 실행 기록과 충돌할 수 있습니다. 대신 마스터 분기를 사용하거나 자체 Git 작업을 위해 다른 분기를 만듭니다.

## <a name="step-5-restore-a-previous-project-snapshot"></a>5단계. 이전 프로젝트 스냅숏 복원 
전체 프로젝트 폴더를 이전 실행 기록 스냅숏 상태로 복원하려면 Machine Learning Workbench에서 다음을 수행합니다.
1. 작업 표시줄(모래 시계 아이콘)에서 **실행**을 선택합니다.
2. **실행 목록** 보기에서 복원하려는 실행을 선택합니다.
3. **실행 정보** 보기에서 **복원**을 선택합니다.

![실행 기록 분기](media/using-git-ml-project/restore_project.png)

필요에 따라 Machine Learning Workbench의 Azure CLI 창에서 다음 명령을 사용할 수 있습니다.

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

이 명령을 실행할 때 주의해야 합니다. 이 명령을 실행하면 특정 실행이 시작될 때 생성된 스냅숏으로 전체 프로젝트 폴더를 덮어쓰게 됩니다. 프로젝트는 현재 분기에 유지됩니다. 즉, 현재 프로젝트 폴더의 **모든 변경 내용을 잃게** 됩니다.  

이 작업을 수행하기 전에 Git을 사용하여 현재 분기에 대한 변경 내용을 커밋할 수 있습니다.

## <a name="step-6-use-the-master-branch"></a>6단계. 마스터 분기 사용
현재 프로젝트 상태를 실수로 잃지 않도록 방지하는 한 가지 방법은 Git 리포지토리의 마스터 분기(또는 직접 만든 분기)에 프로젝트를 커밋하는 것입니다. 마스터 분기에 작동하도록 Git을 명령줄이나 즐겨찾는 Git 클라이언트 도구에서 사용할 수 있습니다. 예: 

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

이제 5단계를 완료하여 프로젝트를 이전 스냅숏으로 안전하게 복원할 수 있습니다. 마스터 분기에서 방금한 커밋 상태로 언제든지 돌아 올 수 있습니다.

## <a name="authentication"></a>인증
Machine Learning의 실행 기록 기능에만 의존하여 프로젝트 스냅숏을 생성하고 복원하는 경우 Git 리포지토리 인증에 대해 걱정할 필요가 없습니다. 인증은 Machine Learning Experiment 서비스 계층에서 처리됩니다.

단, 자체 Git 도구를 사용하여 버전 제어를 관리하는 경우 Team Services의 원격 Git 리포지토리에 대한 인증을 처리해야 합니다. Machine Learning에서 HTTPS 프로토콜을 사용하여 원격 Git 리포지토리가 로컬 리포지토리에 Git 원격으로 추가됩니다. 즉, 원격에 Git 명령(예: 밀어넣기 또는 끌어오기)을 실행할 때 사용자 이름과 암호 또는 개인용 액세스 토큰을 제공해야 합니다. Team Services Git 리포지토리에 개인 액세스 토큰을 만들려면 [개인 액세스 토큰을 사용하여 인증](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate)의 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Team Data Science Process를 사용하여 프로젝트 구조를 구성](how-to-use-tdsp-in-azure-ml.md)하는 방법을 알아봅니다.
