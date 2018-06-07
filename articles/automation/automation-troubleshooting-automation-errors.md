---
title: 일반적인 Azure Automation 문제 해결 | Microsoft Docs
description: 이 문서에서는 일반적인 Azure Automation 오류 해결을 도와줄정보를 제공합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
tags: top-support-issue
keywords: 자동화 오류, 문제 해결, 문제
ms.openlocfilehash: 990422cb686c84501127c3fcf233f96aec5bc065
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-common-issues-in-azure-automation"></a>Azure Automation이 일반적인 문제 해결 
이 문서에서는 Azure Automation에서 발생할 수 있는 일반적인 문제 해결 도움말을 제공하고 가능한 문제 해결 방법을 제시합니다.

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Azure Automation Runbook을 사용할 때 인증 오류
### <a name="scenario-sign-in-to-azure-account-failed"></a>시나리오: Azure 계정 로그인에 실패
**오류:** Add-AzureAccount 또는 Connect-AzureRmAccount cmdlet을 사용하면 "Unknown_user_type: 알 수 없는 사용자 유형" 오류가 수신됩니다.

**오류 원인:** 이 오류는 자격 증명 자산 이름이 올바르지 않거나 Automation 자격 증명 자산을 설정하는 데 사용한 사용자 이름과 암호가 올바르지 않은 경우에 발생합니다.

**문제 해결 팁:** 무엇이 문제인지 확인하기 위해 다음 단계를 수행합니다.  

