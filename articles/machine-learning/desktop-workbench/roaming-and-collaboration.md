---
title: Azure Machine Learning Workbench에서 로밍 및 공동 작업 | Microsoft Docs
description: Azure Machine Learning Workbench에서 로밍 및 공동 작업을 설정하는 방법을 배웁니다.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: b587f5dcc9558ec52b85e4b53dae0e31ad475a4e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Azure Machine Learning Workbench에서 로밍 및 공동 작업
이 문서에서는 Azure Machine Learning Workbench를 사용하여 컴퓨터 간 로밍을 위한 프로젝트를 설정하고 팀원과 공동 작업하는 방법을 설명합니다. 

원격 Git 리포지토리(repo) 링크가 있는 Azure Machine Learning 프로젝트를 만들 때, 프로젝트 메타데이터와 스냅숏은 클라우드에 저장됩니다. 클라우드 링크를 사용하면 다른 컴퓨터에서 프로젝트에 액세스할 수 있습니다(로밍). 또한 팀원에게 프로젝트에 대한 액세스 권한을 제공 하여 팀원과 공동 작업을 수행할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
1. Machine Learning Workbench 앱을 설치합니다. Azure Machine Learning 실험 계정에 액세스할 수 있어야 합니다. 자세한 내용은 [설치 가이드](../service/quickstart-installation.md)를 참조하세요.

