---
title: PowerShell 사용하여 Azure Application Insights 자동화 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 PowerShell에서 리소스, 경고 및 가용성 테스트 생성을 자동화합니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: d6bc4f69386cc8a9119aa852693456f6465f59ce
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
#  <a name="create-application-insights-resources-using-powershell"></a>PowerShell을 사용하여 Application Insights 리소스 만들기
이 문서에서는 Azure Resource Management를 사용하여 [Application Insights](app-insights-overview.md) 리소스의 생성 및 업데이트를 자동화하는 방법을 보여줍니다. 예를 들어 빌드 프로세스의 일부로 이 작업을 수행할 수 있습니다. 기본 Application Insights 리소스와 함께 [가용성 웹 테스트](app-insights-monitor-web-app-availability.md)를 만들고, [경고](app-insights-alerts.md)를 설정하고, [가격 책정 계층](app-insights-pricing.md)을 설정하고, 기타 Azure 리소스를 만들 수 있습니다.

이러한 리소스를 만드는 데 핵심 사항은 [Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)용 JSON 템플릿입니다. 간단히 말하면 절차는 다음과 같습니다. 기존 리소스의 JSON 정의를 다운로드하고, 이름과 같은 특정 값을 매개 변수화한 다음 새 리소스를 만들려고 할 때마다 템플릿을 실행합니다. 여러 리소스를 함께 패키지하여 모두 한꺼번에 만들 수 있습니다(예: 가용성 테스트, 경고 및 연속 내보내기에 대한 저장소를 포함하는 앱 모니터). 일부 매개 변수화에 있는 약간의 미묘한 사항은 여기서 설명합니다.

## <a name="one-time-setup"></a>일 회 설정
아직 Azure 구독에서 PowerShell을 사용한 적이 없을 경우:

스크립트를 실행하려는 컴퓨터에 Azure Powershell 모듈을 설치합니다.

