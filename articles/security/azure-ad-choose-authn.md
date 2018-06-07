---
title: Azure AD 하이브리드 ID 솔루션에 적합한 인증 방법 선택 | Microsoft Docs
description: 이 가이드는 CEO, CIO, CISO, CIA(Chief Identity Architects), 엔터프라이즈 설계자, 그리고 중간 규모부터 대규모 조직에서 Azure AD 하이브리드 ID 솔루션에 대한 인증 방법 선택을 책임지고 있는 보안 및 IT 의사 결정자를 지원하기 위한 것입니다.
services: active-directory
keywords: ''
author: martincoetzer
ms.author: martincoetzer
ms.date: 04/12/2018
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: eb5fb008e602c2026e57d3436875ec485b350af8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895523"
---
# <a name="choosing-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택 

이 문서는 조직에서 완전한 Azure AD 하이브리드 ID 솔루션을 구현하도록 지원하기 위한 일련의 문서 중 첫 번째 문서입니다. 완전한 Azure AD 하이브리드 ID 솔루션은 하이브리드 ID 디지털 변환 프레임워크로 간략하게 설명되었으며, 비즈니스 결과 및 조직이 강력하고 안전한 하이브리드 ID 솔루션을 구현했는지 집중적으로 확인해야 하는 목표를 포함합니다. 프레임워크의 첫 번째 비즈니스 결과는 사용자가 클라우드 앱에 액세스할 때 조직이 인증 프로세스를 보호하기 위한 요구 사항을 자세히 설명합니다. 인증으로 보호된 비즈니스 결과에서 첫 번째 비즈니스 목표는 사용자가 온-프레미스 사용자 이름 및 암호를 사용하여 클라우드 앱에 로그인할 수 있도록 하는 기능입니다. 이 로그인 프로세스 및 사용자가 인증하는 방법은 클라우드에서 모든 작업이 가능하도록 만듭니다.

올바른 인증 방법을 선택하는 것은 앱에서 클라우드로 이동하려는 조직이 첫 번째로 고려해야 할 사항입니다. 이 결정은 다음과 같은 이유로 가볍게 여겨지지 않아야 합니다.

1. 클라우드로 이동하려는 조직에서 내려야 하는 첫 번째 의사 결정입니다. 

2. 인증 방법은 모든 클라우드 데이터와 리소스에 대한 액세스를 제어하므로 클라우드에서 조직의 존재 여부에 매우 중요한 구성 요소입니다.

3. Azure AD에서 모든 다른 고급 보안 및 사용자 경험의 기초가 됩니다.

4. 한 번 구현한 후에는 인증 방법을 변경하기 어렵습니다.

인증은 IT 보안의 새 제어 평면으로 ID를 사용하여 새로운 클라우드 환경으로부터 조직의 액세스를 보호합니다. 조직에서는 ID 제어 평면이 보안을 강화하고 클라우드 앱을 침입자들로부터 안전하게 보호하도록 만들어야 합니다.

### <a name="out-of-scope"></a>범위 외

기존 온-프레미스 디렉터리 공간이 없는 조직은 이 문서의 대상이 아닙니다. 일반적으로 이러한 기업은 클라우드에서만 ID를 생성하므로 하이브리드 ID 솔루션이 필요하지 않습니다. 클라우드 전용 ID는 클라우드에서만 존재하며 해당 온-프레미스 ID와 연결되지 않습니다.  

## <a name="choosing-the-right-authentication-method"></a>올바른 인증 방법 선택

Azure AD 하이브리드 ID 솔루션을 새 제어 평면으로 사용하면 인증이 클라우드 액세스의 기초입니다. 따라서 올바른 인증 방법을 선택하는 것은 Azure AD 하이브리드 ID 솔루션 설정에서 매우 중요한 첫 번째 결정입니다. 인증 방법 구현은 클라우드에서 사용자를 프로비전하는 Azure AD Connect를 사용하여 구성됩니다. 

인증 방법을 선택하려면 시간, 기존 인프라, 복잡성 및 선택을 구현하는 데 드는 비용을 고려해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 

Azure AD에서는 하이브리드 ID 솔루션에 대해 다음과 같은 인증 방법을 지원합니다.

