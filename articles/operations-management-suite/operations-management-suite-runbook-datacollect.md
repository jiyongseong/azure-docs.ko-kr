---
title: Azure Automation에서 Runbook을 사용하여 Log Analytics 데이터 수집 | Microsoft Docs
description: Azure Automation에서 Runbook을 만들어 Log Analytics에서 분석할 수 있게 OMS 리포지토리에 데이터를 수집하는 과정을 안내하는 단계별 자습서입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 0784e2317fbc98561b486547654ca27bb30e76c3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>Azure Automation Runbook을 사용하여 Log Analytics에서 데이터 수집
에이전트의 [데이터 원본](../log-analytics/log-analytics-data-sources.md)을 비롯한 다양한 원본에서 Log Analytics으로 대량의 데이터를 수집할 수 있고 [Azure에서 수집된 데이터](../log-analytics/log-analytics-azure-storage.md)도 가져올 수 있습니다.  이러한 표준 원본을 통해 액세스할 수 없는 데이터를 수집해야 하는 경우도 있습니다.  이러한 경우 [HTTP 데이터 수집기 API](../log-analytics/log-analytics-data-collector-api.md)를 사용하여 REST API 클라이언트에서 Log Analytics로 데이터를 쓸 수 있습니다.  이 데이터 수집을 수행하는 일반적인 방법은 Azure Automation에서 Runbook을 사용하는 것입니다.   

이 자습서는 Azure Automation에서 Runbook을 만들고 Log Analytics에 데이터를 쓰도록 예약하는 프로세스를 안내합니다.


## <a name="prerequisites"></a>필수 조건
이 시나리오를 수행하려면 다음 리소스가 Azure 구독에 구성되어야 합니다.  둘 다 무료 계정일 수 있습니다.

- [Log Analytics 작업 영역](../log-analytics/log-analytics-get-started.md)
- [Azure Automation 계정](../automation/automation-offering-get-started.md)

## <a name="overview-of-scenario"></a>시나리오의 개요
이 자습서에서는 Automation 작업에 대한 정보를 수집하는 Runbook을 작성합니다.  Azure Automation의 Runbook은 PowerShell을 통해 구현되므로 먼저 Azure Automation 편집기에서 스크립트를 작성하고 테스트합니다.  필요한 정보를 제대로 수집하고 있는지 확인한 후에는 해당 데이터를 Log Analytics에 쓰고 사용자 지정 데이터 형식을 확인합니다.  마지막으로 정기적으로 Runbokk을 시작하는 일정을 만듭니다.

> [!NOTE]
> 이 Runbook 없이도 작업 정보를 Log Analytics로 전송하도록 Azure Automation을 구성할 수 있습니다.  이 시나리오는 기본적으로 이 자습서를 지원하는 데 사용되며, 데이터를 테스트 작업 영역으로 전송하는 것이 좋습니다.  


