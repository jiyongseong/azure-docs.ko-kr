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
ms.openlocfilehash: aa21168f1e19ccddc190379b30e1b612d96b56d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="sign-in-users-and-all-the-microsoft-graph-api-from-an-android-app"></a>Android 앱에서 사용자 및 모든 Microsoft Graph API 로그인

이 가이드에서는 네이티브 Android 응용 프로그램이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API를 한 개 이상 호출할 수 있는 방법에 대해 설명합니다.

이 가이드를 완료했으면 응용 프로그램에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 및 학교 계정뿐만 아니라 개인 계정(outlook.com, live.com 등)의 로그인을 수락할 수 있습니다. 그런 다음 응용 프로그램이 Azure Active Directory v2 끝점으로 보호되는 API를 호출합니다.  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리
![이 샘플의 작동 원리](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

이 가이드에서 만든 응용 프로그램 예제는 Azure Active Directory v2 끝점(이 경우에는 Microsoft Graph API)에서 토큰을 수락하는 Web API를 쿼리하는 데 Android 응용 프로그램이 사용되는 시나리오를 기반으로 합니다. 이 시나리오에서는 응용 프로그램이 인증 헤더를 통해 HTTP 요청에 획득한 토큰을 추가합니다. MSAL(Microsoft 인증 라이브러리)에서는 토큰 획득 및 갱신을 처리합니다.

## <a name="prerequisites"></a>필수 조건
* 이 안내식 설정은 Android Studio를 중심으로 설명하지만 다른 Android 응용 프로그램 개발 환경에도 적용됩니다. 
* Android SDK 21 이상이 필요합니다(SDK 25 권장).
* Android용 MSAL의 이 릴리스에는 Google Chrome 또는 사용자 지정 탭을 사용하는 웹 브라우저가 필요합니다.

> [!NOTE]
> Google Chrome은 Visual Studio Emulator for Android에 포함되어 있지 않습니다. 여기 제시된 코드는 Google Chrome이 설치된 API 25가 포함된 에뮬레이터 또는 API 21 이상이 포함된 이미지에서 테스트하는 것이 좋습니다.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>보호되는 Web API에 액세스하기 위한 토큰 획득 처리

응용 프로그램 예제는 사용자를 인증한 후에 Azure Active Directory v2로 보호되는 Microsoft Graph API 또는 Web API를 쿼리하는 데 사용할 수 있는 액세스 토큰을 수신합니다.

Microsoft Graph와 같은 API는 액세스 토큰이 있어야만 특정 리소스에 대한 액세스를 허용합니다. 예를 들어 사용자 프로필을 읽고, 사용자 일정에 액세스하고, 메일을 보내는 데 액세스 토큰이 필요합니다. 응용 프로그램에서는 MSAL을 사용하여 액세스 토큰을 요청하고 API 범위를 지정하여 이러한 리소스에 액세스할 수 있습니다. 그런 다음 이 액세스 토큰은 보호되는 리소스에 대한 모든 호출에 사용될 수 있도록 HTTP 인증 헤더에 추가됩니다. 

MSAL은 사용자를 대신해 액세스 토큰 캐싱 및 새로 고침을 관리하므로 응용 프로그램에서 이러한 작업을 수행할 필요가 없습니다.

## <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|설명|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|MSAL(Microsoft 인증 라이브러리)|
