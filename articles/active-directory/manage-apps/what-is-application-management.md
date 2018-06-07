---
title: Azure Active Directory와 응용 프로그램 관리 | Microsoft Docs
description: 이 문서는 온-프레미스, 클라우드 및 SaaS 응용 프로그램을 사용하여 Azure Active Directory를 통합하는 이점을 얻을 수 있습니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 1a8db218309eb48650443f1c9d57fe1ecdf04a6a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058233"
---
# <a name="managing-applications-with-azure-active-directory"></a>Azure Active Directory로 응용 프로그램 관리
실제 워크플로 또는 콘텐츠를 넘어 비즈니스에는 모든 응용 프로그램에 대한 두 가지 기본 요구 사항이 있습니다.

1. 생산성을 높이려면 응용 프로그램을 쉽게 검색하고 액세스할 수 있어야 합니다.
2. 보안 및 관리를 사용하도록 설정하려면 각 응용 프로그램에 액세스할 수 있는 사람과 실제로 액세스 하는 사람이 누구인지를 제어하고 감독해야 합니다.

클라우드 응용 프로그램 환경에서 이 작업을 수행하는 가장 좋은 방법은 ID를 사용하여 “*누구에게 어떤 작업이 허용되는지*” 제어하는 것입니다.

용어 계산에서

* *누가*는 *ID*라고 합니다. - 사용자 및 그룹 관리
* *무엇*은 *액세스 관리*입니다. - 보호된 리소스에 대한 액세스 관리

두 구성 요소는 모두 *IAM(ID 및 액세스 관리)* 으로 알려져 있으며 [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) 그룹에서 “*적당한 사람이 적당한 때에 적당한 이유로 적당한 리소스에 액세스할 수 있도록 하는 보안 분야*”로 정의됩니다.

그럼 무엇이 문제입니까? IAM이 한 곳에서 통합 솔루션으로 *관리되지 않는* 경우 다음과 같습니다.

* ID 관리자는 모든 응용 프로그램에서 개별적으로 사용자 계정을 만들고 업데이트해야 하며 이는 중복되고 시간이 많이 걸리는 작업입니다.
* 사용자는 작업에 필요한 응용 프로그램에 액세스하기 위해 여러 자격 증명을 기억해야 합니다. 결과적으로 사용자는 자신의 암호를 적어 두거나 다른 암호 관리 솔루션을 사용하는 경향이 있습니다. 이러한 대안은 다른 데이터 보안 위험을 야기합니다.
* 시간이 많이 걸리는 중복 작업은 비즈니스 수익을 높일 수 있는 비즈니스 활동에 사용자 및 관리자가 소비할 수 있는 시간을 감소시킵니다.

따라서 무엇이 일반적으로 조직이 통합된 IAM 솔루션을 채택하지 못하게 합니까?

* 대부분의 기술 솔루션은 각 조직에서 고유의 응용 프로그램에 배포하고 적용해야 하는 소프트웨어 플랫폼을 기반으로 합니다.
* 종종 클라우드 응용 프로그램은 IT 조직이 기존 IAM 솔루션을 통합할 수 있는 것 보다 빠른 속도로 적용됩니다.
* 보안 및 모니터링 도구는 포괄적인 E2E 시나리오를 달성하기 위해 추가 사용자 지정 및 통합이 필요합니다.

## <a name="azure-active-directory-integrated-with-applications"></a>응용 프로그램과 통합된 Azure Active Directory
Azure Active Directory는 Microsoft의 포괄적인 IDaaS(Identity as a Service) 솔루션입니다.

