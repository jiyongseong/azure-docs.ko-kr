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
ms.openlocfilehash: 79b6b8e5d81d3885f9c125f971f3e32e695cf2b1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows 데스크톱 앱에서 Microsoft Graph API 호출

이 가이드에서는 네이티브 Windows Desktop .NET(XAML) 응용 프로그램이 액세스 토큰을 얻고 Azure Active Directory v2 끝점에서 액세스 토큰이 필요한 Microsoft Graph API 또는 다른 API를 호출할 수 있는 방법에 대해 설명합니다.

이 가이드를 완료하면 응용 프로그램은 개인 계정(outlook.com, live.com 등 포함)을 사용하는 보호된 API를 호출할 수 있습니다. 또한 응용 프로그램은 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 및 학교 계정을 사용합니다.  

> [!NOTE] 
> 이 가이드에는 Visual Studio 2015 업데이트 3 또는 Visual Studio 2017이 필요합니다.  이러한 버전이 설치되지 않으셨습니까? [체험용 Visual Studio 2017 다운로드](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 가이드의 작동 방식](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.png)

이 가이드를 사용하여 만든 응용 프로그램 예제를 사용하면 Windows 데스크톱 응용 프로그램이 Microsoft Graph API를 쿼리하거나 Web API가 Azure Active Directory v2 끝점에서 토큰을 수락할 수 있습니다. 이 시나리오에서는 인증 헤더를 통해 HTTP 요청에 토큰을 추가합니다. MSAL(Microsoft 인증 라이브러리)에서는 토큰 획득 및 갱신을 처리합니다.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>보호되는 Web API에 액세스하기 위한 토큰 획득 처리

응용 프로그램 예제는 사용자를 인증한 후에 Azure Active Directory v2로 보호되는 Microsoft Graph API 또는 Web API를 쿼리하는 데 사용할 수 있는 토큰을 수신합니다.

Microsoft Graph와 같은 API에는 특정 리소스에 대한 액세스를 허용하는 토큰이 필요합니다. 예를 들어 사용자 프로필을 읽고, 사용자 일정에 액세스하고, 메일을 보내는 데 토큰이 필요합니다. 응용 프로그램에서는 MSAL을 사용하여 액세스 토큰을 요청하고 API 범위를 지정하여 이러한 리소스에 액세스할 수 있습니다. 그런 다음 이 액세스 토큰은 보호되는 리소스에 대한 모든 호출에 사용될 수 있도록 HTTP 인증 헤더에 추가됩니다. 

MSAL은 사용자를 대신해 액세스 토큰 캐싱 및 새로 고침을 관리하므로 응용 프로그램에서 이러한 작업을 수행할 필요가 없습니다.

## <a name="nuget-packages"></a>NuGet 패키지

이 가이드에서는 다음 NuGet 패키지를 사용합니다.

|라이브러리|설명|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|MSAL(Microsoft 인증 라이브러리)|

