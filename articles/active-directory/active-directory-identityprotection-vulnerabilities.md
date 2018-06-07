---
title: Azure Active Directory ID 보호에서 검색하는 취약성 | Microsoft Docs
description: Azure Active Directory ID 보호에서 검색하는 취약성에 대한 개요입니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0cb37a29847e69dae4a0e0682591bae092b38360
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Azure Active Directory ID 보호에서 검색하는 취약성
취약점은 공격자에 의해 악용될 수 있는 환경의 단점입니다. 이러한 취약성을 해결하여 조직에서 보안 상태를 개선하고 공격자가 이러한 취약성을 악용하지 않도록 방지하는 것이 좋습니다.


![취약성](./media/active-directory-identityprotection-vulnerabilities/101.png "취약성")



다음 섹션에서는 ID 보호에서 보고하는 취약성에 대한 개요를 제공합니다.

## <a name="multi-factor-authentication-registration-not-configured"></a>Multi-Factor Authentication 등록 구성되지 않음
이 취약점을 사용하면 조직에서 Azure Multi-Factor Authentication의 배포를 제어할 수 있습니다. 

Multi-Factor Authentication은 사용자 인증에 두 번째 계층의 보안을 제공합니다. 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 데이터와 응용 프로그램에 대한 액세스를 보호하는 데 도움이 됩니다. 전화 통화, 문자 메시지 또는 모바일 앱 알림 또는 확인 코드 및 타사 OATH 토큰과 같은 다양한 손쉬운 확인 옵션을 통해 강력한 인증을 전달합니다.

사용자 로그인에 Azure Multi-Factor Authentication을 요구하는 것이 좋습니다. 다단계 인증은 ID 보호를 통해 사용할 수 있는 위험 기반 조건부 액세스 정책에서 중요한 역할을 합니다.

자세한 내용은 [Azure Multi-Factor Authentication 정의](authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>관리되지 않은 클라우드 앱
이 취약점을 사용하면 조직에서 관리되지 않은 클라우드 앱을 식별할 수 있습니다.

오늘날 기업에서 IT 부서는 해당 조직의 사용자가 작업하는 데 사용하고 있는 일부 클라우드 응용 프로그램을 인지하지 못하는 경우가 있습니다. 관리자가 회사 데이터에 대한 무단 액세스, 데이터 유출 가능성 및 기타 보안 위험에 대해 우려하는 원인을 쉽게 확인합니다. 

Azure Active Directory를 사용하여 관리되지 않은 클라우드 응용 프로그램을 검색하고 이러한 응용 프로그램을 관리하도록 조직에서 클라우드 앱 검색을 배포하는 것이 좋습니다.

자세한 내용은 [Cloud App Discovery를 사용하여 관리되지 않은 클라우드 응용 프로그램 찾기](manage-apps/cloud-app-discovery.md)를 참조하세요.

## <a name="security-alerts-from-privileged-identity-management"></a>Privileged Identity Management에서 보안 경고
이 취약점을 사용하여 조직에서 권한있는 ID에 대한 경고를 검색하고 해결할 수 있습니다.  

사용자가 권한이 필요한 작업을 수행할 수 있도록 조직에서는 Azure AD, Azure 또는 Office 365 리소스에서 기타 SaaS 앱에 대한 임시 또는 영구 액세스 권한을 사용자에게 부여해야 합니다. 이러한 권한이 있는 사용자는 조직의 공격에 대한 취약성을 증가시킵니다. 이 취약점을 사용하여 불필요한 액세스 권한이 있는 사용자를 식별하고 적절한 조치를 취해 이들이 미치는 위험을 줄이고 제거할 수 있습니다. 

조직에서 Azure AD Privileged Identity Management를 사용하여 Azure AD 및 기타 Microsoft 온라인 서비스(Office 365 또는 Microsoft Intune 등)에서 권한 있는 ID 및 리소스에 대한 액세스를 관리, 제어, 모니터링하는 것이 좋습니다.

자세한 내용은 [Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md)를 참조하세요. 

## <a name="see-also"></a>참고 항목
* [Azure Active Directory ID 보호](active-directory-identityprotection.md)

