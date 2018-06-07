---
title: 자동 조정 전자 메일 알림 방법 가이드 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database는 SQL 쿼리를 분석하고 사용자 워크로드에 자동으로 적용됩니다.
services: sql-database
author: danimir
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 02/05/2018
ms.author: v-daljep
ms.openlocfilehash: 8891e8b9fbf3ce8422036e8add900504ea7e4162
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="email-notifications-for-automatic-tuning"></a>자동 조정에 대한 전자 메일 알림

SQL Database 조정 권장 사항은 Azure SQL Database [자동 조정](sql-database-automatic-tuning.md)에서 생성됩니다. 이 솔루션은 SQL Database의 워크로드를 지속적으로 모니터링하고 분석하여, 각 개별 데이터베이스에 대해 인덱스 만들기, 인덱스 삭제 및 쿼리 실행 계획의 최적화와 관련된 사용자 지정 조정 권장 사항을 제공합니다.

SQL Database 자동 조정 권장 구성은 [Azure Portal](sql-database-advisor-portal.md)에서 [REST API](https://docs.microsoft.com/rest/api/sql/databaserecommendedactions/listbydatabaseadvisor)를 호출하거나 [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/) 및 [ PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabaserecommendedaction) 명령을 사용해서 검색할 수 있습니다. 이 문서는 PowerShell 스크립트를 사용하여 자동 조정 권장 사항을 검색합니다.

## <a name="automate-email-notifications-for-automatic-tuning-recommendations"></a>자동 조정 권장 사항에 대한 전자 메일 알림 자동화

다음 솔루션은 자동 조정 권장 사항을 포함하는 전자 메일 알림 전송을 자동화합니다. 설명된 솔루션은 [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)을 사용하여 조정 권장 사항을 검색하기 위한 PowerShell 스크립트의 실행 자동화와 [Microsoft Flow](https://flow.microsoft.com)를 사용한 전자 메일 전달 작업 예약 자동화로 구성됩니다.

## <a name="create-azure-automation-account"></a>Azure Automation 계정 만들기

Azure Automation을 사용하려는 경우 첫 번째 단계는 자동화 계정을 만들고, PowerShell 스크립트의 실행에 사용할 수 있게 Azure 리소스를 통해 구성하는 것입니다. Azure Automation 및 해당 기능에 대한 자세한 내용은 [Azure Automation 시작](https://docs.microsoft.com/azure/automation/automation-offering-get-started)을 참조하세요.

Marketplace의 Automation 앱을 선택하고 구성하는 방법을 통해 Azure Automation 계정을 만들려면 다음 단계를 따릅니다.

- Azure Portal에 로그인합니다.
- 왼쪽 위 모서리에서 "**+ 리소스 만들기**"를 클릭합니다.
- "**Automation**"을 검색합니다(Enter 키 누름).
- 검색 결과에서 Automation 앱을 클릭합니다.

![Azure Automation 추가](./media/sql-database-automatic-tuning-email-notifications/howto-email-01.png)

- "Automation 계정 만들기" 창에서 "**만들기**"를 클릭합니다.
- 필요한 정보를 입력합니다. 이 Automation 계정에 대한 이름을 입력하고 PowerShell 스크립트 실행에 사용할 Azure 구독 ID 및 Azure 리소스를 선택합니다.
- "**Azure 실행 계정 만들기**" 옵션에서 **예**를 선택하여 Azure Automation의 도움을 받아 PowerShell 스크립트를 실행할 계정의 유형을 구성합니다. 계정 유형에 대한 자세한 내용은 [실행 계정](https://docs.microsoft.com/azure/automation/automation-create-runas-account)을 참조하세요.
- **만들기**를 클릭하여 Automation 계정 만들기를 끝냅니다.

> [!TIP]
> Azure Automation 계정 이름, 구독 ID 및 리소스를 Automation 앱을 만들 때 입력한 대로 정확히 적어 둡니다(예: 메모장에 복사하여 붙여넣기). 나중에 이 정보가 필요합니다.
>

동일한 Automation을 구축하려는 Azure 구독이 여러 개 있는 경우 다른 구독에 대해서도 이 프로세스를 반복해야 합니다.

## <a name="update-azure-automation-modules"></a>Azure Automation 모듈 업데이트

Automation 조정 권장 구성을 검색하는 PowerShell 스크립트는 버전 4 이상으로의 Azure 모듈 업데이트가 필요한 경우 [Get-AzureRmResource](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Get-AzureRmResource) 및 [Get-AzureRmSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/AzureRM.Sql/Get-AzureRmSqlDatabaseRecommendedAction)을 사용합니다.

Azure PowerShell 모듈을 업데이트하려면 다음 단계를 수행합니다.

- Automation 앱 창에 액세스하고 왼쪽 메뉴에서 "**모듈**"을 선택합니다(이 메뉴 항목은 공유 리소스 아래에 있으므로 아래로 스크롤합니다).
- 모듈 창 맨 위에서 "**Azure 모듈 업데이트**"를 클릭하고 "Azure 모듈이 업데이트되었습니다."라는 메시지가 표시될 때까지 기다립니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

![Azure Automation 모듈 업데이트](./media/sql-database-automatic-tuning-email-notifications/howto-email-02.png)

필요한 AzureRM.Resources 및 AzureRM.Sql 모듈 버전은 버전 4 이상입니다.

## <a name="create-azure-automation-runbook"></a>Azure Automation Runbook 만들기

다음 단계는 Azure Automation에서 조정 권장 사항의 검색하기 위한 PowerShell 스크립트가 있는 Runbook을 만드는 것입니다.

Azure Automation Runbook을 만들려면 다음 단계를 수행합니다.

- 이전 단계에서 만든 Azure Automation 계정에 액세스합니다.
- Automation 계정 창 왼쪽에서 "**Runbook**" 메뉴 항목을 클릭하여 PowerShell 스크립트로 새 Azure Automation Runbook을 만듭니다. Automation Runbook을 만드는 방법에 대한 자세한 내용은 [새 Runbook 만들기](../automation/automation-creating-importing-runbook.md)를 참조하세요.
- 새 Runbook을 추가하려면 "**+ Runbook 추가**" 메뉴 옵션을 클릭하고 "**빨리 만들기 – 새 Runbook 만들기**"를 클릭합니다.
- Runbook 창에서 Runbook의 이름을 입력하고(이 예제에서는 "**AutomaticTuningEmailAutomation**" 사용) Runbook의 유형을 **PowerShell**로 선택한 후 이 Runbook의 용도를 나타내는 설명을 작성합니다.
- **만들기** 단추를 클릭하여 새 Runbook 만들기를 완료합니다.

![Azure Automation Runbook 추가](./media/sql-database-automatic-tuning-email-notifications/howto-email-03.png)

만든 Runbook 내에 PowerShell 스크립트를 로드하려면 다음 단계를 수행합니다.

- "**PowerShell Runbook 편집**" 창의 메뉴 트리에서 "**RUNBOOKS**"를 선택하고 Runbook 이름이 보일 때까지 보기를 확장합니다(이 예제에서는 " **AutomaticTuningEmailAutomation**"). 이 Runbook을 선택합니다.
- "PowerShell Runbook 편집"의 첫 번째 줄에(숫자 1부터 시작) 다음 PowerShell 스크립트 코드를 복사하여 붙여 넣습니다. 이 PowerShell 스크립트는 사용자가 시작할 수 있도록만 제공됩니다. 요구에 맞게 스크립트를 수정합니다.

제공된 PowerShell 스크립트의 헤더에서 `<SUBSCRIPTION_ID_WITH_DATABASES>`를 Azure 구독 ID로 바꾸어야 합니다. Azure 구독 ID를 검색하는 방법을 알아보려면 [Azure 구독 GUID 가져오기](https://blogs.msdn.microsoft.com/mschray/2016/03/18/getting-your-azure-subscription-guid-new-portal/)를 참조하세요.

여러 구독이 있는 경우 스크립트 헤더의 "$subscriptions" 속성에 쉼표로 구분해서 구독 ID를 추가할 수 있습니다.

```PowerShell
# PowerShell script to retrieve Azure SQL Database Automatic tuning recommendations.
#
# Provided “as-is” with no implied warranties or support.
# The script is released to the public domain.
#
# Replace <SUBSCRIPTION_ID_WITH_DATABASES> in the header with your Azure subscription ID.
#
# Microsoft Azure SQL Database team, 2018-01-22.

# Set subscriptions : IMPORTANT – REPLACE <SUBSCRIPTION_ID_WITH_DATABASES> WITH YOUR SUBSCRIPTION ID 
$subscriptions = ("<SUBSCRIPTION_ID_WITH_DATABASES>", "<SECOND_SUBSCRIPTION_ID_WITH_DATABASES>", "<THIRD_SUBSCRIPTION_ID_WITH_DATABASES>")

# Get credentials
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Define the resource types
$resourceTypes = ("Microsoft.Sql/servers/databases")
$advisors = ("CreateIndex", "DropIndex");
$results = @()

# Loop through all subscriptions
foreach($subscriptionId in $subscriptions) {    
    Select-AzureRmSubscription -SubscriptionId $subscriptionId    
    $rgs = Get-AzureRmResourceGroup

    # Loop through all resource groups
    foreach($rg in $rgs) {
        $rgname = $rg.ResourceGroupName;

        # Loop through all resource types
        foreach($resourceType in $resourceTypes) {
            $resources = Get-AzureRmResource -ResourceGroupName $rgname -ResourceType $resourceType    

            # Loop through all databases
            # Extract resource groups, servers and databases
            foreach ($resource in $resources) {
                $resourceId = $resource.ResourceId
                if ($resourceId -match ".*RESOURCEGROUPS/(?<content>.*)/PROVIDERS.*") {
                    $ResourceGroupName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*SERVERS/(?<content>.*)/DATABASES.*") {
                    $ServerName = $matches['content']
                } else {
                    continue
                }
                if ($resourceId -match ".*/DATABASES/(?<content>.*)") {
                    $DatabaseName = $matches['content']
                } else {
                    continue 
                }

                # Skip if master
                if ($DatabaseName -eq "master") {
                    continue
                }

                # Loop through all Automatic tuning recommendation types
                foreach ($advisor in $advisors) {
                    $recs = Get-AzureRmSqlDatabaseRecommendedAction -ResourceGroupName $ResourceGroupName -ServerName $ServerName  -DatabaseName $DatabaseName -AdvisorName $advisor
                    foreach ($r in $recs) {
                        if ($r.State.CurrentValue -eq "Active") {
                            $object = New-Object -TypeName PSObject
                            $object | Add-Member -Name 'SubscriptionId' -MemberType Noteproperty -Value $subscriptionId
                            $object | Add-Member -Name 'ResourceGroupName' -MemberType Noteproperty -Value $r.ResourceGroupName
                            $object | Add-Member -Name 'ServerName' -MemberType Noteproperty -Value $r.ServerName
                            $object | Add-Member -Name 'DatabaseName' -MemberType Noteproperty -Value $r.DatabaseName
                            $object | Add-Member -Name 'Script' -MemberType Noteproperty -Value $r.ImplementationDetails.Script
                            $results += $object
                        }
                    }
                }                
            }
        }
    }
}

# Format and output results for the email
$table = $results | Format-List
Write-Output $table
```

오른쪽 위 모서리에서 "**저장**" 단추를 클릭하여 스크립트를 저장합니다. 스크립트가 만족스러우면 "**게시**" 단추를 클릭하여 이 Runbook을 게시합니다. 

주 Runbook 창에서 "**시작**" 단추를 클릭하여 스크립트를 **테스트**하도록 선택할 수 있습니다. "**출력**"을 클릭하여 실행된 스크립트의 결과를 확인합니다. 이 출력은 전자 메일의 콘텐츠로 사용될 것입니다. 스크립트의 샘플 출력은 다음 스크린샷에 확인할 수 있습니다.

![Azure Automation을 사용한 자동 조정 권장 사항 보기 실행](./media/sql-database-automatic-tuning-email-notifications/howto-email-04.png)

사용자 요구에 맞게 PowerShell 스크립트를 사용자 지정하여 콘텐츠를 조정해야 합니다.

위 단계를 따르면 자동 조정 권장 사항을 검색하는 PowerShell 스크립트가 Azure Automation에 로드됩니다. 다음 단계는 전자 메일 배달 작업을 자동화하고 예약하는 것입니다.

## <a name="automate-the-email-jobs-with-microsoft-flow"></a>Microsoft Flow를 사용하여 전자 메일 작업 자동화

솔루션을 완료하려면 최종 단계로, Microsoft Flow에서 다음 3가지 작업으로 구성되는 자동화 흐름을 만듭니다. 

1. "**Azure Automation - 작업 만들기**" – Azure Automation Runbook 내에서 자동 조정 권장 사항을 검색하기 위한 PowerShell 스크립트를 실행하는 데 사용합니다.
2. "**Azure Automation - 작업 출력 가져오기**" – 실행된 PowerShell 스크립트에서 출력을 검색하는 데 사용합니다.
3. "**Office 365 Outlook - 전자 메일 보내기**" – 전자 메일을 보내는 데 사용합니다. 전자 메일은 흐름을 만드는 개인의 Office 365 계정을 사용하여 전송됩니다.

Microsoft Flow 기능에 대한 자세한 내용은 [Microsoft Flow 시작](https://docs.microsoft.com/flow/getting-started)을 참조하세요.

이 단계의 전제 조건은 [Microsoft Flow](https://flow.microsoft.com) 계정을 등록하고 로그인하는 것입니다. 솔루션 내부에서 다음 단계에 따라 **새 흐름**을 설정합니다.

- "**내 흐름**" 메뉴 항목에 액세스합니다.
- 내 흐름 내의 페이지 위쪽에서 "**+빈 페이지에서 만들기**" 페이지를 선택합니다.
- 페이지 아래쪽에서 "**수백 개의 커넥터 및 트리거 검색**" 링크를 클릭합니다.
- 검색 필드에서 "**되풀이**"를 입력하고 검색 결과에서 "**예약 - 되풀이**"를 선택하여 전자 메일 배달 작업이 실행되도록 예약합니다.
- 되풀이 창의 빈도 필드에서 이 흐름을 실행할 예약 빈도(예: 분, 시, 일, 주 등의 빈도로 자동 전자 메일 전송)를 선택합니다.

다음 단계는 새로 만든 되풀이 흐름에 3개의 작업(전자 메일 만들기, 출력 가져오기 및 전자 메일 전송)을 추가하는 것입니다. 필요한 작업을 흐름에 추가하려면 다음 단계를 따릅니다.

1. 조정 권장 사항을 검색하는 PowerShell 스크립트를 실행하는 작업을 만듭니다.
- 되풀이 흐름 창에서 "**+새 단계**","**작업 추가**"를 선택합니다.
- 검색 필드에 "**Automation**"을 입력하고 검색 결과에서 "**Azure Automation – 작업 만들기**"를 선택합니다.
- 작업 만들기 창에서 작업 속성을 구성합니다. 이 구성에 대해 **Automation 계정 창**에서 **이전에 기록한** Azure 구독 ID, 리소스 그룹 및 Automation 계정의 세부 정보가 필요합니다. 이 섹션에서 사용할 수 있는 옵션에 대한 자세한 내용은 [Azure Automation – 작업 만들기](https://docs.microsoft.com/connectors/azureautomation/#Create_job)를 참조하세요.
- "**흐름 저장**"을 클릭하여 이 작업 만들기를 완료합니다.

2. 실행된 PowerShell 스크립트에서 출력을 검색하는 작업을 만듭니다.
- 되풀이 흐름 창에서 "**+새 단계**","**작업 추가**"를 선택합니다.
- 검색 필드에 "**Automation**"을 입력하고 검색 결과에서 "**Azure Automation - 작업 출력 가져오기**"를 선택합니다. 이 섹션에서 사용할 수 있는 옵션에 대한 자세한 내용은 [Azure Automation – 작업 출력 가져오기](https://docs.microsoft.com/connectors/azureautomation/#Get_job_output)를 참조하세요.
- 필수 필드를 채웁니다(이전 작업을 만들 때와 유사) - Azure 구독 ID, 리소스 그룹 및 Automation 계정을 채웁니다(Automation 계정 창에 입력한 것과 같음).
- "**작업 ID**" 필드 내부를 클릭하여 "**동적 콘텐츠**" 메뉴를 표시합니다. 이 메뉴 내에서 "**작업 ID**" 옵션을 선택합니다.
- "**흐름 저장**"을 클릭하여 이 작업 만들기를 완료합니다.

3. Office 365 통합을 사용하여 메일을 전송하기 위한 작업을 만듭니다.
- 되풀이 흐름 창에서 "**+새 단계**","**작업 추가**"를 선택합니다.
- 검색 필드에서 "**전자 메일 보내기**"를 입력하고 검색 결과에서 "**Office 365 Outlook - 전자 메일 보내기**"를 선택합니다.
- "**받는 사람**" 필드에 알림 전자 메일을 보내야 하는 전자 메일 주소를 입력합니다.
- "**제목**" 필드에 전자 메일의 제목(예: "자동 조정 권장 사항 전자 메일 알림")을 입력합니다.
- "**본문**" 필드 내부를 클릭하여 "**동적 콘텐츠**" 메뉴를 표시합니다. 이 메뉴의 "**작업 출력 가져오기**"에서 "**콘텐츠**"를 선택합니다. 
- "**흐름 저장**"을 클릭하여 이 작업 만들기를 완료합니다.

> [!TIP]
> 자동화된 전자 메일을 다른 받는 사람에게 전송하려면 별도 흐름을 만듭니다. 이러한 추가 흐름의 경우 "받는 사람" 필드에서 받는 사람 전자 메일 주소를 변경하고 "제목" 필드에서 전자 메일 제목 줄을 변경합니다. 사용자 지정된 PowerShell 스크립트를 사용하여 Azure Automation에서 새 Runbook을 만들면(예: Azure 구독 ID 변경), 별도의 구독마다 별도의 받는 사람에게 자동 조정 권장 사항에 대한 전자 메일을 전송하는 것과 같이 자동화 시나리오를 추가로 사용자 지정할 수 있습니다.
>

위의 작업으로 전자 메일 배달 작업 워크플로 구성에 필요한 단계가 끝났습니다. 구축한 3가지 작업으로 구성된 전체 흐름이 다음 그림에 나와 있습니다.

![자동 조정 전자 메일 알림 흐름 보기](./media/sql-database-automatic-tuning-email-notifications/howto-email-05.png)

흐름을 테스트하려면 흐름 창 안쪽의 오른쪽 위 모서리에서 "**지금 실행**"을 클릭합니다.

전자 메일 알림이 성공적으로 전송되었음을 나타내는 자동화 작업 실행에 대한 통계를 Flow 분석 창에서 볼 수 있습니다.

![자동 조정 전자 메일 알림에 대한 실행 흐름](./media/sql-database-automatic-tuning-email-notifications/howto-email-06.png)

Flow 분석은 작업 실행의 성공 여부를 모니터링하는 데 도움이 되며, 문제 해결에 필요한 경우에도 유용합니다.  문제 해결의 경우에는 Azure Automation 앱을 통해 액세스할 수 있는 PowerShell 스크립트 실행 로그를 검사하는 것도 좋습니다.

자동화된 전자 메일의 최종 출력은 이 솔루션을 빌드하고 실행한 후에 수신되는 다음 전자 메일과 비슷합니다.

![자동 조정 전자 메일 알림의 샘플 전자 메일 출력](./media/sql-database-automatic-tuning-email-notifications/howto-email-07.png)

PowerShell 스크립트를 조정하여 자동화된 전자 메일의 출력 및 서식을 사용자 필요에 맞게 조정할 수 있습니다.

사용자 지정 시나리오에 따라 특정 조정 이벤트를 기준으로, 구독이나 데이터베이스가 여러 개 있는 경우에는 여러 받는 사람을 기준으로, 전자 메일 알림을 빌드하도록 솔루션을 추가로 사용자 지정할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- 자동 조정이 데이터베이스의 성능을 향상시키는 데 도움이 되는 방식에 대한 자세한 내용은 [Azure SQL Database의 자동 조정](sql-database-automatic-tuning.md)을 참조하세요.
- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 워크로드를 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 자동 조정 권장 사항을 수동으로 검토 및 적용하려면 [성능 권장 사항 찾기 및 적용](sql-database-advisor-portal.md)을 참조하세요.
