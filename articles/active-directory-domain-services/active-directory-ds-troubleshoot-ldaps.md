---
title: 'Azure Active Directory Domain Services: 보안 LDAP 구성 문제 해결 | Microsoft Docs'
description: Azure AD Domain Services에 대한 보안 LDAP 문제 해결
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: ergreenl
ms.openlocfilehash: 38885caacfb98f76627854a782aa56d8a609aad9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Azure AD Domain Services - 보안 LDAP 구성 문제 해결

이 문서에서는 Azure AD Domain Services에 대한 [보안 LDAP를 구성](active-directory-ds-admin-guide-configure-secure-ldap.md)할 때 발생하는 일반적인 문제에 대한 해결 방법을 제공합니다.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: 보안 LDAP 네트워크 보안 그룹 구성

**경고 메시지:**

*관리되는 도메인에 대해 인터넷을 통해 보안 LDAP가 사용되도록 설정됩니다. 그러나 포트 636에 대한 액세스는 네트워크 보안 그룹을 사용하여 잠글 수 없습니다. 이로 인해 관리되는 도메인의 사용자 계정은 암호 무차별 대입 공격에 노출될 수 있습니다.*

### <a name="secure-ldap-port"></a>보안 LDAP 포트

보안 LDAP가 사용되도록 설정될 경우, 특정 IP 주소의 인바운드 LDAPS 액세스만 허용하는 추가 규칙을 만드는 것이 좋습니다. 이러한 규칙은 보안 위협을 초래하는 무차별 암호 대입 공격으로부터 도메인을 보호합니다. 포트 636은 관리되는 도메인에 대한 액세스를 허용합니다. 다음은 보안 LDAP에 대한 액세스를 허용하도록 NSG를 업데이트하는 방법입니다.

1. Azure Portal에서 [네트워크 보안 그룹](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) 탭으로 이동합니다.
2. 테이블에서 도메인에 연결된 NSG를 선택합니다.
3. **인바운드 보안 규칙**을 클릭합니다.
4. 포트 636 규칙 만들기
   1. 위쪽 탐색 모음에서 **추가**를 클릭합니다.
   2. 원본의 **IP 주소**를 선택합니다.
   3. 이 규칙에 대한 원본 포트 범위를 지정합니다.
   4. 대상 포트 범위에 대한 "636"을 입력합니다.
   5. 프로토콜은 **TCP**입니다.
   6. 규칙에 적절한 이름, 설명 및 우선 순위를 지정합니다. 이 규칙의 우선 순위는 "모두 거부" 규칙(있는 경우)의 우선 순위보다 높아야 합니다.
   7. **확인**을 클릭합니다.
5. 규칙이 만들어졌는지 확인합니다.
6. 2시간 후에 도메인 상태를 확인하여 단계를 올바르게 완료했는지 검사합니다.

> [!TIP]
> 636 포트는 Azure AD Domain Services를 원활하게 실행하는 데 필요한 규칙 중 하나입니다. 자세한 내용은 [네트워킹 지침](active-directory-ds-networking.md) 또는 [NSG 구성 문제 해결](active-directory-ds-troubleshoot-nsg.md) 아티클을 참조하세요.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: 보안 LDAP 인증서 만료

**경고 메시지:**

*관리되는 도메인에 대한 보안 LDAP 인증서는 XX에 만료됩니다.*

**해결 방법:**

[보안 LDAP 구성](active-directory-ds-admin-guide-configure-secure-ldap.md) 아티클에 설명된 단계를 수행하여 새 보안 LDAP 인증서를 만듭니다.

## <a name="contact-us"></a>문의처
[지원이 필요하거나 피드백을 공유하려면](active-directory-ds-contact-us.md)Azure Active Directory Domain Services 제품 팀에 문의하세요.
