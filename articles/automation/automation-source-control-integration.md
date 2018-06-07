---
title: Azure Automation에서 원본 제어 통합
description: 이 문서에서는 Azure Automation에서 GitHub를 사용하는 원본 제어 통합을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a75167b6973d5cce8497c07e21d17ad071290f1d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation에서 원본 제어 통합
원본 제어 통합을 사용하면 GitHub 원본 제어 리포지토리에 Automation 계정의 Runbook을 연결할 수 있습니다. 원본 제어를 사용하면 팀과 쉽게 공동 작업하고 변경 내용을 추적하며 이전 버전의 Runbook으로 롤백할 수 있습니다. 예를 들어 원본 제어를 사용하면 개발, 테스트 또는 프로덕션 Automation 계정에 원본 제어의 여러 분기를 동기화할 수 있어 개발 환경에서 테스트된 코드를 프로덕션 Automation 계정 수준으로 쉽게 올릴 수 있습니다.

원본 제어를 사용하면 Azure Automation에서 원본 제어로 코드를 푸시하거나 원본 제어에서 Azure Automation으로 Runbook을 끌어올 수 있습니다. 이 문서에서는 Azure Automation 환경에서 원본 제어를 설치하는 방법을 설명합니다. Azure Automation을 구성하여 GitHub 리포지토리에 액세스하고, 원본 제어 통합을 통해 수행할 수 있는 다양한 작업을 단계별로 안내합니다. 

> [!NOTE]
> 원본 제어는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 뿐만 아니라 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks)의 끌어오기 및 밀어넣기를 지원합니다. [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)은 아직 지원되지 않습니다.<br><br>
> 
> 

이미 GitHub 계정이 있는 경우 Automation 계정에 대해 원본 제어를 구성하는 데 필요한 간단한 두 가지 단계가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

