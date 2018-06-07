---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 6871127ac138fb0113af73ca5222e3e2c2f99d7e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript SPA(단일 페이지 응용 프로그램)에서 Microsoft Graph API 호출

이 가이드에서는 JavaScript SPA(단일 페이지 응용 프로그램)가 개인, 회사 및 학교 계정으로 로그인하고, 액세스 토큰을 얻고, Microsoft Graph API 또는 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 기타 API를 호출할 수 있는 방법에 대해 설명합니다.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 가이드에서 생성된 샘플 앱의 작동 원리](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
## <a name="more-information"></a>추가 정보

이 가이드에서 만든 샘플 응용 프로그램은 JavaScript SPA가 Azure Active Directory v2 끝점에서 토큰을 수락하는 Microsoft Graph API 또는 Web API를 쿼리하도록 합니다. 이 시나리오에서는 사용자가 로그인한 후에 액세스 토큰이 요청되고 권한 부여 헤더를 통해 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.

<!--end-collapse-->

<!--start-collapse-->
## <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript용 Microsoft 인증 라이브러리 미리 보기|

> [!NOTE]
> *msal.js*는 *Azure Active Directory v2 끝점*을 대상으로 하며 이를 통해 개인, 학교 및 회사 계정으로 로그인하여 토큰을 획득할 수 있습니다. *Azure Active Directory v2 끝점*에는 [몇 가지 제한 사항](..\articles\active-directory\develop\active-directory-v2-limitations.md)이 있습니다. 학교 및 회사 계정에만 관심이 있는 경우 *adal.js* 및 *V1 끝점*을 사용합니다. v1 및 v2 끝점 간의 차이점을 이해하려면 [v1-v2 비교](..\articles\active-directory\develop\active-directory-v2-compare.md)를 읽어보세요.

<!--end-collapse-->