2. [Visual Studio Team Services](https://www.visualstudio.com)(Team Services)에 액세스하여 프로젝트를 연결할 리포지토리를 만듭니다. 자세한 내용은 [Machine Learning Workbench 프로젝트와 함께 Git 리포지토리 사용](using-git-ml-project.md)을 참조하세요.

## <a name="create-a-new-machine-learning-project"></a>새 Machine Learning 프로젝트 만들기
Machine Learning Workbench를 열고 새 프로젝트를 만듭니다(예: 아이리스라는 이름의 프로젝트). **Visualstudio.com GIT 리포지토리 URL** 상자에 Team Services Git 리포지토리의 유효한 URL을 입력합니다. 

> [!IMPORTANT]
> 빈 프로젝트 템플릿을 선택하는 경우 사용하기로 선택한 Git 리포지토리에 master 분기가 이미 있을 수도 있습니다. Machine Learning은 단순히 master 분기를 로컬로 복제합니다. aml_config 폴더와 다른 프로젝트 메타데이터 파일을 로컬 프로젝트 폴더에 추가합니다. 
>
> 다른 프로젝트 템플릿을 선택하면 Git 리포지토리에 master 분기가 있을 수 *없습니다*. 그런 경우 오류가 표시됩니다. 대안은 `az ml project create` 명령을 `--force` 스위치와 함께 사용하여 프로젝트를 만드는 것입니다. 이렇게 하면 원래 master 분기의 파일이 삭제되고 선택한 템플릿의 새 파일로 대체됩니다.

프로젝트를 만들었다면 프로젝트 안에 있는 아무 스크립트를 몇 차례 실행해 봅니다. 이 작업은 원격 Git 리포지토리의 실행 기록 분기로 프로젝트 상태를 커밋합니다. 

> [!NOTE] 
> 스크립트만 실행 기록 분기에 대한 트리거 커밋을 실행합니다. 데이터 준비 실행 및 Notebook 실행은 실행 기록 분기에서 프로젝트 스냅숏을 트리거하지 않습니다.

Git 인증을 설정한 경우 master 분기에서 작동할 수 있습니다. 또는 새 분기를 만들 수 있습니다. 

예: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>로밍
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>두 번째 컴퓨터에서 Machine Learning Workbench 열기
Team Services Git 리포지토리가 프로젝트와 연결되면 Machine Learning Workbench가 설치한 모든 컴퓨터에서 아이리스 프로젝트에 액세스할 수 있습니다. 

다른 컴퓨터의 아이리스 프로젝트에 액세스하려면 프로젝트를 만들 때 사용한 것과 동일한 자격 증명으로 앱에 로그인해야 합니다. 또한 동일한 Machine Learning 실험 계정 및 작업 영역에 있어야 합니다. 아이리스 프로젝트는 작업 영역의 다른 프로젝트와 함께 사전순으로 나열됩니다. 

### <a name="download-the-project-on-a-second-computer"></a>두 번째 컴퓨터에서 프로젝트 다운로드
두 번째 컴퓨터에서 작업 영역이 열릴 때 아이리스 프로젝트 옆의 아이콘은 일반적인 폴더 아이콘과는 다릅니다. 다운로드 아이콘은 프로젝트의 콘텐츠가 클라우드에 있으며 프로젝트를 현재 컴퓨터로 다운로드할 수 있다는 의미입니다. 

![프로젝트 만들기](./media/roaming-and-collaboration/downloadable-project.png)

다운로드를 시작하려면 아이리스 프로젝트를 선택합니다. 다운로드가 완료되면 두 번째 컴퓨터에서 프로젝트에 액세스할 수 있습니다. 

Windows에서는 프로젝트 위치가 C:\Users\\<username\>\Documents\AzureML입니다.

macOS에서는 프로젝트 위치가 /home/\<username\>/Documents/AzureML입니다.

향후 릴리스에서는 대상 폴더를 직접 선택할 수 있도록 기능을 개선할 계획입니다. 

> [!NOTE]
> Machine Learning 디렉터리에 프로젝트와 정확히 같은 이름을 가진 폴더가 있는 경우 다운로드가 실패합니다. 이 문제를 해결하려면 일시적으로 기존 폴더를 이름을 바꿉니다.


### <a name="work-on-the-downloaded-project"></a>다운로드한 프로젝트에서 작업 
새로 다운로드한 프로젝트는 프로젝트에서 마지막 실행한 때의 프로젝트 상태를 반영합니다. 실행을 제출할 때마다 프로젝트 상태의 스냅숏이 Team Services Git 리포지토리에 있는 실행 기록 분기에 자동으로 커밋됩니다. 가장 최근 실행과 연결된 스냅숏을 사용하여 두 번째 컴퓨터의 프로젝트를 인스턴스화합니다. 
 

## <a name="collaboration"></a>공동 작업
Team Services Git 리포지토리에 연결된 프로젝트를 팀원과 공동 작업할 수 있습니다. Machine Learning 실험 계정, 작업 영역 및 프로젝트에 대한 권한을 사용자에게 할당할 수 있습니다. 현재는 Azure CLI를 사용하여 Azure Resource Manager 명령을 수행할 수 있습니다. [Azure Portal](https://portal.azure.com)을 사용할 수도 있습니다. 자세한 내용은 [Azure Portal을 사용하여 사용자 추가](#portal)를 참조하세요.    

### <a name="use-the-command-line-to-add-users"></a>명령줄을 사용하여 사용자 추가
예를 들어 Alice는 아이리스 프로젝트의 소유자입니다. Alice는 Bob과 프로젝트에 대한 액세스를 공유하려고 합니다. 

Alice는 **파일** 메뉴를 선택한 다음 **명령 프롬프트** 메뉴 항목을 선택합니다. 아이리스 프로젝트와 함께 명령 프롬프트 창이 열립니다. Alice는 Bob에게 제공할 액세스 수준을 결정할 수 있습니다. 그녀는 다음 명령을 실행하여 권한을 부여합니다.  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

직접 또는 상속을 통해 역할이 할당되면 Bob은 Machine Learning Workbench 프로젝트 목록에서 프로젝트를 볼 수 있습니다. Bob이 프로젝트를 보려면 응용 프로그램을 다시 시작해야 할 수도 있습니다. 그런 후 Bob은 [로밍](#roaming)에 설명된 대로 프로젝트를 다운로드하고 Alice와 공동 작업을 시작할 수 있습니다. 

프로젝트를 공동 작업하는 모든 사용자의 실행 기록은 동일한 원격 Git 리포지토리로 커밋됩니다. Alice가 실행할 때 Bob은 Machine Learning Workbench 앱의 프로젝트 실행 기록 섹션에서 해당 실행을 볼 수 있습니다. 또한 Bob은 Alice가 시작한 실행을 포함하여 프로젝트를 원하는 실행 상태로 복원할 수 있습니다. 

프로젝트에 대한 원격 Git 리포지토리를 공유함으로써 Alice와 Bob은 master 분기도 공동으로 작업할 수 있습니다. 또한 두 사람은 필요한 경우 개인 분기를 만들고 Git 끌어오기 요청 및 병합을 사용하여 공동 작업을 수행할 수 있습니다. 

### <a name="use-the-azure-portal-to-add-users"></a>Azure Portal을 사용하여 사용자 추가
<a name="portal"></a>

Machine Learning 실험 계정, 작업 영역 및 프로젝트는 Azure Resource Manager 리소스입니다. 역할을 할당하려면 **Azure Portal**에서 [액세스 제어](https://portal.azure.com) 링크를 사용하면 됩니다. 

**모든 리소스** 보기를 사용하여 사용자를 추가할 리소스를 찾습니다. **액세스 제어(IAM)** 링크를 선택한 다음 **사용자 추가**를 선택합니다. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>샘플 공동 작업 워크플로
공동 작업 워크플로를 설명하기 위해 예제를 살펴보겠습니다. Contoso의 직원인 Alice와 Bob은 Machine Learning Workbench를 사용하여 데이터 과학 프로젝트에서 공동 작업을 하려고 합니다. 이들의 ID는 동일한 Contoso Azure AD(Azure Active Directory) 테넌트에 속해 있습니다. Alice와 Bob이 수행할 단계는 다음과 같습니다.

1. Alice가 Team Services 프로젝트에 빈 Git 리포지토리를 만듭니다. Team Services 프로젝트는 Contoso Azure AD 테넌트 아래에 생성된 Azure 구독 안에 있어야 합니다. 

2. Alice가 자신의 컴퓨터에 Machine Learning 실험 계정, 작업 영역 및 Machine Learning Workbench 프로젝트를 만듭니다. 그녀는 프로젝트를 만들 때 Team Services Git 리포지토리 URL 입력합니다.

3. Alice가 프로젝트 작업을 시작합니다. 몇 가지 스크립트를 작성하고 몇 가지 실행을 수행합니다. 각각의 실행에 대해 전체 프로젝트 폴더의 스냅숏이 Machine Learning Workbench에서 만든 Team Services Git 리포지토리의 실행 기록 분기에 커밋으로 자동 푸시됩니다.

4. Alice는 진행 중인 작업에 만족합니다. 그녀는 변경 내용을 로컬 master 분기에 커밋하고 Team Services Git 리포지토리 master 분기에 푸시하려고 합니다. 그녀는 프로젝트를 열어 놓은 상태로 Machine Learning Workbench에서 명령 프롬프트 창을 열고 다음 명령을 입력합니다.
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice는 Bob을 작업 영역에 Contributor로 추가합니다. 앞서 보여드린 것처럼, 이 작업은 Azure Portal에서 할 수도 있고 `az role assignment` 명령을 사용하여 할 수도 있습니다. 또한 Alice는 Bob에게 Team Services Git 리포지토리에 대한 읽기/쓰기 권한을 부여합니다.

6. Bob은 자신의 컴퓨터에서 Machine Learning Workbench에 로그인합니다. Alice가 공유한 작업 영역을 볼 수 있습니다. 작업 영역 아래에 나열되는 아이리스 프로젝트를 볼 수 있습니다. 

7. Bob이 프로젝트 이름을 선택합니다. 프로젝트가 그의 컴퓨터에 다운로드됩니다.
    * 다운로드된 프로젝트 파일은 실행 기록에 기록된 최신 실행의 스냅숏 복제본입니다. master 분기의 마지막 커밋은 아닙니다.
    * 로컬 프로젝트 폴더가 master 분기로 설정되고, 변경 내용의 스테이징이 취소됩니다.

8. Bob은 Alice가 수행한 실행을 찾아볼 수 있습니다. 그는 모든 이전 실행의 스냅숏을 복원할 수 있습니다.

9. Bob은 Alice가 최근에 푸시한 변경 내용을 가져온 후 다른 분기에서 작업을 시작하려 합니다. Bob은 Machine Learning Workbench에서 명령 프롬프트 창을 열고 다음 명령을 실행합니다.

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob은 프로젝트를 수정하고 새 실행을 제출합니다. 변경 내용이 bob 분기에 적용됩니다. Bob의 실행은 Alice에게도 보입니다.

11. Bob이 원격 Git 리포지토리에 변경 내용을 푸시할 준비가 완료되었습니다. Alice가 작업 중인 master 분기와의 충돌을 피하기 위해, Bob은 bob이라는 새로운 원격 분기에 자신의 작업을 푸시합니다.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. 자신의 코드에서 알아낸 새로운 요령을 Alice에게 알려주기 위해, Bob은 원격 Git 리포지토리에 bob 분기에서 master 분기로 끌어오기 요청을 만듭니다. 그러면 Alice가 끌어오기 요청을 master 분기에 병합할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Machine Learning Workbench 프로젝트와 함께 Git 리포지토리 사용](using-git-ml-project.md)에 대해 자세히 알아봅니다.
