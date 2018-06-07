---
title: Azure VirtualNetworkCombo UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Network.VirtualNetworkCombo UI 요소에 대해 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 5d806afbfd74d68d139f494c7a5a6e871a7dae36
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Microsoft.Network.VirtualNetworkCombo UI 요소
새 또는 기존 가상 네트워크를 선택하는 컨트롤 그룹입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Network.VirtualNetworkCombo](./media/managed-application-elements/microsoft.network.virtualnetworkcombo.png)

- 위쪽 와이어프레임에서 사용자가 새 가상 네트워크를 선택했으므로 사용자는 각 서브넷의 이름과 주소 접두사를 사용자 지정할 수 있습니다. 이 경우 서브넷을 구성하는 것은 선택 사항입니다.
- 아래쪽 와이어프레임에서 사용자가 기존 가상 네트워크를 선택했으므로 사용자는 배포 템플릿에 필요한 각 서브넷을 기존 서브넷에 매핑해야 합니다. 이 경우 서브넷을 구성해야 합니다.

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- 지정하는 경우 크기가 `defaultValue.addressPrefixSize`인 첫 번째 중복되지 않는 주소 접두사는 사용자의 구독에 있는 기존 가상 네트워크에 따라 자동으로 결정됩니다.
- `defaultValue.name` 및 `defaultValue.addressPrefixSize`의 기본값은 **null**입니다.
- `constraints.minAddressPrefixSize`를 지정해야 합니다. 지정한 값보다 작은 주소 공간이 있는 기존 가상 네트워크는 선택할 수 없습니다.
- `subnets`를 지정해야 하며, 각 서브넷에 대해 `constraints.minAddressPrefixSize`를 지정해야 합니다.
- 새 가상 네트워크를 만들 때 각 서브넷의 주소 접두사는 가상 네트워크의 주소 접두사와 각각의 `addressPrefixSize`에 따라 자동으로 계산됩니다.
- 기존 가상 네트워크를 사용할 때 각각의 `constraints.minAddressPrefixSize`보다 작은 서브넷은 모두 선택할 수 없습니다. 또한 지정하는 경우 `minAddressCount` 값 이상의 사용 가능한 주소를 포함하지 않는 서브넷은 선택할 수 없습니다.
기본값은 **0**입니다. 사용 가능한 주소가 연속적인 주소가 되도록 하려면 `requireContiguousAddresses`에 대해 **true**를 지정합니다. 기본값은 **true**입니다.
- 기존 가상 네트워크에서 서브넷을 만드는 것은 지원되지 않습니다.
- `options.hideExisting`이 **true**이면 사용자가 기존 가상 네트워크를 선택할 수 없습니다. 기본값은 **false**입니다.

## <a name="sample-output"></a>샘플 출력
```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
