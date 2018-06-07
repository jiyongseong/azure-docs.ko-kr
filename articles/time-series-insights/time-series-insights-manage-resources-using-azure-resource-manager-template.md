---
title: Azure Resource Manager 템플릿을 사용하여 Azure Time Series Insights 환경을 관리하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager를 사용하여 프로그래밍 방식으로 Azure Time Series Insights 환경을 관리하는 방법을 설명합니다.
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: 8355248f28a019ef4712f542c8eac731362330ce
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Time Series Insights 리소스 만들기

이 문서에서는 Azure Resource Manager 템플릿, PowerShell 및 Time Series Insights 리소스 공급자를 사용하여 Time Series Insights 리소스를 만들고 배포하는 방법을 설명합니다.

Time Series Insights은 다음 리소스를 지원합니다.
   | 리소스 | 설명 |
   | --- | --- |
   | Environment | Time Series Insights 환경은 이벤트 broker에서 읽고, 저장되고, 쿼리에 대해 사용할 수 있는 이벤트의 논리적 그룹입니다. 자세한 내용은 [Azure Time Series Insights 환경 계획](time-series-insights-environment-planning.md)을 참조하세요. |
   | 이벤트 원본 | 이벤트 원본은 Time Series Insights가 이벤트를 읽고 환경에 수집하는 이벤트 broker에 대한 연결입니다. 현재 지원되는 이벤트 원본은 IoT Hub 및 Event Hub입니다. |
   | 참조 데이터 집합 | 참조 데이터 집합은 환경에서 이벤트에 대한 메타데이터를 제공합니다. 참조 데이터 집합의 메타데이터는 수신 중에 이벤트와 함께 조인됩니다. 참조 데이터 집합은 해당 이벤트 키 속성에 의해 리소스로 정의됩니다. 참조 데이터 집합을 구성하는 실제 메타데이터는 데이터 평면 API를 통해 업로드되거나 수정됩니다. |
   | 액세스 정책 | 액세스 정책은 데이터 쿼리를 실행하고 환경에서 참조 데이터를 조작하며 환경과 관련된 저장된 쿼리 및 관심 사항을 공유 할 수 있는 권한을 부여합니다. 자세한 내용은 [Azure Portal을 사용하여 Time Series Insights 환경에 대한 데이터 액세스 권한 부여](time-series-insights-data-access.md)를 참조하세요. |

Resource Manager 템플릿은 리소스 그룹에서 리소스의 인프라 및 구성을 정의하는 JSON 파일입니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure Resource Manager 개요 - 템플릿 배포](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)

[201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) 빠른 시작 템플릿은 GitHub에 게시됩니다. 이 템플릿은 Time Series Insights 환경, Event Hub의 이벤트를 사용하도록 구성된 자식 이벤트 원본 및 환경의 데이터에 대한 액세스 권한을 부여하는 액세스 정책을 만듭니다. 기존 Event Hub를 지정하지 않으면 배포에서 만들어집니다.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>PowerShell을 사용하여 로컬로 빠른 시작 템플릿을 배포합니다

다음 프로시저에서는 PowerShell을 사용하여 Time Series Insights 환경, Event Hub의 이벤트를 사용하도록 구성된 자식 이벤트 원본 및 환경의 데이터에 대한 액세스 권한을 부여하는 액세스 정책을 만드는 Azure Resource Manager 템플릿을 배포하는 방법을 설명합니다. 기존 Event Hub를 지정하지 않으면 배포에서 만들어집니다.

대략적인 워크플로 다음과 같습니다.

1. PowerShell을 설치합니다.
1. 템플릿 및 매개 변수 파일을 만듭니다.
1. PowerShell에서 Azure 계정에 로그인합니다.
1. 새 리소스 그룹이 아직 없으면 만듭니다.
1. 배포를 테스트합니다.
1. 템플릿을 배포합니다.

### <a name="install-powershell"></a>PowerShell 설치 

[Azure PowerShell 시작하기](/powershell/azure/get-started-azureps)의 지침을 따라서 Azure PowerShell을 설치합니다.

