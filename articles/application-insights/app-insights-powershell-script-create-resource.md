---
title: Application Insights 리소스를 만들기 위한 PowerShell 스크립트 | Microsoft Docs
description: Application Insights 리소스의 생성을 자동화합니다.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: d06b44246b694c0d2a83503ecd1ae0cedfadd9ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Application Insights 리소스를 만들기 위한 PowerShell 스크립트


[Azure Application Insights](https://azure.microsoft.com/services/application-insights/)으로 새 응용 프로그램 또는 응용 프로그램의 새 버전을 모니터링 하려는 경우, Microsoft Azure에서 새 리소스를 설정합니다. 이 리소스는 앱이 분석하고 표시한 원격 분석 데이터에 있습니다. 

PowerShell을 사용하여 새 리소스의 생성을 자동화할 수 있습니다.

예를 들어 모바일 장치 앱을 개발하는 경우, 언제든지 고객이 사용 중인 앱에는 게시된 여러 버전이 있을 가능성이 있습니다. 혼합된 서로 다른 버전의 원격 분석 결과를 가져오지 않으려고 합니다. 따라서 각 빌드에 대한 새 리소스를 만드는 빌드 프로세스를 가져옵니다.

> [!NOTE]
> 모든 리소스 집합을 동시에 만들려면 [Azure 템플릿을 사용하여 리소스를 만드는](app-insights-powershell.md) 것이 좋습니다.
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Application Insights 리소스를 만들기 위한 스크립트
관련 cmdlet 사양을 참조하세요.

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell 스크립트*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>iKey로 수행할 작업
각 리소스는 해당 계측 키(iKey)로 식별됩니다. iKey는 리소스 생성 스크립트의 출력입니다. 빌드 스크립트는 앱에 포함된 Application Insights SDK에 iKey를 제공해야 합니다.

SDK에 사용할 수 있는 iKey에는 두 가지가 있습니다:

* [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)에서: 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* 또는 [초기화 코드](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>참고 항목
* [서식 파일에서 Application Insights 및 웹 테스트 리소스 만들기](app-insights-powershell.md)
* [PowerShell 사용한 Azure 진단의 모니터링 설정](app-insights-powershell-azure-diagnostics.md) 
* [PowerShell을 사용하여 경고 설정](app-insights-powershell-alerts.md)

