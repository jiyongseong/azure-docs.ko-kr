---
title: "리소스 관리자 템플릿을 사용하여 활동 로그 경고 만들기 | Microsoft Docs"
description: "Azure 리소스가 만들어질 때 알림을 받습니다."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: b30912c44bd66f8c6fca548dc905f750e05c8621
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용하여 활동 로그 경고 만들기
이 문서에서는 [Azure 리소스 관리자 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)을 사용하여 활동 로그 경고를 구성하는 방법을 설명합니다. 템플릿을 사용하면 자동화된 배포 프로세스의 일부로 특정 활동 로그 이벤트 조건에 따라 많은 활성화하는 많은 경고를 쉽게 설정할 수 있습니다.

기본 단계는 다음과 같습니다.

1. 활동 로그 경고를 만드는 방법을 설명하는 JSON 파일로 템플릿을 만듭니다.

2. [배포 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)을 사용하여 템플릿을 배포합니다.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>활동 로그 경고에 대한 Resource Manager 템플릿
리소스 관리자 템플릿을 사용하여 활동 로그 경고를 만들려면 `microsoft.insights/activityLogAlerts` 유형의 리소스를 만듭니다. 그런 다음 모든 관련된 속성을 입력합니다. 다음은 활동 로그 경고를 만드는 템플릿입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

[Azure 빠른 시작 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)를 방문하여 활동 로그 경고 템플릿의 몇 가지 예제를 확인해 보세요.

> [!NOTE]

> 모니터 > [경고(미리 보기)](monitoring-overview-unified-alerts.md)에서 향상된 사용자 환경을 사용하여 활동 로그 경고 규칙을 만들 수도 있습니다. 이를 만드는 방법에 대한 자세한 내용은 [이 문서](monitoring-activity-log-alerts-new-experience.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [경고](monitoring-overview-alerts.md)에 대해 자세히 알아보세요.
- [ 템플릿을 사용하여 작업 그룹](monitoring-create-action-group-with-resource-manager-template.md)을 추가하는 방법을 알아보세요.
- [구독의 모든 자동 크기 조정 엔진 작업을 모니터링하기 위한 활동 로그 경고를 만드는](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) 방법을 알아보세요.
- [구독에서 실패한 모든 자동 크기 조정 규모 감축/규모 확장 작업을 모니터링하기 위한 활동 로그 경고를 만드는](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert) 방법을 알아보세요.
