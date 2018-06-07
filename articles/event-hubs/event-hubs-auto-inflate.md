---
title: "Azure Event Hubs 처리량 단위 자동 확장 | Microsoft Docs"
description: "처리량 단위를 자동으로 확장할 네임스페이스에서 자동 확장 사용"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 20ee0e6cff2a07cbd62a79799eada5708c7a0f07
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2018
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Azure Event Hubs 처리량 단위 자동 확장

Azure Event Hubs는 확장성이 뛰어난 데이터 스트리밍 플랫폼입니다. 따라서 서비스를 사용하기 시작한 후에 Event Hubs 사용량이 증가합니다. 그러려면 Event Hubs의 크기를 조정하고 더 빠른 전송 속도를 처리할 수 있도록 미리 지정된 처리량 단위를 늘려야 합니다. Event Hubs의 *자동 확장* 기능은 필요한 사용량에 맞게 처리량 단위를 자동으로 확장합니다. 처리량 단위를 늘리면 다음과 같은 상황에서 제한 시나리오를 예방할 수 있습니다.

* 데이터 수신 속도가 설정된 처리량 단위를 초과하는 경우.
* 데이터 송신 요청 속도가 설정된 처리량 단위를 초과하는 경우.

## <a name="how-auto-inflate-works"></a>자동 확장 작동 방식

Event Hubs 트래픽은 처리량 단위로 제어됩니다. 단일 처리량 단위는 수신의 경우 1MB/초, 송신의 경우 그 두 배입니다. Standard Event Hubs는 처리량 단위를 1-20으로 구성할 수 있습니다. 자동 확장 기능을 사용하면 필요한 최소 처리량 단위의 작은 규모부터 시작할 수 있습니다. 그런 다음 트래픽 증가에 따라 필요한 처리량 단위의 최대 한도까지 자동으로 조정합니다. 자동 확장의 이점은 다음과 같습니다.

- 작은 규모부터 시작하여 사용량 증가에 따라 확장하는 효율적인 크기 조정 메커니즘입니다.
- 제한 문제 없이 지정된 상한까지 자동으로 크기 조정합니다.
- 크기 조정의 정도나 시점을 제어할 때 크기 조정을 더 잘 제어할 수 있습니다.

## <a name="enable-auto-inflate-on-a-namespace"></a>네임스페이스에서 자동 확장 사용

다음 방법 중 하나를 사용하여 Event Hubs 네임스페이스에서 자동 확장을 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)
2. Azure Resource Manager 템플릿

### <a name="enable-auto-inflate-through-the-portal"></a>포털을 통해 자동 확장 사용

Event Hubs 네임스페이스를 만들 때 자동 확장 기능을 사용하도록 설정할 수 있습니다.
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

이 옵션을 사용하도록 설정하면 작은 처리량 단위부터 시작하여 필요한 사용량 증가에 따라 확장할 수 있습니다. 확장 상한은 가격 책정에 즉시 영향을 미치지 않으며 가격 책정은 시간당 사용된 처리량 단위 수에 따라 다릅니다.

포털의 설정 창에서 **크기 조정** 옵션을 사용하여 자동 확장을 사용하도록 설정할 수도 있습니다.
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 자동 확장 사용

Azure Resource Manager 템플릿을 배포하는 동안 자동 확장을 사용하도록 설정할 수 있습니다. 예를 들어 `isAutoInflateEnabled` 속성을 **true**로 설정하고 `maximumThroughputUnits`를 10으로 설정합니다.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

전체 템플릿은 GitHub에서 [Event Hubs 네임스페이스 만들기 및 확장 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) 템플릿을 참조하세요.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)

