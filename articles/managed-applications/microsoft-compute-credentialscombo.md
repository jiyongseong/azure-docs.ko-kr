---
title: Azure CredentialsCombo UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Compute.CredentialsCombo UI 요소에 대해 설명합니다.
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
ms.openlocfilehash: 914e354265754a05476e96411d35e6cb04183213
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI 요소
Windows 및 Linux 암호와 SSH 공개 키에 대한 유효성 검사가 포함된 기본 제공 컨트롤 그룹입니다.

## <a name="ui-sample"></a>UI 샘플
![Microsoft.Compute.CredentialsCombo](./media/managed-application-elements/microsoft.compute.credentialscombo.png)

## <a name="schema"></a>스키마
`osPlatform`이 **Windows**이면 다음 스키마가 사용됩니다.
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

`osPlatform`이 **Linux**이면 다음 스키마가 사용됩니다.
```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{8,}$",
    "customValidationMessage": "The password must contain at least 8 characters, with at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="remarks"></a>설명
- `osPlatform`을 지정해야 하며 **Windows** 또는 **Linux**일 수 있습니다.
- `constraints.required`을 **true**로 설정하면 암호 또는 SSH 공개 키 텍스트 상자에서 유효성을 성공적으로 검사하기 위한 값을 포함해야 합니다. 기본값은 **true**입니다.
- `options.hideConfirmation`을 **true**로 설정하면 사용자의 암호를 확인하는 두 번째 텍스트 상자가 숨겨집니다. 기본값은 **false**입니다.
- `options.hidePassword`를 **true**로 설정하면 암호 인증을 사용하는 옵션이 숨겨집니다. `osPlatform`이 **Linux**인 경우에만 사용할 수 있습니다. 기본값은 **false**입니다.
- 허용되는 암호에 대한 추가 제한 조건은 `customPasswordRegex` 속성을 사용하여 구현할 수 있습니다. 암호가 사용자 지정 유효성 검사에 실패하면 `customValidationMessage`의 문자열이 표시됩니다. 두 속성의 기본값은 **null**입니다.

## <a name="sample-output"></a>샘플 출력
`osPlatform`이 **Windows**이거나 사용자가 SSH 공개 키 대신 암호를 제공한 경우 예상되는 출력은 다음과 같습니다.

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rd",
}
```

사용자가 SSH 공개 키를 제공한 경우 예상되는 출력은 다음과 같습니다.
```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
