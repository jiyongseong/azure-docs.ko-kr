---
title: Resource Manager 템플릿을 사용하여 진단 설정 자동 활성화 | Microsoft Docs
description: Resource Manager 템플릿을 사용하여 Event Hubs로 진단 로그 스트림을 활성화하거나 로그를 저장소 계정에 저장하는 진단 설정을 만드는 방법을 알아봅니다.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2018
ms.author: johnkem
ms.openlocfilehash: 5b372ae5a7ff2ad26e4bb83675f592df3f08931b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 리소스 생성 시 진단 설정 자동 활성화
이 문서에서는 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md) 을 사용하여 리소스 생성 시 리소스에서 진단 설정을 구성하는 방법을 보여 줍니다. 그러면 Event Hubs로 진단 로그 및 메트릭의 스트리밍을 자동으로 시작하거나, 리소스 생성 시 Log Analytics에 보낼 수 있습니다.

Resource Manager 템플릿을 사용하여 진단 로그를 활성화하는 방법은 리소스 형식에 따라 다릅니다.

* **비-Compute** 리소스(예를 들어, 네트워크 보안 그룹, Logic Apps, Automation)는 [이 문서에 설명된 진단 설정](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)을 사용합니다.
* **Compute** 리소스(WAD/LAD 기반)는 [이 문서에 설명된 WAD/LAD 구성 파일](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)을 사용합니다.

이 문서에서는 두 방법 중 하나를 사용하여 진단을 구성하는 방법을 설명합니다.

기본적인 단계는 다음과 같습니다.

1. 리소스를 만들고 진단을 활성화하는 방법을 설명하는 JSON 파일로 템플릿을 만듭니다.
2. [배포 방법을 사용하여 템플릿을 배포합니다](../azure-resource-manager/resource-group-template-deploy.md).

다음은 비-Compute 및 Compute 리소스에 대해 생성해야 하는 템플릿 JSON 파일의 예입니다.

## <a name="non-compute-resource-template"></a>비-Compute 리소스 템플릿
비-Compute 리소스 템플릿의 경우 두 가지 작업을 수행해야 합니다.

1. 저장소 계정 이름, 이벤트 허브 권한 부여 규칙 ID 및/또는 Log Analytics 작업 영역 ID(저장소 계정에 진단 로그 보관 활성화, Event Hubs에 로그 스트리밍 및/또는 Log Analytics에 로그 보내기)에 대한 매개 변수 blob에 매개 변수를 추가합니다.
   
    ```json
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Azure Resource ID of the Log Analytics workspace for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. 진단 로그를 활성화할 리소스의 리소스 배열에서 `[resource namespace]/providers/diagnosticSettings`형식으로 리소스를 추가합니다.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/[parameters('settingName')]",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2017-05-01-preview",
        "properties": {
          "name": "[parameters('settingName')]",
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
          "eventHubName": "[parameters('eventHubName')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ],
          "metrics": [
            {
              "category": "AllMetrics",
              "enabled": true,
              "retentionPolicy": {
                "enabled": false,
                "days": 0
              }
            }
          ]
        }
      }
    ]
    ```

진단 설정에 대한 속성 Blob는 [이 문서에 설명된 형식](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)을 따릅니다. `metrics` 속성을 추가하면 [리소스는 Azure Monitor 메트릭스를 지원합니다](monitoring-supported-metrics.md)를 표시하고 리소스 메트릭을 이러한 동일한 출력으로 보낼 수도 있습니다.

다음은 Logic App을 만들고 Event Hubs로 스트리밍 및 저장소 계정에 저장을 설정하는 전체 예제입니다.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "logicAppName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Logic App that will be created."
      }
    },
    "testUri": {
      "type": "string",
      "defaultValue": "http://azure.microsoft.com/status/feed/"
    },
    "settingName": {
      "type": "string",
      "metadata": {
        "description": "Name of the setting. Name for the diagnostic setting resource. Eg. 'archiveToStorage' or 'forSecurityTeam'."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "eventHubAuthorizationRuleId": {
      "type": "string",
      "metadata": {
        "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
      }
    },
    "eventHubName": {
      "type": "string",
      "metadata": {
        "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Logic/workflows",
      "name": "[parameters('logicAppName')]",
      "apiVersion": "2016-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/[parameters('settingName')]",
          "dependsOn": [
            "[resourceId('Microsoft.Logic/workflows', parameters('logicAppName'))]"
          ],
          "apiVersion": "2017-05-01-preview",
          "properties": {
            "name": "[parameters('settingName')]",
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
            "eventHubName": "[parameters('eventHubName')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "WorkflowRuntime",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ],
            "metrics": [
              {
                "timeGrain": "PT1M",
                "enabled": true,
                "retentionPolicy": {
                  "enabled": false,
                  "days": 0
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Compute 리소스 템플릿
Compute 리소스(예: Virtual Machine 또는 서비스 패브릭 클러스터)에서 진단을 활성화하려면 다음이 필요합니다.

1. Azure 진단 확장을 VM 리소스 정의에 추가합니다.
2. 매개 변수로 저장소 계정 및/또는 이벤트 허브를 지정합니다.
3. 모든 XML 문자를 올바르게 이스케이프하여 WADCfg XML 파일의 내용을 XMLCfg 속성에 추가합니다.

> [!WARNING]
> 이 마지막 단계는 이해하기 어려울 수 있습니다. [이 문서를 참조](../virtual-machines/extensions/diagnostics-template.md#diagnostics-configuration-variables) 하세요.
> 
> 

샘플을 포함한 전체 과정은 [이 문서](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 설명되어 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure 진단 로그에 대해 자세히 알아보기](monitoring-overview-of-diagnostic-logs.md)
* [Event Hubs로 Azure 진단 로그 스트림](monitoring-stream-diagnostic-logs-to-event-hubs.md)

