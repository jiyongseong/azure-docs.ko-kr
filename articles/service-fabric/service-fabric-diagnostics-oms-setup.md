---
title: Azure Service Fabric - Log Analytics로 모니터링 설정 | Microsoft Docs
description: Azure Service Fabric 클러스터를 모니터링하기 위해 Log Analytics를 설정하여 이벤트를 시각화 및 분석하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 4/03/2018
ms.author: srrengar
ms.openlocfilehash: af94e3270493f6967c4f8c484170751c098bf181
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="set-up-log-analytics-for-a-cluster"></a>클러스터에 대해 Log Analytics 설정

Log Analytics는 클러스터 수준 이벤트를 모니터링하기 위한 권장 사항입니다. Azure Resource Manager, PowerShell 또는 Azure Marketplace를 통해 Log Analytics 작업 영역을 설정할 수 있습니다. 나중에 사용하기 위해 배포의 업데이트된 Resource Manager 템플릿을 유지 관리하는 경우 동일한 템플릿을 사용하여 Log Analytics 환경을 설정합니다. 진단을 사용하도록 설정하여 클러스터를 이미 배포한 경우 Marketplace를 통한 배포가 더 용이합니다. 배포하는 계정에 구독 수준 액세스 권한이 없는 경우 PowerShell 또는 Resource Manager 템플릿을 사용하여 배포합니다.

> [!NOTE]
> 클러스터를 모니터링하도록 Log Analytics를 설정하려면 클러스터 수준 또는 플랫폼 수준 이벤트를 보기 위해 진단을 사용하도록 설정해야 합니다. 자세한 내용은 [Windows 클러스터에서 진단을 설정하는 방법](service-fabric-diagnostics-event-aggregation-wad.md) 및 [Linux 클러스터에서 진단을 설정하는 방법](service-fabric-diagnostics-event-aggregation-lad.md)을 참조하세요.

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Azure Marketplace를 사용하여 Log Analytics 작업 영역 배포

클러스터를 배포한 후에 Log Analytics 작업 영역을 추가하려면 포털에서 Azure Marketplace로 이동한 다음, **Service Fabric 분석**을 찾습니다. 이것은 Service Fabric 관련 데이터가 있는 Service Fabric 배포를 위한 사용자 지정 솔루션입니다. 이 프로세스에서 솔루션(정보를 보기 위한 대시보드)과 작업 영역(기본 클러스터 데이터의 집계)을 모두 만듭니다.

1. 왼쪽 탐색 메뉴에서 **새로 만들기**를 선택합니다. 

2. **Service Fabric 분석**을 검색합니다. 표시되는 리소스를 선택합니다.

