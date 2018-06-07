---
title: Azure Resource Manager를 사용하여 응용 프로그램과 서비스 배포 및 업그레이드 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터로 응용 프로그램 및 서비스를 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: 8c8ee30a603d439c0fadd0c1569813762bdf4351
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>응용 프로그램 및 서비스를 Azure Resource Manager 리소스로 관리

Azure Resource Manager를 통해 Service Fabric 클러스터에 응용 프로그램 및 서비스를 배포할 수 있습니다. 즉, 클러스터가 준비될 때까지 기다린 후에 PowerShell 또는 CLI를 통해 응용 프로그램을 배포 및 관리하는 대신, 이제 응용 프로그램 및 서비스를 JSON으로 나타내고 클러스터와 동일한 Resource Manager 템플릿에 배포할 수 있습니다. 응용 프로그램 등록, 프로비저닝 및 배포 프로세스는 모두 한 번에 수행됩니다.

이 방법은 클러스터에 필요한 설치, 거버넌스 또는 클러스터 관리 응용 프로그램을 배포할 때 권장되는 방법입니다. 여기에는 [패치 오케스트레이션 응용 프로그램](service-fabric-patch-orchestration-application.md), Watchdogs 또는 다른 서비스나 응용 프로그램을 배포하기 위해 먼저 클러스터에서 실행 중이어야 하는 모든 응용 프로그램이 포함됩니다. 

해당하는 경우 응용 프로그램을 Resource Manager 리소스로 관리하여 다음을 향상시킬 수 있습니다.
* 감사 내역: Resource Manager는 모든 작업을 감사하고, 이러한 응용 프로그램 및 클러스터에 대한 변경 내용을 추적하는 데 도움이 될 수 있는 자세한 *활동 로그*를 유지합니다.
* RBAC(역할 기반 액세스 제어): 클러스터에 배포된 응용 프로그램 뿐만 아니라 클러스터에 대한 액세스 관리 작업을 동일한 Resource Manager 템플릿을 통해 수행할 수 있습니다.
* Azure Resource Manager(Azure Portal을 통해)는 클러스터 및 중요한 응용 프로그램 배포를 관리하기 위한 원스톱 상점이 됩니다.

다음 코드 조각에서는 한 템플릿을 통해 관리될 수 있는 여러 종류의 리소스를 보여 줍니다.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Resource Manager 템플릿에 새 응용 프로그램 추가

1. 배포를 위해 클러스터의 Resource Manager 템플릿을 준비합니다. 이에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.
2. 클러스터에 배포하려는 일부 응용 프로그램을 고려해보세요. 다른 응용 프로그램이 종속성을 가질 수 있는 응용 프로그램이 항상 실행되고 있나요? 클러스터 거버넌스 또는 설치 응용 프로그램을 배포할 계획인가요? 이러한 종류의 응용 프로그램은 위에서 설명한 것처럼 Resource Manager 템플릿을 통해 가장 잘 관리됩니다. 
3. 이러한 방식으로 배포하려는 응용 프로그램을 결정했으면 응용 프로그램을 패키지하고, 압축하고, 파일 공유에 추가해야 합니다. Azure Resource Manager가 배포 중에 사용할 수 있도록 하려면 REST 끝점을 통해 공유에 액세스할 수 있어야 합니다.
4. Resource Manager 템플릿에서 클러스터 선언 아래에는 각 응용 프로그램의 속성이 설명되어 있습니다. 이러한 속성에는 복제본 또는 인스턴스 수와 리소스(다른 응용 프로그램이나 서비스) 간의 모든 종속성 체인이 포함됩니다. 포괄적인 속성 목록을 보려면 [REST API Swagger 사양](https://aka.ms/sfrpswaggerspec)을 참조하세요. 이 목록은 응용 프로그램 또는 서비스 매니페스트를 대신하지 않으며, 포함된 일부 항목을 클러스터의 Resource Manager 템플릿의 일부로 설명합니다. 다음은 상태 비저장 서비스 *Service1*과 상태 저장 서비스 *Service2*를 *Application1*의 일부로 배포하는 작업을 포함하는 샘플 템플릿입니다.

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > *apiVersion*을 `"2017-07-01-preview"`로 설정해야 합니다. 클러스터가 이미 배포된 경우 이 템플릿을 클러스터와는 별도로 배포할 수도 있습니다.

5. 배포합니다. 

## <a name="manage-an-existing-application-via-resource-manager"></a>Resource Manager를 통해 기존 응용 프로그램 관리

클러스터가 이미 작동 중이며 Resource Manager 리소스로 관리하려는 일부 응용 프로그램이 이미 클러스터에 배포된 경우, 응용 프로그램을 제거한 후 다시 배포하는 대신, 동일한 API를 통해 PUT 호출을 사용하여 응용 프로그램이 Resource Manager 리소스로 승인되도록 할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* [Service Fabric CLI](service-fabric-cli.md) 또는 [PowerShell](service-fabric-deploy-remove-applications.md)을 사용하여 클러스터에 다른 응용 프로그램을 배포합니다. 
* [Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade.md)