## <a name="step-1--create-a-github-repository"></a>1단계 - GitHub 리포지토리 만들기
이미 GitHub 계정과 Azure Automation에 연결하려는 리포지토리가 있는 경우 기존 계정에 로그인하고 아래의 2단계부터 시작합니다. 그렇지 않은 경우 [GitHub](https://github.com/)로 이동하여 새 계정을 등록하고 [새 리포지토리를 만듭니다](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>2단계 - Azure Automation에서 원본 제어 설정
1. Azure Portal의 Automation 계정 페이지의 **계정 설정** 아래에서 **원본 제어**를 클릭합니다. 
   
1. **원본 제어** 페이지가 열리며, 여기서 GitHub 계정 세부 정보를 구성할 수 있습니다. 아래는 구성할 매개 변수의 목록입니다.  
   
   | **매개 변수** | **설명** |
   |:--- |:--- |
   | 원본 선택 |원본을 선택합니다. 현재는 **GitHub** 만 지원됩니다. |
   | 권한 부여 |**권한 부여** 단추를 클릭하여 GitHub 리포지토리에 Azure Automation 액세스 권한을 부여합니다. 이미 다른 창에서 GitHub 계정에 로그인했다면 해당 계정의 자격 증명이 사용됩니다. 권한이 성공적으로 부여되면 페이지의 **권한 부여 속성** 아래에서 GitHub 사용자 이름이 표시됩니다. |
   | 리포지토리 선택 |사용 가능한 리포지토리 목록에서 GitHub 리포지토리를 선택합니다. |
   | 분기 선택 |사용 가능한 분기 목록에서 분기를 선택합니다. 분기를 만들지 않은 경우에는 **마스터** 분기만 표시됩니다. |
   | Runbook 폴더 경로 |Runbook 폴더 경로는 코드를 푸시하거나 끌어오려는 GitHub 리포지토리에 경로를 지정합니다. **/foldername/subfoldername**형식으로 입력되어야 합니다. Runbook 폴더 경로의 Runbook만 Automation 계정에 동기화됩니다. Runbook 폴더 경로의 하위 폴더에 있는 Runbook은 동기화되지 **않습니다** . **/** 를 사용하여 리포지토리 아래의 모든 Runbook을 동기화합니다. |
3. 예를 들어 **RootFolder**라는 폴더를 포함하는 **PowerShellScripts**라는 리포지토리가 있는 경우 이는 **SubFolder**라는 폴더를 포함합니다. 다음 문자열을 사용하여 각 폴더 수준을 동기화할 수 있습니다.
   
   1. **리포지토리**에서 Runbook을 동기화하려면 Runbook 폴더 경로는 */*
   2. **RootFolder**에서 Runbook을 동기화하려면 Runbook 폴더 경로는 */RootFolder*
   3. **SubFolder**에서 Runbook을 동기화하려면 Runbook 폴더 경로는 */RootFolder/SubFolder*입니다.
4. 매개 변수를 구성하면 **원본 제어 설정**페이지에서 매개 변수가 표시됩니다.  
   
    ![원본 제어 구성 페이지](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. **확인**을 클릭하면 이제 Automation 계정에 대한 원본 제어 통합이 구성되며 GitHub 정보로 업데이트되어야 합니다. 이 부분을 클릭하여 모든 원본 제어 동기화 작업 기록을 볼 수 있습니다.  
   
    ![리포지토리 값](media/automation-source-control-integration/automation_03_RepoValues.png)
6. 원본 제어를 설치한 후에 다음 Automation 리소스는 Automation 계정에 만들어집니다.  
   두 개의 [변수 자산](automation-variables.md)이 만들어집니다.  
   
   * **Microsoft.Azure.Automation.SourceControl.Connection** 변수는 아래와 같이 연결 문자열의 값을 포함합니다.  
     
     | **매개 변수** | **값** |
     |:--- |:--- |
     | Name |Microsoft.Azure.Automation.SourceControl.Connection |
     | type |문자열 |
     | 값 |{"Branch":\<*분기 이름*>,"RunbookFolderPath":\<*Runbook 폴더 경로*>,"ProviderType":\<*GitHub에 대한 값 1을 가짐*>,"Repository":\<*리포지토리 이름*>,"Username":\<*GitHub 사용자 이름*>} |

    * **Microsoft.Azure.Automation.SourceControl.OAuthToken**변수는 OAuthToken의 안전하고 암호화된 값을 포함합니다.  

    |**매개 변수**            |**값** |
    |:---|:---|
    | Name  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | type | 알수 없음(암호화됨) |
    | 값 | <*암호화된 OAuthToken*> |  

    ![variables](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Automation 원본 제어**는 GitHub 계정에 권한이 부여된 응용 프로그램으로 추가됩니다. 응용 프로그램을 보려면 GitHub 홈페이지에서 **프로필** > **설정** > **응용 프로그램**으로 이동합니다. 이 응용 프로그램을 사용하면 Azure Automation이 Automation 계정에 GitHub 리포지토리를 동기화할 수 있습니다.  

    ![Git 응용 프로그램](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>자동화에서 원본 제어 사용
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Azure Automation에서 원본 제어로 Runbook 체크 인
Runbook 체크 인을 사용하면 Azure Automation에서 Runbook에 대한 변경 내용을 원본 제어 리포지토리로 푸시할 수 있습니다. Runbook을 체크 인하는 단계는 다음과 같습니다.

1. Automation 계정에서 [새 텍스트 Runbook 만들기](automation-first-runbook-textual.md) 또는 [기존 텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 수행합니다. 이 runbook은 PowerShell 워크플로 또는 PowerShell 스크립트 runbook일 수 있습니다.  
2. Runbook을 편집한 후에 저장하고 **편집** 페이지에서 **체크 인**을 클릭합니다.  
   
    ![체크 인 단추](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Azure Automation의 체크 인은 현재 원본 제어에 있는 코드를 덮어씁니다. 체크 인에 해당하는 Git 명령줄 명령은 **git add + git commit + git push**입니다.  

1. **체크 인**을 클릭하면 확인메시지가 표시되고 **예**를 클릭하여 계속합니다.  
   
    ![체크 인 메시지](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. 체크 인은 원본 제어 Runbook을 시작합니다. **Sync-MicrosoftAzureAutomationAccountToGitHubV1** 이 runbook은 GitHub에 연결하고 Azure Automation에서 리포지토리에 변경 내용을 푸시합니다. 체크 인 작업 기록을 보려면 **원본 제어 통합** 탭으로 돌아가서 [리포지토리 동기화] 페이지를 클릭하여 엽니다. 이 페이지에는 모든 원본 제어 작업이 표시됩니다.  보려는 작업을 선택하고 클릭하여 세부 정보를 봅니다.  
   
    ![체크 인 Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > 원본 제어 Runbook은 보거나 편집할 수 없는 특별한 Automation Runbook입니다. Runbook 목록에 표시되지는 않지만 작업 목록에 표시되는 동기화 작업을 확인할 수 있습니다.
   > 
   > 
3. 수정된 Runbook의 이름은 체크 인된 Runbook의 입력 매개 변수로 전송됩니다. **리포지토리 동기화** 페이지에서 Runbook을 펼쳐 [작업 세부 정보를 확인](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)할 수 있습니다.  
   
    ![체크 인 입력](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. 변경 내용을 보기 위해 작업이 완료되면 GitHub 리포지토리를 새로 고칩니다.  커밋 메시지(**Azure Automation에서 *Runbook 이름*을 업데이트했습니다**)가 있는 커밋이 리포지토리에 있어야 합니다.  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>원본 제어에서 Azure Automation에 runbook 동기화
[리포지토리 동기화] 페이지의 동기화 단추를 사용하면 리포지토리의 Runbook 폴더 경로에 있는 모든 Runbook을 Automation 계정으로 끌어올 수 있습니다. 동일한 리포지토리는 둘 이상의 Automation 계정에 동기화될 수 있습니다. Runbook을 동기화하는 단계는 아래와 같습니다.

1. 원본 제어를 설정한 Automation 계정에서 **원본 제어 통합/리포지토리 동기화** 페이지를 열고 **동기화**를 클릭합니다.  확인 메시지가 표시되면 **예**를 클릭하여 계속합니다.  
   
    ![동기화 단추](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. 동기화는 Runbook을 시작합니다. **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. 이 Runbook은 GitHub에 연결하고 리포지토리에서 Azure Automation으로 변경 내용을 끌어옵니다. 이 작업에 대한 **리포지토리 동기화** 페이지에 새 작업이 표시됩니다. 동기화 작업에 대한 세부 정보를 보려면 클릭하여 작업 세부 정보 페이지를 엽니다.  
   
    ![동기화 Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > 원본 제어의 동기화는 현재 원본 제어에 있는 **모든** Runbook에 대한 Automation 계정에 현재 존재하는 Runbook의 초안 버전을 덮어씁니다. 동기화에 해당하는 Git 명령줄 명령은 **git pull**입니다.


## <a name="troubleshooting-source-control-problems"></a>원본 제어 문제 해결
체크 인 또는 동기화 작업에 오류가 있으면 작업 상태가 '일시 중단됨'이어야 하고 작업 페이지에서 오류에 대한 자세한 정보를 볼 수 있습니다.  **모든 로그** 부분에서는 해당 작업과 관련된 모든 PowerShell 스트림이 표시됩니다. 이렇게 하면 체크 인 또는 동기화 관련 문제를 해결하는 데 필요한 세부 정보가 제공됩니다. 또한 Runbook을 동기화하거나 체크 인하는 동안 발생한 일련의 작업을 보여 줍니다.  

![AllLogs 이미지](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>원본 제어 연결 끊기
GitHub 계정과의 연결을 끊으려면 [리포지토리 동기화] 페이지를 열고 **연결 해제**를 클릭합니다. 원본 제어 연결을 끊으면 이전에 동기화된 Runbook이 여전히 Automation 계정에 남아 있지만 [리포지토리 동기화] 페이지는 활성화되지 않습니다.  

  ![연결 끊기 단추](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>다음 단계
원본 제어 통합에 대한 자세한 내용은 다음 리소스를 참조하세요.  

* [Azure Automation: Azure Automation에서 원본 제어 통합](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [즐겨 찾는 원본 제어 시스템에 대한 투표](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Azure Automation: Visual Studio Team Services를 사용하여 Runbook 원본 제어 통합](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

