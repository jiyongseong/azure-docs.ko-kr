---
title: Team Data Science Process 그룹 관리자 작업 - Azure | Microsoft Docs
description: 데이터 과학 팀 프로젝트의 그룹 관리자 작업에 대한 개요.
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
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 9f9cdd52aff5445f64a4780d6e04590a06c21105
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="group-manager-tasks"></a>그룹 관리자 작업

이 토픽에서는 그룹 관리자가 자신의 데이터 과학 조직을 위해 완료해야 하는 작업에 대해 간략히 설명합니다. 목표는 TDSP([Team Data Science Process](overview.md))를 표준화하는 공동 작업 그룹 환경을 구축하는 것입니다. 이 프로세스를 표준화하는 데이터 과학 팀에서 처리하는 인력 역할 및 관련 작업에 대한 개요는 [Team Data Science Process 역할 및 작업](roles-tasks.md)을 참조하세요.

**그룹 관리자**는 기업 내 전체 데이터 과학 단위의 관리자입니다. 한 데이터 과학 단위에 여러 팀이 있을 수 있고, 각 팀은 고유한 비즈니스 수직 시장에서 여러 데이터 과학 프로젝트를 진행할 수 있습니다. 그룹 관리자는 서로게이트에게 작업을 위임할 수 있지만 역할에 연결된 작업은 동일합니다. 다음 다이어그램에 나와 있는 것처럼 6개의 주요 작업이 있습니다.

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] 아래 지침에서 VSTS를 사용하여 TDSP 그룹 환경을 설정하는 데 필요한 단계를 설명합니다. Microsoft에서 TDSP를 구현하는 방법이기 때문에 VSTS를 사용하여 이러한 작업을 수행하는 방법을 지정합니다. 다른 코드 호스팅 플랫폼이 그룹에 사용되는 경우 그룹 관리자가 완료해야 하는 작업은 일반적으로 변경되지 않습니다. 그러나 이러한 작업을 완료하는 방법은 달라질 것입니다.

1. 그룹의 **VSTS(Visual Studio Team Services) 서버**를 설정합니다.
2. Visual Studio Team Services 서버에서 **그룹 팀 프로젝트**를 만듭니다(VSTS 사용자용).
3. **GroupProjectTemplate** 리포지토리를 만듭니다.
4. **GroupUtilities** 리포지토리를 만듭니다.
5. VSTS 서버용 **GroupProjectTemplate** 및 **GroupUtilities** 리포지토리에 TDSP 리포지토리의 콘텐츠를 시드합니다.
6. GroupProjectTemplate 및 GroupUtilities 리포지토리에 액세스하는 팀 구성원의 **보안 제어**를 설정합니다.

앞의 단계가 구체적으로 설명되어 있습니다. 하지만 먼저 약식 이름을 숙지하고 리포지토리를 사용하기 위한 필수 구성 요소부터 살펴보겠습니다.

### <a name="abbreviations-for-repositories-and-directories"></a>리포지토리 및 디렉터리의 약식 이름

이 자습서에서는 리포지토리 및 디렉터리의 약식 이름을 사용합니다. 이러한 정의를 사용하면 리포지토리와 디렉터리 간의 작업을 쉽게 수행할 수 있습니다. 이 표기법은 다음 섹션에 사용됩니다.

- **G1**: Microsoft TDSP 팀에서 개발하고 관리하는 프로젝트 템플릿 리포지토리입니다.
- **G2**: Microsoft TDSP 팀에서 개발하고 관리하는 유틸리티 리포지토리입니다.
- **R1**: 그룹 관리자가 VSTS 그룹 서버에 설정한 Git의 GroupProjectTemplate 리포지토리입니다.
- **R2**: 그룹 관리자가 VSTS 그룹 서버에 설정한 Git의 GroupUtilities 리포지토리입니다.
- **LG1** 및 **LG2**: 그룹 관리자가 각각 G1 및 G2에 복제하는 컴퓨터의 로컬 디렉터리입니다.
- **LR1** 및 **LR2**: 그룹 관리자가 각각 R1 및 R2에 복제하는 컴퓨터의 로컬 디렉터리입니다.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>리포지토리를 복제하고 시작 및 종료 코드를 확인하기 위한 필수 조건
 
