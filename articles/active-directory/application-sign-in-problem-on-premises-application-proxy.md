---
title: Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 응용 프로그램에 로그인하는 데 문제가 있음 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시를 사용하여 Azure AD와 통합된 온-프레미스 응용 프로그램에 로그인할 수 없을 때 직면하는 일반적인 문제 해결
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 5aba26d14ba0ee8e8ee58f62deb8a82ad147a8aa
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 응용 프로그램에 로그인하는 데 문제가 있음

온-프레미스 응용 프로그램에 로그인하는 데 문제가 있을 경우 아래 단계를 시도하여 문제를 해결할 수 있습니다.

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>내 응용 프로그램을 로드할 수 있지만 페이지의 일부가 손상됨

다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.

  * [내 응용 프로그램으로 이동할 수 있지만 응용 프로그램 페이지가 올바르게 표시되지 않음](application-proxy-page-appearance-broken-problem.md)
  * [내 응용 프로그램으로 이동할 수 있지만 응용 프로그램을 로드하는 데 시간이 너무 오래 걸림](application-proxy-page-load-speed-problem.md)
  * [내 응용 프로그램으로 이동할 수 있지만 응용 프로그램 페이지의 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>내 응용 프로그램에 연결 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [내 응용 프로그램에 대해 어떤 포트를 열지 모름](application-proxy-connectivity-ports-how-to.md)
  * [내 응용 프로그램에 대한 커넥터 그룹에 작동 중인 커넥터가 없으므로 문제가 발생함](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>관리 포털에서 Azure AD 응용 프로그램 프록시를 구성하는 데 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [응용 프로그램 프록시 응용 프로그램을 구성하는 데 문제가 있음](application-proxy-config-how-to.md)
  * [내 응용 프로그램 프록시 응용 프로그램에 대해 Single Sign-On을 구성하는 방법을 모름](application-proxy-config-sso-how-to.md)
  * [관리 포털에 내 응용 프로그램을 만들 때 문제가 발생함](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>내 응용 프로그램에 백 엔드 인증을 설정하는 데 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [Kerberos 제한 위임을 구성하는 방법을 모름](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [PingAccess로 내 응용 프로그램을 구성하는 방법을 모름](application-proxy-back-end-ping-access-how-to.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>내 응용 프로그램에 로그인할 때 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * ["Can't Access this Corporate Application"(회사 응용 프로그램에 액세스할 수 없음) 오류가 표시됨](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>응용 프로그램 프록시 에이전트 커넥터에 문제가 있음
  다음 문서는 이 범주에서 가장 일반적인 몇 가지 문제를 해결하는 데 도움이 될 수 있습니다.
  * [응용 프로그램 프록시 에이전트 커넥터를 설치하는 데 문제가 있음](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>다음 단계
[온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](manage-apps/application-proxy.md)
