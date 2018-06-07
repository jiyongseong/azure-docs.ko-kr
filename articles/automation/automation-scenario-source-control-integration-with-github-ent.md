---
title: GitHub Enterprise와 Azure Automation 소스 제어 통합
description: Automation runbook의 소스 제어를 위해 GitHub Enterprise와의 통합을 구성하는 방법을 자세히 설명합니다.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685d434affd0561658ae99c50bbe7b1fc27a5572
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195525"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure Automation 시나리오 - GitHub Enterprise와 Automation 소스 제어 통합

Automation은 현재 GitHub 소스 제어 리포지토리에 Automation 계정의 Runbook을 연결할 수 있도록 하는 소스 제어 통합을 지원합니다. 그러나 DevOps 작업을 지원하기 위해 [GitHub Enterprise](https://enterprise.github.com/home) 배포한 고객은 비즈니스 프로세스 및 서비스 관리 작업을 자동화하도록 개발된 runbook의 수명 주기를 관리하는 데도 이 기능을 사용하려고 할 수 있습니다.

이 시나리오에서는 Azure Resource Manager 모듈 및 Git 도구가 설치된 Hybrid Runbook Worker로 구성된 데이터 센터에 Windows 컴퓨터가 있습니다. 하이브리드 작업자 컴퓨터에는 로컬 Git 리포지토리의 복제본이 있습니다. Runbook이 Hybrid Worker에서 실행될 때 Git 디렉터리가 동기화되고 runbook 파일 내용이 Automation 계정으로 전달됩니다.

이 문서에서는 Azure Automation 환경에서 이러한 구성을 설정하는 방법을 설명합니다. 보안 자격 증명, 이 시나리오를 지원하는 데 필요한 Runbook 및 데이터 센터에 Hybrid Runbook Worker 배포가 포함된 Automation을 구성하여 Runbook을 실행하고, GitHub Enterprise 리포지토리에 액세스하여 Runbook을 Automation 계정과 동기화합니다.

## <a name="getting-the-scenario"></a>시나리오 가져오기

이 시나리오는 Azure 포털의 [Runbook 갤러리](automation-runbook-gallery.md)에서 직접 가져오거나 [PowerShell 갤러리](https://www.powershellgallery.com)에서 다운로드할 수 있는 2개의 PowerShell runbook으로 구성됩니다.

### <a name="runbooks"></a>Runbook

Runbook | 설명|
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook은 Automation 계정에서 하이브리드 작업자로 RunAs(실행) 인증서를 내보내어 Runbook을 Automation 계정으로 가져오기 위해 작업자의 Runbook이 Azure로 인증하도록 합니다.|
Sync-LocalGitFolderToAutomationAccount | Runbook은 하이브리드 컴퓨터의 로컬 Git 폴더를 동기화한 다음 Runbook 파일(*.ps1)을 Automation 계정으로 가져옵니다.|

### <a name="credentials"></a>자격 증명

자격 증명 | 설명|
-----------|------------|
GitHRWCredential | 하이브리드 작업자에 대한 권한이 있는 사용자에 대한 사용자 이름과 암호를 포함하도록 만드는 자격 증명 자산입니다.|

## <a name="installing-and-configuring-this-scenario"></a>이 시나리오 설치 및 구성

### <a name="prerequisites"></a>필수 조건

1. Sync-LocalGitFolderToAutomationAccount runbook은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 사용하여 인증을 합니다.

2. Azure Automation 솔루션을 사용하도록 설정하고 구성한 Log Analytics 작업 영역도 필요합니다. 이 시나리오를 설치하고 구성하는 데 사용되는 Automation 계정과 연결된 작업 영역이 없으면 Hybrid Runbook Worker에서 **New-OnPremiseHybridWorker.ps1** 스크립트를 실행할 때 해당 작업 영역이 만들어지고 구성됩니다.

    > [!NOTE]
    > 현재 Log Analytics와 Automation의 통합을 지원하는 지역은 **오스트레일리아 남동부**, **미국 동부 2**, **동남 아시아** 및 **유럽 서부**입니다.

3. 전용 Hybrid Runbook Worker로 사용할 수 있는 컴퓨터는 GitHub 소프트웨어를 호스트하고 runbook 파일(*runbook*.ps1)을 파일 시스템의 소스 디렉터리에 유지하여 GitHub와 Automation 계정 간에 동기화를 수행합니다.

### <a name="import-and-publish-the-runbooks"></a>Runbook 가져오기 및 게시

Azure Portal의 Automation 계정에서 Runbook 갤러리의 *Export-RunAsCertificateToHybridWorker* 및 *Sync-LocalGitFolderToAutomationAccount* Runbook을 가져오려면 [Runbook 갤러리에서 Runbook 가져오기](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal)의 절차를 따릅니다. Runbook을 Automation 계정으로 가져온 후에는 Runbook을 게시합니다.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Hybrid Runbook Worker 배포 및 구성

데이터 센터에 Hybrid Runbook Worker를 아직 배포하지 않은 경우 요구 사항을 검토하고 Azure Automation Hybrid Runbook Workers - [Windows](automation-windows-hrw-install.md#automated-deployment) 또는 [Linux](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)용 설치 및 구성 자동화의 절차를 사용하여 자동 설치 단계를 따릅니다. 컴퓨터에 Hybrid Worker를 설치했으면 다음 단계를 수행하여 이 시나리오를 지원하도록 구성을 완료합니다.

1. 로컬 관리자 권한이 있는 계정으로 Hybrid Runbook Worker 역할을 호스팅하는 컴퓨터에 로그온하고 Git Runbook 파일을 저장할 디렉터리를 만듭니다. 디렉터리에 내부 Git 리포지토리를 복제합니다.
1. 아직 실행 계정을 만들지 않았거나 이 용도로 새로운 전용 계정을 만들려면 Azure Portal에서 Automation 계정으로 이동하고, Automation 계정을 선택하고, Hybrid Worker에 대한 권한이 있는 사용자의 사용자 이름 및 암호를 포함하는 [자격 증명 자산](automation-credentials.md)을 만듭니다.
1. Automation 계정에서 [runbook](automation-edit-textual-runbook.md)  **Export-RunAsCertificateToHybridWorker**를 편집하고 강력한 암호를 사용하여 변수 *$Password*의 값을 수정합니다.  값을 수정한 후 **게시**를 클릭하여 runbook 초안 버전을 게시합니다.
1. Runbook **Export-RunAsCertificateToHybridWorker**를 시작하고 **Runbook 시작** 블레이드의 **실행 설정** 옵션에서 **Hybrid Worker** 옵션을 선택하고 드롭다운 목록에서 이 시나리오에 대해 이전에 만든 Hybrid Worker 그룹을 선택합니다.

    이렇게 하면 인증서를 하이브리드 작업자로 내보내어 Azure 리소스를 관리하기 위해 작업자의 Runbook이 실행 연결을 사용하여 Azure로 인증할 수 있도록 합니다(이 시나리오의 경우 특히 Automation 계정으로 Runbook 가져오기).

1. Automation 계정에서 이전에 만든 하이브리드 작업자 그룹을 선택하고, 이 그룹에 대한 [실행 계정을 지정](automation-hrw-run-runbooks.md#runas-account)하고, 방금 또는 이전에 만든 자격 증명 자산을 선택합니다. 이렇게 하면 동기화 Runbook이 Git 명령을 실행할 수 있습니다. 
1. Runbook **Sync-LocalGitFolderToAutomationAccount**를 시작하고 다음의 필수 입력 매개 변수 값을 제공한 다음 **Runbook 시작** 블레이드의 **실행 설정** 옵션에서 **Hybrid Worker** 옵션을 선택하고 드롭다운 목록에서 이 시나리오에 대해 이전에 만든 Hybrid Worker 그룹을 선택합니다.

   * *ResourceGroup* - Automation 계정과 연결된 리소스 그룹의 이름
   * *AutomationAccountName* - 자동화 계정의 이름
   * *GitPath* - Git이 최신 변경 내용을 끌어오도록 설정된 Hybrid Runbook Worker의 로컬 폴더 또는 파일

    그러면 Hybrid Worker 컴퓨터에서 로컬 Git 폴더를 동기화한 다음, 소스 디렉터리에서 Automation 계정으로 .ps1 파일을 가져옵니다.

1. Automation 계정의 **Runbook** 블레이드에서 runbook을 선택하여 runbook에 대한 작업 요약 세부 정보를 확인한 다음 **작업** 타일을 선택합니다. **모든 로그** 타일을 선택하고 자세한 로그 스트림을 검토하여 성공적으로 완료되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* Runbook의 형식, 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
* PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure Automation에서 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
