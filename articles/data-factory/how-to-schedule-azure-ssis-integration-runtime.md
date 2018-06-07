---
title: Azure SSIS 통합 런타임을 예약하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Automation 및 Data Factory를 사용하여 Azure SSIS 통합 런타임의 시작 및 중지를 예약하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 05/18/2018
ms.author: douglasl
ms.openlocfilehash: dfb54aeeff1b1f1640609be708e1b9d767a18c3a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360328"
---
# <a name="how-to-schedule-starting-and-stopping-of-an-azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임의 시작 및 중지를 예약하는 방법 
Azure SSIS(SQL Server Integration Services) IR(통합 런타임)을 실행할 때는 비용이 발생합니다. 따라서 Azure에서 SSIS 패키지를 실행해야 할 때만 IR을 실행하고 필요하지 않을 때는 중지할 수 있습니다. Data Factory UI 또는 Azure PowerShell을 사용하여 [Azure SSIS IR을 수동으로 시작 또는 중지 ](manage-azure-ssis-integration-runtime.md)할 수 있습니다. 이 문서에서는 Azure Automation 및 Azure Data Factory를 사용하여 Azure SSIS IR(통합 런타임)의 시작 및 중지를 예약하는 방법을 설명합니다. 이 문서에서 설명하는 개략적인 단계는 다음과 같습니다.

1. **Azure Automation Runbook 만들기 및 테스트.** 이 단계에서는 Azure SSIS IR을 시작하거나 중지하는 스크립트를 사용하여 PowerShell Runbook을 만듭니다. 그런 다음 START 및 STOP 시나리오에서 Runbook을 테스트하고 IR이 시작 또는 중지되는지 확인합니다. 
2. **Runbook에 대한 2가지 일정 만들기.** 첫 번째 일정에서는 작업으로 START를 사용해서 Runbook을 구성합니다. 두 번째 일정에서는 작업으로 STOP을 사용해서 Runbook을 구성합니다. 두 일정에 대해 Runbook이 실행되는 케이던스를 지정합니다. 예를 들어, 첫 번째 Runbook은 매일 오전 8시에 실행되고, 두 번째 Runbook은 매일 오후 11시에 실행되도록 예약할 수 있습니다. 첫 번째 Runbook이 실행될 때 Azure SSIS IR이 시작됩니다. 두 번째 Runbook이 실행될 때 Azure SSIS IR이 중지됩니다. 
3. START 작업 및 STOP 작업에 대해 하나씩 **Runbook에 대한 2개의 웹후크 만들기**. Data Factory 파이프라인에서 웹 작업을 구성할 때 이러한 웹후크의 URL을 사용합니다. 
4. **Data Factory 파이프라인 만들기**. 만드는 파이프라인은 세 가지 활동으로 구성됩니다. 첫 번째 **웹** 작업은 Azure SSIS IR을 시작하는 첫 번째 웹후크를 호출합니다. **저장 프로시저** 작업은 SSIS 패키지를 실행하는 SQL 스크립트를 실행합니다. 두 번째 **웹** 작업은 Azure SSIS IR을 중지합니다. 저장 프로시저 작업을 사용하여 Data Factory 파이프라인에서 SSIS 패키지를 호출하는 방법에 대한 자세한 내용은 [SSIS 패키지 호출](how-to-invoke-ssis-package-stored-procedure-activity.md)을 참조하세요. 그런 다음 지정한 케이던스에 파이프라인이 실행되도록 예약하는 일정 트리거를 만듭니다.

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. GA(일반 공급) 상태인 Data Factory 버전 1 서비스를 사용 중인 경우 [버전 1에서 저장 프로시저 작업을 사용하여 SSIS 패키지 호출](v1/how-to-invoke-ssis-package-stored-procedure-activity.md)을 참조하세요.

 
## <a name="prerequisites"></a>필수 조건
Azure SSIS 통합 런타임을 아직 프로비전하지 않은 경우 [자습서](tutorial-create-azure-ssis-runtime-portal.md)의 지침에 따라 프로비전합니다. 

## <a name="create-and-test-an-azure-automation-runbook"></a>Azure Automation Runbook 만들기 및 테스트
이 섹션에서는 다음 단계를 수행합니다. 