## <a name="1-install-data-collector-api-module"></a>1. 데이터 수집기 API 모듈 설치
모든 [HTTP 데이터 수집기 API의 요청](../log-analytics/log-analytics-data-collector-api.md#create-a-request)은 서식을 적절히 지정해야 하며 권한 부여 헤더를 포함해야 합니다.  Runbook에서 이 작업을 수행할 수 있지만 이 프로세스를 간소화하는 모듈을 사용하여 필요한 코드의 양을 줄일 수 있습니다.  사용할 수 있는 모듈 하나는 PowerShell 갤러리에 있는 [OMSIngestionAPI 모듈](https://www.powershellgallery.com/packages/OMSIngestionAPI)입니다.

Runbook에서 [모듈](../automation/automation-integration-modules.md)을 사용하려면 Automation 계정에 설치되어야 합니다.  그러면 같은 계정에 있는 모든 Runbook이 해당 모듈의 함수를 사용할 수 있습니다.  Automation 계정에서 **자산** > **모듈** > **모듈 추가**를 선택하여 새 모듈을 설치할 수 있습니다.  

그러나 PowerShell 갤러리는 Automation 계정으로 직접 모듈을 배포할 수 있는 빠른 옵션을 제공하므로, 이 자습서를 위해 해당 옵션을 사용할 수 있습니다.  

![OMSIngestionAPI 모듈](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. [PowerShell 갤러리](https://www.powershellgallery.com/)로 이동합니다.
2. **OMSIngestionAPI**를 검색합니다.
3. **Azure Automation에 배포** 단추를 클릭합니다.
4. Automation 계정을 선택하고 **확인**을 클릭하여 모듈을 설치합니다.


## <a name="2-create-automation-variables"></a>2. Automation 변수 만들기
[Automation 변수](..\automation\automation-variables.md)는 Automation 계정의 모든 Runbook에서 사용할 수 있는 값을 포함합니다.  이러한 변수를 사용하면 실제 Runbook을 편집하지 않고도 이러한 값을 변경할 수 있으므로 훨씬 유연하게 작업할 수 있습니다. HTTP 데이터 수집기 API의 모든 요청에는 OMS 작업 영역의 ID 및 키가 필요하며 변수 자산은 이 정보를 저장하는 데 이상적입니다.  

![variables](media/operations-management-suite-runbook-datacollect/variables.png)

1. Azure Portal에서 Automation 계정으로 이동합니다.
2. **공유 리소스**에서 **변수**를 선택합니다.
2. **변수 추가**를 클릭하고 다음 표에 나오는 두 가지 변수를 만듭니다.

| 자산 | 작업 영역 ID 값 | 작업 영역 키 값 |
|:--|:--|:--|
| Name | WorkspaceId | WorkspaceKey |
| type | 문자열 | 문자열 |
| 값 | Log Analytics 작업 영역의 작업 영역 ID를 붙여 넣습니다. | Log Analytics 작업 영역의 기본 또는 보조 키와 함께 붙여 넣습니다. |
| 암호화 | 아니오 | 예 |



## <a name="3-create-runbook"></a>3. runbook 만들기

Azure Automation은 포털에 Runbook을 편집하고 테스트할 수 있는 편집기가 있습니다.  스크립트 편집기를 사용하여 [PowerShell을 직접](../automation/automation-edit-textual-runbook.md) 사용하거나 [그래픽 Runbook을 만들](../automation/automation-graphical-authoring-intro.md) 수도 있습니다.  이 자습서에서는 PowerShell 스크립트를 사용합니다. 

![Runbook 편집](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. Automation 계정으로 이동합니다.  
2. **Runbook** > **Runbook 추가** > **새 Runbook 만들기**를 클릭합니다.
3. Runbook 이름으로 **Collect-Automation-jobs**를 입력합니다.  Runbook 형식으로 **PowerShell**을 선택합니다.
4. **만들기**를 클릭하여 Runbook을 만들고 편집기를 시작합니다.
5. 다음 코드를 복사하여 Runbook에 붙여 넣습니다.  코드 설명을 보려면 스크립트에 있는 주석을 참조하세요.
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4. Runbook 테스트
Azure Automation에는 Runbook을 게시하기 전에 [runbook을 테스트](../automation/automation-testing-runbook.md)하는 환경이 포함되어 있습니다.  Runbook에서 수집한 데이터를 검사하고, Runbook이 데이터를 프로덕션에 게시하기 전에 예상대로 Log Analytics에 쓰는지 확인할 수 있습니다. 
 
![Runbook 테스트](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. **저장**을 클릭하여 Runbook을 저장합니다.
1. **테스트 창**을 클릭하여 Runbook을 테스트 환경에서 엽니다.
3. Runbook에 매개 변수가 있으므로 해당 값을 입력하라는 메시지가 나타납니다.  작업 정보를 수집하려는 리소스 그룹 및 Automation 계정의 이름을 입력합니다.
4. **시작**을 클릭하여 Runbook을 시작합니다.
3. Runbook은 **대기** 상태로 시작한 후 **실행 중** 상태가 됩니다.  
3. Runbook은 수집된 작업을 포함하는 자세한 출력을 JSON 형식으로 표시합니다.  작업이 목록에 없으면 마지막 시간에 Automation 계정에서 만들어진 작업이 없는 것일 수 있습니다.  Automation 계정에서 Runbook을 시작한 다음 테스트를 다시 수행합니다.
4. Log Analytics에 대한 POST 명령 출력에 오류가 표시되지 않는지 확인합니다.  다음과 유사한 메시지가 표시됩니다.

    ![POST 출력](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5. Log Analytics의 레코드 확인
Runbook 테스트가 완료되고 출력이 성공적으로 수신되었음을 확인한 후에는 [Log Analytics의 로그 검색](../log-analytics/log-analytics-log-searches.md)을 사용하여 레코드를 생성되었는지 확인할 수 있습니다.

![로그 출력](media/operations-management-suite-runbook-datacollect/log-output.png)

1. Azure Portal에서 Log Analytics 작업 영역을 선택합니다.
2. **로그 검색**을 클릭합니다.
3. 다음 명령 `Type=AutomationJob_CL`을 입력하고 검색 단추를 클릭합니다. 레코드 형식에 스크립트에 지정되지 않은 _CL이 포함됩니다.  이 접미사는 사용자 지정 로그 형식임을 나타내기 위해 로그 형식에 자동으로 추가됩니다.
4. Runbook이 예상대로 작동하고 있음을 나타내는 반환된 하나 이상의 레코드를 확인합니다.


## <a name="6-publish-the-runbook"></a>6. Runbook 게시
Runbook이 올바르게 작동하는지 확인한 후에는 프로덕션 환경에서 실행할 수 있도록 게시해야 합니다.  게시된 버전을 수정하지 않고 Runbook을 계속해서 편집하고 테스트할 수 있습니다.  

![Runbook 게시](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. Automation 계정으로 돌아갑니다.
2. **Runbook**을 클릭하고 **Collect-Automation-jobs**를 선택합니다.
3. **편집**, **게시**를 차례로 클릭합니다.
4. 이전에 게시된 버전을 덮어쓸 것인지 확인하도록 요청되면 **예**를 클릭합니다.

## <a name="7-set-logging-options"></a>7. 로깅 옵션 설정 
테스트의 경우 스크립트에 $VerbosePreference 변수를 설정했으므로 [자세한 정보 출력](../automation/automation-runbook-output-and-messages.md#message-streams)을 볼 수 있었습니다.  프로덕션의 경우에는 자세한 정보 출력을 보려면 Runbook의 로깅 속성을 설정해야 합니다.  이 자습서에 사용된 Runbook의 경우 Log Analytics으로 전송되는 JSON 데이터가 표시됩니다.

![로깅 및 추적](media/operations-management-suite-runbook-datacollect/logging.png)

1. Runbook에 대한 속성의 **Runbook 설정**에서 **로깅 및 추적**을 선택합니다.
2. **상세 레코드 기록**에 대한 설정을 **설정**으로 변경합니다.
3. **저장**을 클릭합니다.

## <a name="8-schedule-runbook"></a>8. Runbook 예약
모니터링 데이터를 수집하는 Runbook을 시작하는 가장 일반적인 방법은 자동으로 실행되도록 예약하는 것입니다.  이를 위해 [Azure Automation에서 일정](../automation/automation-schedules.md)을 만들고 Runbook에 첨부하면 됩니다.

![Runbook 예약](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. Runbook에 대한 속성의 **리소스**에서 **일정**을 선택합니다.
2. **일정 추가** > **Runbook에 일정 연결** > **새 일정 만들기**를 클릭합니다.
5. 일정에 대해 다음 값을 입력하고 **만들기**를 클릭합니다.

| 자산 | 값 |
|:--|:--|
| Name | AutomationJobs-Hourly |
| Starts | 현재 시간보다 적어도 5분 이후의 아무 시간이나 선택합니다. |
| 되풀이 | Recurring |
| Recur every | 1시간 |
| Set expiration | 아니오 |

일정을 만든 후에는 이 일정에 따라 Runbook이 시작될 때마다 사용되는 매개 변수 값을 설정해야 합니다.

6. **매개 변수 및 실행 설정 구성**을 클릭합니다.
7. **ResourceGroupName** 및 **AutomationAccountName**에 대한 값을 입력합니다.
8. **확인**을 클릭합니다. 

## <a name="9-verify-runbook-starts-on-schedule"></a>9. Runbook이 일정에 맞게 시작되는지 확인
Runbook이 시작될 때마다 [작업이 만들어지고](../automation/automation-runbook-execution.md) 모든 출력이 로깅됩니다.  실제로는 Runbook이 수집하는 작업과 같습니다.  일정 시작 시간이 경과된 후 Runbook에 대한 작업을 확인하여 Runbook이 예상대로 시작되었는지 확인할 수 있습니다.

![교육](media/operations-management-suite-runbook-datacollect/jobs.png)

1. Runbook에 대한 속성의 **리소스**에서 **작업**을 선택합니다.
2. Runbook이 시작될 때마다 작업 목록이 표시됩니다.
3. 해당 정보를 보려면 작업 중 하나를 클릭합니다.
4. Runbook의 로그 및 출력을 보려면 **모든 로그**를 클릭합니다.
5. 아래 이미지와 유사한 항목을 찾을 때까지 아래쪽으로 스크롤합니다.<br>![자세한 정보 표시](media/operations-management-suite-runbook-datacollect/verbose.png)
6. Log Analytics으로 전송된 자세한 JSON 데이터를 보려면 이 항목을 클릭하세요.



## <a name="next-steps"></a>다음 단계
- [뷰 디자이너](../log-analytics/log-analytics-view-designer.md)를 사용하여 Log Analytics 리포지토리로 수집한 데이터를 표시하는 보기를 만듭니다.
- Runbook을 [관리 솔루션](operations-management-suite-solutions-creating.md)으로 패키지하여 고객에게 배포합니다.
- [Log Analytics](https://docs.microsoft.com/azure/log-analytics/)에 대해 자세히 알아보기
- [Azure Automation](https://docs.microsoft.com/azure/automation/)에 대해 자세히 알아보기
- [HTTP 데이터 수집기 API](../log-analytics/log-analytics-data-collector-api.md)에 대해 자세히 알아보기
