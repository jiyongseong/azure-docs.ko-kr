---
title: PaaS 배포 보안 | Microsoft Docs
description: " PaaS와 다른 클라우드 서비스 모델의 보안 이점을 이해하고 Azure PaaS 배포를 보호하기 위한 권장 사례에 대해 알아봅니다. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: f19c52629a997687692eef9bce2e13b2b7894052
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="securing-paas-deployments"></a>PaaS 배포 보안

이 문서에서 제공하는 유용한 정보는 다음과 같습니다.

- 클라우드에서의 응용 프로그램 호스팅에 대한 보안 이점 이해
- 다른 클라우드 서비스 모델과 비교하여 서비스로서의 플랫폼 (PaaS)에 대한 보안 이점 평가
- 네트워크 중심에서 ID 중심 경계 보안 방식으로 보안 목표 변경
- 일반적인 PaaS 보안 모범 사례 권장 구현

## <a name="cloud-security-advantages"></a>클라우드 보안 이점
클라우드에 해당하는 보안 이점이 있습니다. 온-프레미스 환경에서 조직은 충족되지 않은 책임과 제한된 리소스를 보안에 투자할 가능성이 높으며, 이로 인해 공격자가 모든 계층의 취약점을 악용할 수 있는 환경이 만들어집니다.

![클라우드 시대의 보안 이점][1]

조직에서는 공급자의 클라우드 기반 보안 기능 및 클라우드 인텔리전스를 사용하여 위협 요소 탐지 및 응답 시간을 향상시킬 수 있습니다.  조직은 클라우드 공급자에 책임을 전가함으로써 보안 적용 범위를 더 넓힐 수 있으며, 보안 리소스와 예산을 다른 비즈니스 우선 순위에 다시 할당할 수 있습니다.

## <a name="division-of-responsibility"></a>책임 분담
사용자와 Microsoft 간의 책임 분담을 이해해야 합니다. 온-프레미스에서는 전체 스택을 가지고 있지만 클라우드로 이동할 때 일부 책임이 Microsoft로 이전됩니다. 다음 책임 매트릭스에서는 사용자와 Microsoft가 책임을 지는 SaaS, PaaS 및 IaaS 배포의 스택 영역을 보여 줍니다.

![책임 영역][2]

모든 클라우드 배포 유형에 대해 사용자는 고유의 데이터와 ID를 소유합니다. 사용자는 자신이 제어하는 데이터 및 ID 보안, 온-프레미스 리소스 및 클라우드 구성 요소(서비스 유형에 따라 다름)의 보안을 보호하는 데 책임이 있습니다.

배포 유형에 관계 없이 사용자가 항상 책임을 지는 영역은 다음과 같습니다.

- Data
- Endpoints
- 계좌
- 액세스 관리

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>PaaS 클라우드 서비스 모델의 보안 이점
동일한 책임 매트릭스를 사용하여 Azure PaaS 배포와 온-프레미스 배포 간의 보안 이점을 비교해 보겠습니다.

![PaaS의 보안 이점][3]

Microsoft는 물리적 인프라인 스택 맨 아래부터 시작하여 일반적인 위험과 책임을 덜어줍니다. Microsoft 클라우드는 Microsoft에서 지속적으로 모니터링하므로 공격하기가 어렵습니다. 따라서 공격자가 Microsoft 클라우드를 목표로 삼는 것은 의미가 없습니다. 공격자가 많은 돈과 자원을 갖고 있지 않으면 다른 대상으로 이동할 가능성이 있습니다.  

스택 중간에 수행되는 PaaS 배포와 온-프레미스 배포 간에는 차이가 없습니다. 유사한 위험이 응용 프로그램 계층과 계정/액세스 관리 계층에도 있습니다. 이 문서의 다음 단계 섹션에서는 이러한 위험을 제거하거나 최소화하는 모범 사례로 안내합니다.

스택 맨 위에는 데이터 관리와 권한 관리가 있으며 키 관리를 통해 완화될 수 있는 위험이 하나 있습니다. (키 관리는 모범 사례에서 설명합니다.) 키 관리는 추가적인 책임이지만 더 이상 관리할 필요가 없는 PaaS 배포 영역이 있으므로 리소스를 키 관리로 이동할 수 있습니다.

