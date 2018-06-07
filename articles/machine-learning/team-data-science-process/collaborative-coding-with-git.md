---
title: Git를 사용하여 공동 코딩 - Azure Machine Learning | Microsoft Docs
description: Agile 계획과 함께 Git를 사용하여 데이터 과학 프로젝트용 공동 코드 개발을 수행하는 방법입니다.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 55b47362f334d7366b6cc537a96dc7d046c23733
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="collaborative-coding-with-git"></a>Git를 사용하여 공동 코딩

이 문서에서는 Git를 공유 코드 개발 프레임워크로 사용하여 데이터 과학 프로젝트의 공동 코드 개발을 수행하는 방법을 설명합니다. [Agile 개발](agile-development.md) 및 코드 검토를 수행하는 방법에서 계획한 작업이 이러한 코딩 활동을 연결하는 방법을 다룹니다.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Git 분기와 작업 항목 연결 

VSTS는 작업 항목(스토리 또는 작업)을 Git 분기와 연결하는 편리한 방법을 제공합니다. 이렇게 하면 스토리 또는 작업을 연결되는 코드에 직접 연결할 수 있습니다. 

작업 항목을 새 분기에 연결하려면 작업 항목을 두 번 클릭하고, 팝업 창의 **+ 링크 추가** 아래에서 **새 분기 만들기**를 클릭합니다.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

이 새 분기에 대한 정보(예: 분기 이름, 기본 Git 리포지토리 및 분기)를 제공합니다. 선택한 Git 리포지토리는 작업 항목이 속한 동일한 팀 프로젝트 아래에 있는 리포지토리여야 합니다. 기본 분기는 마스터 분기 또는 다른 기존 분기일 수 있습니다.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

각 스토리 작업 항목에 대해 Git 분기를 만드는 것이 좋습니다. 그런 다음 각 작업 작업 항목에 대해 스토리 분기에 따라 분기를 만듭니다. 스토리-작업 관계에 해당하는 분기를 이러한 계층적 방식으로 구성하는 것은 여러 사람이 동일한 프로젝트의 다른 스토리에 대해 작업하거나 동일한 스토리의 다른 작업을 수행하는 경우에 유용합니다. 각 팀 구성원이 다른 분기에서 작업하는 경우 및 분기를 공유할 때 각 구성원이 다른 코드 또는 다른 아티팩트에서 작업하는 경우에 발생하는 충돌을 최소화할 수 있습니다. 

다음 그림에서는 TDSP에 권장되는 분기 전략을 보여 줍니다. 이 그림과 같이 많은 분기가 필요하지 않을 수 있습니다. 특히 한두 명의 사람이 동일한 프로젝트에서 작업하거나 한 사람이 스토리의 모든 작업을 수행할 때 그렇습니다. 그러나 개발 분기와 마스터 분기는 분리하는 것이 항상 좋습니다. 이렇게 하면 릴리스 분기가 개발 작업으로 인해 중단되는 것을 방지할 수 있습니다. Git 분기 모델에 대한 더 자세한 설명은 [성공적인 Git 분기 모델](http://nvie.com/posts/a-successful-git-branching-model/)에서 찾을 수 있습니다.

![3](./media/collaborative-coding-with-git/3-git-branches.png)

작업하려는 분기로 전환하려면 셸 명령(Windows 또는 Linux)에서 다음 명령을 실행합니다. 

    git checkout <branch name>

*<branch name\>* 을 **master**로 변경하면 **마스터** 분기로 다시 전환됩니다. 작업 중인 분기로 전환하면 해당 작업 항목에 대한 작업을 시작하여 항목을 완성하는 데 필요한 코드 또는 문서 아티팩트를 개발할 수 있습니다. 

또한 작업 항목을 기존 분기에 연결할 수도 있습니다. 작업 항목의 **세부 정보** 페이지에서 **새 분기 만들기**를 클릭하는 대신 **+ 링크 추가**를 클릭합니다. 그런 다음 작업 항목을 연결할 분기를 선택합니다. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Git bash 명령으로 새 분기를 만들 수도 있습니다. <base branch name\>이 누락된 경우 <new branch name\>은 _마스터_ 분기를 기반으로 합니다. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>분기에서 작업 및 변경 내용 커밋 

이제 작업 항목(예: 로컬 컴퓨터의 분기에 R 파일 추가)에 대한 *data\_ingestion* 분기를 변경합니다. Git 셸의 해당 분기에 있으면 다음 Git 명령을 사용하여 이 작업 항목에 대한 분기에 추가된 R 파일을 커밋할 수 있습니다.

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>VSTS에서 끌어오기 요청 만들기 

몇 가지 커밋 및 푸시 후에 준비가 되면 현재 분기를 기본 분기에 병합하기 위해 VSTS 서버에서 **끌어오기 요청**을 제출할 수 있습니다. 

팀 프로젝트의 기본 페이지로 이동하고 **코드**를 클릭합니다. 병합할 분기와 분기를 병합하려는 Git 리포지토리 이름을 선택합니다. 그런 다음 분기 작업을 기본 분기에 병합하기 전에 **끌어오기 요청**을 클릭하고 **새 끌어오기 요청**을 클릭하여 끌어오기 요청 검토를 만듭니다.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

이 끌어오기 요청에 대한 설명을 채우고 검토자를 추가하여 외부로 보냅니다.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>검토 및 병합 

끌어오기 요청이 만들어지면 검토자가 끌어오기 요청을 검토하기 위한 전자 메일 알림을 받습니다. 검토자가 변경 내용의 작동 여부를 확인하고, 가능한 경우 요청자의 변경 내용을 테스트해야 합니다. 검토자는 평가에 따라 끌어오기 요청을 승인하거나 거부할 수 있습니다. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

검토가 완료되면 **완료** 단추를 클릭하여 작업 중인 분기를 기본 분기에 병합합니다. 병합한 후에 작업 중인 분기를 삭제하도록 선택할 수 있습니다. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

왼쪽 위 모서리에서 요청이 **완료됨**으로 표시되는지 확인합니다. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

**코드** 아래의 리포지토리로 돌아가면 마스터 분기로 전환되었음을 알 수 있습니다.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

또한 다음 Git 명령을 사용하여 작업 중인 분기를 기본 분기에 병합하고, 병합한 후에 작업 중인 분기를 삭제할 수 있습니다.

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>다음 단계

[데이터 과학 작업 실행](execute-data-science-tasks.md)은 유틸리티를 사용하여 반복 데이터 탐색, 데이터 분석, 보고 및 모델 생성 등과 같은 여러 가지 일반적인 데이터 과학 작업을 완료하는 방법을 보여 줍니다.

**특정 시나리오**에 대한 프로세스의 모든 단계를 보여 주는 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 자료에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 

