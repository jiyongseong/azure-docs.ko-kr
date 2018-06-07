---
title: b2clogin.com 사용| Microsoft Docs
description: login.microsoftonline.com 대신 b2clogin.com을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/29/2018
ms.author: davidmu
ms.openlocfilehash: d2737e995b91caa2dcc55027baa2b552e64af23e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33660427"
---
#<a name="using-b2clogincom"></a>b2clogin.com 사용

>[!IMPORTANT]
>이 기능은 공개 미리 보기 
>

이제 `login.microsoftonline.com`을 사용하는 대신 `<YourTenantName>.b2clogin.com`을 통해 Azure AD B2C 서비스를 사용하는 옵션이 제공됩니다.  여기에는 많은 이점이 있습니다.
* 동일한 쿠키 헤더 크기 제한을 다른 Microsoft 제품과 더 이상 공유하지 못함
* URL에서 Microsoft에 대한 모든 참조를 제거할 수 있음(`<YourTenantName>.onmicrosoft.com`을 테넌트 ID로 바꿀 수 있음)

 b2clogin.com 활용하기 위해서는 다음 중 일부를 설정해야 합니다.

1. **지금 엔드포인트를 실행**하려면 `login.microsoftonline.com`를 사용하는 대신 `<YourTenantName>.b2clogin.com`을 사용하는지 확인합니다.
2. MSAL를 사용하는 경우 `validateauthority=false`을 설정해야 합니다.  토큰 발급자가 `<YourTenantName>.b2clogin.com`이기 때문입니다.