- Git가 컴퓨터에 설치되어야 합니다. DSVM(데이터 과학 Virtual Machine)을 사용하는 경우 Git가 사전 설치되어 있으므로 계속 진행할 수 있습니다. 그렇지 않은 경우 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)을 참조하세요.  
- **Windows DSVM**을 사용하는 경우 컴퓨터에 [GCM(Git Credential Manager)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)이 설치되어 있어야 합니다. README.md 파일에서 **다운로드 및 설치** 섹션으로 스크롤한 다음 *최신 설치 관리자*를 클릭합니다. 이 단계를 수행하면 최신 설치 관리자 페이지로 이동됩니다. 여기에서 .exe 설치 관리자를 다운로드하여 실행합니다. 
- **Linux DSVM**을 사용하는 경우 DSVM에서 SSH 공개 키를 만들고 그룹 VSTS 서버에 추가합니다. SSH에 대한 자세한 내용은 [플랫폼 및 도구 부록](platforms-and-tools.md#appendix)의 **SSH 공개 키 만들기** 섹션을 참조하세요. 


## <a name="1-create-account-on-vsts-server"></a>1. VSTS 서버에 계정 만들기

VSTS 서버는 다음 리포지토리를 호스트합니다.

- **그룹 일반 리포지토리**: 그룹 내 여러 팀에서 여러 데이터 과학 프로젝트에 사용할 수 있는 범용 리포지토리입니다. *GroupProjectTemplate* 및 *GroupUtilities* 리포지토리를 예로 들 수 있습니다.
- **팀 리포지토리**: 그룹 내 특정 팀을 위한 리포지토리입니다. 이러한 리포지토리는 팀의 요구 사항으로 한정되며, 해당 팀에서 실행한 여러 프로젝트에 사용할 수는 있지만 데이터 과학 그룹 내의 여러 팀이 유용하게 사용하기에는 범용성이 충분하지 않습니다. 
- **프로젝트 리포지토리**: 특정 프로젝트에 사용할 수 있는 리포지토리입니다. 이러한 리포지토리는 한 팀에서 수행하는 여러 프로젝트 및 데이터 과학 그룹 내 여러 팀에서 유용하게 사용하기에는 범용성이 충분하지 않을 수 있습니다.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Microsoft 계정에 VSTS 서버 로그인 설정
    
[Visual Studio Online](https://www.visualstudio.com/)으로 이동하고, 오른쪽 위 모서리에서 **로그인**을 클릭하여 Microsoft 계정에 로그인합니다. 
    
![1](./media/group-manager-tasks/login.PNG)

Microsoft 계정이 없는 경우 **지금 등록**을 클릭하여 Microsoft 계정을 만든 다음 이 계정을 사용하여 로그인합니다. 

소속 조직에서 Visual Studio/MSDN을 구독하는 경우 녹색 **회사 또는 학교 계정으로 로그인** 상자를 클릭하고 이 구독과 연결된 자격 증명을 사용하여 로그인합니다. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
로그인 후에는 다음 이미지처럼 오른쪽 위 모서리에서 **새 계정 만들기**를 클릭합니다.
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
다음 값을 사용하여 **계정 만들기** 마법사에서 만들려는 VSTS 서버에 대한 정보를 입력합니다. 

- **서버 URL**: *mysamplegroup*을 해당 *서버 이름*으로 바꿉니다. 서버 URL은 *https://\<servername\>. visualstudio.com* 형식입니다. 
- **코드 관리 앱:** **_Git_** 를 선택합니다.
- **프로젝트 이름:** *GroupCommon*을 입력합니다. 
- **다음을 사용하여 작업 구성:** *Agile*을 선택합니다.
- **프로젝트를 호스트할 위치:** 지역 위치를 선택합니다. 이 예제에서는 *미국 중남부*를 선택합니다. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] **새 계정 만들기**를 클릭한 후 다음 팝업 창이 보이면 **변경 내용 정보**를 클릭하여 항목별로 모든 필드를 표시해야 합니다.

![5](./media/group-manager-tasks/create-account-2.png)


**계속**을 클릭합니다. 

## <a name="2-groupcommon-team-project"></a>2. GroupCommon 팀 프로젝트

VSTS 서버가 생성된 후 **GroupCommon** 페이지(*https://\<servername\>.visualstudio.com/GroupCommon*)가 열립니다.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. GroupUtilities(R2) 리포지토리 만들기

VSTS 서버 아래에서 **GroupUtilities**(R2) 리포지토리를 만들려면:

- **새 리포지토리 만들기** 마법사를 열려면 팀 프로젝트의 **버전 제어** 탭에서 **새 리포지토리**를 클릭합니다. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- *형식*으로 **Git**를 선택하고, *이름*으로 **GroupUtilities**를 입력한 다음 **만들기**를 클릭합니다. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
이제 **버전 제어** 페이지의 왼쪽 열에 두 개의 Git 리포지토리 **GroupProjectTemplate** 및 **GroupUtilities**가 보일 것입니다. 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. GroupProjectTemplate(R1) 리포지토리 만들기

VSTS 그룹 서버용 리포지토리를 설정하는 과정은 다음 두 작업으로 구성됩니다.

- 기본 **GroupCommon** 리포지토리***GroupProjectTemplate*** 이름을 변경합니다.
- 팀 프로젝트 **GroupCommon** 아래에서 VSTS 서버에 **GroupUtilities** 리포지토리를 만듭니다. 

첫 번째 작업에 대한 지침은 명명 규칙에 대한 설명 또는 리포지토리 및 디렉터리 이후 섹션에 포함되어 있습니다. 두 번째 작업에 대한 지침은 4단계에 대한 다음 섹션에 포함되어 있습니다.

### <a name="rename-the-default-groupcommon-repository"></a>기본 GroupCommon 리포지토리 이름 바꾸기

기본 **GroupCommon** 리포지토리 이름을 *GroupProjectTemplate*으로 바꾸려면(이 자습서에서는 **R1**이라고 함):
    
- **GroupCommon** 팀 프로젝트 페이지에서 **코드에 대해 공동 작업**을 클릭합니다. 그러면 팀 프로젝트 **GroupCommon**의 기본 Git 리포지토리 페이지로 이동됩니다. 현재 이 Git 리포지토리는 비어 있습니다. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- **GroupCommon**의 Git 리포지토리 페이지에서 왼쪽 위 모서리에 있는 **GroupCommon**(다음 그림에서 빨간색 상자로 강조 표시된)을 클릭하고, **리포지토리 관리**(다음 그림에서 녹색 상자로 강조 표시된)를 선택합니다. 이 프로시저를 수행하면 **제어판**이 표시됩니다. 
- 팀 프로젝트의 **버전 제어** 탭을 선택합니다. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- 왼쪽 패널의 **GroupCommon** 리포지토리 오른쪽에 있는 **...** 를 클릭하고 **리포지토리 이름 바꾸기**를 선택합니다. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- 나타나는 **GroupCommon 리포지토리 이름 바꾸기** 마법사에서 **리포지토리 이름** 상자에 *GroupProjectTemplate*을 입력한 다음 **이름 바꾸기**를 클릭합니다. 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. VSTS 서버에 R1 및 R2 리포지토리 시드

본 프로시저의 이번 단계에서는 이전 섹션에서 설정한 *GroupProjectTemplate*(R1) 및 *GroupUtilities*(R2) 리포지토리를 시드합니다. 이러한 리포지토리는 Microsoft에서 관리하는 Team Data Science Process용 ***ProjectTemplate***(**G1**) 및 ***Utilities***(**G2**) 리포지토리로 시드됩니다. 이 시드가 완료되면:

- R1 리포지토리는 G1과 동일한 디렉터리 및 문서 템플릿 집합을 사용하게 됩니다.
- R2 리포지토리는 Microsoft에서 개발한 데이터 과학 유틸리티 집합을 포함하게 됩니다.

시드 프로시저에서는 로컬 DSVM의 디렉터리를 중간 스테이징 사이트로 사용합니다. 다음은 이 섹션에서 이어지는 단계입니다.

- G1 및 G2 - LG1 및 LG2로 복제
- R1 및 R2 - LR1 및 LR2로 복제
- LG1 및 LG2 - LR1 및 LR2로 파일 복사
- (선택 사항) LR1 및 LR2 사용자 지정
- LR1 및 LR2 - R1 및 R2에 콘텐츠 추가


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>로컬 DSVM에 G1 및 G2 리포지토리 복제

이 단계에서는 TDSP github 리포지토리의 TDSP(Team Data Science Process) ProjectTemplate 리포지토리(G1) 및 Utilities(G2)를 로컬 DSVM의 폴더에 LG1 및 LG2로 복제합니다.

- 리포지토리의 모든 복제본을 호스트하는 루트 디렉터리 역할을 할 디렉터리를 만듭니다. 
    -  Windows DSVM에서 *C:\GitRepos\TDSPCommon* 디렉터리를 만듭니다. 
    -  Linux DSVM에서 홈 디렉터리에 *GitRepos\TDSPCommon* 디렉터리를 만듭니다. 

- *GitRepos\TDSPCommon* 디렉터리에서 다음 명령 집합을 실행합니다.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- 축약된 리포지토리 이름을 사용하면 다음과 같은 스크립트가 작성됩니다. 
    - G1 - LG1에 복제
    - G2 - LG2에 복제
- 복제가 완료되면 **GitRepos\TDSPCommon** 디렉터리 아래에 두 디렉터리 _ProjectTemplate_ 및 _Utilities_가 보일 것입니다. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>로컬 DSVM에 R1 및 R2 리포지토리 복제

이 단계에서는 GroupProjectTemplate 리포지토리(R1) 및 GroupUtilities 리포지토리(R2)를 DSVM의 **GitRepos\GroupCommon** 아래에 있는 로컬 디렉터리(각각 LR1 및 LR2)에 복제합니다.

- R1 및 R2 리포지토리의 URL을 가져오려면 VSTS에서 **GroupCommon** 홈페이지로 이동합니다. 일반적으로 URL *https://\<VSTS 서버 이름\>.visualstudio.com/GroupCommon*이 붙습니다. 
- **코드**를 클릭합니다. 
- **GroupProjectTemplate** 및 **GroupUtilities** 리포지토리를 선택합니다. 다음 스크립트에 사용할 수 있도록 **복제 URL** 요소의 각 URL을 차례대로 복사 및 저장합니다(HTTPS는 Windows용, SSH는 Linux용).  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Windows 또는 Linux DSVM에서 **GitRepos\GroupCommon** 디렉터리로 변경하고 다음 명령 집합 중 하나를 실행하여 R1 및 R2를 해당 디렉터리로 복제합니다.
        
다음은 Windows 및 Linux 스크립트입니다.

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] LR1 및 LR2가 비어 있다는 경고 메시지를 받게 될 것입니다.    

- 축약된 리포지토리 이름을 사용하면 다음과 같은 스크립트가 작성됩니다. 
    - R1 - LR1에 복제
    - R2 - LR2에 복제   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>GroupProjectTemplate(LR1) 및 GroupUtilities(LR2) 복제

다음으로, 로컬 컴퓨터에서 GitRepos\TDSPCommon의 ProjectTemplate 및 Utilities 디렉터리 콘텐츠(.git 디렉터리의 메타데이터 제외)를 **GitRepos\GroupCommon**의 GroupProjectTemplate 및 GroupUtilities 디렉터리에 복사합니다. 이 단계에서는 다음 두 작업을 완료해야 합니다.

- GitRepos\TDSPCommon\ProjectTemplate(**LG1**)의 파일을 GitRepos\GroupCommon\GroupProjectTemplate(**LR1**)에 복사 
- GitRepos\TDSPCommon\Utilities(**LG2**)의 파일을 GitRepos\GroupCommon\Utilities(**LR2**)에 복사 

이 두 작업을 수행하려면 PowerShell 콘솔(Windows) 또는 셸 스크립트 콘솔(Linux)에서 다음 스크립트를 실행합니다. LG1, LR1, LG2 및 LR2의 완전한 경로를 입력하라는 메시지가 표시됩니다. 입력하는 경로의 유효성이 검사됩니다. 존재하지 않는 디렉터리를 입력하면 경로를 다시 입력하라는 메시지가 표시됩니다. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

이제 LG1 및 LG1 디렉터리의 파일(.git 디렉터리의 파일 제외)이 각각 LR1 및 LR2로 복사된 것을 볼 수 있습니다.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
이제 두 폴더의 파일(.git 디렉터리의 파일 제외)이 각각 GroupProjectTemplate 및 GroupUtilities에 복사된 것을 볼 수 있습니다.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- 축약된 리포지토리 이름을 사용하면 다음과 같은 스크립트가 작성됩니다.
    - LG1 - LR1으로 파일 복사
    - LG2 - LR2로 파일 복사

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>LR1 및 LR2의 콘텐츠를 사용자 지정하는 옵션
    
그룹의 특정 요구 사항을 충족하도록 LR1 및 LR2의 콘텐츠를 사용자 지정하려는 경우 본 프로시저의 이번 단계에서 수행하는 것이 적합합니다. 템플릿 문서를 수정하고, 디렉터리 구조체를 변경하고, 그룹에서 개발한 또는 전체 그룹에 유용한 기존 유틸리티를 추가할 수 있습니다. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>LR1 및 LR2의 콘텐츠를 그룹 서버의 R1 및 R2에 추가

이제 LR1 및 LR2의 콘텐츠를 R1 및 R2 리포지토리에 추가해야 합니다. 다음은 Windows PowerShell 또는 Linux에서 실행할 수 있는 git bash 명령입니다. 

GitRepos\GroupCommon\GroupProjectTemplate 디렉터리에서 다음 명령을 실행합니다.

    git status
    git add .
    git commit -m"push from DSVM"
    git push

-m 옵션을 사용하면 git 커밋에 대한 메시지를 설정할 수 있습니다.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

그룹의 VSTS 서버에서 GroupProjectTemplate 리포지토리의 파일이 즉시 동기화되는 것을 볼 수 있습니다.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

마지막으로, **GitRepos\GroupCommon\GroupUtilities** 디렉터리로 변경하고 동일한 git bash 명령 집합을 실행합니다.

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Git 리포지토리에 처음으로 커밋하는 경우 `git commit` 명령을 실행하기 전에 전역 매개 변수 *user.name* 및 *user.email*을 구성해야 합니다. 다음 두 명령을 실행합니다.
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>여러 Git 리포지토리에 커밋하는 경우 각 Git 리포지토리에 커밋할 때 동일한 이름과 이메일 주소를 사용합니다. 동일한 이름과 이메일 주소를 사용하면 나중에 여러 리포지토리에서 Git 활동을 추적하기 위해 PowerBI 대시보드를 빌드할 때 편리합니다.


- 축약된 리포지토리 이름을 사용하면 다음과 같은 스크립트가 작성됩니다.
    - LR1 - R1에 콘텐츠 추가
    - LR2 - R2에 콘텐츠 추가

## <a name="6-add-group-members-to-the-group-server"></a>6. 그룹 서버에 그룹 구성원 추가

그룹 VSTS 서버의 홈페이지에서 오른쪽 상단의 사용자 이름 옆에 있는 **톱니 바퀴 아이콘**을 클릭하고 **보안** 탭을 선택합니다. 여기서 다양한 권한을 사용하여 그룹에 구성원을 추가할 수 있습니다.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>다음 단계

다음은 Team Data Science Process에서 정의한 역할 및 작업에 대한 자세한 설명으로 연결되는 링크입니다.

- [데이터 과학 팀에 대한 그룹 관리자 작업](group-manager-tasks.md)
- [데이터 과학 팀에 대한 팀 리더 작업](team-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 리더 작업](project-lead-tasks.md)
- [데이터 과학 팀에 대한 프로젝트 개별 참가자 작업](project-ic-tasks.md)