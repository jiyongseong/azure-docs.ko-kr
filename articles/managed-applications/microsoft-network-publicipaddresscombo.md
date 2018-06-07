---
title: Azure PublicIpAddressCombo UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Network.PublicIpAddressCombo UI 요소에 대해 설명합니다.
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
ms.date: 04/30/2018
ms.author: tomfitz
ms.openlocfilehash: bf0ef5be609fba14ab12e1e6f9f97bc63f032aae
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Microsoft.Network.PublicIpAddressCombo UI 요소
새 또는 기존 공용 IP 주소를 선택하는 컨트롤 그룹입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft.network.publicipaddresscombo.png)

- 사용자가 공용 IP 주소에 대해 '없음'을 선택하면 도메인 이름 레이블 텍스트 상자가 숨겨집니다.
- 사용자가 기존 공용 IP 주소를 선택하면 도메인 이름 레이블 텍스트 상자가 비활성화됩니다. 이 값은 선택한 IP 주소의 도메인 이름 레이블입니다.
- 도메인 이름 접미사(예: westus.cloudapp.azure.com)는 선택한 위치에 따라 자동으로 업데이트됩니다.

## <a name="schema"></a>스키마
```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- `constraints.required.domainNameLabel`을 **true**로 설정하면 사용자가 새 공용 IP 주소를 만들 때 도메인 이름 레이블을 제공해야 합니다. 레이블이 없는 기존의 공용 IP 주소는 선택할 수 없습니다.
- `options.hideNone`을 **true**로 설정하면 공용 IP 주소에 대해 **없음**을 선택하는 옵션이 숨겨집니다. 기본값은 **false**입니다.
- `options.hideDomainNameLabel`이 **true**로 설정하면 도메인 이름 레이블의 텍스트 상자가 숨겨집니다. 기본값은 **false**입니다.
- `options.hideExisting`이 true이면 사용자가 기존 공용 IP 주소를 선택할 수 없습니다. 기본값은 **false**입니다.
- `zone`의 경우 지정된 영역에 대한 유일한 공용 IP 주소 또는 영역 복원력 있는 공용 IP 주소를 사용할 수 있습니다.

## <a name="sample-output"></a>샘플 출력
사용자가 공용 IP 주소를 선택하지 않는 경우 예상되는 출력은 다음과 같습니다.
```json
{
  "newOrExistingOrNone": "none"
}
```

사용자가 새 또는 기존 IP 주소를 선택하는 경우 예상되는 출력은 다음과 같습니다.
```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "newOrExistingOrNone": "new"
}
```
- `options.hideNone`이 **true**로 지정되면 `newOrExistingOrNone`은 **새 값** 또는 **기존 값**만 갖습니다.
- `options.hideDomainNameLabel`이 **true**로 지정되면 `domainNameLabel`이 선언되지 않습니다.

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