1. Azure Automation 계정을 만듭니다.
2. Azure Automation 계정에서 PowerShell Runbook을 만듭니다. Runbook과 연결된 PowerShell 스크립트는 사용자가 OPERATION 매개 변수에 지정한 명령에 따라 Azure SSIS IR 프로그램을 시작하거나 중지합니다. 
3. START 및 STOP 시나리오에서 Runbook을 테스트하여 제대로 작동하는지 확인합니다. 

### <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기
Azure Automation 계정이 없는 경우 이 단계의 지침에 따라 하나 만듭니다. 자세한 내용은 [Azure Automation 계정 만들기](../automation/automation-quickstart-create-account.md)를 참조하세요. 이 단계의 일부로 **Azure 실행** 계정(Azure Active Directory의 서비스 사용자)을 만든 후 Azure 구독의 **참가자** 역할에 추가합니다. Azure SSIS IR이 있는 데이터 팩터리가 포함된 구독과 같은 구독인지 확인합니다. Azure Automation 기능은 이 계정을 사용하여 Azure Resource Manager에서 인증을 받고 리소스에 작동합니다. 

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.    
3. 왼쪽 메뉴에서 **새로 만들기**를 선택하고 **모니터링 + 관리**를 선택한 후 **Automation**을 선택합니다. 

    ![새로 만들기 -> 모니터링 + 관리 -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/new-automation.png)
2. **Automation 계정 추가** 창에서 다음 단계를 수행합니다. 

    1. Automation 계정의 **이름**을 지정합니다. 
    2. Azure SSIS IR이 있는 데이터 팩터리를 포함하는 **구독**을 선택합니다. 
    3. **리소스 그룹**에 대해 **새로 만들기**를 선택하여 새 리소스 그룹을 만들거나 **기존 항목 사용**을선 택하여 기존 리소스 그룹을 선택합니다. 
    4. Automation 계정에 대한 **위치**를 선택합니다. 
    5. **실행 계정 만들기**가 **예**로 설정되어 있는지 확인합니다. 서비스 사용자가 Azure Active Directory에서 생성됩니다. 이 사용자는 Azure 구독의 **참가자** 역할에 추가됩니다.
    6. 포털의 대시보드에서 볼 수 있도록 **대시보드에 고정**을 선택합니다. 
    7. **만들기**를 선택합니다. 

        ![새로 만들기 -> 모니터링 + 관리 -> Automation](./media/how-to-schedule-azure-ssis-integration-runtime/add-automation-account-window.png)
3. **배포 상태**가 대시보드 및 알림에 표시됩니다. 
    
    ![Automation 배포 중](./media/how-to-schedule-azure-ssis-integration-runtime/deploying-automation.png) 
4. 성공적으로 만들어지면 Automation 계정에 대한 홈페이지가 표시됩니다. 

    ![Automation 홈페이지](./media/how-to-schedule-azure-ssis-integration-runtime/automation-home-page.png)

### <a name="import-data-factory-modules"></a>Data Factory 모듈 가져오기

1. 왼쪽 메뉴의 **공유 리소스** 섹션에서 **모듈**을 선택하고 모듈 목록에 **AzureRM.Profile** 및 **AzureRM.DataFactoryV2**가 표시되는지 확인합니다.

    ![필요한 모듈 확인](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image1.png)