### <a name="cloud-authentication"></a>클라우드 인증
이 인증 방법을 선택하면 Azure AD가 사용자에 대한 로그인 프로세스를 처리합니다. 원활한 SSO(Single Sign-On)와 결합되어 있으므로, 사용자는 자격 증명을 다시 입력하지 않고도 클라우드 앱에 로그인할 수 있습니다. 클라우드 인증을 사용할 경우 다음 두 가지 옵션 중에서 선택할 수 있습니다. 

**PHS(암호 해시 동기화)** – Azure AD에서 온-프레미스 디렉터리 개체에 대한 인증을 사용하는 가장 간단한 방법입니다. 암호 해시 동기화를 사용하면 추가 인프라를 배포하지 않고도 사용자가 온-프레미스에서 사용하는 것과 동일한 사용자 이름과 암호를 사용할 수 있습니다. ID 보호와 같은 Azure AD의 일부 프리미엄 기능에는 선택한 인증 방법에 관계없이 암호 해시 동기화가 필요합니다.

> [!NOTE] 
> 암호는 일반 텍스트로 저장되거나 Azure AD에서 복원 가능한 알고리즘을 사용하여 암호화되지 않습니다. 암호 해시 동기화의 실제 프로세스에 대한 자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)을 참조하세요. 

**PTA(통과 인증)** – 하나 이상의 온-프레미스 서버에서 실행되는 소프트웨어 에이전트를 사용하여 Azure AD 인증 서비스에 대한 간단한 암호 유효성 검사를 제공합니다. 온-프레미스 Active Directory에서 직접 사용자를 검증하므로 클라우드에서는 암호 유효성 검사가 수행되지 않습니다. 온-프레미스 사용자 계정 상태, 암호 정책 및 로그온 시간을 즉시 적용해야 하는 보안 요구 사항이 있는 회사에서 이 인증 방법을 사용합니다. 실제 통과 인증 프로세스에 대한 자세한 내용은 [Azure Active Directory 통과 인증으로 사용자 로그인](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)을 참조하세요.

### <a name="federated-authentication"></a>페더레이션 인증
이 인증 방법을 선택하면 Azure AD에서 인증 프로세스를 온-프레미스 AD FS(Active Directory Federation Services) 등과 같은 별도의 신뢰할 수 있는 인증 시스템에 넘겨서 사용자 암호의 유효성을 검사합니다. 인증 시스템에서 스마트 카드 기반 인증 또는 타사 다단계 인증 등의 추가 인증 요구 사항을 제공할 수 있습니다. 자세한 내용은 [Active Directory Federation Services 배포](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)를 참조하세요.

다음 섹션은 의사 결정 트리를 사용하여 적합한 인증 방법을 결정하는 데 도움이 됩니다. Azure AD 하이브리드 ID 솔루션에 대해 클라우드 인증을 배포할지 또는 페더레이션 인증을 배포할지 결정하도록 지원합니다.

## <a name="azure-ad-authentication-decision-tree"></a>Azure AD 인증 의사 결정 트리

![image1](media/azure-ad/azure-ad-authn-image1.png)

## <a name="detailed-considerations-on-authentication-methods"></a>인증 방법에 대한 자세한 고려 사항

### <a name="cloud-authentication-password-hash-sync"></a>클라우드 인증: 암호 해시 동기화 

* **활동:** 암호 해시 동기화는 사용자가 Office 365, SaaS 앱 및 기타 Azure AD 기반 리소스에 로그인만 할 수 있도록 해야 하는 조직에서 배포, 유지 관리 및 인프라와 관련한 최소한의 활동이 필요합니다. 사용하도록 설정한 경우, 암호 해시 동기화는 Azure AD Connect 동기화 프로세스의 일부로 2분마다 실행됩니다. 

* **사용자 환경:** 조직에서는 암호 해시 동기화를 사용하는 원활한 SSO(Single Sign-On)를 배포하여 사용자가 로그인한 후 불필요한 프롬프트를 방지함으로써 사용자의 로그인 환경을 개선하는 것이 좋습니다.

* **고급 시나리오:** 조직에서 선택하는 경우, 유출된 자격 증명 보고서 등의 Azure AD ID 보호 보고서에서 ID의 정보를 사용할 수 있습니다. 비즈니스용 Windows Hello는 [암호 해시 동기화를 사용할 때 특정 요구 사항](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)이 있는 또 다른 옵션입니다. 암호 해시 동기화와 함께 다단계 인증을 필요로 하는 조직은 Azure AD 다단계 인증을 사용해야 하며 타사 또는 온-프레미스 다단계 인증 방법은 사용할 수 없습니다.