1. Azure에 연결할 때 사용하는 Automation 자격 증명 자산에 **@** 문자를 비롯한 특수 문자가 들어 있지는 않은지 확인합니다.  
2. Azure Automation 자격 증명에 저장된 사용자 이름 및 암호를 로컬 PowerShell ISE 편집기에서 사용할 수 있는지 확인합니다. PowerShell ISE에서 다음 cmdlet을 실행하여 확인할 수 있습니다.  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Connect-AzureRmAccount –Credential $Cred
3. 인증이 로컬에서 실패하면 Azure Active Directory 자격 증명을 올바르게 설정하지 않았다는 뜻입니다. Azure Active Directory 계정을 올바르게 설정하는 방법은 [Azure Active Directory를 사용하여 Azure에 인증](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 블로그 게시물을 참조하세요.  

### <a name="scenario-unable-to-find-the-azure-subscription"></a>시나리오: Azure 구독을 찾을 수 없음
**오류:** Select-AzureSubscription 또는 Select-AzureRmSubscription cmdlet을 사용할 때 ``<subscription name>`` 구독을 찾을 수 없습니다." 오류가 발생합니다.

**오류 원인:** 이 오류는 구독 이름이 올바르지 않거나 구독 세부 정보를 가져오려는 Azure Active Directory 사용자가 구독 관리자로 구성되지 않은 경우에 발생합니다.

**문제 해결 팁:** Azure에 올바르게 인증하여 선택하려는 구독에 대한 액세스 권한을 획득했는지 확인하기 위해 다음 단계를 수행합니다.  

1. **Add-AzureAccount**를 실행한 후 **Select-AzureSubscription** cmdlet을 실행합니다.  
2. 이 오류 메시지가 계속 나타나면 **Add-AzureAccount** 뒤에 **Get-AzureSubscription** cmdlet을 추가하여 코드를 수정한 다음 코드를 실행합니다. 이제 Get-AzureSubscription의 출력에 구독 세부 정보가 포함되었는지 확인합니다.  

   * 출력에 구독 세부 정보가 보이지 않으면 아직 구독이 초기화되지 않았다는 뜻입니다.  
   * 출력에 구독 세부 정보가 보이면 **Select-AzureSubscription** cmdlet을 사용하여 올바른 구독 이름 또는 ID를 사용하고 있는지 확인합니다.   

### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>시나리오: Multi-Factor Authentication이 활성화되어 Azure 인증에 실패
**오류:** Azure 사용자 이름 및 암호를 사용하여 Azure에 인증하면 “Add-AzureAccount: AADSTS50079: 강력한 인증 등록(증명)이 필요합니다.” 오류가 수신됩니다.

**오류 원인:** Azure 계정에서 Multi-Factor Authentication을 사용하면 Azure Active Directory 사용자를 사용하여 Azure에 인증할 수 없습니다. 그 대신 인증서 또는 서비스 주체를 사용하여 Azure에 인증해야 합니다.

**문제 해결 팁:** Azure 클래식 배포 모델 cmdlet에 인증서를 사용하려면 [인증서를 만들고 추가하여 Azure 서비스 관리](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)를 참조하세요. Azure Resource Manager cmdlet에 서비스 주체를 사용하려면 [Azure Portal을 사용하여 서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md) 및 [Azure Resource Manager를 사용하여 서비스 주체 인증](../azure-resource-manager/resource-group-authenticate-service-principal.md)을 참조하세요.

## <a name="common-errors-when-working-with-runbooks"></a>Runbook을 사용할 때 발생하는 일반적인 오류
### <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-it-failed-to-start-each-time"></a>시나리오: Runbook 작업 시작을 세 번 시도했지만 매번 시작하지 못했습니다.
**오류:** "작업을 세 번 시도했지만 실패했습니다." 오류가 발생하여 Runbook이 실패합니다.

**오류의 원인:** 이 오류는 다음과 같은 원인으로 인해 발생할 수 있습니다.  

1. 메모리 제한. 샌드박스 [Automation 서비스 제한](../azure-subscription-service-limits.md#automation-limits)에 할당 된 메모리 양에 대한 제한이 문서화되어 있으므로 400MB 이상의 메모리를 사용하는 경우 작업이 실패할 수 있습니다. 

2. 모듈이 호환되지 않음. 이는 모듈 종속성이 올바르지 않은 경우에 발생할 수 있으며 그렇지 않은 경우 일반적으로 Runbook은 "명령을 찾을 수 없습니다." 또는 "매개 변수를 바인딩할 수 없습니다." 메시지를 반환합니다. 

**문제 해결 팁:** 다음 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

* 메모리 제한 내에서 작업하는 경우 여러 Runbook 간에 워크로드를 분할하고, 메모리에서 많은 데이터를 처리하지 않고, Runbook에서 불필요한 출력을 작성하지 않으며, PowerShell 워크플로 Runbook에 작성하는 검사점의 수를 고려하는 것이 좋습니다.  

* [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md) 단계에 따라 Azure 모듈을 업데이트합니다.  


### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>시나리오: 역직렬화된 개체로 인해 Runbook 실패
**오류:** "``<ParameterName>`` 매개 변수를 바인딩할 수 없습니다. 역직렬화된 형식 ``<ParameterType>``의 ``<ParameterType>`` 값을 ``<ParameterType>`` 형식으로 변환할 수 없습니다." 오류와 함께 Runbook이 실패합니다.

**오류 원인:** Runbook이 PowerShell 워크플로인 경우 워크플로가 일시 중단되더라도 Runbook 상태를 유지하기 위해 복합 개체를 역직렬화된 형식으로 저장합니다.

**문제 해결 팁:** 다음 3가지 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

1. 한 cmdlet에서 다른 cmdlet으로 복합 개체를 파이핑하는 경우 이러한 cmdlet을 InlineScript에 래핑합니다.
2. 개체 전체를 전달하지 말고 복합 개체에서 필요한 이름 또는 값만 전달합니다.
3. PowerShell 워크플로 Runbook 대신 PowerShell Runbook을 사용합니다.

### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>시나리오: 할당된 할당량을 초과하여 Runbook 작업 실패
**오류:** "이 구독에 할당된 월간 총 작업 실행에 도달했습니다" 오류와 함께 Runbook이 실패합니다.

**오류의 원인:** 작업 실행 시간이 계정에 할당된 무료 500분을 초과하면 이 오류가 발생합니다. 이 할당량은 작업을 테스트하고, 포털에서 작업을 시작하고, 웹 후크를 사용하여 작업을 실행하고, Azure 포털 또는 데이터 센터를 사용하여 실행할 작업을 예약하는 등의 모든 작업 실행에 적용됩니다. Automation 가격 책정에 대한 자세한 내용은 [Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

**문제 해결 팁:** 매월 처리 시간을 500분 이상 사용하려면 구독을 무료 계층에는 기본 계층으로 변경해야 합니다. 다음 단계에 따라 기본 계층으로 업그레이드할 수 있습니다.  

1. Azure 구독에 로그인합니다.  
2. 업그레이드할 Automation 계정을 선택합니다.  
3. **설정** > **가격 책정**을 클릭합니다.
4. 페이지 아래쪽의 **사용**을 클릭하여 계정을 **기본** 계층으로 업그레이드합니다.

### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>시나리오: Runbook을 실행해도 cmdlet이 인식되지 않음
**오류:** "``<cmdlet name>``: ``<cmdlet name>``이라는 용어가 cmdlet의 이름, 함수, 스크립트 파일 또는 사용이 가능한 프로그램으로 인식되지 않습니다" 오류와 함께 Runbook 작업이 실패하게 됩니다.

**오류 원인:** PowerShell 엔진이 사용자가 Runbook에서 사용하는 cmdlet을 찾을 수 없는 경우에 이 오류가 발생합니다. cmdlet을 포함하고 있는 모듈이 계정에 없어서, Runbook 이름과 충돌하는 이름이 있어서 또는 다른 모듈에도 cmdlet이 있어서 Azure Automation이 이름을 확인할 수 없는 것이 원인일 수 있습니다.

**문제 해결 팁:** 다음 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.  

* cmdlet 이름을 올바르게 입력했는지 확인합니다.  
* Automation 계정에 Cmdlet이 있고 충돌이 없는지 확인합니다. cmdlet이 있는지 확인하려면 Runbook을 편집 모드로 열고 라이브러리에서 원하는 cmdlet을 검색하거나 **Get-Command ``<CommandName>``** 를 실행합니다. cmdlet을 계정에 사용할 수 있고 다른 cmdlet 또는 Runbook과 이름이 충돌하지 않는 것으로 확인되면 캔버스에 cmdlet을 추가하고 Runbook에 유효한 매개 변수 집합을 사용하고 있는지 확인합니다.  
* 이름이 충돌하고 cmdlet이 서로 다른 두 모듈에서 사용되는 경우 cmdlet에 대한 정규화된 이름을 사용하여 이 문제를 해결할 수 있습니다. 예를 들어 **ModuleName\CmdletName**을 사용할 수 있습니다.  
* Hybrid Worker 그룹에서 runbook 온-프레미스를 실행하는 경우 모듈/cmdlet이 Hybrid Worker를 호스트하는 시스템에 설치되어야 합니다.

### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>시나리오: 장기 실행 runbook이 “동일한 검사점에서 반복적으로 제거되어 작업을 계속 실행할 수 없습니다.” 예외로 인해 지속적으로 실패함
**오류 원인:** Azure Automation 내의 프로세스에 대한 "공평 분배" 모니터링으로 인해 의도적으로 설계된 동작입니다. 이 동작은 Runbook이 3시간 이상 실행되는 경우 자동으로 일시 중단합니다. 그러나 반환된 오류 메시지에서는 "다음 단계" 옵션을 제공하지 않습니다. runbook은 다양한 이유로 일시 중단될 수 있습니다. 일시 중단은 주로 오류로 인해 발생합니다. 예를 들어 Runbook의 catch되지 않은 예외, 네트워크 오류 또는 Runbook을 실행하는 Runbook Worker의 작동 중단은 모두 Runbook을 일시 중단시키며, 재개 시 마지막 검사점에서 시작되도록 합니다.

**문제 해결 팁:** 이 문제를 방지하기 위한 문서화된 해결 방법은 워크플로에서 검사점을 사용하는 것입니다. 자세한 내용은 [PowerShell 워크플로 학습](automation-powershell-workflow.md#checkpoints)을 참조하세요. "공평 분배" 및 검사점에 대한 자세한 설명은 [Runbook에서 검사점 사용](https://azure.microsoft.com/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/) 블로그 문서에서 확인 수 있습니다.

## <a name="common-errors-when-importing-modules"></a>모듈을 가져올 때 발생하는 일반적인 오류
### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>시나리오: 모듈이 가져오기를 실패하거나 가져오기를 실행한 후 cmdlet을 실행할 수 없음
**오류:** 모듈이 가져오기를 실패하거나 성공하더라도 cmdlet이 추출되지 않습니다.

**오류 원인:** 모듈이 Azure Automation을 가져오지 못하는 몇 가지 일반적인 이유는 다음과 같습니다.

* 구조가 Automation에서 요구하는 구조와 일치하지 않습니다.
* 모듈이 Automation 계정에 배포되지 않은 다른 모듈에 종속되어 있습니다.
* 모듈 폴더에 종속성이 없습니다.
* **New-AzureRmAutomationModule** cmdlet이 모듈 업로드에 사용되고 있으며, 사용자가 공개적으로 액세스 가능한 URL을 사용하여 저장소 전체 경로를 입력하거나 모듈을 로드하지 않았습니다.

**문제 해결 팁:** 다음 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

* 모듈이 다음 형식을 따르는지 확인합니다. ModuleName.Zip **->** 모듈 이름 또는 버전 번호 **->** (ModuleName.psm1, ModuleName.psd1)
* .Psd1 파일을 열고 모듈에 종속성이 있는지 확인합니다. 종속성이 있으면 이러한 모듈을 Automation 계정에 업로드합니다.
* 모듈 폴더에 참조되는 .dll이 있는지 확인합니다.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>DSC(필요한 상태 구성)으로 작업하는 경우 일반적인 오류 문제
### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>시나리오: 노드가 "찾을 수 없음" 오류로 실패한 상태임
**오류:** 노드에는 "유효한 구성 ``<guid>``를 찾을 수 없기 때문에 https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction 서버에서 작업을 가져오려는 시도가 실패함"이라는 오류를 포함하는 **실패** 상태의 보고서가 있습니다.

**오류 원인:** 이 오류는 일반적으로 노드가 노드 구성 이름(예: ABC.WebServer) 대신 구성 이름(예: ABC)에 할당된 경우에 발생합니다.

**문제 해결 팁:**

* "구성 이름"이 아니라 "노드 구성 이름"으로 노드를 할당해야 합니다.
* Azure 포털 또는 PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당할 수 있습니다.

  * Azure Portal을 사용하여 노드 구성을 노드에 할당하려면 **DSC 노드** 페이지를 열고, 노드를 선택한 다음, **노드 구성 할당** 단추를 클릭합니다.  
  * PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당하려면 **Set-AzureRmAutomationDscNode** cmdlet을 사용합니다.

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>시나리오: 구성이 컴파일될 때 생성된 노드 구성(mof 파일)이 없음
**오류:** DSC 컴파일 작업이 “컴파일은 성공적으로 완료되었지만 노드 configuration.mof가 생성되지 않음”이라는 오류로 일시 중단되었습니다.

**오류 원인:** DSC 구성에서 **Node** 키워드 다음에 오는 식이 `$null`로 평가되면 노드 구성이 생성되지 않습니다.

**문제 해결 팁:** 다음 해결 방법 중 하나로 이 문제를 해결할 수 있습니다.

* 구성 정의에서 **Node** 키워드 옆의 식이 $null로 계산되지 않는지 확인합니다.
* 구성을 컴파일할 때 ConfigurationData를 전달하는 경우 [ConfigurationData](automation-dsc-compile.md#configurationdata)에서 구성에 필요한 값을 전달해야 합니다.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a>시나리오: DSC 노드 보고서가 "진행 중" 상태로 중단됨
**오류:** DSC 에이전트는 "주어진 속성 값으로 인스턴스를 찾을 수 없음"을 출력합니다.

**오류 원인:** WMF 버전을 업그레이드했고 WMI가 손상되었습니다.

**문제 해결 팁:** 문제를 해결하려면 [DSC 알려진 문제 및 제한 사항](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc) 문서의 지침을 따릅니다.

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a>시나리오: DSC 구성에서 자격 증명을 사용할 수 없습니다.
**오류:** DSC 컴파일 작업이 "``<some resource name>`` 형식의 'Credential' 속성을 처리하는 System.InvalidOperationException 오류: PSDscAllowPlainTextPassword가 true로 설정된 경우에만 암호화된 암호를 일반 텍스트로 변환하고 저장할 수 있습니다." 오류로 인해 일시 중단되었습니다.

**오류에 대한 원인:** 구성에서 자격 증명을 사용하지만 각 노드 구성에 대해 **PSDscAllowPlainTextPassword**를 true로 설정하는 적절한 **ConfigurationData**를 제공하지 않았습니다.

**문제 해결 팁:**

* 적절한 **ConfigurationData**에 전달하여 구성에서 언급한 각 노드의 구성에 대해 **PSDscAllowPlainTextPassword**를 true로 설정하도록 합니다. 자세한 내용은 [Azure Automation DSC의 자산](automation-dsc-compile.md#assets)을 참조하세요.

## <a name="common-errors-when-onboarding-solutions"></a>솔루션을 온보딩할 때 발생하는 일반적인 오류

솔루션을 온보딩할 때 오류가 발생할 수 있습니다. 다음은 발생할 수 있는 일반적인 오류 목록입니다.

### <a name="computergroupqueryformaterror"></a>ComputerGroupQueryFormatError

**오류 이유:**

이 오류 코드는 솔루션을 대상으로 사용되는 저장된 검색 컴퓨터 그룹 쿼리 형식이 올바르지 않음을 의미합니다. 사용자가 쿼리를 변경했거나 시스템에서 변경되었을 수 있습니다.

**문제 해결 팁:**

이 솔루션에 대한 쿼리를 삭제하고, 솔루션을 다시 온보딩하여 쿼리를 다시 만들 수 있습니다. 쿼리를 작업 영역 내의 **저장된 검색**에서 찾을 수 있습니다. 쿼리의 이름은 **MicrosoftDefaultComputerGroup**이고, 쿼리의 범주는 이 쿼리와 연결된 솔루션의 이름입니다. 여러 솔루션이 사용되도록 설정되면 **MicrosoftDefaultComputerGroup**이 **저장된 검색** 아래에 여러 번 표시됩니다.

### <a name="policyviolation"></a>PolicyViolation

**오류 이유:**

이 오류 코드는 하나 이상의 정책 위반으로 인해 배포가 실패했음을 의미합니다.

**문제 해결 팁:**

솔루션을 성공적으로 배포하려면 표시된 정책을 변경하는 것이 좋습니다. 정의할 수 있는 정책 유형이 다양하기 때문에 필요한 특정 변경 사항은 위반된 정책에 따라 달라집니다. 예를 들어 리소스 그룹 내 특정 유형의 리소스 콘텐츠를 변경할 수 있는 권한을 거부한 리소스 그룹에 정책이 정의된 경우, 예를 들면 다음과 같은 조치를 취할 수 있습니다.

*   정책을 모두 제거합니다.
* 다른 리소스 그룹에 등록을 시도합니다.
* 정책을 수정합니다. 예를 들면 다음과 같이 수정합니다.
   * 정책의 대상을 특정 리소스(예: 특정 Automation 계정)로 다시 지정합니다.
   * 정책이 거부하도록 구성된 리소스 집합을 수정합니다.

실패한 배포를 확인하려면 Azure Portal의 오른쪽 위 모서리에서 알림을 확인하거나 자동화 계정을 포함하는 리소스 그룹으로 이동하여 **설정**에 있는 **배포**를 선택합니다. Azure Policy에 대해 자세히 알아보려면 [Azure Policy 개요](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json)를 참조하세요.

## <a name="next-steps"></a>다음 단계

위의 문제 해결 단계를 수행해도 답을 찾을 수 없는 경우 아래의 추가 지원 옵션을 검토할 수 있습니다.

* Azure 전문가에게 도움을 받으세요. [MSDN Azure 또는 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에 문제를 제출하세요.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동한 다음 **기술 및 대금 청구 지원**에서 **지원 받기**를 클릭합니다.
* Azure Automation Runbook 솔루션 또는 통합 모듈을 찾고 있는 경우 [스크립트 센터](https://azure.microsoft.com/documentation/scripts/) 에 스크립트 요청을 게시하세요.
* Azure Automation에 대한 의견이나 기능 요청이 있으면 [사용자 의견](https://feedback.azure.com/forums/34192--general-feedback)에 게시하세요.