3. **만들기**를 선택합니다.

    ![Marketplace의 OMS SF 분석](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric 분석 만들기 창에서 **OMS 작업 영역** 필드에 대해 **작업 영역 선택**을 선택한 다음, **새 작업 영역 만들기**를 선택합니다. 필수 정보를 입력합니다. 단, Service Fabric 클러스터와 작업 영역에 대한 구독이 동일해야 합니다. 입력의 유효성이 검사되면 작업 영역이 배포되기 시작합니다. 배포에 몇 분밖에 걸리지 않습니다.

5. 완료되면 Service Fabric 분석 만들기 창의 맨 아래에서 **만들기**를 다시 선택합니다. **OMS 작업 영역** 아래에 새 작업 영역이 표시되는지 확인합니다. 이 작업은 방금 만든 작업 영역에 솔루션을 추가합니다.

Windows를 사용하는 경우 다음 단계를 계속 진행하여 클러스터 이벤트가 저장된 저장소 계정에 OMS를 연결합니다. 

>[!NOTE]
>Linux 클러스터에 대해서는 이 환경을 설정할 수 없습니다. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Log Analytics 작업 영역을 클러스터에 연결 

1. 클러스터에서 가져오는 진단 데이터에 작업 영역을 연결해야 합니다. Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동합니다. **ServiceFabric\<nameOfWorkspace\>** 를 선택하고 개요 페이지로 이동합니다. 여기서 솔루션 설정과 작업 영역 설정을 변경하고 OMS 포털에 액세스할 수 있습니다.

2. 왼쪽 탐색 메뉴의 **작업 영역 데이터 원본**에서 **저장소 계정 로그**를 선택합니다.

3. **저장소 계정 로그** 페이지에서 맨 위의 **추가**를 선택하여 작업 영역에 클러스터 로그를 추가합니다.

4. **저장소 계정**을 선택하여 클러스터에서 만든 적절한 계정을 추가합니다. 기본 이름을 사용한 경우 저장소 계정은 **sfdg\<resourceGroupName\>** 입니다. **applicationDiagnosticsStorageAccountName**에 사용된 값을 확인하여 클러스터를 배포하는 데 사용된 Azure Resource Manager 템플릿으로 이 이름을 확인할 수도 있습니다. 이름이 표시되지 않는 경우 아래로 스크롤하여 **추가 로드**를 선택합니다. 저장소 계정 이름을 선택합니다.

5. 데이터 형식을 지정합니다. **Service Fabric 이벤트**로 설정합니다.

6. 원본이 **WADServiceFabric\*EventTable**로 자동으로 설정되었는지 확인합니다.

7. **확인**을 선택하여 작업 영역을 클러스터 로그에 연결합니다.

    ![OMS에 저장소 계정 로그 추가](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

이제 계정이 작업 영역의 데이터 원본에서 저장소 계정 로그의 일부로 표시됩니다.

OMS Log Analytics 작업 영역에 추가한 Service Fabric 분석 솔루션이 이제 클러스터의 플랫폼 및 응용 프로그램 로그 표에 제대로 연결되었습니다. 같은 방식으로 작업 영역에 추가적인 원본을 추가할 수 있습니다.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 OMS 배포

Resource Manager 템플릿을 사용하여 클러스터를 배포하는 경우 템플릿은 새 OMS 작업 영역을 만들고, 작업 영역에 Service Fabric 솔루션을 추가하고, 적절한 저장소 테이블에서 데이터를 읽도록 구성합니다.

[이 샘플 템플릿](https://github.com/krnese/azure-quickstart-templates/tree/master/service-fabric-oms)을 사용하고 요구 사항에 맞게 수정할 수 있습니다.

다음과 같이 수정합니다.
1. *template.json* 파일에 정의된 매개 변수에 다음 코드 조각을 추가하여 매개 변수에 `omsWorkspaceName` 및 `omsRegion`을 추가합니다. 기본값을 원하는 대로 자유롭게 수정합니다. 또한 *parameters.json* 파일에 새 매개 변수 두 개를 추가하여 리소스 배포에 대한 해당 값을 정의합니다.
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    `omsRegion` 값은 특정 값 집합을 준수해야 합니다. 클러스터 배포에 가장 가까운 값을 선택합니다.

2. 응용 프로그램 로그를 OMS로 보내는 경우 먼저 `applicationDiagnosticsStorageAccountType` 및 `applicationDiagnosticsStorageAccountName`이 템플릿에 매개 변수로 포함되어 있는지 확인합니다. 포함되어 있지 않으면 변수 섹션에 추가하고 필요에 따라 값을 편집합니다. 앞의 형식에 따라 매개 변수로 포함할 수도 있습니다.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Service Fabric OMS 솔루션을 템플릿의 변수에 추가합니다.

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. 리소스 섹션의 끝에서 Service Fabric 클러스터 리소스가 선언된 위치 뒤에 다음을 추가합니다.

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > `applicationDiagnosticsStorageAccountName`을 변수로 추가한 경우 이에 대한 각 참조를 `parameters('applicationDiagnosticsStorageAccountName')` 대신 `variables('applicationDiagnosticsStorageAccountName')`으로 수정해야 합니다.

5. AzureRM PowerShell 모듈에서 `New-AzureRmResourceGroupDeployment` API를 사용하여 템플릿을 리소스 관리자 업그레이드로 클러스터에 배포합니다. 예제 명령은 다음과 같습니다.

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    Azure Resource Manager는 이 명령이 기존 리소스에 대한 업데이트임을 감지합니다. 기존 배포를 구동하는 템플릿과 제공된 새 템플릿 간의 변경 내용만 처리합니다.

## <a name="deploy-oms-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 OMS 배포

`New-AzureRmOperationalInsightsWorkspace` 명령을 사용하여 PowerShell을 통해 OMS Log Analytics 리소스를 배포할 수도 있습니다. 이 방법을 사용하려면 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1)을 설치했는지 확인합니다. 이 스크립트를 사용하여 새 OMS Log Analytics 작업 영역을 만들고 여기에 Service Fabric 솔루션을 추가합니다. 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

완료되면 이전 섹션의 단계에 따라 OMS Log Analytics를 해당 저장소 계정에 연결합니다.

PowerShell을 사용하여 OMS 작업 영역에 다른 솔루션을 추가하거나 다른 수정 작업을 할 수도 있습니다. 자세한 내용은 [PowerShell을 사용하여 Log Analytics 관리](../log-analytics/log-analytics-powershell-workspace-configuration.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* 노드에 [OMS 에이전트를 배포](service-fabric-diagnostics-oms-agent.md)하여 성능 카운터를 수집하고 컨테이너에 대한 docker 통계 및 로그를 수집합니다.
* Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능 알아보기
* [뷰 디자이너를 사용하여 Log Analytics에서 사용자 지정 보기 만들기](../log-analytics/log-analytics-view-designer.md)
