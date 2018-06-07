---
title: "사용자 지정 개발 응용 프로그램에 권한을 부여하는 방법 | Microsoft Docs"
description: "Azure AD 포털 또는 URL 매개 변수를 사용하여 사용자 지정 개발 응용 프로그램에 대한 권한을 부여하는 방법"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 0f13efd5e629bde592a3ab3a3e2db4a22180b5bb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>사용자 지정 개발 응용 프로그램에 권한을 부여하는 방법

앱에 대해 사전에 동의하려 하거나 앱에 대해 동의하지 않은 오류가 발생한 경우 아래 단계를 수행해 보세요.

## <a name="how-to-perform-admin-consent-for-your-application"></a>응용 프로그램에 대해 관리자 동의를 수행하는 방법

조직의 모든 사용자에 대해 응용 프로그램에 동의하는 효과가 있습니다.

1. **전역 관리자**로서 **앱 등록** 블레이드로 이동하고 앱을 선택합니다.

2. **필요한 권한**을 선택하고 마지막으로 블레이드 맨 위에 있는 **권한 부여** 단추를 누릅니다.

또는 앱 구성과 함께 *login.microsoftonline.com*으로 요청을 구성하고 *&prompt=admin\_consent*에 추가할 수 있습니다. 관리자 자격 증명으로 로그인한 후 앱에서 모든 사용자에 대한 동의를 부여했습니다.

## <a name="how-to-force-user-consent-for-your-application"></a>응용 프로그램에 대해 사용자 동의를 강제로 적용하는 방법

* 최종 사용자가 인증할 때마다 동의해야 하는 *&prompt=consent*를 인증 요청에 추가합니다.

## <a name="next-steps"></a>다음 단계

[동의 및 AzureAD와 앱 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[AzureAD v2.0 수렴형 앱에 대한 동의 및 권한 부여](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