또한 Azure 플랫폼은 다양한 네트워크 기반 기술을 사용하여 강력한 DDoS 보호 기능을 제공합니다. 하지만 모든 유형의 네트워크 기반 DDoS 보호 방법에는 링크별 및 데이터 센터별로 제한이 있습니다. 대규모 DDoS 공격의 영향을 피하기 위해 Azure의 핵심 클라우드 기능을 활용하여 DDoS 공격으로부터 자동으로 신속하게 확장할 수 있습니다. 권장 사례 항목에서 이 작업을 수행하는 방법에 대해 자세히 설명하겠습니다.

## <a name="modernizing-the-defenders-mindset"></a>방어자의 사고 방식 현대화
PaaS를 배포하면 전반적인 보안 접근 방식이 바뀌게 됩니다. 스스로 모든 것을 제어해야 하는 것에서 Microsoft와 책임을 분담하는 것으로 태도를 바꾸어야 합니다.

PaaS 배포와 기존 온-프레미스 배포 간의 또 다른 중요한 차이점은 기본 보안 경계를 정의하는 새로운 개념에 있습니다. 전통적으로 기본 온-프레미스 보안 경계는 네트워크였으며 대부분의 온-프레미스 보안 설계에서는 네트워크를 기본 보안 중심축으로 사용하고 있습니다. 하지만 PaaS 배포의 경우 ID가 기본 보안 경계라고 생각하면 더 효과적으로 수행할 수 있습니다.

## <a name="identity-as-the-primary-security-perimeter"></a>기본 보안 경계로서의 ID
클라우드 컴퓨팅의 필수적인 다섯 가지 특성 중 하나는 광범위한 네트워크 액세스이며, 이는 네트워크 중심적 사고의 관련성을 떨어뜨립니다. 클라우드 컴퓨팅의 목표는 사용자가 위치에 관계 없이 리소스에 액세스할 수 있도록 하는 것입니다. 대부분의 사용자에 대한 위치는 인터넷 어딘가에 있을 것입니다.

다음 그림에서는 보안 경계가 네트워크 경계에서 ID 경계까지 전개되는 방식을 보여 줍니다. 보안 수준이 네트워크 보호에 대해서는 낮아지고 데이터를 보호하고 앱과 사용자의 보안을 관리하는 방법에 대해서는 높아집니다. 주요 차이점은 보안을 회사의 중요한 요소에 더 가깝게 두려는 것입니다.

![새로운 보안 경계로서의 ID][4]

초기에는 Azure PaaS 서비스(예: 웹 역할 및 Azure SQL)에서 기존의 네트워크 경계 방어를 거의 또는 전혀 제공하지 않았습니다. 요소의 목적은 인터넷(웹 역할)에 노출하는 것이고 인증은 새로운 경계(예: BLOB 또는 Azure SQL)를 제공한다는 것이 이해되었습니다.

최신 보안 사례에서는 공격자가 네트워크 경계를 침범했다고 가정합니다. 따라서 최신 방어 사례가 ID로 전환했습니다. 조직은 강력한 인증 및 권한 부여(모범 사례)로 ID 기반 보안 경계를 설정해야 합니다.

## <a name="recommendations-for-managing-the-identity-perimeter"></a>ID 경계를 관리하기 위한 권장 사항

네트워크 경계의 원리와 패턴은 수십 년 동안 사용할 수 있었습니다. 반면 업계에서는 ID를 기본 보안 경계로 사용한 경험이 비교적 적습니다. 그렇게 말하면서도 현장에서 증명되고 거의 모든 PaaS 서비스에 적용할 수 있는 몇 가지 일반적인 권장 사항을 제공하기에 충분한 경험을 축적했습니다.

다음은 ID 경계 관리에 대한 일반적인 모범 사례 접근 방식을 요약하고 있습니다.

