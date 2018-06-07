---
title: Azure에서 Linux VM에 정책을 사용하여 보안 적용 | Microsoft Docs
description: Azure Resource Manager Linux Virtual Machine에 정책을 적용하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: singhkay
ms.openlocfilehash: 12066fe622ec3ed2eded74ecf7b791689ed873d5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Azure Resource Manager를 사용하여 Linux VM에 정책 적용
조직은 정책을 사용하여 엔터프라이즈 전체에 다양한 규칙을 적용할 수 있습니다. 원하는 동작을 적용하여 조직의 성공에 기여함과 동시에 위험을 완화할 수 있습니다. 이 문서에서는 Azure Resource Manager 정책을 사용하여 조직의 Virtual Machines에 대해 원하는 동작을 정의하는 방법을 설명합니다.

정책에 대한 소개는 [Azure Policy란?](../../azure-policy/azure-policy-introduction.md)을 참조하세요.

## <a name="permitted-virtual-machines"></a>허용되는 Virtual Machines
조직에 대한 가상 머신이 응용 프로그램과 호환되는지 확인하기 위해 허용된 운영 체제를 제한할 수 있습니다. 다음 정책 예제에서는 Ubuntu 14.04.2-LTS Virtual Machines만 만들 수 있도록 허용합니다.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

와일드 카드를 사용하여 모든 Ubuntu LTS 이미지를 허용하도록 이전 정책을 수정합니다. 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

정책 필드에 대한 자세한 내용은 [정책 별칭](../../azure-policy/policy-definition.md#aliases)을 참조하세요.

## <a name="managed-disks"></a>관리 디스크

관리 디스크 사용을 요구하려면 다음 정책을 사용합니다.

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Virtual Machines에 대한 이미지 

보안상의 이유로 승인된 사용자 지정 이미지만 환경에 배포하도록 요구할 수 있습니다. 승인된 이미지를 포함하는 리소스 그룹이나 특정한 승인된 이미지를 지정할 수 있습니다.

다음 예제에서는 승인된 리소스 그룹의 이미지가 필요합니다.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

다음 예제에서는 승인된 이미지 ID를 명시합니다.

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Virtual Machine 확장 

특정 유형의 확장을 사용하지 못하게 하고자 할 수 있습니다. 예를 들어 한 확장이 특정 사용자 지정 가상 머신 이미지와 호환되지 않을 수 있습니다. 다음 예제에서는 특정 확장을 차단하는 방법을 보여 줍니다. 게시자 및 유형을 사용하여 차단할 확장을 판단합니다.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="next-steps"></a>다음 단계
* 앞의 예제와 표시된 바와 같이 정책 규칙을 정의한 후에는 정책 정의를 만들고 범위에 할당해야 합니다. 범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. 정책을 할당하려면 [Azure Portal을 사용하여 리소스 정책 할당 및 관리](../../azure-policy/assign-policy-definition.md), [PowerShell을 사용하여 정책 할당](../../azure-policy/assign-policy-definition-ps.md) 또는 [Azure CLI를 사용하여 정책 할당](../../azure-policy/assign-policy-definition-cli.md)을 참조하세요.
* 리소스 정책에 대한 소개는 [Azure Policy란?](../../azure-policy/azure-policy-introduction.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](../../azure-resource-manager/resource-manager-subscription-governance.md)를 참조하세요.
