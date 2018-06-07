---
title: Azure Multi-Factor Authentication에 대한 보안 지침
description: 이 문서에서는 Azure 계정으로 Azure MFA를 사용하는 지침을 제공합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/15/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 76e622f500ee954852d7b42268318d35bb2133a4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868801"
---
# <a name="security-guidance-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Azure AD 계정으로 Azure Multi-Factor Authentication을 사용하기 위한 보안 지침

인증 프로세스를 향상시키려는 대부분의 조직은 2단계 인증을 기본으로 선택합니다. Azure MFA(Multi-Factor Authentication)를 사용하면 회사에서 자체의 보안 및 준수 요구사항을 충족하면서도 사용자에게 단순한 로그인 경험을 제공할 수 있습니다. 이 문서에서는 Azure MFA를 채택하기 위한 계획을 할 때 고려해야 할 몇 가지 팁을 설명합니다.

## <a name="deploy-azure-mfa-in-the-cloud"></a>클라우드에서 Azure MFA 배포

[모든 사용자에 대해 Azure MFA를 사용하도록 설정](howto-mfa-getstarted.md)하는 방법에는 두 가지가 있습니다.

* 각 사용자에 대해 라이선스 구입(Azure MFA, Azure AD Premium 또는 Enterprise Mobility + Security)
* Multi-Factor Auth 공급자 및 사용자 기준 또는 인증 기준 요금제 만들기

### <a name="licenses"></a>라이선스
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Azure AD Premium 또는 Enterprise Mobility + Security 라이선스가 있는 경우 Azure MFA가 이미 있는 것입니다. 2단계 인증 기능을 조직의 모든 사용자에게 확장하기 위해 추가로 아무 것도 필요하지 않습니다. 사용자에게 라이선스를 할당해야 하며 그러면 MFA를 사용할 수 있습니다.

Multi-factor Authentication을 설정할 때 고려해야 할 사항은 다음과 같습니다.

* 인증 기준 Multi-Factor Auth 공급자를 만들지 않도록 합니다. 이렇게 하면 이미 라이선스가 있는 사용자로부터 지불 확인 요청이 발생할 수 있습니다.
* 모든 사용자를 위한 충분한 라이선스가 없으면 조직의 나머지 사용자를 위해 사용자 기준 Multi-Factor Auth 공급자를 만들 수 있습니다. 
* Azure AD Connect는 온-프레미스 Active Directory 환경을 Azure AD 디렉터리와 동기화하는 경우에만 필요합니다. Active Directory의 온-프레미스 인스턴스와 동기화되지 않은 Azure AD 디렉터리를 사용하는 경우에는 Azure AD Connect가 필요하지 않습니다.

