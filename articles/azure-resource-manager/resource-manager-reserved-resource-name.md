---
title: 예약된 Azure 리소스 이름 오류 | Microsoft Docs
description: 예약어가 포함된 리소스 이름을 제공할 때 발생하는 오류를 해결하는 방법을 설명합니다.
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
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-reserved-resource-name-errors"></a>예약된 리소스 이름 오류 해결

이 문서에서는 이름에 예약어가 포함된 리소스를 배포할 때 발생하는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

공용 끝점을 통해 사용할 수 있는 리소스를 배포할 때 다음 오류가 표시될 수 있습니다.

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>원인

공용 끝점이 있는 리소스의 이름에는 예약어 또는 상표를 사용할 수 없습니다.

예약어는 다음과 같습니다.

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

전체 단어 또는 이름의 하위 문자열로 사용할 수 없는 단어는 다음과 같습니다.

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>해결 방법

예약어 중 하나를 사용하지 않는 이름을 제공합니다.