* 클라우드 서비스로 IAM 사용 
* 중앙 액세스 관리, SSO(Single Sign-On) 및 보고 제공 
* Salesforce, Google Apps, Box, Concur 등을 포함하여 응용 프로그램 갤러리에서 [수천 개의 응용 프로그램](https://azure.microsoft.com/marketplace/active-directory/) 에 대한 통합된 액세스 관리를 지원합니다. 

Azure Active Directory를 사용하여 파트너 및 고객(비즈니스 또는 소비자)을 위해 게시한 모든 응용 프로그램에는 동일한 ID 및 액세스 관리 기능이 있습니다.<br> 이렇게 하면 운영 비용을 크게 줄일 수 있습니다.

응용 프로그램 갤러리에 아직 나열되지 않은 응용 프로그램을 구현해야 하는 경우 어떻게 합니까? 응용 프로그램 갤러리에서 응용 프로그램에 SSO를 구성하는 것 보다 약간 더 시간이 걸리지만 Azure AD는 구성에 도움이 되는 마법사를 제공합니다.

Azure AD의 값은 “그저” 클라우드 응용 프로그램의 수준을 넘어섭니다. 또한 보안된 원격 액세스를 제공하여 온-프레미스 응용 프로그램과 함께 사용할 수도 있습니다. 보안 원격 액세스를 사용하면 VPN 또는 기타 기존의 원격 액세스 관리 구현이 필요하지 않습니다.

모든 응용 프로그램에 대한 중앙 액세스 관리 및 SSO(Single Sign On)를 제공하여 Azure AD는 주요 데이터 보안 및 생산성 문제에 대한 솔루션을 제공합니다.

* 사용자는 한 번의 로그온으로 여러 응용 프로그램에 액세스할 수 있기 때문에 소득 창출이나 비즈니스 운영 활동에 더 많은 시간을 할애할 수 있습니다.
* ID 관리자는 한 곳에서 응용 프로그램에 대한 액세스를 관리할 수 있습니다.

사용자 및 회사에 주는 혜택은 분명합니다. ID 관리자 및 조직에 대한 혜택은 좀 더 자세히 살펴보겠습니다.

## <a name="integrated-application-benefits"></a>통합된 응용 프로그램 혜택
SSO 프로세스는 두 단계로 진행됩니다.

* 인증은 사용자 ID의 유효성을 검사하는 과정입니다.
* 권한 부여는 액세스 정책을 사용하여 리소스에 대한 액세스를 활성화 또는 차단하도록 결정합니다.

Azure AD를 사용하여 응용 프로그램을 관리하고 SSO를 사용하도록 설정하는 경우 다음과 같습니다.

* 인증은 사용자의 온-프레미스(예: AD) 또는 Azure AD 계정에서 수행됩니다.
* 권한 부여는 일관된 최종 사용자 환경을 보장하고 내부 기능에 관계 없이 모든 응용 프로그램에 할당, 위치 및 MFA 조건을 추가하여 Azure AD에서 할당 및 보호 정책을 실행합니다.

권한 부여가 대상 응용 프로그램에서 시행되는 방식은 응용 프로그램이 Azure AD와 통합된 방법에 따라 달라진다는 점을 이해해야 합니다.

* **서비스 공급자에 의해 미리 통합된 응용 프로그램** Office 365나 Azure와 같이 이러한 응용 프로그램은 Azure AD에서 직접 작성하고 해당되는 포괄적인 ID 및 액세스 관리 기능을 사용합니다. 이러한 응용 프로그램에 대한 액세스는 디렉터리 정보 및 토큰 발급을 통해 사용되도록 설정됩니다.
* **Microsoft 및 사용자 지정 응용 프로그램에서 통합된 응용 프로그램** 내부 응용 프로그램 디렉터리를 사용하고 Azure AD와 독립적으로 작동할 수 있는 독립적인 클라우드 응용 프로그램입니다. 이러한 응용 프로그램에 대한 액세스는 응용 프로그램 계정이 매핑되는 응용 프로그램별 자격 증명을 발급하여 활성화됩니다. 응용 프로그램 기능에 따라 자격 증명은 응용 프로그램에서 이전에 프로비전된 계정의 페더레이션 토큰 또는 사용자 이름 및 암호일 수도 있습니다.
* **온-프레미스 응용 프로그램** 기본적으로 온-프레미스 응용 프로그램에 대한 액세스를 사용하여 Azure AD 응용 프로그램 프록시를 통해 게시된 응용 프로그램입니다. 이러한 응용 프로그램은 Windows Server Active Directory와 같은 온-프레미스 디렉터리를 사용합니다. 이러한 응용 프로그램에 대한 액세스를 사용하면 온-프레미스 로그인 요구 사항을 적용하는 동안 프록시를 트리거하여 최종 사용자에게 응용 프로그램 콘텐츠를 제공할 수 있습니다.

예를 들어 사용자가 조직에 합류하는 경우 기본 로그인 작업에 Azure AD의 사용자에 대한 계정을 만들어야 합니다. 이 사용자가 Salesforce와 같은 관리 응용 프로그램에 대한 액세스를 필요로 하는 경우 또한 Salesforce에서 이 사용자에 대한 계정을 만들고 Azure 계정에 연결하여 SSO를 작동시킵니다. 사용자가 조직을 떠나는 경우 Azure AD 계정 및 사용자가 액세스한 응용 프로그램의 IAM 스토어에서 모든 해당 사용자 계정을 삭제하는 것이 좋습니다.

## <a name="access-detection"></a>액세스 감지
오늘날 기업에서 IT 부서가 사용되는 클라우드 응용 프로그램의 일부를 인지하지 못하는 경우가 있습니다. 클라우드 앱 검색과 함께 Azure AD는 이러한 응용 프로그램을 검색하는 솔루션을 제공합니다.

## <a name="account-management"></a>계정 관리
일반적으로 다양한 응용 프로그램의 계정을 관리하는 작업은 조직의 IT 또는 지원 담당자가 수행하는 수동 프로세스입니다. Azure AD는 서비스 공급자가 통합한 응용 프로그램 및 자동화된 사용자 프로비전 및 SAML Just-In-Time 프로비전을 지원하는 Microsoft에서 미리 통합한 응용 프로그램 전반에서 계정 관리를 완전히 자동화합니다.

## <a name="automated-user-provisioning"></a>자동화된 사용자 프로비전
일부 응용 프로그램은 계정 만들기 및 제거(또는 비활성화)에 자동화 인터페이스를 제공합니다. 공급자가 이러한 인터페이스를 제공하는 경우 Azure AD에서 활용됩니다. 관리 작업이 자동으로 발생하기 때문에 운영 비용을 감소하고 무단으로 액세스할 가능성이 줄어들기 때문에 사용자 환경의 보안을 향상시킵니다.

## <a name="access-management"></a>액세스 관리
Azure AD를 통해 개별 또는 규칙 기반 할당을 사용하는 응용 프로그램에 대한 액세스를 관리할 수 있습니다. 또한 최상의 감독을 보장하고 Helpdesk의 부담을 줄이는 조직에서 적당한 사람에게 액세스 관리를 위임할 수 있습니다.

## <a name="on-premises-applications"></a>온-프레미스 응용 프로그램
기본 제공 응용 프로그램 프록시를 사용하면 결과적으로 사용자에게 최신 클라우드 응용 프로그램과 함께 일관된 액세스 환경 및 Azure AD 모니터링, 보고 및 보안 기능에서 혜택을 주어 온-프레미스 응용 프로그램을 게시할 수 있습니다.

## <a name="reporting-and-monitoring"></a>보고 및 모니터링
Azure AD는 응용 프로그램에 액세스하는 사람과 응용 프로그램을 실제로 사용한 시간을 알 수 있도록 사전 통합된 보고 및 모니터링 기능을 제공합니다.

## <a name="related-capabilities"></a>관련 기능
Azure AD를 사용하여 세부적인 액세스 정책 및 사전 통합된 MFA로 응용 프로그램을 보호할 수 있습니다. Azure MFA에 대해 자세히 알아보려면 [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/)를 참조하세요.

## <a name="getting-started"></a>시작
응용 프로그램을 Azure AD와 통합하기 시작하려면 [응용 프로그램과 Azure Active Directory 통합 시작 가이드](plan-an-application-integration.md)를 살펴봅니다.

## <a name="see-also"></a>참고 항목
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](../active-directory-apps-index.md)
* [SSO를 SaaS 응용 프로그램에 배포하기 위한 단계별 계획](http://aka.ms/ssodeploymentplan)

