---
title: Azure AD 응용 프로그램 프록시로 업그레이드 | Microsoft Docs
description: Microsoft Forefront 또는 Unified Access Gateway를 업그레이드하는 경우 가장 좋은 프록시 솔루션을 선택합니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: cf1c39d0270dcb7de3ae8ff487b21874b23db74a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="compare-remote-access-solutions"></a>원격 액세스 솔루션 비교

Azure Active Directory 응용 프로그램 프록시는 Microsoft에서 제공하는 두 가지 원격 액세스 솔루션 중 하나입니다. 다른 하나는 온-프레미스 버전인 웹 응용 프로그램 프록시입니다. 이 두 가지 솔루션은 이전에 Microsoft에서 제공한 제품인 Microsoft Forefront TMG(Threat Management Gateway) 및 UAG(Unified Access Gateway)를 대체합니다. 이 문서를 사용하여 이러한 네 가지 솔루션을 서로 비교하는 방법을 이해합니다. 사용되지 않는 TMG 또는 UAG 솔루션을 아직도 사용하는 경우 이 문서를 사용하여 응용 프로그램 프록시 중 하나로 마이그레이션하는 계획을 세워보세요. 


## <a name="feature-comparison"></a>기능 비교

이 표를 사용하여 TMG(Threat Management Gateway), UAG(Unified Access Gateway), WAP(웹 응용 프로그램 프록시) 및 Azure AD AP(응용 프로그램 프록시)를 서로 비교하는 방법을 이해합니다.

| 기능 | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| 인증서 인증 | 예 | 예 | - | - |
| 선택적으로 브라우저 앱 게시 | 예 | 예 | 예 | 예 |
| 사전 인증 및 Single Sign-On | 예 | 예 | 예 | 예 | 
| 계층 2/3 방화벽 | 예 | 예 | - | - |
| 전달 프록시 기능 | 예 | - | - | - |
| VPN 기능 | 예 | 예 | - | - |
| 다양한 프로토콜 지원 | - | 예 | 예, HTTP를 통해 실행하는 경우 | 예, HTTP 또는 원격 데스크톱 게이트웨이를 통해 실행하는 경우 |
| ADFS 프록시 서버 역할 수행 | - | 예 | 예 | - |
| 응용 프로그램 액세스에 대한 단일 포털 | - | 예 | - | 예 |
| 응답 본문 링크 변환 | 예 | 예 | - | 예 | 
| 헤더를 사용한 인증 | - | 예 | - | 예, PingAccess 사용 | 
| 클라우드 규모 보안 | - | - | - | 예 | 
| 조건부 액세스 | - | 예 | - | 예 |
| DMZ(완충 영역)에 구성 요소 없음 | - | - | - | 예 |
| 인바운드 연결 없음 | - | - | - | 예 |

대부분의 시나리오에서 Azure AD 응용 프로그램을 최신 솔루션으로 사용하는 것이 좋습니다. 웹 응용 프로그램 프록시는 AD FS용 프록시 서버가 필요한 시나리오에서만 사용할 수 있으며, Azure Active Directory에서는 사용자 지정 도메인을 사용할 수 없습니다. 

Azure AD 응용 프로그램 프록시는 유사한 제품과 비교할 때 다음을 포함한 특유의 이점을 제공합니다.

- Azure AD를 온-프레미스 리소스로 확장
   - 클라우드 규모 보안 및 보호
   - 조건부 액세스 및 Multi-Factor Authentication과 같이 쉽게 사용할 수 있는 기능
- 완충 영역에 구성 요소 없음
- 필요한 인바운드 연결 없음
- 사용자가 O365, Azure AD 통합 SaaS 앱 및 온-프레미스 웹앱을 포함한 모든 응용 프로그램을 사용하기 위해 이동할 수 있는 단일 액세스 패널 


## <a name="next-steps"></a>다음 단계

- [Azure AD 응용 프로그램을 사용하여 온-프레미스 응용 프로그램에 대한 보안된 원격 액세스를 제공하는 방법](application-proxy.md)
- [Forefront TMG 및 UAG에서 응용 프로그램 프록시로 전환(영문)](https://blogs.technet.microsoft.com/isablog/2015/06/30/modernizing-microsoft-application-access-with-web-application-proxy-and-azure-active-directory-application-proxy/)