- **키 또는 자격 증명을 잃지 마세요.** PaaS 배포를 보호하려면 키와 자격 증명을 반드시 안전하게 보호해야 합니다. 키와 자격 증명을 잃어 버리는 것은 일반적인 문제입니다. 좋은 해결 방법 중 하나는 HSM(하드웨어 보안 모듈)에 키와 비밀을 저장할 수 있는 중앙 집중식 솔루션을 사용하는 것입니다. Azure에서는 [Azure Key Vault](../key-vault/key-vault-whatis.md)를 사용하여 HSM을 클라우드에 제공하고 있습니다.
- **원본 코드 또는 GitHub에 자격 증명 및 기타 비밀을 넣지 마세요.** 키 및 자격 증명을 잃어 버리는 것보다 나쁜 것은 권한이 없는 사람이 권한을 얻는 것입니다. 공격자는 봇 기술을 이용하여 GitHub와 같은 코드 리포지토리에 저장된 키와 비밀을 찾을 수 있습니다. 따라서 공개 원본 코드 리포지토리에 키와 비밀 정보를 넣지 두면 안됩니다.
- **하이브리드 PaaS 및 IaaS 서비스에서 VM 관리 인터페이스를 보호하세요.**  IaaS 및 PaaS 서비스는 VM(가상 머신)에서 실행됩니다. 서비스 종류에 따라 이러한 VM을 원격으로 직접 관리할 수 있는 몇 가지 관리 인터페이스를 사용할 수 있습니다. [SSH(보안 셸)](https://en.wikipedia.org/wiki/Secure_Shell), [RDP(원격 데스크톱 프로토콜)](https://support.microsoft.com/kb/186607) 및 [원격 PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting)과 같은 원격 관리 프로토콜을 사용할 수 있습니다. 일반적으로 인터넷에서 VM에 대해 직접 원격 액세스를 사용하지 않는 것이 좋습니다. 가능하다면 Azure 가상 네트워크에 가상 사설망을 사용하는 것과 같은 다른 방법을 사용해야 합니다. 다른 방법을 사용할 수 없는 경우 복잡한 암호 구문을 사용하고, 가능한 경우 2단계 인증(예: [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md))을 사용해야 합니다.
- **강력한 인증 및 권한 부여 플랫폼을 사용합니다.**

  - 사용자 지정 사용자 저장소 대신 Azure AD의 페더레이션 ID를 사용합니다. 페더레이션 ID를 사용하면 플랫폼 기반 접근 방식을 활용하고 권한 있는 ID 관리를 파트너에게 위임합니다. 페더레이션 ID 접근 방식은 직원이 해고되고 여러 ID와 권한 부여 시스템을 통해 정보를 반영해야 하는 경우에 특히 중요합니다.
  - 사용자 지정 코드 대신 플랫폼에서 제공하는 인증 및 권한 부여 메커니즘을 사용합니다. 이는 사용자 지정 인증 코드를 개발하면 오류가 발생할 수 있기 때문입니다. 대부분의 개발자는 보안 전문가가 아니며 인증 및 권한 부여의 미묘한 측면과 최신 개발 정보를 알지 못할 것입니다. 상용 코드(예: Microsoft 개발 코드)는 종종 광범위하게 보안을 검토되고 있습니다.
  - 다단계 인증을 사용합니다. 다단계 인증은 인증의 사용자 이름과 암호 유형에 내재된 보안 약점을 방지할 수 있기 때문에 인증 및 권한 부여의 현재 표준입니다. Azure 관리(포털/원격 PowerShell) 인터페이스와 고객 대면 서비스에 대한 액세스는 [Azure MFA(Multi-Factor Authentication)](../active-directory/authentication/multi-factor-authentication.md)을 사용하도록 설계되고 구성되어야 합니다.
  - OAuth2 및 Kerberos와 같은 표준 인증 프로토콜을 사용합니다. 이러한 프로토콜은 광범위하게 검토되었으며 인증 및 권한 부여를 위한 플랫폼 라이브러리의 일부로 구현될 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure PaaS 배포의 보안 이점을 집중적으로 설명했습니다. 그런 다음 PaaS 웹 및 모바일 솔루션 보안을 위한 권장 사례에 대해 알아보았습니다. 이제는 Azure App Service, Azure SQL Database, Azure SQL Data Warehouse로 시작하겠습니다. 다른 Azure 서비스에 권장되는 사례에 대한 문서를 사용할 수 있으며, 해당 링크는 다음 목록에서 제공합니다.

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database 및 Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Azure Storage
- Azure REDIS Cache
- Azure Service Bus
- 웹 응용 프로그램 방화벽

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
