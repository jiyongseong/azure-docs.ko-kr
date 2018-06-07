---
title: 앱 등록 포털 도움말 항목 | Microsoft Docs
description: Microsoft 앱 등록 포털의 다양한 기능을 설명합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.openlocfilehash: 9d38f6e6d6b9fa47b1cd1497820f7ff887954ad5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156190"
---
# <a name="app-registration-reference"></a>앱 등록 참조
이 문서에서는 Microsoft 앱 등록 포털([https://apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/))에 있는 다양한 기능에 대한 컨텍스트와 설명을 제공합니다.

## <a name="my-applications"></a>내 응용 프로그램
이 목록에는 Azure AD v2.0 끝점에서 사용하도록 등록된 모든 응용 프로그램이 포함되어 있습니다. 이러한 응용 프로그램은 Azure Active Directory에서 개인 Microsoft 계정 및 회사/학교 계정 모두를 사용하는 사용자를 로그인할 수 있습니다. Azure AD v2.0 엔드포인트에 대한 자세한 내용은 [v2.0 개요](active-directory-appmodel-v2-overview.md)를 참조하세요. 이러한 응용 프로그램을 사용하여 Microsoft 계정 인증 끝점인 `https://login.live.com`과 통합할 수도 있습니다.

## <a name="live-sdk-applications"></a>Live SDK 응용 프로그램
이 목록에는 Microsoft 계정으로만 사용하도록 등록된 모든 응용 프로그램이 포함되어 있습니다. Azure Active Directory에는 사용할 수 없습니다. 여기서는 이전에 `https://account.live.com/developers/applications`의 MSA 개발자 포털에 등록된 모든 응용 프로그램을 찾을 수 있습니다. 이전에 `https://account.live.com/developers/applications`에서 수행한 모든 함수를 이제 이 새 포털 `https://apps.dev.microsoft.com`에서 수행할 수 있습니다. Microsoft 계정 응용 프로그램에 대한 추가 질문이 있으면 문의해 주세요.

## <a name="application-secrets"></a>응용 프로그램 암호
응용 프로그램 암호는 Azure AD에서 응용 프로그램에서 신뢰할 수 있는 [클라이언트 인증](http://tools.ietf.org/html/rfc6749#section-2.3) 이 되도록 하는 자격 증명입니다. OAuth 및 OpenID Connect에서 응용 프로그램 비밀은 일반적으로 `client_secret`라고 합니다. v2.0 프로토콜에서, 웹 주소 지정 가능한 위치에서 `https` 구성표를 사용하여 보안 토큰을 받는 모든 응용 프로그램에서는 해당 보안 토큰 상환 시 Azure AD에서 자신을 식별할 수 있도록 응용 프로그램 암호를 사용해야 합니다. 또한 장치에서 토큰을 받는 모든 네이티브 클라이언트는 응용 프로그램 비밀을 사용하여 클라이언트 인증을 수행할 수 없도록 금지됩니다. 이에 따라 안전하지 않은 환경에서 비밀을 저장할 필요가 없습니다.

각 앱에는 지정된 시점에 유효한 두 개의 응용 프로그램 비밀이 포함될 수 있습니다. 두 개의 비밀을 유지하여 응용 프로그램의 전체 환경에서 정기적으로 키 롤오버를 수행할 수 있습니다. 응용 프로그램 전체를 새 비밀로 마이그레이션하면 이전 비밀을 삭제하고 새 비밀을 프로비전할 수 있습니다.

현재 앱 등록 포털에는 두 가지 유형의 응용 프로그램 비밀만 허용됩니다. **새 암호 생성**을 선택하면 응용 프로그램에 사용할 수 있는 공유 비밀이 각 데이터 저장소에 생성되어 저장됩니다. **새 키 쌍 생성**을 선택하면 Azure AD에 대한 클라이언트 인증을 위해 다운로드하여 사용할 수 있는 새 공개/개인 키가 만들어집니다. **공개 키 업로드**를 선택하면 자신의 공개/개인 키 쌍을 사용할 수 있습니다.
공개 키가 포함된 인증서를 업로드해야 합니다.

## <a name="profile"></a>프로필
앱 등록 포털의 프로필 섹션을 사용하여 응용 프로그램에 대한 로그인 페이지를 사용자 지정할 수 있습니다. 현재 로그인 페이지의 응용 프로그램 로고, 서비스 약관 URL 및 개인정보처리방침 URL을 변경할 수 있습니다. 로고는 15KB 이하인 GIP, PNG 또는 JPEG 파일로 된 투명한 48 x 48 또는 50 x 50 픽셀 이미지여야 합니다. 값을 변경하고 결과 로그인 페이지를 확인해 보세요!

## <a name="live-sdk-support"></a>Live SDK 지원
"Live SDK 지원"을 사용하도록 설정하면 만든 응용 프로그램 암호 모두가 Azure AD 및 Microsoft 계정 데이터 저장소 모두에 프로비전됩니다. 이렇게 하면 응용 프로그램을 Microsoft 계정 서비스(login.live.com)와 직접 통합할 수 있습니다. Azure AD v2.0 엔드포인트를 사용하는 것이 아니라 Microsoft 계정을 직접 사용하여 앱을 빌드하려면 Live SDK 지원을 사용하도록 설정되어 있는지 확인해야 합니다.

Live SDK 지원을 사용하지 않도록 설정하면 응용 프로그램 비밀만 Azure AD 데이터 저장소에 기록됩니다. Azure AD 데이터 저장소는 FISMA 규정 준수 등의 특정 표준을 충족하는 엔터프라이즈급 규정을 포함합니다. Live SDK 지원을 사용하도록 설정하면 응용 프로그램이 이러한 일부 표준을 준수하지 못할 수 있습니다.

Azure AD v2.0 끝점만 사용하려면 안전하게 Live SDK 지원을 사용하지 않도록 설정할 수 있습니다.

