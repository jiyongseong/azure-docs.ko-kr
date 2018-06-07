---
title: Azure AD 응용 프로그램 프록시용 SSO 관리 | Microsoft Docs
description: 응용 프로그램 프록시의 Single Sign-On 기본 사항 알아보기
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: barbkess
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1d31b11c3307cc2e54b91e68e1e1a3811ae2ef96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="how-does-azure-ad-application-proxy-provide-single-sign-on"></a>Azure AD 응용 프로그램 프록시에서 Single Sign-On을 제공하는 방법

Single Sign-On은 Azure AD 응용 프로그램 프록시의 핵심 요소입니다.  사용자가 클라우드에서 Azure Active Directory에만 로그인하면 되기 때문에 최상의 사용자 환경을 제공합니다. 사용자가 Azure Active Directory에 인증하면 응용 프로그램 프록시 커넥터에서 온-프레미스 응용 프로그램에 대한 인증을 처리합니다. 백 엔드 응용 프로그램은 응용 프로그램 프록시를 통한 원격 사용자 로그인과 도메인에 가입된 장치의 정상적인 사용 간 차이점을 구별할 수 없습니다. 

응용 프로그램 Single Sign-On에 Azure Active Directory를 사용하려면 사전 인증 방법으로 **Azure Active Directory**를 선택해야 합니다. **통과**를 선택하면 사용자가 Azure Active Directory에 전혀 인증되지 않고 바로 응용 프로그램으로 이동됩니다. 응용 프로그램을 처음으로 게시할 때 이 설정을 구성할 수도 있고, Azure Portal에서 응용 프로그램으로 이동하여 응용 프로그램 프록시 설정을 편집할 수도 있습니다. 

Single Sign-On 옵션을 보려면 다음 단계를 따릅니다.

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **모든 응용 프로그램**으로 이동합니다.
3. Single Sign-On 옵션을 관리하려는 앱을 선택합니다.
4. **Single Sign-On**을 선택합니다.

   ![SSO 드롭다운 메뉴](./media/application-proxy-single-sign-on/single-sign-on-mode.png)

드롭다운 메뉴에는 응용 프로그램 Single Sign-On에 대한 5가지 옵션이 있습니다.

* Azure AD Single Sign-On 사용 안 함
* 암호 기반 로그온
* 연결된 로그온
* Windows 통합 인증
* 헤더 기반 로그온

## <a name="azure-ad-single-sign-on-disabled"></a>Azure AD Single Sign-On 사용 안 함

응용 프로그램에 Azure Active Directory Single Sign-On 통합을 사용하지 않으려면 **Azure AD Single Sign-On 사용 안 함**을 선택합니다. 이 옵션을 선택하면 사용자가 두 번 인증할 수 있습니다. 첫 번째로, Azure Active Directory에 인증한 후 응용 프로그램 자체에 로그인합니다. 

이 옵션은 사용자가 온-프레미스 응용 프로그램에 인증하지 않아도 되지만 Azure Active Directory를 원격 액세스의 보안 레이어로 추가하려는 경우에 적합한 선택입니다. 

## <a name="password-based-sign-on"></a>암호 기반 로그온

Azure Active Directory를 온-프레미스 응용 프로그램의 암호 자격 증명으로 사용하려는 경우 **암호 기반 로그온**을 선택합니다. 이 옵션은 응용 프로그램에서 액세스 토큰 또는 헤더 대신 사용자 이름/암호 조합을 사용하여 인증하는 경우에 적합한 선택입니다. 암호 기반 로그온을 사용하면 사용자가 응용 프로그램에 처음으로 액세스할 때 응용 프로그램에 로그인해야 합니다. 그 후에는 Azure Active Directory가 사용자를 대신하여 사용자 이름 및 암호를 제공합니다. 

암호 기반 로그온 설정에 대한 내용은 [응용 프로그램 프록시를 사용하여 Single Sign-On에 대한 암호 자격 증명 모음 설정](application-proxy-configure-single-sign-on-password-vaulting.md)을 참조하세요.

## <a name="linked-sign-on"></a>연결된 로그온

온-프레미스 ID에 대한 Single Sign-On 솔루션이 이미 설치된 경우 **연결된 로그온**을 선택합니다. 이 옵션을 사용하면 Azure Active Directory가 기존 SSO 솔루션을 활용하지만, 사용자에게 응용 프로그램에 대한 원격 액세스를 계속 제공합니다. 

연결된 로그온(공식적으로 기존 Single Sign-On으로 알려짐)에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work)를 참조하세요.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

온-프레미스 응용 프로그램에서 IWA(Windows 통합 인증)를 사용하거나 Single Sign-On에 KCD(Kerberos 제한된 위임)를 사용하려는 경우 **Windows 통합 인증**을 선택합니다. 이 옵션을 선택하면 사용자가 Azure Active Directory에만 인증하면 됩니다. 그러면 응용 프로그램 프록시 커넥터가 사용자를 가장하여 Kerberos 토큰을 가져오고 응용 프로그램에 로그인합니다. 

Windows 통합 인증 설정에 대한 내용은 [응용 프로그램 프록시를 사용하는 Single Sign-On에 대한 Kerberos 제한된 위임](application-proxy-configure-single-sign-on-with-kcd.md)을 참조하세요.

## <a name="header-based-sign-on"></a>헤더 기반 로그온 

응용 프로그램에서 인증에 헤더를 사용하는 경우 **헤더 기반 로그온**을 선택합니다. 이 옵션을 선택하면 사용자가 Azure Active Directory에만 인증하면 됩니다. Microsoft는 PingAccess라고 하는 타사 인증 서비스를 사용하는데, 이 서비스는 Azure Active Directory 액세스 토큰을 응용 프로그램의 헤더 형식으로 변환합니다. 

헤더 기반 인증 설정에 대한 내용은 [응용 프로그램 프록시를 사용하는 Single Sign-On에 대한 헤더 기반 인증](application-proxy-configure-single-sign-on-with-ping-access.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 프록시를 사용하여 Single Sign-On에 대한 암호 자격 증명 모음 설정](application-proxy-configure-single-sign-on-password-vaulting.md)
- [응용 프로그램 프록시를 사용하는 Single Sign-On에 대한 Kerberos 제한된 위임](application-proxy-configure-single-sign-on-with-kcd.md)
- [응용 프로그램 프록시를 사용하는 Single Sign-On에 대한 헤더 기반 인증](application-proxy-configure-single-sign-on-with-ping-access.md) 
