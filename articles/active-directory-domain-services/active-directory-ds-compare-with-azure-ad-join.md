---
title: "Azure AD 조인 및 Azure Active Directory Domain Services 비교 | Microsoft Docs"
description: "Azure AD 조인과 Azure AD Domain Services 중에서 결정"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: fb28c7a66d26119444dd720c2e134f2818d46cfc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Azure Active Directory 조인과 Azure Active Directory Domain Services 중에서 선택
이 문서에서는 Azure AD(Active Directory) 조인과 Azure AD Domain Services의 차이를 설명하고 사용 사례를 기반으로 선택하도록 도와줍니다.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Azure AD 등록 및 Azure AD 가입 장치
Azure AD를 사용하면 조직에서 사용하는 장치의 ID를 관리하고 이러한 장치에서 회사 리소스에 대한 액세스를 제어할 수 있습니다. 사용자는 자신의 개인(bring-your-own) 장치를 Azure AD에 등록할 수 있고 Azure AD는 장치에 ID를 제공합니다. 그런 다음 Azure AD는 사용자가 Azure AD에 로그인하고 장치를 사용하여 보안 리소스에 액세스할 때 장치를 인증할 수 있습니다. 또한 Microsoft Intune과 같은 MDM(모바일 장치 관리) 소프트웨어를 사용하여 장치를 관리할 수 있습니다. 이 기능을 사용하면 관리되고 정책을 준수하는 장치에서 중요한 리소스에 대한 액세스를 제한할 수 있습니다.

조직 소유 장치를 Azure AD에 조인할 수도 있습니다. 이 메커니즘은 Azure AD에 개인 장치를 등록하는 것과 동일한 이점을 제공합니다. 또한 사용자는 회사 자격 증명을 사용하여 장치에 로그인할 수 있습니다. Azure AD 가입 장치에는 다음과 같은 이점이 있습니다.
* Azure AD로 보호되는 응용 프로그램에 대한 SSO(Single Sign-On)
* 장치 전반에 걸친 사용자 설정의 엔터프라이즈 정책 준수 로밍.
* 회사 자격 증명을 사용하여 비즈니스용 Windows 스토어에 액세스.
* 비즈니스용 Windows Hello
* 회사 정책을 준수하는 장치에서 앱 및 리소스에 대한 제한적 액세스.

| **장치의 유형** | **장치 플랫폼** | **메커니즘** |
|:---| --- | --- |
| 개인 장치 | Windows 10, iOS, Android, Mac OS | Azure AD 등록 |
| 온-프레미스 AD에 조인되지 않은 조직 소유 장치 | Windows 10 | Azure AD 가입 |
| 온-프레미스 AD에 조인된 조직 소유 장치 | Windows 10 | 하이브리드 Azure AD 가입 |

Azure AD 가입 또는 등록 장치에서 사용자 인증은 최신 OAuth/OpenID Connect 기반 프로토콜을 사용하여 이루어집니다. 이러한 프로토콜은 인터넷을 통해 작동하도록 설계되었으며 사용자가 어디서든 회사 리소스에 액세스하는 모바일 시나리오에 적합합니다.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Azure AD Domain Services 관리되는 도메인에 도메인 가입
Azure AD Domain Services는 Azure 가상 네트워크에서 관리되는 AD 도메인을 제공합니다. 기존 도메인 가입 메커니즘을 사용하여 관리되는 도메인에 컴퓨터를 조인할 수 있습니다. Windows 클라이언트(Windows 7, Windows 10) 및 Windows Server 컴퓨터는 관리되는 도메인에 조인될 수 있습니다. 또한 Linux 및 Mac OS 컴퓨터를 관리되는 도메인에 조인할 수도 있습니다. 컴퓨터를 AD 도메인에 조인하면 사용자는 자신의 회사 자격 증명을 사용하여 컴퓨터에 로그인 할 수 있습니다. 이러한 컴퓨터는 그룹 정책을 사용하여 관리할 수 있으므로 조직의 보안 정책을 준수하도록 강제 적용할 수 있습니다.

도메인에 가입된 컴퓨터에서 사용자 인증은 NTLM 또는 Kerberos 인증 프로토콜을 사용하여 수행됩니다. 사용자 인증이 작동하려면 도메인에 가입된 컴퓨터는 관리되는 도메인의 도메인 컨트롤러와 송수신이 가능해야 합니다. 따라서 도메인에 가입된 컴퓨터는 관리되는 도메인과 동일한 가상 네트워크에 있어야 합니다. 또는 도메인에 가입된 컴퓨터가 피어링된 가상 네트워크 또는 사이트 간 VPN/ExpressRoute 연결을 통해 관리되는 도메인에 연결되어야 합니다. 따라서 이 메커니즘은 모바일 장치 또는 회사 네트워크 외부의 리소스에 연결되는 장치에 적합하지 않습니다.

> [!NOTE]
> 기술적으로는 사이트 간 VPN 또는 ExpressRoute 연결을 통해 온-프레미스 클라이언트 워크스테이션을 관리되는 도메인에 조인하는 것이 가능합니다. 그러나 최종 사용자 장치의 경우 장치를 Azure AD에 등록(개인 장치)하거나 장치를 Azure AD에 조인(회사 장치)하는 것이 좋습니다. 이 메커니즘은 인터넷을 통해 보다 효과적으로 작동하며 최종 사용자가 어디에서나 작업할 수 있도록 해줍니다. Azure AD Domain Services는 응용 프로그램이 배포되는 Azure 가상 네트워크에 배포된 Windows 또는 Linux Server 가상 컴퓨터에 적합합니다.


## <a name="summary---key-differences"></a>요약 - 주요 차이점
| **측면** | **Azure AD 조인** | **Azure AD Domain Services** |
|:---| --- | --- |
| 다음에 의해 장치가 제어됨 | Azure AD | Azure AD Domain Services 관리되는 도메인 |
| 디렉터리의 표현 | Azure AD 디렉터리의 장치 개체. | AAD-DS 관리되는 도메인의 컴퓨터 개체. |
| 인증 | OAuth/OpenID Connect 기반 프로토콜 | Kerberos, NTLM 프로토콜 |
| 관리 | Intune과 같은 MDM(모바일 장치 관리) 소프트웨어 | 그룹 정책 |
| 네트워킹 | 인터넷을 통해 작동 | 컴퓨터가 관리되는 도메인과 동일한 가상 네트워크에 있어야 합니다.|
| 유용한 경우 | 최종 사용자 모바일 또는 데스크톱 장치 | Azure에 배포된 서버 가상 컴퓨터 |


## <a name="next-steps"></a>다음 단계
### <a name="learn-more-about-azure-ad-domain-services"></a>Azure AD Domain Services에 대한 자세한 정보
* [Azure AD Domain Services 개요](active-directory-ds-overview.md)
* [기능](active-directory-ds-features.md)
* [배포 시나리오](active-directory-ds-scenarios.md)
* [Azure AD Domain Services가 사용 사례에 적합한 경우 찾기](active-directory-ds-comparison.md)
* [Azure AD Domain Services를 Azure AD 디렉터리와 동기화하는 방법 이해](active-directory-ds-synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Azure AD 조인에 대해 자세히 알아보기
* [Azure Active Directory의 장치 관리 소개](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Azure AD Domain Services 시작
* [Azure Portal을 사용하여 Azure AD Domain Services 활성화](active-directory-ds-getting-started.md)
