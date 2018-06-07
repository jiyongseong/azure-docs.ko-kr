---
title: AKS(Azure Kubernetes Service) 상태 모니터링(미리 보기) | Microsoft Docs
description: 이 문서에서는 AKS 컨테이너의 성능을 쉽게 검토하여 호스트된 Kubernetes 환경의 사용률을 빠르게 이해하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: magoedte
ms.openlocfilehash: f0501d4404375ee44b96ae4514c15e69b616d38a
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>AKS(Azure Kubernetes Service) 컨테이너 상태 모니터링(미리 보기)

이 문서에서는 Azure Monitor 컨테이너 상태를 설정하고 사용하여 AKS(Azure Kubernetes Service)에 호스트된 Kubernetes 환경에 배포된 워크로드의 성능을 모니터링하는 방법을 설명합니다.  Kubernetes 클러스터 및 컨테이너를 모니터링하는 것은 중요하며, 특히 여러 응용 프로그램을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우 그렇습니다.

컨테이너 상태는 메트릭 API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너에서 메모리 및 프로세서 메트릭을 수집하여 성능 모니터링 기능을 제공합니다.  컨테이너 상태를 사용하도록 설정하면, 이러한 메트릭이 Linux용 OMS 에이전트의 컨테이너화 버전을 사용하여 자동으로 수집된 후 [Log Analytics](../log-analytics/log-analytics-overview.md) 작업 영역에 저장됩니다.  포함된 미리 정의된 보기에는 사용자의 이해를 돕기 위해 상주하는 컨테이너 워크로드와 Kubernetes클 러스터의 성능 상태에 영향을 미치는 다음과 같은 요인이 표시됩니다.  

* 노드에서 실행 중인 컨테이너와 리소스 병목 상태를 나타낼 해당 평균 프로세서 및 메모리 사용률
* 컨트롤러 및/또는 Pod에서 컨테이너가 상주하는 위치를 식별하여 컨트롤러 또는 Pod의 전반적인 성능 확인 
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률 검토
* 평균 부하 및 최대 부하 상태의 클러스터 동작을 이해하여 용량 요구를 파악하고 지속 가능한 최대 부하 결정 

Docker 및 Windows 컨테이너 호스트를 모니터링하고 관리하여 구성, 감사 및 리소스 사용률을 확인하려는 경우 [컨테이너 모니터링 솔루션](../log-analytics/log-analytics-containers.md)을 참조하세요.

## <a name="requirements"></a>요구 사항 
시작하기 전에 다음 세부 정보를 검토하여 지원되는 필수 구성 요소를 이해합니다.

- 버전 1.7.7 ~ 1.9.6의 AKS 클러스터가 지원됩니다.
- Linux용 컨테이너화 OMS 에이전트 버전 microsoft / oms:ciprod04202018 이상. 이 에이전트는 컨테이너 상태를 등록하는 동안 자동으로 설치됩니다.  
- Log Analytics 작업 영역.  새 AKS 클러스터에 대한 모니터링을 사용하도록 설정할 때 만들거나 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) 또는 [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)에서 만들 수 있습니다.


## <a name="components"></a>구성 요소 

이 기능은 컨테이너화된 Linux용 OMS 에이전트를 사용하여 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집합니다.  이 에이전트를 컨테이너 모니터링을 사용하도록 설정한 후에 지정된 Log Analytics 작업 영역에 자동으로 배포되고 등록됩니다. 

>[!NOTE] 
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다.  
>

## <a name="log-in-to-azure-portal"></a>Azure Portal에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다. 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>새 클러스터에 대해 컨테이너 상태 모니터링 사용
Azure Portal에서 배포할 때는 AKS 클러스터의 모니터링만 사용하도록 설정할 수 있습니다.  빠른 시작 문서 [AKS(Azure Kubernetes Service) 클러스터 배포](../aks/kubernetes-walkthrough-portal.md)의 단계를 따르세요.  **모니터링** 페이지에서 **모니터링 사용** 옵션에 대해 **예**를 선택하여 사용하도록 설정한 후, 기존 Log Analytics 작업 영역을 선택하거나 새로 만듭니다.  

