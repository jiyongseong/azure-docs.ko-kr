---
title: 단일 AD FS를 사용하여 여러 Azure AD를 페더레이션 | Microsoft Docs
description: 이 문서에서는 하나의 AD FS를 통해 Azure AD를 페더레이션하는 방법에 대해 설명합니다.
keywords: 페더레이션, ADFS, AD FS, 다중 테넌트, 단일 AD FS, 단일 ADFS, 다중 테넌트 페더레이션, 다중 포리스트 ADFS, AAD 연결, 페더레이션, 테넌트 간 페더레이션
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.openlocfilehash: c55a4232c54308c5d000cfefc2c7dca2800b462c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>AD FS의 단일 인스턴스를 사용하여 Azure AD의 여러 인스턴스를 페더레이션

양방향 트러스트가 있는 경우 하나의 고가용성 AD FS 팜에서 여러 포리스트를 페더레이션할 수 있습니다. 이러한 다중 포리스트는 동일한 Azure Active Directory에 해당하거나 그렇지 않을 수 있습니다. 이 문서에서는 단일 AD FS 배포와 다른 Azure AD에서 동기화하는 둘 이상의 포리스트 간에 페더레이션을 구성하는 방법에 대한 지침을 제공합니다.

![단일 AD FS로 다중 테넌트 페더레이션](media/active-directory-aadconnectfed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> 이 시나리오에서는 장치 쓰기 저장 및 자동 장치 연결이 지원되지 않습니다.

> [!NOTE]
> Azure AD Connect는 단일 Azure AD에서 도메인에 대한 페더레이션을 구성할 수 있으므로 이 시나리오에서 페더레이션을 구성하는 데 사용할 수 없습니다.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>여러 Azure AD를 사용하여 AD FS를 페더레이션하는 단계

contoso.onmicrosoft.com Azure Active Directory의 contoso.com 도메인은 이미 contoso.com 온-프레미스 Active Directory 환경에 설치된 AD FS 온-프레미스와 통합되어 있다고 가정합니다. fabrikam.com은 fabrikam.onmicrosoft.com Azure Active Directory의 도메인입니다.

## <a name="step-1-establish-a-two-way-trust"></a>1단계: 양방향 트러스트 설정
 
contoso.com의 AD FS가 fabrikam.com에서 사용자를 인증하려면 contoso.com과 fabrikam.com 간에 양방향 트러스트 관계가 필요합니다. 이 [문서](https://technet.microsoft.com/library/cc816590.aspx)의 지침에 따라 양방향 트러스트를 만듭니다.
 
## <a name="step-2-modify-contosocom-federation-settings"></a>2단계: contoso.com 페더레이션 설정 수정 
 
AD FS에 페더레이션된 단일 도메인에 대해 설정되는 기본 발급자는 "http://ADFSServiceFQDN/adfs/services/trust"입니다(예: “http://fs.contoso.com/adfs/services/trust”). Azure Active Directory에는 각 페더레이션 도메인마다 고유한 발급자가 필요합니다. 동일한 AD FS에서 두 도메인을 페더레이션하므로 Azure Active Directory와 페더레이션하는 각 도메인 AD FS에 대해 고유하도록 발급자 값을 수정해야 합니다. 
 
AD FS 서버에서 Azure AD PowerShell을 열고 다음 단계를 수행합니다.
 
contoso.com Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain에 대한 페더레이션 설정인 contoso.com 도메인 Connect-MsolService Update가 포함된 Azure Active Directory에 연결합니다.
 
도메인 페더레이션 설정의 발급자가 "http://contoso.com/adfs/services/trust"로 변경되고, Azure AD 신뢰 당사자 트러스트에서 UPN 접미사를 기반으로 하는 올바른 issuerId 값을 발급하도록 발급 클레임 규칙이 추가됩니다.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>3단계: AD FS로 fabrikam.com 페더레이션
 
Azure AD PowerShell 세션에서 다음 단계를 수행합니다. fabrikam.com 도메인이 포함된 Azure Active Directory에 연결합니다.

    Connect-MsolService
fabrikam.com 관리되는 도메인을 페더레이션된 도메인으로 변환합니다.

    Convert-MsolDomainToFederated -DomainName anandmsft.com -Verbose -SupportMultipleDomain
 
위의 작업에서는 동일한 AD FS를 통해 fabrikam.com 도메인을 페더레이션합니다. 두 도메인 모두에 대해 Get-MsolDomainFederationSettings를 사용하여 도메인 설정을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Active Directory와 Azure Active Directory 연결](active-directory-aadconnect.md)