* **비즈니스 연속성:** 암호 해시 동기화는 기본적으로 모든 Microsoft 데이터 센터로 확장 가능한 클라우드 서비스만큼 가용성이 높습니다. 재해 복구를 위해 보조 Azure AD Connect 서버를 대기 구성으로 준비 모드에서 배포하는 것이 좋습니다.

* **고려할 사항:** 암호 해시 동기화는 현재 온-프레미스 계정 상태에서 변경 내용을 즉시 적용하지 않습니다. 이런 경우 사용자는 사용자 계정 상태가 Azure AD와 동기화될 때까지 클라우드 앱에 대한 액세스 권한을 갖게 됩니다. 조직에서 이 제한을 해결하려면 관리자가 계정을 사용하지 않도록 설정하는 등과 같이 온-프레미스 사용자 계정 상태에 대한 대량 업데이트를 수행한 후 새 동기화 주기를 활성화하는 것이 좋습니다. 

> [!NOTE] 
> 암호 만료 및 계정 잠김 상태는 현재, Azure AD Connect를 사용하여 Azure AD에 동기화되지 않습니다. 

배포 단계에 대해서는 [암호 해시 동기화 구현](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization)을 참조하세요.

### <a name="cloud-authentication-pass-through-authentication"></a>클라우드 인증: 통과 인증  

* **활동:** 통과 인증에서는 온-프레미스 AD 도메인 컨트롤러를 포함하여, 온-프레미스 Active Directory Domain Services에 대한 액세스 권한이 있는 하나 이상(3개 권장)의 경량 에이전트가 기존 서버에 설치되어 있어야 합니다. 이러한 에이전트는 인터넷에 대한 아웃바운드 액세스가 필요하며 사용자 도메인 컨트롤러에 액세스할 수 있습니다. 이런 이유로 도메인 컨트롤러에 대한 무제한 네트워크 액세스가 필요하기 때문에 경계 네트워크에 에이전트를 배포하도록 지원되지 않습니다. 모든 네트워크 트래픽은 암호화되고 인증 요청을 제한합니다. 이 프로세스에 대한 자세한 내용은 통과 인증에 대한 [보안 심층 분석](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-security-deep-dive)을 참조하세요.

* **사용자 환경**: 조직에서는 통과 인증을 사용하는 원활한 SSO(Single Sign-On)를 배포하여 사용자가 로그인한 후 불필요한 프롬프트를 방지함으로써 사용자의 로그인 환경을 개선하는 것이 좋습니다.

* **고급 시나리오**: 로그인 시 통과 인증이 온-프레미스 계정 정책을 적용합니다. 예를 들어, 온-프레미스 사용자의 계정이 사용 안 함, 잠김, [암호 만료됨](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication ) 또는 사용자의 허용된 로그온 시간을 벗어남 상태인 경우 액세스가 거부됩니다.  통과 인증과 함께 다단계 인증을 요구하는 조직은 Azure MFA(Multi-Factor Authentication)를 사용해야 하며, 타사 또는 온-프레미스 다단계 인증 방법을 사용할 수 없습니다. ID 보호의 유출된 자격 증명 보고서 등과 같은 고급 기능을 사용하려면 통과 인증 선택 여부에 관계없이 암호 해시 동기화를 배포해야 합니다.

* **비즈니스 연속성**: 인증 요청의 높은 가용성을 보장하기 위해 Azure AD Connect 서버의 첫 번째 에이전트 외에도 두 개의 추가적인 통과 인증 에이전트를 배포하는 것이 좋습니다. 세 개의 에이전트를 배포하면 유지 관리를 위해 에이전트 하나가 중지된 경우에도 다른 에이전트가 실패해도 괜찮습니다. 통과 인증에 더하여 암호 해시 동기화를 배포했을 때의 또 다른 이점은 온-프레미스 서버를 사용할 수 없는 경우 등과 같이 더 이상 기본 인증 방법을 사용할 수 없을 때 백업 인증 방법으로 작동할 수 있다는 것입니다.