### <a name="create-a-template"></a>템플릿 만들기

GitHub에서 [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) 템플릿을 복제하거나 복사합니다.

### <a name="create-a-parameters-file"></a>매개 변수 파일 만들기

매개 변수 파일을 만들려면 [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) 파일을 복사합니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>필수 매개 변수

   | 매개 변수 | 설명 |
   | --- | --- |
   | eventHubNamespaceName | 원본 Event Hub의 네임스페이스입니다. |
   | eventHubName | 원본 Event Hub의 이름입니다. |
   | consumerGroupName | Time Series Insights 서비스가 Event Hub에서 데이터를 읽는 데 사용하는 소비자 그룹의 이름입니다. **참고:** 리소스 경합을 방지하려면 이 소비자 그룹은 Time Series Insights 서비스 전용이며, 다른 판독기와 공유되지 않아야 합니다. |
   | environmentName | 환경의 이름입니다. 이름은 '<', '>', '%', '&', ':', '\\', '?', '/' 및 제어 문자를 포함할 수 없습니다. 다른 문자를 모두 허용합니다.|
   | eventSourceName | 이벤트 원본 자식 리소스의 이름입니다. 이름은 '<', '>', '%', '&', ':', '\\', '?', '/' 및 제어 문자를 포함할 수 없습니다. 다른 문자를 모두 허용합니다. |

