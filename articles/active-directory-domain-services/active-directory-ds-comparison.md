---
title: 'Azure AD Domain Services: DIY 도메인 컨트롤러와 Azure AD Domain Services 비교 | Microsoft Docs'
description: DIY 도메인 컨트롤러와 Azure Active Directory Domain Services 비교
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: c384046d280e03de5a808d245dd273fdf7b44549
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Azure AD Domain Services가 사용 사례에 적합한지 결정하는 방법
Azure AD Domain Services를 사용하면 Azure에서 ID 인프라 유지 관리에 대해 걱정할 필요없이 Azure 인프라 서비스에서 워크로드를 배포할 수 있습니다. 이 관리되는 서비스는 고유한 서비스에 배포하고 관리하는 일반적인 Windows Server Active Directory 배포와 다릅니다. 서비스는 쉽게 배포할 수 있으며 자동화된 상태 모니터링 및 업데이트 관리를 제공합니다. 서비스를 지속적으로 개발하여 일반적인 배포 시나리오에 대한 지원을 추가할 예정입니다.

Azure AD Domain Services를 사용할 것인지를 결정하려면 다음 자료를 읽어보는 것이 좋습니다.

* [Azure AD Domain Services에서 제공하는 기능](active-directory-ds-features.md)목록을 봅니다.
* 일반적인 [Azure AD Domain Services용 배포 시나리오](active-directory-ds-scenarios.md)를 검토합니다.
* 마지막으로 [Azure AD Domain Services를 자체 AD 옵션과 비교](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure)합니다.

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Azure에서 DIY AD 도메인과 Azure AD Domain Services 비교
다음 테이블에서는 Azure AD Domain Services를 사용할지 아니면 Azure에서 고유한 AD 인프라를 관리할지를 결정하도록 도와줍니다.

