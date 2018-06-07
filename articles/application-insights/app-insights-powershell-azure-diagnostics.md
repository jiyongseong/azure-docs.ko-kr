---
title: Azure에서 PowerShell을 사용하여 Application Insights 설정 | Microsoft Docs
description: Application Insights에 대한 파이프에 Azure 진단 자동화 구성
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 4ac803a8-f424-4c0c-b18f-4b9c189a64a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: mbullwin
ms.openlocfilehash: 1be5e07da1f8d9ba2db6bbe37c84fa242b830d35
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868163"
---
# <a name="using-powershell-to-set-up-application-insights-for-an-azure-web-app"></a>PowerShell을 사용하여 Azure 웹앱에서 Application Insights 설정
[Microsoft Azure](https://azure.com)는 [Azure Application Insights](app-insights-overview.md)에 [Azure 진단을 보내도록 구성](app-insights-azure-diagnostics.md)될 수 있습니다. 진단은 Azure Cloud Services 및 Azure VM과 연관됩니다. 이들 항목은 Application Insights SDK를 사용하여 앱 내부에서 보내는 원격 분석을 보완합니다. Azure에서 새 리소스 생성 과정에 대한 자동화의 일환으로 PowerShell을 사용하여 진단을 구성할 수 있습니다.

## <a name="azure-template"></a>Azure 템플릿
웹앱이 Azure에 있고 Azure Resource Manager 템플릿을 사용하여 리소스를 만드는 경우 리소스 노드에 이를 추가하여 Application Insights를 구성할 수 있습니다.

    {
      resources: [
        /* Create Application Insights resource */
        {
          "apiVersion": "2015-05-01",
          "type": "microsoft.insights/components",
          "name": "nameOfAIAppResource",
          "location": "centralus",
          "kind": "web",
          "properties": { "ApplicationId": "nameOfAIAppResource" },
          "dependsOn": [
            "[concat('Microsoft.Web/sites/', myWebAppName)]"
          ]
        }
       ]
     } 

* `nameOfAIAppResource` - Application Insights 리소스의 이름
* `myWebAppName` - 웹앱의 ID

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>클라우드 서비스 배포의 일부로 진단 확장을 사용하도록 설정
`New-AzureDeployment` cmdlet에는 `ExtensionConfiguration` 매개 변수가 있으며, 진단 구성의 배열을 사용합니다. 이것은 `New-AzureServiceDiagnosticsExtensionConfig` cmdlet을 사용하여 만들 수 있습니다. 예: 

```ps

    $service_package = "CloudService.cspkg"
    $service_config = "ServiceConfiguration.Cloud.cscfg"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

    $primary_storagekey = (Get-AzureStorageKey `
     -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
       -StorageAccountName $diagnostics_storagename `
       -StorageAccountKey $primary_storagekey

    $webrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WebRole" -Storage_context $storageContext `
      -DiagnosticsConfigurationPath $webrole_diagconfigpath
    $workerrole_diagconfig = `
     New-AzureServiceDiagnosticsExtensionConfig `
      -Role "WorkerRole" `
      -StorageContext $storage_context `
      -DiagnosticsConfigurationPath $workerrole_diagconfigpath

    New-AzureDeployment `
      -ServiceName $service_name `
      -Slot Production `
      -Package $service_package `
      -Configuration $service_config `
      -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)

``` 

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>기존 클라우드 서비스에 진단 확장을 사용하도록 설정
기존 서비스에서 `Set-AzureServiceDiagnosticsExtension`을 사용합니다.

```ps

    $service_name = "MyService"
    $diagnostics_storagename = "myservicediagnostics"
    $webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml" 
    $workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"
    $primary_storagekey = (Get-AzureStorageKey `
         -StorageAccountName "$diagnostics_storagename").Primary
    $storage_context = New-AzureStorageContext `
        -StorageAccountName $diagnostics_storagename `
        -StorageAccountKey $primary_storagekey

    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $webrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WebRole" 
    Set-AzureServiceDiagnosticsExtension `
        -StorageContext $storage_context `
        -DiagnosticsConfigurationPath $workerrole_diagconfigpath `
        -ServiceName $service_name `
        -Slot Production `
        -Role "WorkerRole"
```

## <a name="get-current-diagnostics-extension-configuration"></a>현재 진단 확장 구성 가져오기
```ps

    Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```


## <a name="remove-diagnostics-extension"></a>진단 확장 제거
```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Role 매개 변수 없이 `Set-AzureServiceDiagnosticsExtension` 또는 `New-AzureServiceDiagnosticsExtensionConfig`를 사용하여 진단 확장을 사용하도록 설정했다면, Role 매개 변수 없이 `Remove-AzureServiceDiagnosticsExtension`을 사용하여 확장을 제거할 수 있습니다. Role 매개 변수가 확장을 사용하도록 설정할 때 사용되었으면, 확장을 제거할 때도 사용되어야 합니다.

각각의 개별 역할에서 진단 확장을 제거하려면:

```ps

    Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```


## <a name="see-also"></a>참고 항목
* [Application Insights로 Azure Cloud Services 앱 모니터링](app-insights-cloudservices.md)
* [Application Insights에 Azure 진단 보내기](app-insights-azure-diagnostics.md)
* [구성 경고 자동화](app-insights-powershell-alerts.md)

