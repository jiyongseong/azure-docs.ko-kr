---
title: Azure ID 관리의 기초
description: 클라우드 기반 ID는 현재, 사용자가 회사 응용 프로그램 및 데이터에 액세스하는 방법과 시기를 제어하고 가시성을 유지하는 가장 좋은 방법입니다.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 3603a1e91bc6bcf8a472b41bb0d0a276bf231d7c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302862"
---
# <a name="fundamentals-of-azure-identity-management"></a>Azure ID 관리의 기초

더 많은 회사의 디지털 리소스가 회사 네트워크 외부의 클라우드와 장치에 상주함에 따라 뛰어난 클라우드 기반 ID 및 액세스 관리 솔루션이 필요하게 되었습니다. 클라우드 기반 ID는 현재, 사용자가 회사 응용 프로그램 및 데이터에 액세스하는 방법과 시기를 제어하고 가시성을 유지하는 가장 좋은 방법입니다.

Microsoft는 10년 넘게 클라우드 기반 ID를 보호했으며 이제 [Azure AD(Active Directory)](active-directory-whatis.md)를 사용하여 이러한 동일한 보호 시스템을 사용자에게 제공할 수 있습니다. 엔터프라이즈 관리자는 Azure AD를 통해 이전보다 더 우수한 보안 및 관리 기능으로 사용자 및 관리자의 책임을 쉽게 확인할 수 있습니다.

