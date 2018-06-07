---
title: Azure Section UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Common.Section UI 요소에 대해 설명합니다.
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
ms.openlocfilehash: e6d7d5d7b205d275c72e96df527a354b072a9dd3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI 요소
제목 아래에 하나 이상의 요소를 그룹화하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>스키마
```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Some section",
  "elements": [
    {
      "name": "element1",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 1"
    },
    {
      "name": "element2",
      "type": "Microsoft.Common.TextBox",
      "label": "Some text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>설명
- `elements`는 하나 이상의 요소를 포함해야 하며, `Microsoft.Common.Section`을 제외한 모든 요소 형식을 포함할 수 있습니다.
- 이 요소는 `toolTip` 속성을 지원하지 않습니다.

## <a name="sample-output"></a>샘플 출력
`elements`에 있는 요소의 출력 값에 액세스하려면 [basics()](create-uidefinition-functions.md#basics) 또는 [steps()](create-uidefinition-functions.md#steps) 함수와 점 표기법을 사용합니다.

```json
basics('section1').element1
```

`Microsoft.Common.Section` 형식의 요소에는 출력 값 자체가 없습니다.

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