1. [Microsoft 웹 플랫폼 설치 관리자(v5 이상)](http://www.microsoft.com/web/downloads/platform.aspx)를 설치합니다.
2. 이를 사용하여 Microsoft Azure Powershell을 설치합니다.

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기
새 .json 파일을 만듭니다. 이 예제에서는 `template1.json`입니다. 아래 내용을 이 파일에 복사합니다.

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Application Insights 리소스 만들기
1. PowerShell에서 Azure에 로그인합니다.
   
    `Connect-AzureRmAccount`
2. 다음과 같은 명령을 실행합니다.
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`은 새 리소스를 만들려는 그룹입니다.
   * `-TemplateFile`은 사용자 지정 매개 변수 앞에 있어야 합니다.
   * `-appName` 만들려는 리소스의 이름입니다.

다른 매개 변수를 추가할 수 있습니다. 템플릿의 매개 변수 섹션에서 해당 설명을 찾을 수 있습니다.

## <a name="to-get-the-instrumentation-key"></a>계측 키를 가져오려면
응용 프로그램 리소스를 만든 후 계측 키가 필요할 수 있습니다. 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>가격 계획 설정

[가격 계획](app-insights-pricing.md)을 설정할 수 있습니다.

위의 템플릿을 사용하여 엔터프라이즈 가격 계획이 있는 앱 리소스를 만들려면

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|계획|
|---|---|
|1|Basic|
|2|Enterprise|

* 기본적인 Basic 가격 계획만 사용하려는 경우 템플릿에서 CurrentBillingFeatures 리소스를 생략해도 됩니다.
* 구성 요소 리소스를 만든 후에 가격 계획을 변경하려는 경우 "microsoft.insights/components" 리소스를 생략하는 템플릿을 사용할 수 있습니다. 또한 청구 리소스에서 `dependsOn` 노드를 생략합니다. 

업데이트된 요금제를 확인하려면 브라우저에서 **사용량 및 예상 비용 페이지** 블레이드를 살펴보세요. **브라우저 보기를 새로 고쳐** 최신 상태를 표시합니다.



## <a name="add-a-metric-alert"></a>메트릭 경고 추가

앱 리소스와 동시에 메트릭 경고를 설정하려면 다음과 같이 코드를 템플릿 파일에 병합합니다.

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

템플릿을 호출할 때 선택적으로 이 매개 변수를 추가할 수 있습니다.

    `-responseTime 2`

물론 다른 필드를 매개 변수화할 수 있습니다. 

다른 경고 규칙의 형식 이름 및 구성 세부 정보를 찾으려면 수동으로 규칙을 만든 다음 [Azure Resource Manager](https://resources.azure.com/)에서 검사합니다. 


## <a name="add-an-availability-test"></a>가용성 테스트 추가

이 예는 ping 테스트(단일 페이지 테스트용)에 대한 것입니다.  

가용성 테스트에는 테스트 자체와 실패를 알리는 경고 부분인 **두 부분이 있습니다**.

다음 코드를 앱을 만드는 템플릿 파일에 병합합니다.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

다른 테스트 위치의 코드를 검색하거나 좀 더 복잡한 웹 테스트의 생성을 자동화하려면 수동으로 예제를 만든 다음 [Azure Resource Manager](https://resources.azure.com/)에서 코드를 매개 변수화합니다.

## <a name="add-more-resources"></a>리소스 추가

다양한 종류의 다른 리소스의 생성을 자동화하려면 수동으로 예제를 만든 다음 [Azure Resource Manager](https://resources.azure.com/)에서 해당 코드를 복사하고 매개 변수화합니다. 

1. [Azure 리소스 관리자](https://resources.azure.com/)를 엽니다. `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`를 통해 응용 프로그램 리소스로 이동합니다. 
   
    ![Azure 리소스 탐색기에서 탐색](./media/app-insights-powershell/01.png)
   
    *구성 요소* 는 응용 프로그램을 표시하기 위한 기본 Application Insights 리소스입니다. 연결된 경고 규칙 및 가용성 웹 테스트에 대한 별도의 리소스가 있습니다.
2. 구성 요소의 JSON을 `template1.json`의 적절한 위치에 복사합니다.
3. 다음 속성을 삭제합니다.
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. webtests 및 alertrules 섹션을 열고 개별 항목에 대한 JSON을 템플릿에 복사합니다. (webtests 또는 alertrules 노드에서 복사하지 말고 그 아래에 있는 항목으로 이동)
   
    각 웹 테스트에는 연결된 경고 규칙이 있으므로 둘 다 복사해야 합니다.
   
    메트릭에 대한 경고를 포함할 수도 있습니다. [메트릭 이름](app-insights-powershell-alerts.md#metric-names)
5. 각 리소스에 다음 줄을 삽입합니다.
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>템플릿 매개 변수화
이제 특정 이름을 매개 변수로 대체해야 합니다. [템플릿을 매개 변수화](../azure-resource-manager/resource-group-authoring-templates.md)하려면 [도우미 함수 집합](../azure-resource-manager/resource-group-template-functions.md)을 사용하여 식을 작성합니다. 

문자열의 일부만 매개 변수화할 수 없으므로 `concat()`을 사용하여 문자열을 빌드합니다.

다음은 만들 수 있는 대체 예제입니다. 각 대체가 여러 번 발생합니다. 템플릿에서 다른 사항이 필요할 수 있습니다. 이러한 예제에서는 템플릿의 위쪽에서 정의한 매개 변수 및 변수를 사용합니다.

| find | 대체 |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (소문자) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>GUID 및 ID를 삭제합니다. |

### <a name="set-dependencies-between-the-resources"></a>리소스 간의 종속성 설정
Azure에서는 엄격한 순서로 리소스를 설정해야 합니다. 다음 설정 시작 전에 하나의 설정이 완료되게 하려면 종속성 줄을 추가합니다.

* 가용성 테스트 리소스:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* 가용성 테스트에 대한 경고 리소스:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>다음 단계
다른 자동화 문서:

* [Application Insights 리소스 만들기](app-insights-powershell-script-create-resource.md) - 템플릿을 사용하지 않는 빠른 방법입니다.
* [경고 설정](app-insights-powershell-alerts.md)
* [웹 테스트 만들기](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Application Insights에 Azure 진단 보내기](app-insights-powershell-azure-diagnostics.md)
* [GitHub에서 Azure로 배포](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [릴리스 주석 만들기](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