모니터링을 사용하도록 설정한후 에 모든 구성 작업이 성공적으로 완료되면 다음 두 가지 방법 중 하나로 클러스터의 성능을 모니터링할 수 있습니다.

1. 왼쪽 창에서 **상태**를 선택하여 AKS 클러스터에서 직접<br><br> 
2. 선택한 클러스터에 대한 AKS 클러스터 페이지에서 **컨테이너 상태 모니터링** 타일을 클릭하여  Azure Monitor의 왼쪽 창에서 **상태**를 선택합니다.  

![AKS에서 컨테이너 상태를 선택하는 옵션](./media/monitoring-container-health/container-performance-and-health-select-01.png)

모니터링이 사용되도록 설정된 후에 클러스터에 대한 운영 데이터를 볼 수 있을 때까지 15분 정도 걸릴 수 있습니다.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>관리되는 기존 클러스터에 대해 컨테이너 상태 모니터링 사용
이미 배포된 AKS 컨테이너에 대한 모니터링의 경우 포털에서 사용하도록 설정할 수 없으며, 제공된 Azure Resource Manager 템플릿을 사용하여 **New-AzureRmResourceGroupDeployment** 또는 Azure CLI를 통해서만 사용하도록 설정할 수 있습니다.  모니터링을 사용하도록 구성을 지정하는 JSON 템플릿도 있고, 다음을 지정하도록 구성하는 매개 변수 값을 포함하는 JSON 템플릿도 있습니다.

* AKS 컨테이너 리소스 ID 
* 클러스터가 배포된 리소스 그룹 
* Log Analytics 작업 영역 및 해당 작업 영역을 만들 지역 

Log Analytics 작업 영역은 수동으로 만들어야 합니다.  작업 영역을 만들려면 [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), [Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)을 통해 설정할 수 있습니다.

PowerShell에서 템플릿을 사용하여 리소스를 배포하는 개념을 잘 모를 경우 [Resource Manager 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)를 참조하고, Azure CLI에 대한 자세한 내용은 [Resource Manager 템플릿 및 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)를 참조하세요.

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬로 설치하고 사용해야 합니다.  Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

### <a name="create-and-execute-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster resource id"
        }
    },
    "aksResourceLocation": {
      "type": "string",
      "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
        }
      },
      "workspaceId": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics resource id"
        }
      },
      "workspaceRegion": {
        "type": "string",
        "metadata": {
          "description": "Azure Monitor Log Analytics workspace region"
        }
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": true,
              "config": {
                "logAnalyticsWorkspaceResourceID": "[parameters('workspaceId')]"
              }
            }
          }
        }
      },
      {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
    }
    ```

2. 이 파일을 **existingClusterOnboarding.json**으로 로컬 폴더에 저장합니다.
3. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "East US"
        },
        "workspaceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "workspaceRegion": {
          "value": "eastus"
        }
      }
    }
    ```

4. **aksResourceId**, **aksResourceLocation**의 값을 AKS 클러스터에 대한 **AKS 개요** 페이지에서 찾을 수 있는 값으로 편집합니다.  **workspaceId** 값은 Log Analytics 작업 영역의 이름이어야 하고, **workspaceRegion**에 대해 작업 영역이 만들어진 위치를 지정합니다.    
5. 이 파일을 **existingClusterParam.json**으로 로컬 폴더에 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

    * 템플릿이 포함된 폴더에서 다음 PowerShell 명령을 사용합니다.

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

        ```powershell
        provisioningState       : Succeeded
        ```

    * Linux에서 Azure CLI를 사용하여 다음 명령을 실행하려면
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

        ```azurecli
        provisioningState       : Succeeded
        ```
모니터링이 사용되도록 설정된 후에 클러스터에 대한 운영 데이터를 볼 수 있을 때까지 15분 정도 걸릴 수 있습니다.  

## <a name="verify-agent-deployed-successfully"></a>에이전트가 성공적으로 배포되었는지 확인
OMS 에이전트가 제대로 배포되었는지 확인하려면 ` kubectl get ds omsagent -—namespace=kube-system` 명령을 실행합니다.

출력은 다음과 비슷하며 제대로 배포되었음을 나타냅니다.

