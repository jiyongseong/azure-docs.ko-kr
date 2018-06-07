---
title: Azure AD에서 유럽 고객의 ID 데이터를 저장하는 위치 | Microsoft Docs
description: Microsoft Azure Active Directory에서 유럽 고객의 ID 관련 데이터를 저장하는 위치에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.author: lizross
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.custom: it-pro
ms.openlocfilehash: 19dc163dbb6dd296a417f5c313a36c7f7c9e50d7
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305688"
---
# <a name="where-does-microsoft-azure-active-directory-azure-ad-store-identity-data-for-european-customers"></a>Microsoft Azure AD(Azure Active Directory)에서 유럽 고객의 ID 데이터를 저장하는 위치
Azure AD를 통해 사용자 ID를 관리하고 인텔리전스 기반 액세스 정책을 만들어 조직의 리소스를 보호할 수 있습니다. ID 데이터는 서비스를 구독할 때 조직에서 제공한 주소에 기반된 위치에 저장됩니다. 예를 들어 Office 365 또는 Azure를 구독하는 경우입니다. ID 데이터를 저장하는 위치에 대한 특정 정보는 Microsoft 보안 센터의 [데이터를 어디에 배치하나요?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) 섹션을 사용할 수 있습니다.

대부분의 Azure AD 관련 유럽 ID 데이터가 유럽 데이터 센터에 보관되는 반면 미국 데이터 센터에 저장된 데이터에는 일반적으로 다섯 개의 사용자 관련 특성이 있습니다. 이러한 특성은 GivenName, Surname, userPrincipalName, Domain 및 PasswordHash입니다. 누군가가 PasswordHash 값을 Azure AD와 동기화할 수 없도록 중지하는 온-프레미스 페더레이션된 인증 방법을 사용하는 경우 PasswordHash 특성은 예외일 수 있으며 미국에 저장되지 않습니다. 또한 정상 Azure AD 작업에 필요한 몇 가지 서비스 관련 운영 데이터가 있습니다. 이 데이터는 미국에 저장되고 개인 데이터를 포함하지 않습니다.

## <a name="data-stored-outside-of-european-datacenters-for-european-customers"></a>유럽 데이터 센터 외부에 저장된 유럽 고객의 데이터

유럽 기반 주소를 사용하는 조직의 경우 대부분의 Azure AD 관련 유럽 ID 데이터는 유럽 데이터 센터에 유지됩니다. 유럽 데이터 센터에 저장되지 않은 Azure AD 데이터에는 다음이 포함됩니다.

- **ID 관련 특성**

    다음 ID 관련 특성은 미국에 복제됩니다.

    - GivenName
    - Surname
    - userPrincipalName
    - 도메인
    - PasswordHash
    - SourceAnchor
    - AccountEnabled
    - PasswordPolicies
    - StrongAuthenticationRequirement
    - ApplicationPassword
    - PUID

- **Microsoft Azure MFA(Azure Multi-Factor Authentication) 및 Azure AD SSPR(셀프 서비스 암호 재설정)**
    
    MFA는 모든 유휴 사용자 데이터를 유럽 데이터 센터에 저장합니다. 그러나 다음을 비롯한 일부 MFA 서비스 관련 데이터는 미국에 저장됩니다.
    
    - MFA 또는 SSPR을 사용 중인 경우 2단계 인증 및 관련된 개인 데이터는 미국에 저장될 수 있습니다.
        - 전화 통화 또는 SMS를 사용하는 모든 2단계 인증은 미국 이동 통신 사업자에 의해 완료될 수 있습니다.
        - Microsoft Authenticator 앱을 사용하는 푸시 알림에는 유럽 외부에 있을 수 있는 제조업체 알림 서비스(Apple 또는 Google)의 알림이 필요합니다.
        - OATH 코드는 미국에서 항상 유효성이 검사됩니다. 
    - 일부 MFA 및 SSPR 로그는 인증 유형에 관계 없이 30일 동안 미국에 저장됩니다.

- **Microsoft Azure AD B2C(Azure Active Directory B2C)**

    Azure AD B2C는 모든 유휴 사용자 데이터를 유럽 데이터 센터에 저장합니다. 그러나 개인 데이터를 제거한 작업 로그는 사용자가 서비스에 액세스하는 위치에 유지됩니다. 예를 들어 B2C 사용자가 미국에 있는 서비스에 액세스하는 경우 작업 로그는 미국에 유지됩니다. 또한 개인 데이터를 포함하지 않는 모든 정책 구성 데이터는 미국에만 저장됩니다. 정책 구성에 대한 자세한 내용은 [Azure Active Directory B2C: 기본 제공 정책](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-policies) 아티클을 참조하세요.

- **Microsoft Azure AD B2B(Azure Active Directory B2B)** 
    
    Azure AD B2B는 모든 유휴 사용자 데이터를 유럽 데이터 센터에 저장합니다. 그러나 B2B는 미국 데이터 센터 내의 테이블에 해당 개인 메타데이터를 저장합니다. 이 표에는 redeemUrl, invitationTicket, 리소스 테넌트 ID, InviteRedirectUrl 및 InviterAppId와 같은 필드가 포함됩니다.

- **Microsoft Azure AD DS(Azure Active Directory Domain Services)**

    Azure AD DS는 고객이 선택한 Azure Virtual Network와 동일한 위치에 사용자 데이터를 저장합니다. 따라서 네트워크가 유럽 밖에 있는 경우 데이터가 유럽 외부에 복제되고 저장됩니다.

- **Azure AD와 통합된 서비스 및 앱**

    Azure AD와 통합되는 모든 서비스 및 앱은 ID 데이터에 대한 액세스 권한이 있습니다. 각 서비스 및 앱을 평가하여 해당 특정 서비스 및 앱에서 ID 데이터를 처리하는 방법 및 회사의 데이터 저장소 요구 사항을 충족하는지를 결정합니다.

    Microsoft 서비스의 데이터 상주에 대한 자세한 내용은 Microsoft 보안 센터의 [데이터가 어디에 있나요?](https://www.microsoft.com/en-us/trustcenter/privacy/where-your-data-is-located) 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계
위에서 설명한 기능에 대한 자세한 내용은 다음과 같은 문서를 참조하세요.
- [Azure Active Directory 시작](get-started-azure-ad.md)
- [Multi-Factor Authentication이란?](https://docs.microsoft.com/en-us/azure/active-directory/authentication/multi-factor-authentication)
- [Azure AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/en-us/azure/active-directory/authentication/active-directory-passwords-overview)
- [Azure Active Directory B2C란?](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2B 공동 작업이란?](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure AD(Active Directory) Domain Services](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-overview)