### <a name="multi-factor-auth-provider"></a>Multi-Factor Auth 공급자
![Multi-Factor Auth 공급자](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Azure MFA를 포함하는 라이선스가 없는 경우 [MFA Auth 공급자를 만들](concept-mfa-authprovider.md) 수 있습니다.

Auth 공급자를 만드는 경우 디렉터리를 선택하고 다음 세부 사항을 고려해야 합니다.

* Multi-Factor Auth 공급자를 만드는 데 Azure AD 디렉터리가 필요하지는 않지만 더 많은 기능을 사용할 수 있습니다. Azure AD 디렉터리와 Auth 공급자를 연결하면 다음과 같은 기능을 사용할 수 있게 됩니다.
  * 2단계 인증을 모든 사용자에게 확장합니다.
  * 관리 포털, 사용자 지정 인사말, 보고서 등의 글로벌 관리자 추가 기능을 제공합니다.
* 온-프레미스 Active Directory 환경을 Azure AD 디렉터리와 동기화하는 경우 DirSync 또는 AAD Sync가 필요합니다. Active Directory의 온-프레미스 인스턴스와 동기화되지 않은 Azure AD 디렉터리를 사용하는 경우에는 DirSync 또는 AAD 동기화가 필요하지 않습니다.
* 귀사에 가장 적합한 소비 모델을 선택 니다. 사용 모델을 선택한 후에는 변경할 수 없습니다. 두 모델은 다음과 같습니다.
  * 인증 기준: 각 인증에 대한 요금을 청구합니다. 특정 사용자가 아닌 특정 앱에 액세스하는 사용자에 대한 2단계 인증을 하려는 경우 이 모델을 사용합니다.
  * 활성화된 사용자 당: Azure MFA에 대해 사용하도록 설정한 각 사용자에 대한 요금을 청구합니다. 일부 사용자가 Azure AD Premium 또는 Enterprise Mobility Suite 라이선스를 갖고 일부 사용자가 라이선스를 갖지 않은 경우 이 모델을 사용합니다.

### <a name="supportability"></a>지원 가능성
사용자의 대부분은 암호만 사용하여 인증하는 데 익숙하므로 회사가 이 프로세스에 관하여 모든 사용자에게 인식하게 하는 것이 중요합니다. 이렇게 인식하면 사용자가 MFA와 관련된 경미한 문제에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다. 그러나 MFA를 일시적으로 비활성화가 필요한 시나리오도 있습니다. 그러한 시나리오를 처리하는 방법에 대해 이해하려면 다음 지침을 따릅니다.

* 모바일 앱 또는 전화에서 알림 또는 전화 통화를 받지 않았으므로 사용자가 로그인하지 못하는 시나리오를 처리할 수 있도록 기술 지원 요원을 교육합니다. 기술 지원은 사용자가 2단계 인증을 "바이패스"하여 1회 인증할 수 있도록 [일회성 바이패스](howto-mfa-mfasettings.md#one-time-bypass) 옵션을 사용하도록 설정할 수 있습니다. 바이패스는 일시적이며 지정된 시간(초) 이후 만료됩니다.
* Azure MFA의 [신뢰할 수 있는 IP 기능](howto-mfa-mfasettings.md#trusted-ips)은 2단계 인증을 최소화하는 방법으로 사용할 수 있습니다. 이 기능을 사용하여 관리되는 또는 페더레이션된 테넌트의 관리자가 회사의 로컬 인트라넷에서 로그인하는 사용자를 위해 2단계 인증을 바이패스할 수 있습니다. 기능은 Azure AD Premium, Enterprise Mobility Suite 또는 Azure Multi-Factor Authentication 라이선스가 있는 Azure AD 테넌트에서 사용할 수 있습니다.

## <a name="best-practices-for-an-on-premises-deployment"></a>온-프레미스 배포에 대한 모범 사례
회사가 자체 인프라를 이용하여 MFA를 사용하도록 결정한 경우 [Azure Multi-Factor Authentication 서버 온-프레미스를 배포](howto-mfaserver-deploy.md)해야 합니다. MFA 서버 구성 요소는 다음 다이어그램에 나와 있습니다.

![기본 MFA 서버 구성 요소: 콘솔, 동기화 엔진, 관리 포털, 클라우드 서비스](./media/multi-factor-authentication-security-best-practices/server.png) \*기본적으로 설치되지 않음\** 설치되지만 기본적으로 사용되지 않도록 설정됨

Azure Multi-Factor Authentication Server는 페더레이션을 사용하여 클라우드 리소스 및 온-프레미스 리소스의 보안을 유지할 수 있습니다. AD FS를 보유하고 이를 Azure AD 테넌트와 페더레이션해야 합니다.
Multi-Factor Authentication 서버를 설정할 때 고려해야 할 세부 사항은 다음과 같습니다.

* AD FS(Active Directory Federation Services)를 사용하여 Azure AD 리소스를 보호하는 경우 첫 번째 확인 단계는 AD FS를 사용하여 온-프레미스로 수행합니다. 두 번째 단계는 클레임을 적용하여 온-프레미스에서 수행됩니다.
* AD FS 페더레이션 서버에 Azure Multi-Factor Authentication 서버를 설치하지 않아도 됩니다. 그러나 AD FS를 실행하는 Windows Server 2012 R2에서 AD FS용 Multi-Factor Authentication 어댑터를 설치해야 합니다. 지원되는 버전이기만 하면 다른 컴퓨터에 서버를 설치할 수 있으며, AD FS 어댑터를 AD FS 페더레이션 서버에 따로 설치할 수 있습니다. 
* Multi-Factor Authentication AD FS 어댑터 설치 마법사는 Active Directory에서 PhoneFactor Admins이라고 하는 보안 그룹을 만든 다음 AD FS 서비스 계정을 이 그룹에 추가합니다. PhoneFactor Admins 그룹이 도메인 컨트롤러에 생성되고 AD FS 서비스 계정이 이 그룹의 멤버인지 확인합니다. 필요한 경우 도메인 컨트롤러의 PhoneFactor Admins 그룹에 AD FS 서비스 계정을 수동으로 추가합니다.

### <a name="user-portal"></a>사용자 포털
사용자 포털을 통해 셀프 서비스 기능을 허용하고 사용자 관리 기능의 전체 집합을 제공합니다. IIS(인터넷 정보 서버) 웹 사이트에서 실행됩니다. 이 구성 요소를 구성하려면 다음 지침을 따르세요.

* IIS 6 이상 사용
* ASP.NET v2.0.507207 설치 및 등록
* 이 서버를 경계 네트워크에 배포할 수 있는지 확인합니다.

### <a name="app-passwords"></a>앱 암호
조직이 Azure AD를 사용하여 SSO에 대해 페더레이션되고 Azure MFA를 사용할 계획인 경우 다음 사항에 유의하세요.

* 이 앱 암호는 Azure AD에서 확인되므로 페더레이션을 바이패스합니다. 앱 암호를 설정할 때에만 페더레이션이 사용됩니다.
* 페더레이션된(SSO) 사용자의 경우 암호는 조직 ID에 저장됩니다. 사용자가 회사를 떠나는 경우 해당 정보는 DirSync를 사용하는 조직 ID에 유입되어야 합니다. 계정 사용 안 함/삭제 설정은 동기화에 최대 3시간이 걸리며 Azure AD에서 앱 암호의 사용 안 함/삭제가 지연됩니다.
* 앱 암호를 사용할 경우 온-프레미스 클라이언트 Access Control 설정은 적용되지 않습니다.
* 온-프레미스 인증 로깅/감사 기능은 앱 암호에 사용할 수 없습니다
* 특정 고급 아키텍처 디자인은 클라이언트와 2단계 인증을 사용하는 경우 인증 위치에 따라 조직의 사용자 이름과 암호 및 앱 암호를 조합하여 사용할 필요가 있습니다. 온-프레미스 인프라에 대해 인증하는 클라이언트의 경우 조직의 사용자 이름과 암호를 사용합니다. Azure AD에 대해 인증하는 클라이언트의 경우 앱 암호를 사용합니다.
* 기본적으로 사용자가 앱 암호를 만들 수 없습니다. 사용자가 앱 암호를 만들 수 있도록 해야 할 경우 **사용자가 브라우저에 기반하지 않는 응용 프로그램에 로그인하기 위해 앱 암호를 만들 수 있음** 옵션을 선택합니다.

## <a name="additional-considerations"></a>추가 고려 사항
온-프레미스로 배포할 각 구성 요소에 대한 추가 고려 사항 및 지침을 보려면 다음 목록을 사용하세요.

- [Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md)를 사용하여 Azure Multi-Factor Authentication 설정
- [RADIUS 인증](howto-mfaserver-dir-radius.md)을 사용하여 Azure MFA Server 설정 및 구성
- [IIS 인증](howto-mfaserver-iis.md)을 사용하여 Azure MFA Server 설정 및 구성
- [Windows 인증](howto-mfaserver-windows.md)을 사용하여 Azure MFA Server 설정 및 구성
- [LDAP 인증](howto-mfaserver-dir-ldap.md)을 사용하여 Azure MFA Server 설정 및 구성
- [RADIUS를 사용한 원격 데스크톱 게이트웨이 및 Azure Multi-Factor Authentication 서버](howto-mfaserver-nps-rdg.md)로 Azure MFA Server 설정 및 구성
- Azure MFA Server 및 [Windows Server Active Directory](howto-mfaserver-dir-ad.md) 간에 동기화 설정 및 구성
- [Azure Multi-Factor Authentication 서버 모바일 앱 웹 서비스 배포](howto-mfaserver-deploy-mobileapp.md)
- LDAP 또는 RADIUS를 사용하여 Cisco ASA, Citrix Netscaler 및 Juniper/Pulse Secure VPN 어플라이언스에 대해 [Azure Multi-Factor Authentication을 통한 고급 VPN 구성](howto-mfaserver-nps-vpn.md)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure MFA에 대한 몇 가지 모범 사례를 강조하지만 MFA 배포를 계획하는 동안에도 사용할 수 있는 다른 리소스가 있습니다. 아래 목록에는 이 프로세스 중에 도움이 될 수 있는 몇 가지 주요 문서가 있습니다.

* [Azure Multi-Factor Authentication에서 보고서](howto-mfa-reporting.md)
* [2단계 확인 등록 환경](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)
* [Azure Multi-Factor Authentication FAQ](multi-factor-authentication-faq.md)