#### <a name="optional-parameters"></a>선택적 매개 변수

   | 매개 변수 | 설명 |
   | --- | --- |
   | existingEventHubResourceId | 이벤트 원본을 통해 Time Series Insights 환경에 연결될 기존 Event Hub의 선택적 리소스 ID입니다. **참고:** 템플릿을 배포하는 사용자는 Event Hub에서 listkey 작업을 수행할 권한이 있어야 합니다. 값이 전달되지 않으면 새 Event Hub가 템플릿에 의해 만들어집니다. |
   | environmentDisplayName | 환경 이름 대신 도구 또는 사용자 인터페이스에 표시할 선택적 이름입니다. |
   | environmentSkuName | SKU의 이름입니다. 자세한 내용은 [Time Series Insights 가격 책정 페이지](https://azure.microsoft.com/pricing/details/time-series-insights/)를 참조하세요.  |
   | environmentSkuCapacity | SKU의 단위 용량입니다. 자세한 내용은 [Time Series Insights 가격 책정 페이지](https://azure.microsoft.com/pricing/details/time-series-insights/)를 참조하세요.|
   | environmentDataRetentionTime | 환경의 이벤트를 쿼리에 사용할 수 있는 최소 시간 간격입니다. ISO 8601 형식으로 값을 지정해야 합니다(예: 30일이라는 보존 정책의 경우 "P30D"). |
   | eventSourceDisplayName | 이벤트 원본 이름 대신 도구 또는 사용자 인터페이스에 표시할 선택적 이름입니다. |
   | eventSourceTimestampPropertyName | 이벤트 원본의 타임스탬프로 사용될 이벤트 속성입니다. timestampPropertyName에 대한 값을 지정하지 않은 경우 또는 null 또는 빈 문자열을 지정하는 경우 이벤트 생성 시간이 사용됩니다. |
   | eventSourceKeyName | Time Series Insights 서비스가 Event Hub에 연결하는 데 사용하는 공유 액세스 키의 이름입니다. |
   | accessPolicyReaderObjectIds | Azure AD에서 환경에 대한 판독기 액세스 권한이 있어야 하는 사용자 또는 응용 프로그램의 개체 ID 목록입니다. **Get-AzureRMADUser** 또는 **Get-AzureRMADServicePrincipal** cmdlet을 호출하여 서비스 주체 objectId를 가져올 수 있습니다. Azure AD 그룹에 대한 액세스 정책을 만드는 작업은 아직 지원되지 않습니다. |
   | accessPolicyContributorObjectIds | Azure AD에서 환경에 대한 참가자 액세스 권한이 있어야 하는 사용자 또는 응용 프로그램의 개체 ID 목록입니다. **Get-AzureRMADUser** 또는 **Get-AzureRMADServicePrincipal** cmdlet을 호출하여 서비스 주체 objectId를 가져올 수 있습니다. Azure AD 그룹에 대한 액세스 정책을 만드는 작업은 아직 지원되지 않습니다. |

예를 들어, 다음 매개 변수 파일은 기존 Event Hub의 이벤트를 읽는 환경 및 이벤트 원본을 만드는 데 사용할 수 있습니다. 또한 환경에 대한 참가자 액세스 권한을 부여하는 두 개의 액세스 정책을 만듭니다.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

자세한 내용은 [매개 변수](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) 문서를 참조하세요.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Azure에 로그인하고 Azure 구독 설정

PowerShell 프롬프트에서 다음 명령을 실행합니다.

```powershell
Connect-AzureRmAccount
```

Azure 계정에 로그온하라는 메시지가 표시됩니다. 로그온한 후 다음 명령을 실행하여 사용 가능한 구독을 확인합니다.

```powershell
Get-AzureRMSubscription
```

이 명령은 사용 가능한 Azure 구독 목록을 반환합니다. 다음 명령을 실행하여 현재 세션에 대한 구독을 선택합니다. `<YourSubscriptionId>`는 사용할 Azure 구독의 GUID로 바꿉니다.

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>리소스 그룹 설정

기존 리소스 그룹이 없는 경우 **New-AzureRmResourceGroup** 명령을 사용하여 새 리소스 그룹을 만듭니다. 사용할 리소스 그룹의 이름과 위치를 입력합니다. 예: 

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

성공하면 새 리소스 그룹에 대한 요약이 표시됩니다.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>배포 테스트

`Test-AzureRmResourceGroupDeployment` cmdlet을 실행하여 배포의 유효성을 검사합니다. 배포를 테스트할 때는 배포를 실행할 때처럼 정확하게 매개 변수를 제공합니다.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>배포 만들기

새 배포를 만들려면 `New-AzureRmResourceGroupDeployment` cmdlet을 실행하고 메시지가 표시되면 필요한 매개 변수를 입력합니다. 매개 변수에는 배포 이름, 리소스 그룹 이름 및 템플릿 파일의 경로 또는 URL이 포함됩니다. **Mode** 매개 변수가 지정되지 않은 경우 기본값 **Incremental**이 사용됩니다. 자세한 내용은 [증분 및 전체 배포](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)를 참조하세요.

다음 명령은 PowerShell 창에서 다섯 개의 필수 매개 변수를 입력하라는 메시지를 표시합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

그 대신에 매개 변수 파일을 지정하려면 다음 명령을 사용합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

배포 cmdlet을 실행하면 인라인 매개 변수를 사용할 수도 있습니다. 이 명령은 다음과 같습니다.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

[전체](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) 배포를 실행하려면 **Mode** 매개 변수를 **Complete**로 설정합니다.

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>배포 확인

리소스가 성공적으로 배포되면 배포의 요약이 PowerShell 창에 표시됩니다.

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Azure Portal을 통해 빠른 시작 템플릿을 배포합니다

GitHub에서 빠른 시작 템플릿의 홈페이지에는 **Azure에 배포** 단추도 포함됩니다. 이 단추를 클릭하면 Azure Portal에서 사용자 지정 배포 페이지를 엽니다. 이 페이지의 [필수 매개 변수](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) 또는 [선택적 매개 변수](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) 테이블에서 각 매개 변수에 대한 값을 입력하거나 선택할 수 있습니다. 설정을 입력한 후에 **구매** 단추를 클릭하면 템플릿 배포를 시작합니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>다음 단계

- REST API를 사용하여 프로그래밍 방식으로 Time Series Insights 리소스를 관리하는 방법에 대한 자세한 내용은 [Time Series Insights 관리](https://docs.microsoft.com/rest/api/time-series-insights-management/)를 참조하세요.