```
User@aksuser:~$ kubectl get ds omsagent -—namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>성능 사용률 보기
컨테이너 상태를 열면 페이지에 즉시 클러스터 노드의 성능 사용률이 표시됩니다.  AKS 클러스터에 대한 정보 보기는 다음 세 가지 관점으로 구성됩니다.

- 노드 
- Controllers  
- 컨테이너

행 계층은 클러스터의 노드로 시작하는 Kubernetes 개체 모델을 따릅니다.  노드를 확장하면 노드에서 실행 중인 하나 이상의 Pod가 표시됩니다. 둘 이상의 컨테이너가 하나의 Pod로 그룹화되면 계층 구조에서 마지막 행으로 표시됩니다.<br><br> ![성능 보기의 Kubernetes 노드 계층 예제](./media/monitoring-container-health/container-performance-and-health-view-03.png)

페이지의 위쪽에서 컨트롤러 또는 컨테이너를 선택하고, 해당 개체에 대한 상태 및 리소스 사용률을 검토할 수 있습니다.  화면 맨 위에 있는 드롭다운 상자를 사용하여 네임스페이스, 서비스 및 노드별로 필터링할 수 있습니다. 대신, 메모리 사용률을 검토하려면 **메트릭** 드롭다운 목록에서 **메모리 RSS** 또는 **메모리 작업 집합**을 선택합니다.  **메모리 RSS**는 Kubernetes 버전 1.8 이상에서만 지원됩니다. 그렇지 않은 경우, **AVG %** 값이 정의되지 않았거나로 표현할 수 없는 값을 나타내는 숫자 데이터 형식인 *NaN %* 로 표시됩니다. 

![컨테이너 성능 노드 성능 보기](./media/monitoring-container-health/container-performance-and-health-view-04.png)

기본적으로 성능 데이터는 마지막 6시간을 기준으로 하지만, 페이지의 오른쪽 위 모서리에 있는 **시간 범위** 드롭다운 목록에서 이 기간을 변경할 수 있습니다. 현재, 이 페이지는 자동으로 새로 고쳐지지 않으므로 수동으로 새로 고쳐야 합니다. 

다음 예제에서는 노드 *aks-agentpool-3402399-0*의 **컨테이너** 값이 배포된 전체 컨테이너 수의 롤업인 10이 됩니다.<br><br> ![노드당 컨테이너 롤업 예제](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> 이를 통해 클러스터의 노드 간에 컨테이너가 적절히 분산되어 있지 않을 때 빠르게 알 수 있습니다.  

다음 표에서는 노드를 볼 때 표시되는 정보에 대해 설명합니다.

| 열 | 설명 | 
|--------|-------------|
| Name | 호스트의 이름입니다. |
| 상태 | 노드 상태의 Kubernetes 보기입니다. |
| AVG % | 선택한 기간 동안의 선택한 메트릭을 기준으로 하는 평균 노드 비율입니다. |
| AVERAGE | 선택한 기간 동안의 선택한 메트릭을 기준으로 하는 평균 노드 실제 값입니다.  Average 값은 노드에 대해 설정된 CPU/메모리 제한에서 측정됩니다. Pod 및 컨테이너의 경우에는 호스트에서 보호한 평균 값입니다. |
| 컨테이너 | 컨테이너의 수입니다. |
| 작동 시간 | 노드가 시작되었거나 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| Pod | 컨테이너 전용입니다. 컨테이너가 있는 Pod를 표시합니다. |
| Controllers | 컨테이너 및 Pod용입니다. 컨테이너 또는 Pod가 있는 컨트롤러를 표시합니다. 모든 Pod가 컨트롤러에 있는 것은 아니므로 N/A를 표시할 수 있습니다. | 
| Trend AVG% | 컨테이너 및 노드 평균 메트릭 %를 기준으로 하는 막대 그래프 추세입니다. |


선택기에서 **Controllers**를 선택합니다.<br><br> ![컨트롤러 보기 선택](./media/monitoring-container-health/container-performance-and-health-view-08.png)

여기에서 컨트롤러의 성능 상태를 볼 수 있습니다.<br><br> ![<Name> 컨트롤러 성능 보기](./media/monitoring-container-health/container-performance-and-health-view-05.png)

행 계층은 컨트롤러로 시작하고 컨트롤러를 확장하며, 하나 이상의 Pod 또는 하나 이상의 컨테이너가 표시됩니다.  Pod를 확장하면 마지막 행에 Pod로 그룹화된 컨테이너가 표시됩니다.  

다음 표에서는 컨트롤러를 볼 때 표시되는 정보에 대해 설명합니다.

| 열 | 설명 | 
|--------|-------------|
| Name | 컨트롤러의 이름입니다.|
| 상태 | *Terminated*, *Failed* *Stopped* 또는 *Paused*와 같은 상태로 실행이 완료된 경우의 컨테이너 상태입니다. 컨테이너가 실행 중이지만 상태가 제대로 표시되지 않았거나 에이전트에 의해 선택되지 않았고 30분 넘게 응답하지 않은 경우 상태는 *Unknown*이 됩니다. |
| AVG % | 선택한 메트릭에 대한 각 엔터티 평균 %의 평균 롤업입니다. |
| AVERAGE | 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다.  Average 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| 컨테이너 | 컨트롤러 또는 Pod에 대한 컨테이너의 총 수입니다. |
| Restarts | 컨테이너에서 다시 시작한 횟수의 롤업입니다. |
| 작동 시간 | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| Pod | 컨테이너 전용입니다. 컨테이너가 있는 Pod를 표시합니다. |
| 노드 | 컨테이너 및 Pod용입니다. 컨테이너 또는 Pod가 있는 컨트롤러를 표시합니다. | 
| Trend AVG% | 컨테이너의 평균 메트릭 %를 나타내는 막대 그래프 추세입니다. |

선택기에서 **Containers**를 선택합니다.<br><br> ![컨테이너 보기 선택](./media/monitoring-container-health/container-performance-and-health-view-09.png)

여기에서 컨테이너의 성능 상태를 볼 수 있습니다.<br><br> ![<Name> 컨트롤러 성능 보기](./media/monitoring-container-health/container-performance-and-health-view-06.png)

다음 표에서는 컨테이너를 볼 때 표시되는 정보에 대해 설명합니다.

| 열 | 설명 | 
|--------|-------------|
| Name | 컨트롤러의 이름입니다.|
| 상태 | 컨테이너의 롤업 상태입니다(있는 경우). |
| AVG % | 선택한 메트릭에 대한 각 엔터티 평균 %의 평균 롤업입니다. |
| AVERAGE | 컨테이너의 평균 CPU 밀리코어 또는 메모리 성능의 롤업입니다. Average 값은 Pod에 대해 설정된 CPU/메모리 제한에서 측정됩니다. |
| 컨테이너 | 컨트롤러에 대한 컨테이너의 총 수입니다.|
| Restarts | 컨테이너가 시작된 이후 경과된 시간을 나타냅니다. |
| 작동 시간 | 컨테이너가 시작 또는 다시 부팅된 이후 경과된 시간을 나타냅니다. |
| Pod | 컨테이너가 있는 Pod 정보입니다. |
| 노드 |  컨테이너가 있는 노드입니다.  | 
| Trend AVG% | 컨테이너의 평균 메트릭 %를 나타내는 막대 그래프 추세입니다. |

## <a name="container-data-collection-details"></a>컨테이너 데이터 컬렉션 세부 정보
컨테이너 상태는 컨테이너 호스트 및 컨테이너로부터 다양한 성능 메트릭과 로그 데이터를 수집합니다. 데이터는 3분마다 수집됩니다.

### <a name="container-records"></a>컨테이너 레코드

다음 표에서는 컨테이너 상태에 의해 수집된 레코드 및 로그 검색 결과에 표시된 데이터 형식의 예를 보여줍니다.

| 데이터 형식 | 로그 검색의 데이터 유형 | 필드 |
| --- | --- | --- |
| 호스트 및 컨테이너에 대한 성능 | `Perf` | 컴퓨터, ObjectName, CounterName &#40;%프로세서 시간, 디스크 읽기 MB, 디스크 쓰기 MB, 메모리 사용 MB, 네트워크 수신 바이트, 네트워크 송신 바이트, 프로세서 사용 초, 네트워크&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| 컨테이너 인벤토리 | `ContainerInventory` | TimeGenerated, 컴퓨터, 컨테이너 이름, ContainerHostname, 이미지, ImageTag, ContainerState, ExitCode, EnvironmentVar, 명령, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| 컨테이너 이미지 인벤토리 | `ContainerImageInventory` | TimeGenerated, 컴퓨터, 이미지, ImageTag, ImageSize, VirtualSize, 실행 중, 일시 중지됨, 중지됨, 실패, SourceSystem, ImageID, TotalContainer |
| 컨테이너 로그 | `ContainerLog` | TimeGenerated, 컴퓨터, 이미지 ID, 컨테이너 이름, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| 컨테이너 서비스 로그 | `ContainerServiceLog`  | TimeGenerated, 컴퓨터, TimeOfCommand, 이미지, 명령, SourceSystem, ContainerID |
| 컨테이너 노드 인벤토리 | `ContainerNodeInventory_CL`| TimeGenerated, 컴퓨터, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| 컨테이너 프로세스 | `ContainerProcess_CL` | TimeGenerated, 컴퓨터, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes 클러스터의 Pod 인벤토리 | `KubePodInventory` | TimeGenerated, Computer, ClusterId , ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Kubernetes 클러스터의 노드 부분 인벤토리 | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| kubernetes 이벤트 | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Kubernetes 클러스터의 서비스 | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Kubernetes 클러스터의 노드 부분에 대한 성능 메트릭 | Perf &#124; where ObjectName == “K8SNode” | cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes | 
| Kubernetes 클러스터의 컨테이너 부분에 대한 성능 메트릭 | Perf &#124; where ObjectName == “K8SContainer” | cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes | 

## <a name="search-logs-to-analyze-data"></a>로그를 검색하여 데이터 분석
Log Analytics에서는 현재 클러스터 구성이 최적 상태로 실행되고 있는지를 파악하는 데 도움이 되도록 추세를 찾아보거나, 병목 상태를 진단하거나, 예측하거나 데이터 간 상관 관계를 파악할 있습니다.  미리 정의된 로그 검색을 즉시 사용하거나, 사용자 지정하여 원하는 방식으로 정보를 반환할 수 있습니다. 

컨테이너를 확장하면 맨 오른쪽에서 사용할 수 있는 **로그 보기** 옵션을 선택하여 작업 영역에서 데이터를 대화형으로 분석할 수 있습니다.  포털에서 작업 중이던 페이지 바로 위에 **로그 검색** 페이지가 나타납니다.<br><br> ![Log Analytics에서 데이터 분석](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Log Analytics에 전달되는 컨테이너 로그 출력은 STDOUT 및 STDERR입니다. 컨테이너 상태는 Azure 관리 Kubernetes(AKS)를 모니터링하고 이로 인해 생성되는 데이터 양이 많으므로 Kube 시스템은 오늘 수집되지 않습니다.     

### <a name="example-log-search-queries"></a>로그 검색 쿼리 예제
한두 가지 예제로 쿼리 구성을 시작한 다음, 요구에 맞게 수정하는 것이 유용한 경우가 종종 있습니다. 다음 샘플 쿼리로 테스트하면 고급 쿼리를 빌드하는 데 도움이 될 수 있습니다.

| 쿼리 | 설명 | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | 모든 컨테이너의 수명 주기 정보 나열| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | kubernetes 이벤트|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | 이미지 인벤토리 | 
| **고급 분석에서 꺾은선형 차트를 선택합니다**.<br> Perf<br> &#124; where ObjectName == "Container" and CounterName == "% Processor Time"<br> &#124; summarize AvgCPUPercent = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 컨테이너 CPU | 
| **고급 분석에서 꺾은선형 차트를 선택합니다**.<br> Perf &#124; where ObjectName == "Container" and CounterName == "Memory Usage MB"<br> &#124; summarize AvgUsedMemory = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | 컨테이너 메모리 |

## <a name="how-to-stop-monitoring-with-container-health"></a>컨테이너의 상태를 사용하여 모니터링을 중지하는 방법
AKS 컨테이너에 대한 모니터링을 사용하도록 설정한 후에 더 이상 모니터링하지 않기로 결정한 경우 제공된 Azure Resource Manager 템플릿을 사용하여 **New-AzureRmResourceGroupDeployment** 또는 Azure CLI에서 *옵트아웃(opt out)* 할 수 있습니다.  *옵트아웃(opt out)* 할 구성을 지정하는 JSON 템플릿도 있고, AKS 클러스터 리소스 ID 및 클러스터가 배포된 리소스 그룹을 지정하기 위해 구성하는 매개 변수 값이 포함되어 있는 JSON 템플릿도 있습니다.  PowerShell에서 템플릿을 사용하여 리소스를 배포하는 개념을 잘 모를 경우 [Resource Manager 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy.md)를 참조하고, Azure CLI에 대한 자세한 내용은 [Resource Manager 템플릿 및 Azure CLI를 사용하여 리소스 배포](../azure-resource-manager/resource-group-template-deploy-cli.md)를 참조하세요.

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬로 설치하고 사용해야 합니다.  Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

### <a name="create-and-execute-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster resource id"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. 이 파일을 로컬 폴더에 **OptOutTemplate.json**으로 저장합니다.
3. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. **aksResourceId** 및 **aksResourceLocation**의 값을 선택한 클러스터에 대한 **속성** 페이지에서 찾을 수 있는 AKS 클러스터 값으로 편집합니다.<br><br> ![컨테이너 속성 페이지](./media/monitoring-container-health/container-properties-page.png)<br>

    **속성** 페이지에서 **작업 영역 리소스 ID**도 복사합니다.  이 값은 Log Analytics 작업 영역 삭제 작업을 이 프로세스 중에 수행하지 않고 나중에 수행하기로 한 경우에 필요합니다.  

5. 이 파일을 로컬 폴더에 **OptOutParam.json**으로 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

    * 템플릿이 포함된 폴더에서 다음 PowerShell 명령을 사용하려면

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Linux에서 Azure CLI를 사용하여 다음 명령을 실행하려면

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

        ```azurecli
        ProvisioningState       : Succeeded
        ```

클러스터 모니터링을 지원하려는 목적으로만 작업 영역을 만들었으며 더 이상 필요하지 않은 경우 수동으로 삭제해야 합니다. 작업 영역을 삭제하는 방법을 모를 경우 [Azure Portal에서 Azure Log Analytics 작업 영역 삭제](../log-analytics/log-analytics-manage-del-workspace.md)를 참조하세요.  이전에 4단계에서 복사한 **작업 영역 리소스 ID**는 나중에 필요하므로 기억해두세요.  

## <a name="troubleshooting"></a>문제 해결
이 섹션에서는 컨테이너 상태와 관련된 문제 해결에 도움이 되는 정보를 제공합니다.

컨테이너 상태가 성공적으로 사용되도록 설정 및 구성되었으나 로그 검색을 수행할 대 Log Analytics에 상태 정보나 결과가 표시되지 않을 경우 다음 단계를 수행하여 문제를 진단하는 데 도움이 될 수 있습니다.   

1. 명령 `kubectl get ds omsagent --namespace=kube-system`을 실행하여 에이전트의 상태를 확인합니다.

    출력은 다음과 유사하며 에이전트가 클러스터의 모든 노드에서 실행되고 있음을 나타냅니다.  예를 들어 이 클러스터에는 두 개의 노드가 있으며 해당 값은 노드 수와 같아야 합니다.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. `kubectl get pods --namespace=kube-system` 명령을 통해 Pod의 상태를 검토하여 실행 중인지 여부를 확인합니다.

    출력은 다음과 비슷하며 omsagent의 상태는 *Running*입니다.

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. 에이전트 로그를 확인합니다. 컨테이너화 에이전트는 배포되면 OMI 명령을 실행하여 빠른 검사를 실행하고 에이전트 및 Docker 공급자의 버전을 표시합니다. 에이전트가 성공적으로 온보드되었는지 확인하려면 `kubectl logs omsagent-484hw --namespace=kube-system` 명령을 실행합니다.

    상태는 다음과 유사합니다.

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>다음 단계

[로그를 검색](../log-analytics/log-analytics-log-search.md)하여 자세한 컨테이너 상태 및 응용 프로그램 성능 정보를 확인합니다.  