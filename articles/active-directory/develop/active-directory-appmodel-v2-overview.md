---
title: Azure Active Directory v2.0 끝점 | Microsoft Docs
description: Microsoft 계정 및 Azure Active Directory 로그인을 사용하는 응용 프로그램 빌드를 소개합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 2dee579f-fdf6-474b-bc2c-016c931eaa27
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: 0fde3cb77389994352b62f06d370c709fa115749
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="sign-in-microsoft-account-and-azure-active-directory-users-in-a-single-application"></a>단일 응용 프로그램의 로그인 Microsoft 계정 및 Azure Active Directory 사용자
과거 Azure Active Directory에서 Microsoft 개인 계정과 회사 계정을 모두 지원하려는 응용 프로그램 개발자는 별도의 두 시스템과 통합해야 했습니다. Azure AD(Azure Active Directory) v 2.0 끝점에서는 이 프로세스를 간소화하는 새 인증 API 버전을 도입합니다. Azure AD v2.0 끝점에서는 단일 통합을 사용하여 두 가지 유형의 계정에서 로그인할 수 있도록 합니다. 또한 Azure AD v2.0 끝점을 사용하는 응용 프로그램은 두 가지 계정 유형 중 하나를 사용하여 [Microsoft Graph API](https://graph.microsoft.io)의 REST API를 사용할 수 있습니다.

## <a name="getting-started"></a>시작
다음 목록에서 즐겨찾는 플랫폼을 선택하여 Microsoft 오픈 소스 라이브러리 및 프레임워크로 응용 프로그램을 빌드합니다. OAuth 2.0 및 OpenID Connect 프로토콜을 사용하여 인증 라이브러리를 사용하지 않고도 프로토콜 메시지를 직접 주고받을 수도 있습니다.
<br />

[!INCLUDE [Azure AD v2.0 endpoint platforms](../../../includes/active-directory-v2-quickstart-table.md)]

## <a name="learn-more-about-the-azure-ad-v20-endpoint"></a>Azure AD v2.0 끝점에 대해 자세히 알아보기
Azure AD v2.0 끝점으로 수행할 수 있는 작업에 대해 자세히 알아봅니다.

* [Azure AD v2.0 끝점으로 빌드할 수 있는 응용 프로그램 유형](active-directory-v2-flows.md)을 검색합니다.
* Azure AD v2.0 끝점에 대한 [제한, 제한 사항 및 제약 조건](active-directory-v2-limitations.md)을 이해합니다.
* Azure AD v 2.0 끝점의 개요를 보려면 다음 비디오를 시청하세요.

>[!VIDEO https://channel9.msdn.com/Events/Build/2017/P4031/player]

## <a name="additional-resources"></a>추가 리소스
Azure AD v2.0 끝점 플랫폼에 대한 자세한 정보를 살펴봅니다.

* [Azure AD v 2.0 프로토콜 참조](active-directory-v2-protocols.md)
* [Azure AD v 2.0 토큰 참조](active-directory-v2-tokens.md)
* [Azure AD v2.0 인증 라이브러리 참조](active-directory-v2-libraries.md)
* [Azure AD v2.0 끝점의 범위 및 동의](active-directory-v2-scopes.md)
* [Microsoft Graph API](https://graph.microsoft.io)

> [!NOTE]
> Azure Active Directory에서 회사 계정과 학교 계정에만 로그인해야 하는 경우 [개발자용 Azure AD 가이드](active-directory-developers-guide.md)를 시작합니다. Azure AD v2.0 끝점은 Microsoft 개인 계정에 명시적으로 로그인해야 하는 개발자가 사용하기 위한 것입니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
