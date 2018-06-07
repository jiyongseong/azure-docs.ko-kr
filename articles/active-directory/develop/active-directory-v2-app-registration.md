---
title: 포털을 사용하는 Azure AD v2.0 끝점에 응용 프로그램 등록 | Microsoft Docs
description: v2.0 끝점을 사용하여 Microsoft 서비스 로그인 및 액세스를 사용하도록 설정하기 위해 Microsoft에 앱을 등록하는 방법
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 8ab4e6b5b2813a216b6dd6f0fc108a09239ca9a6
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157846"
---
# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>v2.0 끝점을 사용하여 앱을 등록하는 방법
개인 MSA(Microsoft 계정) 및 회사 또는 학교 계정(Azure AD) 로그인을 모두 허용하는 앱을 빌드하려면 먼저 앱을 Microsoft에 등록해야 합니다. 지금은 Azure AD나 MSA를 사용하여 가지고 있는 기존의 앱은 사용할 수 없습니다. - 새 앱을 만들어야 합니다.

> [!NOTE]
> 일부 Azure Active Directory 시나리오 및 기능만 v2.0 끝점에서 지원합니다. v2.0 엔드포인트를 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.


## <a name="visit-the-microsoft-app-registration-portal"></a>Microsoft 앱 등록 포털 방문
먼저 [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에 있는 Microsoft 앱 등록 포털로 이동합니다. 

개인, 직장 또는 학교 Microsoft 계정 중 하나로 로그인 합니다. 계정이 하나라도 없다면, 새로운 개인 계정을 등록합니다.

등록했나요? 아마도 비어있을 Microsoft 앱의 목록을 보고 있을 것입니다. 변경을 시작해봅시다.

**앱 추가**를 클릭하여 이름을 지정합니다. 포털에서는 나중에 코드에서 사용할 전역적으로 고유한 응용 프로그램 ID를 앱에 할당합니다. 앱이 API(예: Office, Azure 또는 사용자의 고유한 웹 API)를 호출하는 액세스 토큰이 필요한 서버 측 구성 요소를 포함한 경우, **응용 프로그램 암호** 또한 만들어야 합니다.

다음으로, 앱에서 사용할 **플랫폼**을 추가합니다.

* 웹 기반 앱의 경우 로그인 메시지를 보낼 수 있는 **리디렉션 URI**를 제공합니다.
* 모바일 앱의 경우 자동으로 만들어진 기본 리디렉션 URI를 복사합니다.
* 웹 API의 경우 웹 API에 대한 기본 액세스 범위가 자동으로 생성됩니다. **범위 추가** 단추를 사용하여 추가 범위를 추가하도록 선택할 수 있습니다. **사전 승인된 응용 프로그램** 양식을 사용하여 웹 API를 사용하도록 미리 승인된 응용 프로그램을 추가할 수도 있습니다. 

필요에 따라 **프로필** 섹션에서 로그인 페이지의 디자인을 사용자 지정할 수 있습니다. 다음 단계로 넘어가기 전에 **저장** 을 클릭하십시오.

> [!NOTE]
> [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)를 사용하여 응용 프로그램을 만드는 경우 응용 프로그램은 포털에 로그인하는 데 사용하는 계정의 홈 테넌트에 등록됩니다. 즉, 개인 Microsoft 계정을 사용하여 Azure AD 테넌트에 응용 프로그램을 등록할 수 없습니다. 응용 프로그램을 특정 테넌트에 명시적으로 등록하려면 해당 테넌트에 원래 만든 계정을 사용하여 로그인합니다.


## <a name="build-a-quickstart-app"></a>빠른 시작 앱 빌드
이제 Microsoft 앱이 있으므로 v2.0 빠른 시작 자습서 중 하나를 완료할 수 있습니다. 몇 가지 권장 사항입니다.

[!INCLUDE [active-directory-v2-quickstart-table](../../../includes/active-directory-v2-quickstart-table.md)]

