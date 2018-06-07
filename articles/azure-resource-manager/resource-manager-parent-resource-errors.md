---
title: Azure 부모 리소스 오류 | Microsoft Docs
description: 부모 리소스 사용 시 발생하는 오류를 해결하는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: c996a644f206051cb58522065f87f95a4058cdee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-parent-resources"></a>부모 리소스 오류 해결

이 문서에서는 부모 리소스에 종속된 리소스를 배포할 때 발생할 수 있는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

다른 리소스의 자식 리소스를 배포할 때 다음 오류가 표시될 수 있습니다.

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>원인

한 리소스가 다른 리소스의 자식인 경우 자식 리소스를 만들기 전에 부모 리소스가 있어야 합니다. 자식 리소스의 이름에 부모 이름이 포함됩니다. 예를 들어 SQL Database는 다음과 같이 정의될 수 있습니다.

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

그러나 서버에 대한 종속성을 지정하지 않으면 서버가 배포되기 전에 데이터베이스 배포가 먼저 시작될 수 있습니다.

## <a name="solution"></a>해결 방법

이 오류를 해결하려면 종속성을 포함하세요.

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

자세한 내용은 [Azure Resource Manager 템플릿에서 리소스를 배포하는 순서 정의](resource-group-define-dependencies.md)를 참조하세요.