| **기능** | **Azure AD Domain Services** | **Azure VM의 '직접' AD** |
| --- |:---:|:---:|
| [**관리되는 서비스**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**보안 배포**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |관리자는 배포를 보호해야 합니다. |
| [**DNS 서버**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(관리되는 서비스) |**&#x2713;** |
| [**Domain or Enterprise administrator privileges**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**도메인 가입**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**NTLM 및 Kerberos를 사용하여 도메인 인증**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Kerberos 제한 위임**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|리소스 기반|리소스 기반 및 계정 기반|
| [**사용자 지정 OU 구조**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**스키마 확장**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**AD 도메인/포리스트 트러스트**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP read**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**보안 LDAP(LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP write**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Group Policy**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**지리적으로 분산된 배포**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>관리되는 서비스
Azure AD Domain Services 도메인은 Microsoft에서 관리합니다. 패치, 업데이트, 모니터링, 백업 그리고 도메인의 가용성 보장을 걱정할 필요가 없습니다. 이러한 관리 태스크는 Microsoft Azure에서 관리되는 도메인에 대해 서비스로 제공합니다.

#### <a name="secure-deployments"></a>보안 배포
관리되는 도메인은 AD 배포에 대한 Microsoft의 보안 권장 사항을 기준으로 안전하게 잠겨 있습니다. 이러한 권장 사항은 AD 배포 환경을 엔지니어링하고 지원하는 AD 제품 팀의 수십 년에 걸친 경험에서 비롯되었습니다. 직접 배포의 경우 배포를 잠그거나/보호하는 특정 배포 단계를 수행해야 합니다.

#### <a name="dns-server"></a>DNS 서버
Azure AD Domain Services 관리되는 도메인은 관리되는 DNS 서비스를 포함합니다. 'AAD DC 관리자' 그룹의 구성원은 관리되는 도메인에서 DNS를 관리할 수 있습니다. 이 그룹의 구성원에게는 관리되는 도메인에 대한 모든 DNS 관리 권한이 주어집니다. RSAT(원격 서버 관리 도구) 패키지에 포함된 'DNS 관리 콘솔'을 사용하여 DNS 관리를 수행할 수 있습니다.
[자세한 정보](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>도메인 또는 엔터프라이즈 관리자 권한
AAD DS 관리되는 도메인에서는 이러한 상승된 권한이 제공되지 않습니다. 관리자 권한을 요구하는 응용 프로그램은 AAD DS 관리되는 도메인에 대해 배포할 수 없습니다. 관리 권한의 작은 하위 집합은 'AAD DC 관리자'라는 위임된 관리 그룹의 구성원에 제공됩니다. 이러한 권한에는 DNS를 구성하고 그룹 정책을 구성하며 도메인 가입 컴퓨터에서 관리자 권한을 얻을 수 있는 권한 등이 포함됩니다.

#### <a name="domain-join"></a>도메인 가입
AD 도메인에 컴퓨터를 가입하는 방법과 비슷하게 가상 머신을 관리되는 도메인에 가입시킬 수 있습니다.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>NTLM 및 Kerberos를 사용하여 도메인 인증
Azure AD Domain Services에서 회사 자격 증명을 사용하여 관리되는 도메인으로 인증할 수 있습니다. 자격 증명은 Azure AD 테넌트와 동기화된 상태로 유지됩니다. 동기화된 테넌트의 경우 Azure AD Connect를 통해 온-프레미스를 만든 자격 증명의 변경 내용을 Azure AD로 동기화하도록 합니다. DIY(Do-It-Yourself) 도메인 설치를 사용하여 사용자가 해당 회사 자격 증명으로 인증하도록 온-프레미스 AD와 AD 도메인 트러스트 관계를 설정해야 할 수 있습니다. 또는 AD 복제를 설정하여 Azure 도메인 컨트롤러 가상 머신에 사용자 암호를 동기화해야 합니다.

#### <a name="kerberos-constrained-delegation"></a>Kerberos 제한 위임
Active Directory Domain Services 관리되는 도메인에서 '도메인 관리자' 권한이 없습니다. 따라서 계정 기반(기존의) Kerberos 제한 위임을 구성할 수 없습니다. 하지만 보다 안전한 리소스 기반 제한 위임은 구성할 수 있습니다.
[자세한 정보](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>사용자 지정 OU 구조
'AAD DC 관리자' 그룹의 구성원은 관리되는 도메인 내에서 사용자 지정 OU를 만들 수 있습니다. 사용자 지정 OU를 만든 사용자에게는 OU에 대한 모든 관리 권한이 부여됩니다.
[자세한 정보](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>스키마 확장
Azure AD Domain Services 관리되는 도메인의 기본 스키마를 확장할 수 없습니다. 따라서 AD 스키마에 대한 확장에 의존하는 응용 프로그램(예: 사용자 개체에서 새 특성)을 AAD-DS 도메인으로 전환할 수 없습니다.

#### <a name="ad-domain-or-forest-trusts"></a>AD 도메인 또는 포리스트 트러스트
관리되는 도메인을 구성하여 다른 도메인과 트러스트 관계(인바운드/아웃바운드)을 설정할 수 없습니다. 따라서 리소스 포리스트 배포 시나리오는 Azure AD Domain Services를 사용할 수 없습니다. 마찬가지로 Azure AD와 암호를 동기화하지 않으려는 배포에서는 Azure AD Domain Services를 사용할 수 없습니다.

#### <a name="ldap-read"></a>LDAP 읽기
관리되는 도메인은 LDAP 읽기 워크로드를 지원합니다. 따라서 관리되는 도메인에 대한 LDAP 읽기 작업을 수행하는 응용 프로그램을 배포할 수 있습니다.

#### <a name="secure-ldap"></a>보안 LDAP
Azure AD Domain Services를 구성하여 인터넷을 포함하여 관리되는 도메인에 대한 보안 LDAP 액세스를 제공할 수 있습니다.
[자세한 정보](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>LDAP 쓰기
관리되는 도메인은 사용자 개체에 대한 읽기 전용입니다. 따라서 사용자 개체의 특성에 대한 LDAP 쓰기 작업을 수행하는 응용 프로그램은 관리되는 도메인에서 작동하지 않습니다. 또한 사용자 암호는 관리되는 도메인 내에서 변경할 수 없습니다. 또 다른 예로는 관리되는 도메인 내에서 그룹 멤버 자격 또는 그룹 특성을 수정하는 작업이 있으며 이는 허용되지 않습니다. 그러나 (PowerShell/Azure Portal을 통해) Azure AD 또는 온-프레미스 AD에서 만든 사용자 특성 또는 암호를 변경하는 경우 AAD-DS 관리되는 도메인에 동기화됩니다.

#### <a name="group-policy"></a>그룹 정책
“AADDC 컴퓨터” 및 “AADDC 사용자” 컨테이너에 대한 기본 제공 GPO가 있습니다. 이러한 기본 제공 GPO를 사용자 지정하여 그룹 정책을 구성할 수 있습니다. 'AAD DC 관리자' 그룹의 구성원은 사용자 지정 GPO를 만들고 사용자 지정 OU를 포함한 기존의 OU에 연결할 수 있습니다.
[자세한 정보](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>지역 분산된 배포
Azure AD Domain Services 관리되는 도메인은 Azure의 단일 가상 네트워크에서 사용할 수 있습니다. 도메인 컨트롤러를 전 세계 여러 Azure 지역에서 사용할 수 있어야 하는 시나리오의 경우 Azure IaaS VM에서 도메인 컨트롤러를 설정하는 것이 적절한 대안이 될 수 있습니다.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>DIY('직접') AD 배포 옵션
Windows Server AD 설치에서 제공되는 기능 중 일부가 필요한 배포 사용 사례가 있을 수 있습니다. 이러한 경우에는 다음 DIY(직접) 옵션 중 하나를 고려해 보세요.

* **독립 실행형 클라우드 도메인:** 도메인 컨트롤러로 구성된 Azure 가상 머신을 사용하여 독립 실행형 '클라우드 도메인'을 설정할 수 있습니다. 이 인프라는 온-프레미스 AD 환경과 통합되지 않습니다. 이 옵션에서는 다른 '클라우드 자격 증명' 집합이 로그인하거나 클라우드에서 VM을 관리해야 합니다.
* **리소스 포리스트 배포:** 도메인 컨트롤러로 구성된 Azure 가상 머신을 사용하여 리소스 포리스트 토폴로지에서 도메인을 설정할 수 있습니다. 다음으로 온-프레미스 AD 환경으로 AD 트러스트 관계를 구성할 수 있습니다. 클라우드에서 이 리소스 포리스트에 컴퓨터(Azure VM)를 도메인 가입시킬 수 있습니다. 사용자 인증은 온-프레미스 디렉터리에 대한 VPN/ExpressRoute 연결 중 하나를 통해 발생합니다.
* **온-프레미스 도메인을 Azure로 확장:** VPN/ExpressRoute 연결을 사용하여 온-프레미스 네트워크에 Azure Virtual Network를 연결할 수 있습니다. 이렇게 설정하면 Azure VM이 온-프레미스 AD에 가입할 수 있습니다. 또 다른 방법은 Azure에서 온-프레미스 도메인의 복제 도메인 컨트롤러를 VM으로 승격하는 것입니다. 온-프레미스 디렉터리에 대한 VPN/ExpressRoute 연결을 통해 복제되도록 설정할 수 있습니다. 이 배포 모드는 Azure에 온-프레미스 도메인을 효과적으로 확장합니다.

> [!NOTE]
> 배포 사용 사례에 DIY 옵션이 더 적합한지 결정할 수 있습니다. [의견 공유](active-directory-ds-contact-us.md) 를 고려하여 나중에 Azure AD Domain Services를 선택하는 데 도움이 되는 기능을 이해할 수 있습니다. 이 피드백은 서비스를 배포 요구 사항 및 사용 사례에 적합하도록 발전시키는 데 도움이 됩니다.
>
>

[Azure Virtual Machines에 Windows Server Active Directory를 배포하기 위한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)을 게시하여 DIY 설치를 보다 쉽게 만들 수 있습니다.

## <a name="related-content"></a>관련 콘텐츠
* [기능 - Azure AD Domain Services](active-directory-ds-features.md)
* [배포 시나리오 - Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Azure Virtual Machines에 Windows Server Active Directory를 배포하기 위한 지침](https://msdn.microsoft.com/library/azure/jj156090.aspx)
