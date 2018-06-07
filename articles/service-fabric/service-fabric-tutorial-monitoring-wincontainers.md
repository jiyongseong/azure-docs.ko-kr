---
title: Azure Service Fabric의 Windows 컨테이너에 대한 모니터링 및 진단 | Microsoft Docs
description: 이 자습서에서는 Azure Service Fabric에서 오케스트레이션되는 Windows 컨테이너에 대한 모니터링 및 진단을 설정합니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 9024036c5340e9afb2369feedde140d84e880265
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>자습서: Log Analytics를 사용하여 Service Fabric에서 Windows 컨테이너 모니터링

자습서의 2부이며, Service Fabric에서 오케스트레이션되는 Windows 컨테이너를 모니터링하도록 Log Analytics를 설정하는 과정을 안내합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric 클러스터에 대한 Log Analytics 구성
> * Log Analytics 작업 영역을 사용하여 컨테이너 및 노드의 로그 보기 및 쿼리
> * 컨테이너 및 노드 메트릭을 수집하도록 OMS 에이전트 구성

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에 다음을 수행해야 합니다.
- Azure에 클러스터가 있거나, [이 자습서에 따라 새로 만들어야](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 합니다.
- [컨테이너화된 응용 프로그램을 배포](service-fabric-host-app-in-a-container.md)해야 합니다.

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Resource Manager 템플릿에서 클러스터를 사용하여 Log Analytics 설정

이 자습서의 1부에서 [제공된 템플릿](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial)을 사용한 경우 제네릭 Service Fabric Azure Resource Manager 템플릿에 다음 내용을 추가해야 합니다. Log Analytics를 사용하여 컨테이너를 모니터링하기 위해 설정하려면 고유한 클러스터가 있는 경우:
* Resource Manager 템플릿을 다음과 같이 변경합니다.
* PowerShell을 통해 배포하여 [템플릿 배포](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)를 통해 클러스터를 업그레이드합니다. Azure Resource Manager는 리소스가 있음을 인식하므로 업그레이드로 배포됩니다.

### <a name="adding-log-analytics-to-your-cluster-template"></a>클러스터 템플릿에 Log Analytics 추가

*template.json*을 다음과 같이 변경합니다.

1. *parameters* 섹션에 Log Analytics 작업 영역 위치 및 이름을 추가합니다.
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
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
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    사용되는 값을 변경하려면 *template.parameters.json*에 동일한 매개 변수를 추가하고 여기서 사용되는 값을 변경합니다.

2. *variables*에 솔루션 이름 및 솔루션을 추가합니다. 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. OMS Microsoft Monitoring Agent를 가상 머신 확장으로 추가합니다. 가상 머신 확장 집합 리소스를 찾습니다. *resources* > *“apiVersion”: “[variables(‘vmssApiVersion’)]”*. *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions*의 *ServiceFabricNode* 확장 아래에 다음과 같은 확장 설명을 추가합니다. 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Log Analytics 작업 영역을 개별 리소스로 추가합니다. *resources*에서 가상 머신 확장 집합 리소스 뒤에 다음을 추가합니다.
    
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
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

필요에 따라 참조할 수 있는 이러한 모든 변경 내용이 있는 샘플 템플릿(이 자습서의 1부에서 사용됨)은 [여기](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json)를 참조하세요. 이러한 변경 내용은 리소스 그룹에 Log Analytics 작업 영역을 추가합니다. 작업 영역은 [Windows Azure 진단](service-fabric-diagnostics-event-aggregation-wad.md) 에이전트를 통해 구성된 저장소 테이블에서 Service Fabric 플랫폼 이벤트를 수집하도록 구성됩니다. 또한 OMS 에이전트(Microsoft Monitoring Agent)가 클러스터의 각 노드에 가상 머신 확장으로 추가되었습니다. 따라서 클러스터 크기를 조정할 때 에이전트가 자동으로 각 컴퓨터에 구성되고 동일한 작업 영역에 연결됩니다.

새 변경 내용이 포함된 템플릿을 배포하여 현재 클러스터를 업그레이드합니다. 이 작업이 완료되면 리소스 그룹에 Log Analytics 리소스가 표시됩니다. 클러스터가 준비되 컨테이너화된 응용 프로그램을 배포합니다. 다음 단계에서는 컨테이너 모니터링을 설정합니다.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Log Analytics 작업 영역에 컨테이너 모니터링 솔루션 추가

작업 영역에서 컨테이너 솔루션을 설정하려면 *컨테이너 모니터링 솔루션*을 검색하고 컨테이너 리소스를 만듭니다(모니터링 + 관리 범주).

![컨테이너 솔루션 추가](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

*Log Analytics 작업 영역*에 대한 메시지가 표시되면 리소스 그룹에서 만든 작업 영역을 선택하고 **만들기**를 클릭합니다. 이렇게 하면 *컨테이너 모니터링 솔루션*이 작업 영역에 추가되고 템플릿에 의해 배포된 OMS 에이전트가 docker 로그 및 통계 수집을 자동으로 시작합니다. 

*리소스 그룹*으로 돌아가면 이제 새로 추가된 모니터링 솔루션이 표시됩니다. 클릭하면 방문 페이지에 실행 중인 컨테이너 이미지 수가 표시됩니다. 

*자습서의 [2부](service-fabric-host-app-in-a-container.md)에서는 fabrikam 컨테이너 인스턴스를 5개 실행했습니다.*

![컨테이너 솔루션 방문 페이지](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

**컨테이너 모니터 솔루션**을 클릭하면 자세한 대시보드로 이동되며, 여기서 여러 패널을 스크롤하고 Log Analytics에서 쿼리를 실행할 수 있습니다. 

*2017년 9월부터 솔루션에서 일부 업데이트가 수행됩니다. 동일한 솔루션에 여러 조정자를 통합할 때 Kubernetes 이벤트에 대해 발생할 수 있는 오류를 무시하세요.*

에이전트는 docker 로그를 수집하기 때문에 기본적으로 *stdout* 및 *stderr*을 표시합니다. 오른쪽으로 스크롤하면 컨테이너 이미지 인벤토리, 상태, 메트릭 및 더 유용한 데이터를 가져오기 위해 실행할 수 있는 샘플 쿼리가 표시됩니다. 

![컨테이너 솔루션 대시보드](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

이 패널을 클릭하면 표시된 값을 생성하는 Log Analytics 쿼리로 이동됩니다. 쿼리를 *\** 로 변경하여 수집되는 모든 종류의 로그를 표시합니다. 여기서 컨테이너 성능이나 로그를 쿼리 또는 필터링하거나 Service Fabric 플랫폼 이벤트를 확인할 수 있습니다. 또한 에이전트는 클러스터 구성이 변경될 경우 모든 컴퓨터에서 데이터가 수집되고 있는지 확인하기 위해 검사할 수 있는 각 노드에서 지속적으로 하트비트를 내보냅니다.   

![컨테이너 쿼리](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>성능 카운터를 수집하도록 OMS 에이전트 구성

OMS 에이전트를 사용하는 또 다른 이점은 Azure 진단 에이전트를 구성하고 Resource Manager 템플릿 기반 업그레이드를 매번 수행하는 대신 OMS UI 환경을 통해 수집할 성능 카운터를 변경하는 기능입니다. 이 작업을 수행하려면 컨테이너 모니터링(또는 Service Fabric) 솔루션의 방문 페이지에서 **OMS 포털**을 클릭합니다.

![OMS 포털](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

OMS 포털의 해당 작업 영역으로 이동되며, 여기서 솔루션을 보고, 사용자 지정 대시보드를 만들고, OMS 에이전트를 구성할 수 있습니다. 
* 화면의 오른쪽 위에서 **톱니바퀴 휠**을 클릭하여 *설정* 메뉴를 엽니다.
* **연결된 원본** > **Windows 서버**를 클릭하여 *5대의 Windows 컴퓨터가 연결*되어 있는지 확인합니다.
* **데이터** > **Windows 성능 카운터**를 클릭하여 새 성능 카운터를 검색하고 추가합니다. 여기에는 수집할 수 있는 성능 카운터에 대한 Log Analytics의 권장 사항 목록과 다른 카운터를 검색하는 옵션이 표시됩니다. **선택한 성능 카운터 추가**를 클릭하여 제안된 메트릭 수집을 시작합니다.

    ![성능 카운터](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

Azure Portal로 돌아가서 컨테이너 모니터링 솔루션을 몇 분 내에 **새로 고침**하면 *컴퓨터 성능* 데이터가 들어오는 것이 표시되기 시작해야 합니다. 이 정보를 통해 리소스가 사용되는 방식을 이해할 수 있습니다. 이러한 메트릭을 사용하여 클러스터 크기 조정에 대한 적절한 결정을 내리거나 클러스터가 예상대로 부하 균형을 조정하는지 확인할 수도 있습니다.

*참고: 시간 필터가 이러한 메트릭을 사용하도록 적절하게 설정되었는지 확인합니다.* 

![성능 카운터 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Service Fabric 클러스터에 대한 Log Analytics 구성
> * Log Analytics 작업 영역을 사용하여 컨테이너 및 노드의 로그 보기 및 쿼리
> * 컨테이너 및 노드 메트릭을 수집하도록 Log Analytics 에이전트 구성

이제 컨테이너화된 응용 프로그램에 대한 모니터링을 설정했으므로 다음을 시도해 보세요.

* 위와 유사한 단계에 따라 Linux 클러스터에 대해 Log Analytics를 설정합니다. [이 템플릿](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)을 참조하여 Resource Manager 템플릿 내용을 변경합니다.
* 검색 및 진단에 도움이 되는 [자동 경고](../log-analytics/log-analytics-alerts.md)를 설정하도록 Log Analytics를 구성합니다.
* 클러스터에 대해 구성할 Service Fabric의 [권장 성능 카운터](service-fabric-diagnostics-event-generation-perf.md) 목록을 탐색합니다.
* Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능을 알아봅니다.