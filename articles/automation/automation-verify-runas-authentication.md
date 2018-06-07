---
title: Azure Automation 계정 구성 유효성 검사
description: 이 문서에서는 Automation 계정 구성이 제대로 설정되었는지 확인하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9bf5a3a1dc67752e3f911d07f23c7b408e6d38c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Azure Automation 실행 계정 인증 테스트
Automation 계정이 성공적으로 만들어지면 새로 만들어지거나 업데이트된 Automation 실행 계정을 사용하여 Azure Resource Manager 또는 Azure 클래식 배포에서 성공적으로 인증할 수 있는지를 확인하는 간단한 테스트를 수행할 수 있습니다.    

## <a name="automation-run-as-authentication"></a>Automation 실행 인증
아래 샘플 코드를 사용하여 [PowerShell Runbook을 만들어](automation-creating-importing-runbook.md) 실행 계정 사용 인증을 확인하고 사용자 지정 Runbook에서도 Automation 계정으로 리소스 관리자 리소스를 인증하고 관리합니다.   

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

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Runbook - **Connect-AzureRmAccount**에서 인증에 사용되는 cmdlet은 *ServicePrincipalCertificate* 매개 변수 집합을 사용합니다.  이것은 자격 증명이 아니라 서비스 주체 인증서를 사용하여 인증합니다.  

실행 계정의 유효성을 검증하기 위해 [Runbook을 실행](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)할 경우 [Runbook 작업](automation-runbook-execution.md)이 만들어지고, 작업 페이지가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중* 으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  Runbook 작업이 완료되면 **완료됨** 상태가 나타나야 합니다.

Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.  **출력** 페이지에서 인증에 성공하고 구독의 모든 리소스 그룹에서 모든 리소스 목록을 반환하는 것을 확인할 수 있습니다.  

단, Runbook에 코드를 다시 사용할 경우 `#Get all ARM resources from all resource groups` 주석으로 시작하는 코드 블록을 반드시 제거하세요.

## <a name="classic-run-as-authentication"></a>클래식 실행 인증
아래 샘플 코드를 사용하여 [PowerShell Runbook을 만들어](automation-creating-importing-runbook.md) 실행 계정 사용 인증을 확인하고 사용자 지정 Runbook에서도 클래식 배포 모델의 리소스를 인증하고 관리합니다.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

실행 계정의 유효성을 검증하기 위해 [Runbook을 실행](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)할 경우 [Runbook 작업](automation-runbook-execution.md)이 만들어지고, 작업 페이지가 표시되며, **작업 요약** 타일에 작업 상태가 표시됩니다. 작업 상태는 클라우드의 Runbook 작업자가 사용 가능해질 때까지 기다리고 있음을 나타내는 *대기 중* 으로 시작합니다. 작업자가 작업을 요구한 경우, *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.  Runbook 작업이 완료되면 **완료됨** 상태가 나타나야 합니다.

Runbook의 자세한 결과를 보려면 **출력** 타일을 클릭합니다.  **출력** 페이지에서 인증에 성공하고 구독에서 배포된 모든 VMName별 Azure VM 목록을 반환하는 것을 확인할 수 있습니다.  

단, Runbook에 코드를 다시 사용할 경우 cmdlet **Get-AzureVM**을 반드시 제거하세요.

## <a name="next-steps"></a>다음 단계
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