Azure AD Premium은 고급 보호 기능이 있는 클라우드 기반 ID 및 액세스 관리 솔루션으로 모든 앱에 하나의 보안 ID와 ID 보호([Microsoft 인텔리전스 보안 그래프](https://www.microsoft.com/en-us/security/intelligence)로 향상됨) 및 Privileged Identity Management를 사용할 수 있습니다. Azure AD Premium은 다른 모니터링 또는 보고 도구뿐만 아니라 사용자의 ID를 실시간으로 보호할 수 있으며 위험 기반의 적응 액세스 정책을 만들어 조직의 데이터를 보호할 수 있습니다.

다음 짧은 비디오에서 Azure AD ID 관리 및 보호에 대한 간략한 개요를 살펴보세요.
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft는 모든 곳에서 사용할 수 있는 ID뿐만 아니라 조직 내에서 IT를 자동화하고 보호하며 관리할 수 있는 도구 모음도 제공합니다. 클라우드 컴퓨팅의 출현 이후에도 사용자 암호, 사용자 그룹 관리 및 응용 프로그램 요청을 재설정하기 위한 헬프데스크 호출 같은 IT 작업을 관리 및 제어하려는 요구는 계속해서 발생합니다. 더욱 복잡한 경우에 직원은 이제 개인 장치를 작동시키고 지원되는 SaaS 응용 프로그램을 사용할 수 있습니다. 기업 데이터 센터 및 공용 클라우드 플랫폼에서 해당 응용 프로그램에 대한 제어를 유지 관리하는 중요한 과제를 수행합니다.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>Azure AD 및 Office 365를 사용하여 온-프레미스 Active Directory 연결
온-프레미스 Active Directory에 대규모 투자를 한 조직은 온-프레미스 디렉터리를 Azure AD와 함께 [하이브리드 ID 관리](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview)로 통합하는 방법으로 클라우드로 투자를 확장할 수 있습니다. 이렇게 하면 사용자가 위치에 관계 없이 리소스에 액세스하기 위한 공통의 ID를 제공하여 더욱 생산성을 높일 수 있습니다. 그런 다음, 사용자 및 조직은 SSO(Single Sign-On)를 사용하여 Office 365와 같은 온-프레미스 리소스와 클라우드 서비스에 모두 액세스 할 수 있습니다.

[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)는 통합을 완료하는 데 필요한 유일한 도구입니다. Azure AD Connect는 ID 동기화 요구를 지원하는 기능을 제공하며 DirSync 및 Azure AD Sync와 같은 이전 버전의 ID 통합 도구를 대체합니다. Azure AD Connect를 사용하면 다음을 통해 온-프레미스와 Azure AD 간의 ID 관리 및 동기화가 가능합니다.

- 동기화 - 이 구성 요소는 사용자, 그룹 및 기타 개체 생성을 담당합니다. 온-프레미스 사용자 및 그룹의 ID 정보가 클라우드와 일치하도록 만드는 것도 담당합니다. 암호 쓰기 저장을 사용하면 사용자가 Azure AD에서 자신의 암호를 업데이트할 때 온-프레미스 디렉터리를 동기화 상태로 유지할 수 있습니다.
- AD FS - 페더레이션은 Azure AD Connect에서 제공하는 선택적 기능으로, 온-프레미스 AD FS 인프라를 통해 하이브리드 환경을 구성하는 데 사용할 수 있습니다. 페더레이션은 조직에서 스마트 카드 또는 타사 MFA 등의 복잡한 배포를 처리하는 데 사용될 수 있습니다.
- 상태 모니터링 - [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)는 강력한 모니터링을 제공하고 Azure Portal에서 중앙 위치를 제공하여 이 활동을 볼 수 있습니다.

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>셀프 서비스 및 Single Sign-On 환경으로 생산성 향상 및 헬프데스크 비용 절감

직원은 단일 사용자 이름 및 암호를 기억하도록 하고 모든 장치에 대해 일관된 환경을 제공할 수 있을 때 생산성이 향상됩니다. 또한 헬프데스크의 도움을 기다리지 않고 [잊어버린 암호를 재설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)하거나 응용 프로그램에 대한 액세스를 요청하는 등의 셀프 서비스 작업을 수행할 때 시간을 절약할 수 있습니다.

Azure AD는 [온-프레미스 Active Directory를 클라우드로 확장](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)하여 사용자가 기본 조직 계정을 도메인 가입 장치, 회사 리소스 및 작업을 완료하기 위해 사용해야 하는 모든 웹 및 SaaS 응용 프로그램에 사용할 수 있도록 합니다. 여러 사용자 이름과 암호들을 기억할 필요가 없을 뿐만 아니라, 조직 그룹 구성원 혹은 구성원의 상태에 따라 사용자의 응용 프로그램 액세스를 자동으로 프로비전하거나 프로비전을 해제할 수 있습니다. 그리고 [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)를 통해 개발 및 게시된 사용자 고유의 온-프레미스 앱이나 갤러리 앱에 대한 액세스를 제어할 수 있습니다.

## <a name="manage-and-control-access-to-corporate-resources"></a>회사 리소스에 대한 액세스 관리 및 제어
Microsoft ID 및 액세스 관리 솔루션은 IT가 [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) 및 [조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)과 같은 추가 수준의 유효성 검사를 활성화하여 회사 데이터 센터에 걸친 응용 프로그램 및 리소스에 대한 액세스 및 클라우드로 액세스를 보호하도록 도움을 줍니다. 고급 보안 보고, 감사 및 경고를 통해 의심스러운 작업을 모니터링하여 잠재적인 보안 문제를 완화시킵니다.

Azure AD Premium의 조건부 액세스 정책은 엔터프라이즈 관리자에게 Azure AD에 연결된 응용 프로그램(SaaS 앱, 클라우드에서 실행되는 사용자 지정 앱 또는 온-프레미스 웹 응용 프로그램)에 대한 정책 기반 액세스 규칙을 만드는 기능을 제공합니다. Azure AD는 이러한 정책을 실시간으로 평가하고 사용자가 응용 프로그램 액세스를 시도할 때마다 정책을 적용합니다. Azure ID 보호 정책을 사용하면 의심스러운 활동이 검색될 경우 자동으로 조치를 취할 수 있습니다. 이러한 조치로는 위험이 높은 사용자 액세스 차단, 다단계 인증 적용, 자격 증명이 손상된 것처럼 보이는 경우 사용자 암호 다시 설정 등이 포함될 수 있습니다.


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

Azure Active Directory Premium P2 제품에 포함된 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started)를 사용하면 Azure Active Directory 및 기타 Microsoft 온라인 서비스에서 관리 계정과 리소스에 대한 액세스를 검색, 제한 및 모니터링할 수 있습니다. 또한 필요한 정확한 기간 동안 주문형 관리 액세스를 관리할 수도 있습니다.

Privileged Identity Management는 주문형 관리자 권한을 적용할 수 있으므로 관리자는 계정이 일반 사용자 상태로 돌아가기 전에 사전 구성된 기간 동안 다단계 인증된 임시 권한 상승을 요청할 수 있습니다.

## <a name="benefits-of-azure-identity"></a>Azure ID의 이점

Azure ID 관리를 통해 다음을 수행할 수 있습니다.

-   엔터프라이즈 전체에서 각 사용자에 대한 단일 ID를 만들고 관리하여 사용자, 그룹 및 장치를 [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)와 동기화된 상태로 유지합니다.

-   사전 통합된 수천 개의 SaaS 앱을 비롯하여 응용 프로그램에 대한 SSO(Single sign-on) 액세스를 제공하거나 [Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)를 사용하여 온-프레미스 SaaS 응용 프로그램에 보안 원격 액세스를 제공합니다.

-   온-프레미스 및 클라우드 응용 프로그램 모두에 대해 규칙 기반 [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)을 적용하여 응용 프로그램 액세스 보안을 사용하도록 설정합니다.

-   [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords) 및 [MyApps 포털](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help)을 사용한 그룹 및 응용 프로그램 액세스 요청으로 사용자 생산성을 개선합니다.

-   전 세계 엔터프라이즈급, 클라우드 기반 ID 및 액세스 관리 솔루션의 [고가용성 및 안정성](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications)을 활용합니다.

## <a name="next-steps"></a>다음 단계
[Azure ID 솔루션에 대한 자세한 정보](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)