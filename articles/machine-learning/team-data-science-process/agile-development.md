---
title: 데이터 과학 프로젝트의 Agile 개발 - Azure Machine Learning | Microsoft Docs
description: TDSP(Team Data Science Process)를 사용하여 개발자가 프로젝트 팀 내에서 체계적이고 버전을 제어하고 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행할 수 있는 방법.
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
ms.openlocfilehash: 03bc287969559308e743a5b8cc192cddfdc085cc
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="agile-development-of-data-science-projects"></a>데이터 과학 프로젝트의 Agile 개발

이 문서에서는 [TDSP(Team Data Science Process)](overview.md)를 사용하여 개발자가 프로젝트 팀 내에서 체계적이고 버전을 제어하고 공동 작업을 수행하는 방식으로 데이터 과학 프로젝트를 실행하는 방법에 대해 설명합니다. TDSP는 Microsoft에서 개발하여 클라우드 기반 예측 분석 솔루션을 효율적으로 실행하는 일련의 구조화된 작업을 제공하는 프레임워크입니다. 이 프로세스를 표준화하는 데이터 과학 팀에서 처리하는 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요. 

이 문서에는 다음을 수행하는 방법에 대한 지침이 포함되어 있습니다. 

1. 프로젝트와 관련된 작업 항목에 대해 **스프린트 계획**을 수행합니다.<br> 스프린트 계획에 익숙하지 않은 경우 [여기](https://en.wikipedia.org/wiki/Sprint_(software_development) "여기")서 세부 정보와 일반 정보를 찾을 수 있습니다. 
2. 스프린트에 **작업 항목을 추가**합니다. 

> [!NOTE]
> 다음 지침 집합에서는 VSTS(Visual Studio Team Services)를 사용하여 TDSP 팀 환경을 설정하는 데 필요한 단계에 대해 설명합니다. Microsoft에서 TDSP를 구현하는 방법이기 때문에 VSTS를 사용하여 이러한 작업을 수행하는 방법을 지정합니다.  VSTS을 사용하도록 선택하는 경우 위 목록의 (3)과 (4) 항목은 자연스럽게 얻는 이점입니다. 그룹에 다른 코드 호스팅 플랫폼이 사용되는 경우 팀 리더가 수행해야 하는 작업은 일반적으로 변경되지 않습니다. 그러나 이러한 작업을 완료하는 방법은 다를 수 있습니다. 예를 들어 섹션 6 **Git 분기와 작업 항목 연결** 항목은 VSTS에서처럼 쉽지 않을 수 있습니다.
>
>

다음 그림에서는 데이터 과학 프로젝트 구현과 관련된 일반적인 스프린트 계획, 코딩 및 소스 제어 워크플로를 보여 줍니다.

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>용어 

TDSP 스프린트 계획 프레임워크에는 자주 사용되는 네 가지 유형의**작업 항목**, 즉 **기능**, **사용자 스토리**, **작업** 및 **버그**가 있습니다. 각 팀 프로젝트에서 모든 작업 항목에 대한 단일 백로그를 유지 관리합니다. 팀 프로젝트의 Git 리포지토리 수준에는 백로그가 없습니다. 정의는 다음과 같습니다.

- **기능**: 프로젝트 참여에 해당하는 기능입니다. 클라이언트와의 다른 참여는 다른 기능으로 간주됩니다. 마찬가지로 클라이언트가 있는 프로젝트의 다른 단계를 다른 기능으로 간주하는 것이 가장 좋습니다. 기능 이름을 지정하기 위해 ***ClientName-EngagementName***과 같은 스키마를 선택하면 이름 자체에서 프로젝트/참여의 컨텍스트를 쉽게 인식할 수 있습니다.
- **스토리**: 종단 간에 기능(프로젝트)을 완료하는 데 필요한 별도의 작업 항목입니다. 스토리의 예는 다음과 같습니다.
    - 데이터 가져오기 
    - 데이터 탐색 
    - 기능 생성
    - 모델 작성
    - 모델 조작 
    - 모델 재학습
- **작업**: 할당 가능한 코드 또는 문서 작업 항목 또는 특정 스토리를 완료하기 위해 수행해야 하는 다른 작업입니다. 예를 들어 *데이터 가져오기* 스토리의 작업은 다음과 같습니다.
    -  SQL Server 자격 증명 가져오기 
    -  SQL Data Warehouse에 데이터 업로드. 
- **버그**: 일반적으로 작업을 완료할 때 수행되는 기존 코드 또는 문서에 필요한 수정을 참조합니다. 각 스테이지 또는 작업이 누락되어 버그가 발생하는 경우 스토리 또는 작업이 되도록 에스컬레이션할 수 있습니다. 

> [!NOTE]
> 기능, 스토리, 작업 및 버그 개념은 데이터 과학에 사용되는 SCM(소프트웨어 코드 관리)에서 빌려온 것이며, 기존의 SCM 정의와 약간 다를 수 있습니다.
>
>

> [!NOTE]
> 데이터 과학자는 특히 TDSP 수명 주기 단계에 맞게 조정되는 Agile 템플릿을 사용하여 훨씬 편안하게 작업할 수 있습니다. 이 점을 염두에 두고 Agile에서 파생된 스프린트 계획 템플릿이 만들어졌으며 스토리, 대규모 사용자 스토리 등이 TDSP 수명 주기 단계 또는 하위 단계로 대체됩니다. Agile 템플릿을 만드는 방법에 대한 자세한 내용은 [Visual Studio Online에서 Agile 데이터 과학 프로세스 설정](agile-development.md#set-up-agile-dsp-6)을 참조하세요.
>
>

## 2. <a name='SprintPlanning-2'></a>스프린트 계획 

스프린트 계획은 프로젝트 우선 순위 지정 및 리소스 계획 및 할당에 유용합니다. 많은 데이터 과학자들이 여러 프로젝트에 참여하고 있으며 각 프로젝트는 완료하는 데 몇 달이 걸릴 수 있습니다. 프로젝트는 종종 다른 속도로 진행됩니다. VSTS 서버에서 팀 프로젝트의 작업 항목을 쉽게 생성, 관리 및 추적하고 스프린트 계획을 수행하여 프로젝트가 예상대로 진행되도록 할 수 있습니다. 

VSTS의 스프린트 계획에 대한 단계별 지침은 [이 링크](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning)를 참조하세요. 


## 3. <a name='AddFeature-3'></a>기능 추가  

팀 프로젝트 아래에 프로젝트 리포지토리가 만들어지면 팀 **개요** 페이지로 이동하여 **작업 관리**를 클릭합니다.

![2](./media/agile-development/2-sprint-team-overview.png)

백로그에 기능을 포함하려면 **백로그** --> **기능** --> **새로 만들기**를 차례로 클릭하고, 기능 **제목**(일반적으로 프로젝트 이름)을 입력한 다음, **추가**를 클릭합니다.

![3](./media/agile-development/3-sprint-team-add-work.png)

만든 기능을 두 번 클릭합니다. 설명을 입력하고, 팀 구성원에게 이 기능을 할당하고, 이 기능의 계획 매개 변수를 설정합니다. 

또한 이 기능을 프로젝트 리포지토리에 연결할 수도 있습니다. **개발** 섹션 아래에서 **링크 추가**를 클릭합니다. 기능 편집을 완료하면 **저장 후 닫기**를 클릭하여 종료합니다.


## 4. <a name='AddStoryunderfeature-4'></a>기능 아래에서 스토리 추가 

이 기능을 사용하면 프로젝트(기능)를 완료하는 데 필요한 주요 단계를 설명하는 스토리를 추가할 수 있습니다. 새 스토리를 추가하려면 백로그 보기에서 기능의 왼쪽에 있는 **+** 기호를 클릭합니다.  

![4](./media/agile-development/4-sprint-add-story.png)

팝업 창에서 상태, 설명, 주석, 계획 및 우선 순위와 같은 스토리 세부 정보를 편집할 수 있습니다.

![5](./media/agile-development/5-sprint-edit-story.png)

**개발** 아래에서 **+ 링크 추가**를 클릭하여 이 스토리를 기존 리포지토리에 연결할 수 있습니다. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>스토리에 작업 추가 

작업은 각 스토리를 완료하는 데 필요한 구체적인 세부 단계입니다. 스토리의 모든 작업이 완료되면 스토리도 완료됩니다. 

스토리에 작업을 추가하려면 스토리 항목 옆에 있는 **+** 기호를 클릭하고, **작업**을 선택한 다음, 팝업 창에서 이 작업에 대한 자세한 정보를 채웁니다.

![7](./media/agile-development/7-sprint-add-task.png)

기능, 스토리 및 작업을 만들면 **백로그** 또는 **보드** 보기에서 볼 수 있습니다.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Visual Studio Online에서 Agile TDSP 작업 템플릿 설정

이 문서에서는 TDSP 데이터 과학 수명 주기 단계를 사용하고 VSO(Visual Studio Online)으로 작업 항목을 추적하는 Agile 데이터 과학 프로세스 템플릿을 설정하는 방법에 대해 설명합니다. 다음 단계는 데이터 과학 특유의 Agile 프로세스 템플릿 *AgileDataScienceProcess*을 설정하는 예제를 연습하며 템플릿을 기반으로 데이터 과학 작업 항목을 만드는 방법을 보여 줍니다.

### <a name="agile-data-science-process-template-setup"></a>Agile 데이터 과학 프로세스 템플릿 설정

1. 서버 홈 페이지로 이동하고  -> **프로세스**를 구성합니다.

    ![10](./media/agile-development/10-settings.png) 

2. **모든 프로세스** -> **프로세스**로 이동하고 **Agile** 아래에서 **상속된 프로세스 만들기**를 클릭합니다. 그런 다음 프로세스 이름 "AgileDataScienceProcess"을 입력하고 **프로세스 만들기**를 클릭합니다.

    ![11](./media/agile-development/11-agileds.png)

3. **AgileDataScienceProcess** -> **작업 항목 형식** 탭 아래에서 **구성 -> 비활성화**에 의해 **대규모 사용자 스토리**, **기능**, **사용자 스토리** 및 **작업** 작업 항목을 비활성화합니다.

    ![12](./media/agile-development/12-disable.png)

4. **AgileDataScienceProcess** -> **백로그 수준** 탭으로 이동합니다. **구성** -> **편집/이름 변경**을 클릭하여 "대규모 사용자 스토리"를 "TDSP 프로젝트"로 이름 변경합니다. 같은 대화 상자의 "데이터 과학 프로젝트"에서 **+새 작업 항목 종류**를 클릭하고 **기본 작업 항목 종류**의 값을 "TDSP 프로젝트"로 설정합니다. 

    ![13](./media/agile-development/13-rename.png)  

5. 마찬가지로 백로그 이름 "기능"을 "TDSP 단계"로 변경하고 **새 작업 항목 종류**에 다음 내용을 추가합니다.

    - 비즈니스 이해
    - 데이터 취득
    - 모델링
    - 배포

6. 기본 작업 항목 종류를 새로 만든 "TDSP 하위 단계" 형식으로 설정한 상태에서 "사용자 스토리"를 "TDSP 하위 단계"로 이름 변경합니다.

7. "작업"을 새로 만든 작업 항목 종류 "TDSP 태스크"를 설정합니다. 

8. 이러한 단계를 수행한 후 백로그 수준은 다음과 같이 표시됩니다.

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>데이터 과학 작업 항목 만들기

데이터 과학 프로세스 템플릿이 만들어진 후 TDSP 수명 주기에 해당하는 데이터 과학 작업 항목을 만들고 추적할 수 있습니다.

1. 새 팀 프로젝트를 만들 때 **작업 항목 프로세스**로 "Agile\AgileDataScienceProcess"를 선택합니다.

    ![15](./media/agile-development/15-newproject.png)

2. 새로 만든 팀 프로젝트로 이동하고 **작업** -> **백로그**를 클릭합니다.

3. **팀 설정 구성**을 클릭하여 "TDSP 프로젝트"를 볼 수 있게 만들고 "TDSP 프로젝트"를 선택한 다음 저장합니다.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. 이제 데이터 과학 특유의 작업 항목을 만들기 시작할 수 있습니다.

    ![17](./media/agile-development/17-dsworkitems.png)

5. 다음은 데이터 과학 프로젝트 작업 함옥이 표시되는 방법의 예입니다.

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>다음 단계

[Git를 사용하여 공동 코딩](collaborative-coding-with-git.md)은 Git를 공유 코드 개발 프레임워크로 사용하여 데이터 과학 프로젝트에 대한 공동 코드 개발을 수행하는 방법 및 Agile 프로세스를 사용하여 이러한 코딩 작업을 계획된 작업에 연결하는 방법을 설명합니다.

다음은 Agile 프로세스의 리소스에 대한 추가 링크입니다.

- Agile 프로세스 [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Agile 프로세스 작업 항목 형식 및 워크플로 [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


**특정 시나리오**에 대한 프로세스의 모든 단계를 보여 주는 연습도 제공됩니다. 이러한 단계는 [예제 연습](walkthroughs.md) 자료에서 미리 보기 설명과 함께 나열되고 연결되어 있습니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 응용 프로그램을 만드는 방법을 보여 줍니다. 