2.  [AzureRM.DataFactoryV2 0.5.2 모듈](https://www.powershellgallery.com/packages/AzureRM.DataFactoryV2/0.5.2)에 대해 PowerShell 갤러리로 이동하여 **Azure Automation에 배포** 및 Automation 계정을 차례로 선택한 다음, **확인**을 선택합니다. 왼쪽 메뉴에서 **공유 리소스** 섹션의 **모듈**을 보려면 돌아가서 **AzureRM.DataFactoryV2 0.5.2** 모듈의 **상태**가 **사용 가능**으로 변경될 때까지 대기합니다.

    ![데이터 팩터리 모듈 확인](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image2.png)

3.  [AzureRM.Profile 4.5.0 모듈](https://www.powershellgallery.com/packages/AzureRM.profile/4.5.0)에 대해 PowerShell 갤러리로 이동하여 **Azure Automation에 배포**를 클릭하고 Automation 계정을 선택한 다음, **확인**을 선택합니다. 왼쪽 메뉴에서 **공유 리소스** 섹션의 **모듈**을 보려면 돌아가서 **AzureRM.Profile 4.5.0** 모듈의 **상태**가 **사용 가능**으로 변경될 때까지 대기합니다.

    ![프로필 모듈 확인](media/how-to-schedule-azure-ssis-integration-runtime/automation-fix-image3.png)

### <a name="create-a-powershell-runbook"></a>PowerShell Runbook 만들기
다음 절차에서는 PowerShell Runbook을 만드는 단계를 제공합니다. Runbook과 연결된 스크립트는 사용자가 **OPERATION** 매개 변수에 지정한 명령에 따라 Azure SSIS IR 프로그램을 시작/중지합니다. 이 섹션에서는 Runbook 만들기에 대한 세부 정보는 제공하지 않습니다. 자세한 내용은 [Runbook 만들기](../automation/automation-quickstart-create-runbook.md) 문서를 참조하세요.

1. **Runbook** 탭으로 전환한 후 도구 모음에서 **+ Runbook 추가**를 선택합니다. 

    ![Runbook 추가 단추](./media/how-to-schedule-azure-ssis-integration-runtime/runbooks-window.png)
2. **새 Runbook 만들기**를 선택하고 다음 단계를 수행합니다. 

    1. **이름**으로 **StartStopAzureSsisRuntime**을 입력합니다.
    2. **Runbook 형식**으로 **PowerShell**을 선택합니다.
    3. **만들기**를 선택합니다.
    
        ![Runbook 추가 단추](./media/how-to-schedule-azure-ssis-integration-runtime/add-runbook-window.png)
3. Runbook 스크립트 창에 다음 스크립트를 복사/붙여넣습니다. 도구 모음에서 **저장** 및 **게시** 단추를 사용하여 Runbook을 저장한 후 게시합니다. 

    ```powershell
    Param
    (
          [Parameter (Mandatory= $true)]
          [String] $ResourceGroupName,
    
          [Parameter (Mandatory= $true)]
          [String] $DataFactoryName,
    
          [Parameter (Mandatory= $true)]
          [String] $AzureSSISName,
    
          [Parameter (Mandatory= $true)]
          [String] $Operation
    )
    
    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
    
        "Logging in to Azure..."
        Connect-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
        if (!$servicePrincipalConnection)
        {
            $ErrorMessage = "Connection $connectionName not found."
            throw $ErrorMessage
        } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
        }
    }
    
    if($Operation -eq "START" -or $operation -eq "start")
    {
        "##### Starting #####"
        Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $AzureSSISName -Force
    }
    elseif($Operation -eq "STOP" -or $operation -eq "stop")
    {
        "##### Stopping #####"
        Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    }  
    "##### Completed #####"    
    ```

    ![PowerShell Runbook 편집](./media/how-to-schedule-azure-ssis-integration-runtime/edit-powershell-runbook.png)
5. 도구 모음에서 **시작** 단추를 선택하여 Runbook을 테스트합니다. 

    ![Runbook 시작 단추](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-button.png)
6. **Runbook 시작** 창에서 다음 단계를 수행합니다. 

    1. **리소스 그룹 이름**으로 Azure SSIS IR이 있는 데이터 팩터리를 포함하는 리소스 그룹의 이름을 입력합니다. 
    2. **데이터 팩터리 이름**으로 Azure SSIS IR이 있는 데이터 팩터리의 이름을 입력합니다. 
    3. **AZURESSISNAME**으로 Azure SSIS IR의 이름을 입력합니다. 
    4. **OPERATION**에 **START**를 입력합니다. 
    5. **확인**을 선택합니다.  

        ![Runbook 시작 창](./media/how-to-schedule-azure-ssis-integration-runtime/start-runbook-window.png)
7. 작업 창에서 **출력** 타일을 선택합니다. 작업의 출력 창에서 **##### Starting #####** 메시지가 표시된 후에 **##### Completed #####** 메시지가 표시될 때까지 기다립니다. Azure SSIS IR을 시작하는 데 20분 정도 걸립니다. **작업** 창을 닫고 **Runbook** 창으로 돌아갑니다.

    ![Azure SSIS IR - 시작됨](./media/how-to-schedule-azure-ssis-integration-runtime/start-completed.png)
8.  위의 두 단계를 반복하되, **OPERATION** 값으로 **STOP**을 사용합니다. 도구 모음에서 **시작** 단추를 선택하여 Runbook을 다시 시작합니다. 리소스 그룹 이름, 데이터 팩터리 이름 및 Azure SSIS IR 이름을 지정합니다. **OPERATION**에 대해 **STOP**을 입력합니다. 

    작업의 출력 창에서 **##### Stopping #####** 메시지가 표시된 후에 **##### Completed #####** 메시지가 표시될 때까지 기다립니다. Azure SSIS IR 중지는 Azure SSIS IR 시작만큼 오래 걸리지 않습니다. **작업** 창을 닫고 **Runbook** 창으로 돌아갑니다.

## <a name="create-schedules-for-the-runbook-to-startstop-the-azure-ssis-ir"></a>Azure SSIS IR을 시작/중지하기 위한 Rrunbook 일정 만들기
이전 섹션에서는 Azure SSIS IR을 시작하거나 중지할 수 있는 Azure Automation Runbook을 만들었습니다. 이 섹션에서는 Runbook에 대한 2가지 일정을 만듭니다. 첫 번째 일정을 구성할 때는 OPERATION 매개 변수에 대해 START를 지정합니다. 마찬가지로, 두 번째 일정을 구성할 때는 OPERATION 매개 변수에 대해 STOP을 지정합니다. 일정을 만들기 위한 자세한 단계는 [일정 만들기](../automation/automation-schedules.md#creating-a-schedule)를 참조하세요.

1. **Runbook** 창에서 **일정**을 선택하고 도구 모음에서 **+ 일정 추가**를 선택합니다. 

    ![Azure SSIS IR - 시작됨](./media/how-to-schedule-azure-ssis-integration-runtime/add-schedules-button.png)
2. **Runbook 예약** 창에서 다음 단계를 수행합니다. 

    1. **Runbook에 일정 연결**을 선택합니다. 
    2. **새 일정 만들기**를 선택합니다.
    3. **새 일정** 창에서 **이름**으로 **IR 매일 시작**을 입력합니다. 
    4. **시작 섹션**에서 시간으로, 현재 시간보다 몇 분 이전 시간을 지정합니다. 
    5. **되풀이**에 대해 **정기**를 선택합니다. 
    6. **되풀이 간격** 섹션에서 **일**을 선택합니다. 
    7. **만들기**를 선택합니다. 

        ![Azure SSIS IR 시작 일정](./media/how-to-schedule-azure-ssis-integration-runtime/new-schedule-start.png)
3. **매개 변수 및 실행 설정** 탭으로 전환합니다. 리소스 그룹 이름, 데이터 팩터리 이름 및 Azure SSIS IR 이름을 지정합니다. **OPERATION**에 **START**를 입력합니다. **확인**을 선택합니다. **확인**을 다시 선택하여 Runbook의 **일정** 페이지에서 일정을 확인합니다. 

    ![Azure SSIS IR 시작 일정](./media/how-to-schedule-azure-ssis-integration-runtime/start-schedule.png)
4. 이전 2단계를 반복해서 **IR 매일 중지**라는 일정을 만듭니다. **IR 매일 시작** 일정에 대해 지정한 시간보다 30분 이상 더 빠른 시간을 지정합니다. **OPERATION**으로 **STOP**을 지정합니다. 
5. **Runbook** 창의 왼쪽 메뉴에서 **작업**을 선택합니다. 지정된 시간에 일정에 따라 만들어진 작업과 해당 상태가 표시됩니다. Runbook 테스트 시 표시된 것과 비슷한 출력을 통해 작업에 대한 세부 정보를 확인할 수 있습니다. 

    ![Azure SSIS IR 시작 일정](./media/how-to-schedule-azure-ssis-integration-runtime/schedule-jobs.png)
6. 테스트가 완료된 후 일정을 편집하고 **사용**에 대해 **아니요**를 선택하여 일정을 사용하지 않도록 설정합니다. 왼쪽 메뉴에서 **일정**을 선택하고 **IR 시작 매일/IR 매일 중지**를 선택한 후 **사용**에 대해 **아니요**를 선택합니다. 

## <a name="create-webhooks-to-start-and-stop-the-azure-ssis-ir"></a>Azure SSIS IR을 시작 및 중지하는 웹후크 만들기
[웹후크 만들기](../automation/automation-webhooks.md#creating-a-webhook)의 지침에 따라 Runbook에 대한 2개의 웹후크를 만듭니다. 첫 번째 웹후크의 경우 OPERATION으로 START를 지정하고, 두 번째 웹후크의 경우 OPERATION으로 STOP을 지정합니다. 두 웹후크의 URL을 임의 위치(예: 텍스트 파일 또는 OneNote 전자 필기장)에 저장합니다. Data Factory 파이프라인에서 웹 작업을 구성할 때 이러한 URL을 사용합니다. 다음 이미지는 Azure SSIS IR을 시작하는 웹후크를 만드는 방법 예제를 표시합니다.

1. **Runbook** 창의 왼쪽 메뉴에서 **웹후크**를 선택하고 도구 모음에서 **+ 웹후크 추가**를 선택합니다. 

    ![웹후크 -> 웹후크 추가](./media/how-to-schedule-azure-ssis-integration-runtime/add-web-hook-menu.png)
2. **웹후크 추가** 창에서 **새 웹후크 만들기**를 선택하고 다음 작업을 수행합니다. 

    1. **이름**으로 **StartAzureSsisIR**을 입력합니다. 
    2. **사용**이 **예**로 설정되어 있는지 확인합니다. 
    3. **URL**을 복사한 후 저장합니다. 이 단계는 중요합니다. 이 URL이 나중에는 표시되지 않기 때문입니다. 
    4. **확인**을 선택합니다. 

        ![새 웹후크 창](./media/how-to-schedule-azure-ssis-integration-runtime/new-web-hook-window.png)
3. **매개 변수 및 실행 설정** 탭으로 전환합니다. 리소스 그룹 이름, 데이터 팩터리 이름 및 Azure SSIS IR 이름을 지정합니다. **OPERATION**에 **START**를 입력합니다. **확인**을 클릭합니다. 그런 다음에 **만들기**를 클릭합니다. 

    ![웹후크 - 매개 변수 및 실행 설정](./media/how-to-schedule-azure-ssis-integration-runtime/webhook-parameters.png)
4. 앞의 세 단계를 반복해서 **StopAzureSsisIR**이라는 다른 웹후크를 만듭니다. URL을 복사하는 것을 잊지마세요. 매개 변수 및 실행 설정을 지정할 때 **OPERATION**에 대해 **STOP**을 입력합니다. 

두 개의 URL, 즉 **StartAzureSsisIR** 웹후크에 대한 URL과 **StopAzureSsisIR** 웹후크에 대한 URL이 있어야 합니다. 이러한 URL로 HTTP POST 요청을 전송하여 Azure SSIS IR을 시작/중지할 수 있습니다. 

## <a name="create-and-schedule-a-data-factory-pipeline-that-startsstops-the-ir"></a>IR을 시작/중지하는 Data Factory 파이프라인 만들기 및 예약
이 섹션에서는 웹 작업을 사용하여 이전 섹션에서 만든 웹후크를 호출하는 방법을 보여 줍니다.

만드는 파이프라인은 세 가지 활동으로 구성됩니다. 

1. 첫 번째 **웹** 작업은 Azure SSIS IR을 시작하는 첫 번째 웹후크를 호출합니다. 
2. **SSIS 패키지 실행** 작업 또는 **저장 프로시저** 작업은 SSIS 패키지를 실행합니다.
3. 두 번째 **웹** 작업은 Azure SSIS IR을 중지하는 웹후크를 호출합니다. 

이 파이프라인을 만들고 테스트한 후에는 일정 트리거를 만들고 파이프라인과 연결합니다. 일정 트리거는 파이프라인에 대한 일정을 정의합니다. 매일 오후 11시에 실행되도록 예약된 트리거를 만든다고 가정해보겠습니다. 이 트리거는 매일 오후 11시에 파이프라인을 실행합니다. 이 파이프라인은 Azure SSIS IR을 시작하고, SSIS 패키지를 실행한 후 Azure SSIS IR을 중지합니다. 

### <a name="create-a-data-factory"></a>데이터 팩터리를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.    
2. 왼쪽 메뉴에서 **새로 만들기**를 클릭하고 **데이터 + 분석**, **Data Factory**를 차례로 클릭합니다. 
   
   ![새로 만들기->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **새 데이터 팩터리** 페이지에서 **이름**에 대해 **MyAzureSsisDataFactory**를 입력합니다. 
      
     ![새 데이터 팩터리 페이지](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure Data Factory의 이름은 **전역적으로 고유**해야 합니다. 다음 오류가 표시되는 경우 데이터 팩터리 이름을 변경하고(예: yournameMyAzureSsisDataFactory) 다시 만듭니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory - 명명 규칙](naming-rules.md) 문서를 참조하세요.
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. 데이터 팩터리를 만들려는 위치에 Azure **구독**을 선택합니다. 
4. **리소스 그룹**에 대해 다음 단계 중 하나를 수행합니다.
     
      - **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. 
      - **새로 만들기**를 선택하고 리소스 그룹의 이름을 입력합니다.   
         
      리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/resource-group-overview.md)를 참조하세요.  
4. **버전**에 **V2(미리 보기)** 를 선택합니다.
5. 데이터 팩터리의 **위치** 를 선택합니다. 데이터 팩터리 만들기를 지원하는 위치만 목록에 표시됩니다.
6. **대시보드에 고정**을 선택합니다.     
7. **만들기**를 클릭합니다.
8. 대시보드에서 **데이터 팩터리 배포 중** 상태의 타일이 표시됩니다. 

    ![데이터 팩터리 배포 중 타일](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
9. 만들기가 완료되면 이미지와 같은 **Data Factory** 페이지가 표시됩니다.
   
   ![데이터 팩터리 홈페이지](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
10. **작성 및 모니터링**을 클릭하여 별도의 탭에서 Data Factory UI(사용자 인터페이스)를 시작합니다.

### <a name="create-a-pipeline"></a>파이프라인을 만듭니다.

1. **시작** 페이지에서 **파이프라인 만들기**를 선택합니다. 

   ![시작 페이지](./media/how-to-schedule-azure-ssis-integration-runtime/get-started-page.png)
2. **작업** 도구 상자에서 **일반**을 펼치고, **웹** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. **속성** 창의 **일반** 탭에서 작업 이름을 **StartIR**로 변경합니다.

   ![첫 번째 웹 작업 - 일반 탭](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-general-tab.png)
3. **속성** 창에서 **설정** 탭으로 전환하고 다음 작업을 수행합니다. 

    1. **URL**에 Azure SSIS IR을 시작하는 웹후크의 URL을 붙여 넣습니다. 
    2. **메서드**로 **POST**를 선택합니다. 
    3. **본문**에 `{"message":"hello world"}`를 입력합니다. 
   
        ![첫 번째 웹 작업 - 설정 탭](./media/how-to-schedule-azure-ssis-integration-runtime/first-web-activity-settnigs-tab.png)

4. SSIS 패키지 실행 작업 또는 **작업** 도구 모음의 **일반** 섹션에서 저장 프로시저 작업을 끌어서 놓습니다. 작업 이름을 **RunSSISPackage**로 설정합니다. 

5. SSIS 패키지 실행 작업을 선택하는 경우 [Azure Data Factory에서 SSIS 작업을 사용하여 SSIS 패키지 실행](how-to-invoke-ssis-package-ssis-activity.md)의 지침을 따라 작업 생성을 완료합니다.  시작하는 데 최대 30분이 걸리므로 Azure-SSIS IR의 가용성을 기다리는 데 충분한 빈도인 충분한 다시 시도 횟수를 지정해야 합니다. 

    ![다시 시도 설정](media/how-to-schedule-azure-ssis-integration-runtime/retry-settings.png)

6. 저장 프로시저 작업을 선택하는 경우 [Azure Data Factory에서 저장 프로시저 작업을 사용하여 SSIS 패키지 호출](how-to-invoke-ssis-package-stored-procedure-activity.md)의 지침을 따라 작업 생성을 완료합니다. 시작하는 데 최대 30분이 걸리므로 Azure-SSIS IR의 가용성을 기다리는 Transact-SQL 스크립트를 삽입해야 합니다.
    ```sql
    DECLARE @return_value int, @exe_id bigint, @err_msg nvarchar(150)

    -- Wait until Azure-SSIS IR is started
    WHILE NOT EXISTS (SELECT * FROM [SSISDB].[catalog].[worker_agents] WHERE IsEnabled = 1 AND LastOnlineTime > DATEADD(MINUTE, -10, SYSDATETIMEOFFSET()))
    BEGIN
        WAITFOR DELAY '00:00:01';
    END

    EXEC @return_value = [SSISDB].[catalog].[create_execution] @folder_name=N'YourFolder',
        @project_name=N'YourProject', @package_name=N'YourPackage',
        @use32bitruntime=0, @runincluster=1, @useanyworker=1,
        @execution_id=@exe_id OUTPUT 

    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1

    EXEC [SSISDB].[catalog].[start_execution] @execution_id = @exe_id, @retry_count = 0

    -- Raise an error for unsuccessful package execution, check package execution status = created (1)/running (2)/canceled (3)/
    -- failed (4)/pending (5)/ended unexpectedly (6)/succeeded (7)/stopping (8)/completed (9) 
    IF (SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id = @exe_id) <> 7 
    BEGIN
        SET @err_msg=N'Your package execution did not succeed for execution ID: '+ CAST(@execution_id as nvarchar(20))
        RAISERROR(@err_msg, 15, 1)
    END
    ```

7. **웹** 작업을 **SSIS 패키지 실행** 또는 **저장 프로시저** 작업에 연결합니다. 

    ![웹 및 저장 프로시저 활동 연결](./media/how-to-schedule-azure-ssis-integration-runtime/connect-web-sproc.png)

8. 다른 **웹** 작업을 **SSIS 패키지 실행** 작업 또는 **저장 프로시저** 작업의 오른쪽으로 끌어서 놓습니다. 작업 이름을 **StopIR**로 설정합니다. 
9. **속성** 창에서 **설정** 탭으로 전환하고 다음 작업을 수행합니다. 

    1. **URL**에 Azure SSIS IR을 중지하는 웹후크의 URL을 붙여 넣습니다. 
    2. **메서드**로 **POST**를 선택합니다. 
    3. **본문**에 `{"message":"hello world"}`를 입력합니다.  
10. **SSIS 패키지 실행** 작업 또는 **저장 프로시저** 작업을 마지막 **웹** 작업에 연결합니다.

    ![전체 파이프라인](./media/how-to-schedule-azure-ssis-integration-runtime/full-pipeline.png)
11. 도구 모음에서 **유효성 검사**를 클릭하여 파이프라인 설정에 대한 유효성을 검사합니다. **>>** 단추를 클릭하여 **파이프라인 유효성 검사 보고서**를 닫습니다. 

    ![파이프라인 유효성 검사](./media/how-to-schedule-azure-ssis-integration-runtime/validate-pipeline.png)

### <a name="test-run-the-pipeline"></a>파이프라인 실행 테스트

1. 파이프라인에 대한 도구 모음에서 **테스트 실행**을 선택합니다. **출력** 창의 아래쪽에 출력이 표시됩니다. 

    ![테스트 실행](./media/how-to-schedule-azure-ssis-integration-runtime/test-run-output.png)
2. Azure Automation 계정의 **Runbook** 페이지에서 Azure SSIS IR을 시작 및 중지하는 작업이 실행되었는지 확인할 수 있습니다. 

    ![Runbook 작업](./media/how-to-schedule-azure-ssis-integration-runtime/runbook-jobs.png)
3. SQL Server Management Studio를 시작합니다. **서버에 연결** 창에서 다음 작업을 수행합니다. 

    1. **서버 이름**에서 **&lt;Azure SQL Database&gt;.database.windows.net**을 지정합니다.
    2. **옵션 >>** 을 선택합니다.
    3. **데이터베이스에 연결**에 대해 **SSISDB**를 선택합니다.
    4. **연결**을 선택합니다. 
    5. **Integration Services 카탈로그** -> **SSISDB** -> 사용자 폴더 -> **프로젝트** -> 사용자 SSIS 프로젝트 -> **패키지**를 펼칩니다. 
    6. SSIS 패키지를 마우스 오른쪽 단추로 클릭하고 **보고서** -> **표준 보고서** -> **모든 실행**을 선택합니다. 
    7. SSIS 패키지가 실행되었는지 확인합니다. 

        ![SSIS 패키지 실행 확인](./media/how-to-schedule-azure-ssis-integration-runtime/verfiy-ssis-package-run.png)

### <a name="schedule-the-pipeline"></a>파이프라인 예약 
파이프라인이 예상대로 작동하므로 이 파이프라인을 지정된 케이던스에 실행하는 트리거를 만들 수 있습니다. 일정 트리거를 파이프라인에 연결하는 방법에 대한 자세한 내용은 [일정에 따라 파이프라인 트리거](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

1. 파이프라인에 대한 도구 모음에서 **트리거**를 선택하고 **새로 만들기/편집**을 선택합니다. 

    ![트리거 -> 새로 만들기/편집](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-new-menu.png)
2. **트리거 추가** 창에서 **+ 새로 만들기**를 선택합니다.

    ![트리거 추가 - 새로 만들기](./media/how-to-schedule-azure-ssis-integration-runtime/add-triggers-new.png)
3. **새 트리거**에서 다음 작업을 수행합니다. 

    1. **이름**에 트리거에 대한 이름을 지정합니다. 다음 예제에서는 **매일 실행**이 트리거의 이름입니다. 
    2. **형식**으로 **예약**을 선택합니다. 
    3. **시작 날짜**에 대해 시작 날짜와 시간을 선택합니다. 
    4. **되풀이**에 대해 트리거에 대한 일정을 지정합니다. 다음 예제에서는 매일 1번입니다. 
    5. **종료**에 대해 **지정한 날짜** 옵션을 선택하여 날짜 및 시간을 지정할 수 있습니다. 
    6. **활성화됨**을 선택합니다. 트리거는 Data Factory에 솔루션을 게시한 직후에 활성화됩니다. 
    7. **다음**을 선택합니다.

        ![트리거 -> 새로 만들기/편집](./media/how-to-schedule-azure-ssis-integration-runtime/new-trigger-window.png)
4. **트리거 실행 매개 변수** 페이지에서 경고를 검토하고 **마침**을 선택합니다. 
5. 왼쪽 창에서 **모두 게시**를 선택하여 Data Factory에 솔루션을 게시합니다. 

    ![모두 게시](./media/how-to-schedule-azure-ssis-integration-runtime/publish-all.png)
6. 트리거 실행 및 파이프라인 실행을 모니터링하려면 왼쪽의 **모니터링** 탭을 사용합니다. 자세한 내용은 [파이프라인 모니터링](quickstart-create-data-factory-portal.md#monitor-the-pipeline)을 참조하세요.

    ![파이프라인 실행](./media/how-to-schedule-azure-ssis-integration-runtime/pipeline-runs.png)
7. 파이프라인 실행과 연결된 작업 실행을 보려면 **작업** 열에서 첫 번째 링크(**작업 실행 보기**)를 선택합니다. 파이프라인의 각 활동과 관련된 세 가지 활동 실행(첫 번째 웹 활동, 저장 프로시저 활동 및 두 번째 웹 활동)이 표시됩니다. 되돌아가서 파이프라인 실행을 보려면 위쪽의 **파이프라인** 링크를 선택합니다.

    ![작업 실행](./media/how-to-schedule-azure-ssis-integration-runtime/activity-runs.png)
8. 위쪽의 **파이프라인 실행** 옆에 있는 드롭다운 목록에서 **트리거 실행**을 선택하여 트리거 실행을 볼 수도 있습니다. 

    ![트리거 실행](./media/how-to-schedule-azure-ssis-integration-runtime/trigger-runs.png)

## <a name="next-steps"></a>다음 단계
SSIS 설명서에서 다음 문서를 참조하세요. 

- [Azure에서 SSIS 패키지 배포, 실행 및 모니터링](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure에서 SSIS 카탈로그에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에서 패키지 실행 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 인증을 사용하는 온-프레미스 데이터 원본에 연결](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)