* **고려할 사항:** 암호 해시 동기화를 통과 인증에 대한 백업 인증 방법으로 사용하는 경우 에이전트에서 사용자 자격 증명의 유효성을 검사할 수 없으면 암호 해시 동기화에 대한 장애 조치(failover)가 자동으로 수행되지 않습니다. Azure AD Connect를 사용하여 수동으로 로그인 방법을 전환해야 합니다. 통과 인증에서는 최신 인증을 사용하거나 ActiveSync, POP3 및 IMAP4와 같은 특정 Exchange Online 프로토콜을 사용하는 클라우드 앱만 지원합니다. 예를 들어 [Microsoft Office 2013 이상에서는 최신 인증을 지원하지만 이전 버전은 지원하지 않습니다](https://blogs.office.com/en-us/2015/11/19/updated-office-365-modern-authentication-public-preview/)(자세한 내용은 Office 앱 지원 참조). 대체 ID 지원을 포함하여 통과 인증에 대한 [질문과 대답](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq) 및 기타 고려 사항을 참조하세요.

배포 단계에 대해서는 [통과 인증 구현](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication)을 참조하세요.

### <a name="federated-authentication"></a>페더레이션 인증

* **활동:** 페더레이션 인증을 사용하는 시스템은 외부 시스템을 통해 사용자를 인증합니다. 일부 기업은 Azure AD 하이브리드 ID 솔루션과 함께 자사의 기존 페더레이션 시스템 투자를 재사용하려고 합니다. 페더레이션 시스템의 유지 및 관리는 Azure AD의 제어를 벗어납니다. 페더레이션 시스템이 안전하게 배포되어 인증 부하를 처리할 수 있도록 하는 것은 해당 페더레이션 시스템을 사용하는 조직의 책임입니다. 

* **사용자 환경:** 페더레이션 인증의 사용자 환경은 기능, 토폴로지 및 페더레이션 팜 구성의 구현 방식에 따라 달라집니다. 일부 조직에서는 페더레이션 팜에 대한 액세스를 보안 요구 사항에 맞춰 적용 및 구성할 수 있는 유연성이 필요합니다. 예를 들어 자격 증명을 확인하지 않고 사용자가 자동으로 로그인되도록 내부적으로 연결된 사용자 및 장치를 구성할 수 있는데, 이런 경우 해당 사용자는 이미 장치에 로그인되었기 때문입니다. 한편, 필요한 경우 일부 고급 보안 기능에서는 사용자의 로그인 프로세스를 더 어렵게 만들 수 있습니다.

* **고급 시나리오:** 페더레이션 인증 솔루션은 일반적으로 고객에게 Azure AD에서 기본적으로 지원되지 않는 인증 요구 사항이 있는 경우 필요합니다. 자세한 정보는 [여기에 나열](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)되어 있지만, 다음과 같은 일반적인 요구 사항이 포함됩니다.

    * 스마트 카드 또는 인증서를 요구하는 인증
    * 온-프레미스 MFA 서버 또는 타사 다단계 인증 공급자 사용
    * 타사 인증 솔루션을 사용하는 인증 [Azure AD 페더레이션 호환성 목록](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)을 참조하세요.
    * 사용자는 user@domain.com 등의 UPN(사용자 계정 이름)을 사용하는 대신 DOMAIN\username과 같은 자신의 sAMAccountName을 사용하여 로그인해야 합니다.

* **비즈니스 연속성:** 페더레이션 시스템에는 일반적으로 인증 요청에 대한 고가용성을 보장하기 위해 내부 네트워크와 경계 네트워크 토폴로지에 구성된 부하 분산된 서버 배열(팜이라고도 함)이 필요합니다. 암호 해시 동기화는 온-프레미스 서버를 사용할 수 없는 경우 등과 같이 더 이상 기본 인증 방법을 사용할 수 없을 때 백업 인증 방법으로 페더레이션 인증과 함께 배포할 수 있습니다. 일부 대기업 조직에서는 대기 시간이 짧은 인증 요청에 대한 지리적 DNS를 사용하여 구성된 여러 인터넷 수신 지점을 지원하기 위해 페더레이션 솔루션이 필요합니다.

* **고려할 사항:** 페더레이션 시스템에는 일반적으로 온-프레미스 인프라에 더 많은 투자를 해야 합니다. 대부분의 조직은 온-프레미스 페더레이션 투자가 이미 되어 있고 단일 ID 공급자를 사용해야 하는 강력한 비즈니스 요구 사항이 있는 경우에 이 옵션을 선택합니다. 페더레이션은 클라우드 인증 솔루션에 비해 작동 및 문제 해결이 더 복잡합니다. Azure AD에서 확인할 수 없는 라우팅 불가능한 도메인에서 사용자 ID를 사용하여 로그인하려면 추가 구성을 구현해야 합니다. 이 요구 사항을 대체 로그인 ID 지원이라고 합니다. 제한 사항 및 요구 사항에 대해서는 [대체 로그인 ID 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)을 참조하세요. 페더레이션에 타사 다단계 인증 공급자를 사용하도록 선택하는 경우, 장치에서 Azure AD에 가입할 수 있도록 공급자가 WS-Trust를 지원하는지 확인합니다.

배포 단계에 대해서는 [페더레이션 서비스 구현](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)을 참조하세요.

> [!NOTE] 
> Azure AD 하이브리드 ID 솔루션을 배포하는 경우 Azure AD Connect에서 지원되는 토폴로지 중 하나를 구현해야 합니다. [Azure AD Connect에 대한 토폴로지](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-topologies)에서 지원되는 구성 및 지원되지 않는 구성을 알아보세요.

## <a name="architecture-diagrams"></a>아키텍처 다이어그램

다음 다이어그램에서는 Azure AD 하이브리드 ID 솔루션에서 사용할 수 있는 각 인증 방법에 대해 필요한 상위 수준 아키텍처 구성 요소를 간략하게 설명합니다. 솔루션 간의 차이점을 비교하는 개요를 제공합니다.

다음 다이어그램에서는 암호 해시 동기화 솔루션의 단순성을 간략하게 설명합니다.

![PHS](media/azure-ad/azure-ad-authn-image2.png)

다음 다이어그램에서는 통과 인증의 에이전트 요구 사항을 간략하게 설명합니다.

![PTA](media/azure-ad/azure-ad-authn-image3.png)

다음 다이어그램에서는 조직의 경계 및 내부 네트워크에서 페더레이션에 필요한 구성 요소를 간략하게 설명합니다.

![ADFS](media/azure-ad/azure-ad-authn-image4.png)

## <a name="comparing-the-authentication-methods"></a>인증 방법 비교

|고려 사항|암호 해시 동기화 + 원활한 SSO|통과 인증 + 원활한 SSO|AD FS로 페더레이션|
|:-----|:-----|:-----|:-----|
|인증은 어디서 수행되나요?|클라우드|클라우드에서 온-프레미스 인증 에이전트와 보안 암호 확인을 교환한 후|온-프레미스|
|프로비저닝 시스템(Azure AD Connect) 이외의 온-프레미스 서버 요구 사항은 무엇인가요?|없음|각 추가 인증 에이전트마다 서버 1개|둘 이상의 AD FS 서버<br>경계/DMZ 네트워크에 둘 이상의 WAP 서버|
|프로비저닝 시스템 이외의 온-프레미스 인터넷 및 네트워킹 요구 사항은 무엇인가요?|없음|인증 에이전트를 실행하는 서버의[아웃바운드 인터넷 액세스](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start)|경계에 있는 WAP 서버에 대한 [인바운드 인터넷 액세스](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br>경계에 있는 WAP 서버에서 AD FS 서버로의 인바운드 네트워크 액세스<br>네트워크 부하 분산|
|SSL 인증서 요구 사항이 있나요?|아니오|아니요|예|
|상태 모니터링 솔루션이 있나요?|필요하지 않음|[Azure Active Directory 관리 센터](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)에서 제공한 에이전트 상태|[Azure AD Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)|
|사용자가 회사 네트워크 내의 도메인 가입 장치에서 Single Sign-On 방식으로 클라우드 리소스에 액세스할 수 있나요?|[원활한 SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)의 경우 예|[원활한 SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)의 경우 예|예|
|지원되는 로그인 유형은 무엇인가요?|UserPrincipalName + 암호<br>[원활한 SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)를 사용하는 Windows 통합 인증<br>[대체 로그인 ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-get-started-custom)|UserPrincipalName + 암호<br>[원활한 SSO](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-sso)를 사용하는 Windows 통합 인증<br>[대체 로그인 ID](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-faq)|UserPrincipalName + 암호<br>sAMAccountName + 암호<br>Windows 통합 인증<br>[인증서 및 스마트 카드 인증](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br>[대체 로그인 ID](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|비즈니스용 Windows Hello가 지원되나요?|[키 신뢰 모델](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Intune을 사용하는 인증서 신뢰 모델](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[키 신뢰 모델](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[Intune을 사용하는 인증서 신뢰 모델](https://blogs.technet.microsoft.com/microscott/setting-up-windows-hello-for-business-with-intune/)|[키 신뢰 모델](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>[인증서 신뢰 모델](https://docs.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|다단계 인증 옵션은 무엇인가요?|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)|[Azure MFA](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/)<br>[Azure MFA 서버](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfaserver-deploy)<br>[타사 MFA](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)|
|지원되는 사용자 계정 상태는 무엇인가요?|비활성화된 계정<br>(최대 30분 지연)|비활성화된 계정<br>계정 잠김<br>암호 만료됨<br>로그온 시간|비활성화된 계정<br>계정 잠김<br>암호 만료됨<br>로그온 시간|
|조건부 액세스 옵션이란 무엇인가요?|[Azure AD 조건부 액세스](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 조건부 액세스](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)|[Azure AD 조건부 액세스](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)<br>[AD FS 클레임 규칙](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|레거시 프로토콜 차단이 지원되나요?|아니오|아니오|[예](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|로그인 페이지에서 로고, 이미지 및 설명을 사용자 지정할 수 있나요?|[Azure AD Premium의 경우 예](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[Azure AD Premium의 경우 예](https://docs.microsoft.com/en-us/azure/active-directory/customize-branding)|[예](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-management#customlogo)|
|지원되는 고급 시나리오는 무엇인가요?|[스마트 암호 잠금](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-secure-passwords)<br>[유출된 자격 증명 보고서](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-risk-events)|[스마트 암호 잠금](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)|다중 사이트 낮은 대기 시간 인증 시스템<br>[AD FS 엑스트라넷 잠금](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)<br>[타사 ID 시스템과 통합](https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-federation-compatibility)|

## <a name="recommendations-and-considerations-from-azure-ad"></a>Azure AD에서 권장 사항 및 고려할 사항

ID 시스템을 통해 사용자는 마이그레이션하여 클라우드에서 사용할 수 있도록 만든 클라우드 앱 및 기간 업무 앱에 대한 액세스 권한을 가질 수 있습니다. 인증은 권한이 있는 사용자가 생산성을 유지하고 믿을 수 없는 행위자가 조직의 중요한 데이터에 접근하지 못하도록 앱에 대한 액세스 권한을 제어합니다. 이러한 이유로 조직에 맞는 인증 방법을 선택할 때는 다음 권장 사항을 고려해야 합니다. 

다음과 같은 이유에 대해서는 선택한 인증 방법에 관계없이 암호 해시 동기화를 사용하거나 사용하도록 설정합니다.

1. **고가용성 및 재해 복구:** 통과 인증 및 페더레이션이 온-프레미스 인프라에 의존합니다. 통과 인증의 경우, 통과 인증 에이전트에 필요한 서버 하드웨어 및 네트워크가 온-프레미스 공간에 포함됩니다. 페더레이션의 경우, 경계 네트워크의 서버에서 인증 요청 및 내부 페더레이션 서버를 프록시해야 하므로 온-프레미스 공간이 훨씬 커집니다. 단일 지점에서 실패를 방지하기 위해 조직은 중복 서버를 배포하여 어떤 구성 요소가 실패하는 경우에도 인증 요청이 항상 처리되도록 해야 합니다. 또한 통과 인증과 페더레이션 둘 다에서 도메인 컨트롤러를 사용하여 인증 요청에 응답하며, 실패할 수도 있습니다. 이러한 많은 구성 요소를 정상적인 상태로 유지하려면 유지 관리가 필요하며, 유지 관리가 제대로 플랜 및 구현되지 않은 경우 중단이 발생할 가능성이 커집니다. Microsoft Azure AD 클라우드 인증 서비스가 전 세계로 확장되어 있으며 항상 사용할 수 있기 때문에 암호 해시 동기화를 사용하면 중단을 방지할 수 있습니다.

2. **온-프레미스 중단에서 생존:** 사이버 공격이나 재해로 인한 온-프레미스 중단은 브랜드 이미지 손상부터 해당 공격을 처리할 수 없을 정도의 조직 마비에 이르기까지 막대한 영향을 줄 수 있습니다. 지난 해에는 대부분의 조직이 표적형 랜섬웨어를 포함하여 온-프레미스 서버를 중단시키는 맬웨어 공격으로 피해를 입었습니다. 고객이 이러한 종류의 공격을 처리할 수 있도록 지원하면서 Microsoft는 두 가지 범주의 조직이 있음을 알았습니다.

   a. 이전에 암호 해시 동기화를 사용하도록 설정했던 조직은 암호 해시 동기화를 사용하도록 인증 방법을 변경하여 몇 시간 만에 다시 온라인 상태가 되었습니다. Office 365를 통한 이메일 액세스를 사용하여 문제를 해결하기 위해 작업하고 다른 클라우드 기반 워크로드에 액세스할 수 있었습니다.

   나. 이전에 암호 해시 동기화를 사용하지 않았던 조직은 통신 및 문제 해결을 위해 신뢰할 수 없는 외부 소비자 이메일 시스템에 의존해야 했습니다. 이 경우 다시 가동 및 실행되는 데 몇 주 이상 걸렸습니다.

3. **ID 보호:** Azure AD Identity Protection은 클라우드에서 사용자를 보호하는 가장 좋은 방법 중 하나입니다. Microsoft는 악의적인 행위자가 판매하고 다크 웹에서 사용할 수 있도록 만드는 사용자 및 암호 목록에 대해 인터넷을 지속적으로 검사합니다. Azure AD는 이 정보를 사용하여 사용자 조직의 사용자 이름 및 암호가 훼손되었는지 확인할 수 있습니다. 따라서 사용하는 인증 방법이 페더레이션 인증이든 또는 통과 인증이든 관계없이 암호 해시 동기화를 사용하도록 설정하는 것이 매우 중요합니다. 유출된 자격 증명은 보고서로 제공되므로 사용자가 유출된 암호로 로그인하려고 할 때 이를 사용하여 해당 사용자를 차단하거나 유출된 암호를 변경하도록 할 수 있습니다.

마지막으로 [Gartner](https://info.microsoft.com/landingIAMGartnerreportregistration.html)에 따르면, Microsoft는 가장 완전하게 갖추어진 ID 및 액세스 관리 기능을 제공합니다. Microsoft는 거의 모든 장치에서 Office 365와 같은 수천 개의 SaaS 응용 프로그램에 액세스할 수 있도록 매달 [4500억 개의 인증 요청](https://www.microsoft.com/en-us/security/intelligence-report)을 처리합니다. 

## <a name="conclusion"></a>결론

이 문서에서는 조직에서 클라우드 앱에 대한 액세스를 지원하기 위해 구성하여 배포할 수 있는 다양한 인증 옵션을 설명합니다. 다양한 비즈니스, 보안 및 기술적 요구 사항을 충족하기 위해 조직은 암호 해시 동기화, 통과 인증 및 페더레이션 중에서 선택할 수 있습니다. 각 인증 방법에서 조직은 비즈니스 요구 사항이 솔루션 배포 활동 및 로그인 프로세스의 사용자 환경에 의해 해결될지 여부를 선택할 수 있습니다. 또한 조직에 각 인증 방법의 고급 시나리오 및 비즈니스 연속성 기능이 필요한지 여부도 평가해야 합니다. 마지막으로, 선택한 방법을 구현하지 못하도록 하는 요소가 있는지 알아보기 위해 각 인증 방법에서 고려할 사항을 평가해야 합니다.

## <a name="next-steps"></a>다음 단계

오늘날의 세계에는 하루 24시간 어디에나 위협이 존재합니다. 올바른 인증 방법을 구현하면 보안 위험을 완화하고 사용자의 ID를 보호하는 데 도움이 됩니다. 

Azure AD로 [시작](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad)하여 조직에 맞는 올바른 인증 솔루션을 배포하세요.

페더레이션 인증에서 클라우드 인증으로 마이그레이션를 고려하는 경우 [로그인 방법 변경](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-user-signin#changing-the-user-sign-in-method)에 대해 자세히 알아보세요. 마이그레이션을 계획하고 구현하는 데 도움이 되도록 [이러한 프로젝트 계획을 사용하여 사용자를 지원](http://aka.ms/deploymentplans)할 수 있습니다.
