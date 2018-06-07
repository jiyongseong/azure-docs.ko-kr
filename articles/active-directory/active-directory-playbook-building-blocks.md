---
title: 'Azure Active Directory 개념 증명 플레이 북: 문서 블록 | Microsoft Docs'
description: ID 및 액세스 관리 시나리오를 탐색하고 신속하게 구현
services: active-directory
keywords: Azure Active Directory, 플레이 북, 개념 증명, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: 97a77de2fe1111f0a65b5325a5db96ad30f40f15
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157702"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Azure Active Directory 개념 증명 플레이 북: 문서 블록

## <a name="catalog-of-roles"></a>역할 카탈로그

| 역할 | 설명 | 개념 증명(PoC) 책임 |
| --- | --- | --- |
| **ID 아키텍처/개발 팀** | 이 팀은 일반적으로 솔루션을 설계하고, 프로토타입을 구현하고, 승인을 추진하고, 마지막으로 작업으로 전달하는 팀입니다. | 환경을 제공하고 관리 효율성 측면에서 다양한 시나리오를 평가합니다. |
| **온-프레미스 ID 작업 팀** | Active Directory 포리스트, LDAP 디렉터리, HR 시스템 및 페더레이션 ID 공급자와 같은 다양한 ID 원본을 온-프레미스에서 관리합니다. | PoC 시나리오에 필요한 온-프레미스 리소스에 대한 액세스를 제공합니다.<br/>가능한 한 적게 포함되어야 합니다.|
| **응용 프로그램 기술 소유자** | Azure AD와 통합될 다양한 클라우드 앱 및 서비스의 기술 소유자입니다. | SaaS 응용 프로그램(잠재적으로 테스트할 인스턴스)에 대한 세부 정보를 제공합니다. |
| **Azure AD 전역 관리자** | Azure AD 구성을 관리합니다. | 동기화 서비스를 구성할 자격 증명을 제공합니다. 일반적으로 PoC 중에는 ID 아키텍처와 같은 팀이지만 작업 단계 중에는 별개 팀입니다.|
| **데이터베이스 팀** | 데이터베이스 인프라의 소유자입니다. | 특정 시나리오 준비를 위해 SQL 환경(ADFS 또는 Azure AD Connect)에 대한 액세스를 제공합니다.<br/>가능한 한 적게 포함되어야 합니다. |
| **네트워크 팀** | 네트워크 인프라의 소유자입니다. | 동기화 서버가 데이터 원본 및 클라우드 서비스(방화벽 규칙, 열린 포트, IPSec 규칙 등)에 제대로 액세스하도록 네트워크 수준에서 필요한 액세스 권한을 제공합니다. |
| **보안 팀** | 보안 전략을 정의하고, 다양한 원본에서 보안 보고서를 분석하고, 결과를 완료합니다. | 대상 보안 평가 시나리오를 제공합니다. |

## <a name="common-prerequisites-for-all-building-blocks"></a>모든 문서 블록에 대한 일반적인 필수 구성 요소

Azure AD Premium에서 POC에 필요한 일부 필수 구성 요소는 다음과 같습니다.

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 유효한 Azure 구독을 통해 정의된 Azure AD 테넌트 | [Azure Active Directory 테넌트를 얻는 방법](active-directory-howto-tenant.md)<br/>**참고:** 이미 Azure AD 프리미엄 라이선스가 포함된 환경이 있는 경우에는 https://aka.ms/accessaad로 이동하여 무제한 구독을 얻을 수 있음 <br/>https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ 및 https://technet.microsoft.com/library/dn832618.aspx에 대한 자세한 정보 |
| 정의 및 확인된 도메인 | [Azure Active Directory에 사용자 지정 도메인 이름 추가](active-directory-domains-add-azure-portal.md)<br/>**참고:** Power BI 등의 일부 작업은 내부적으로 Azure AD 테넌트를 프로비전했을 수 있습니다. 지정된 도메인이 테넌트에 연결되어 있는지 확인하려면 https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration로 이동합니다. 성공적인 응답을 받으면 도메인이 이미 테넌트에 할당된 것이고 인수가 필요할 수 있습니다. 이 경우 Microsoft에 추가 지침을 문의하세요. 인수 옵션에 대한 참고 항목: [Azure의 셀프 서비스 등록이란?](active-directory-self-service-signup.md) |
| Azure AD Premium 또는 EMS 평가판 사용 | [한 달 동안 Azure Active Directory Premium 체험](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Azure AD Premium 또는 EMS 라이선스를 PoC 사용자에게 할당했습니다. | [Azure Active Directory에서 사용자 본인 및 사용자의 사용자 라이선스](active-directory-licensing-get-started-azure-portal.md) |
| Azure AD 전역 관리자 자격 증명 | [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md) |
| 선택 사항이지만 강력하게 권장됨: 대체로서 병렬 랩 환경 | [Azure AD Connect에 대한 필수 구성 요소](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>디렉터리 동기화 - PHS(암호 해시 동기화) - 새 설치

예상 완료 시간: PoC 사용자가 1,000명 미만인 경우 1시간

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| Azure AD Connect를 실행할 서버 | [Azure AD Connect에 대한 필수 구성 요소](./connect/active-directory-aadconnect-prerequisites.md) |
| OU 및 보안 그룹의 동일한 도메인 및 부분에 있는 대상 POC 사용자 | [Azure AD Connect의 사용자 지정 설치](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| POC에 필요한 Azure AD Connect 기능이 식별됨 | [Active Directory와 Azure Active Directory 연결 - 동기화 기능 구성](./connect/active-directory-aadconnect.md#configure-sync-features) |
| 온-프레미스 및 클라우드 환경에 대한 자격 증명이 필요함  | [Azure AD Connect: 계정 및 사용 권한](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 최신 버전의 Azure AD Connect를 다운로드합니다. | [Download Microsoft Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594)(Microsoft Azure Active Directory Connect 다운로드) |
| 가장 간단한 경로로 Azure AD Connect를 설치합니다. Express <br/>1. 동기화 주기 시간을 최소화하기 위해 대상 OU를 필터링합니다.<br/>2. 온-프레미스 그룹에서 대상 사용자 집합을 선택합니다.<br/>3. 기타 POC 테마에 필요한 기능을 배포합니다. | [Azure AD Connect: 사용자 지정 설치: 도메인 및 OU 필터링](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Azure AD Connect: 사용자 지정 설치: 그룹 기반 필터링](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Azure AD Connect: Azure Active Directory와 온-프레미스 ID 통합: 동기화 기능 구성](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Azure AD Connect UI를 확인하고 실행 중인 프로필이 완료되었는지 확인합니다(가져오기, 동기화 및 내보내기). | [Azure AD Connect 동기화: Scheduler](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| [Azure AD management portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)(Azure AD 관리 포털)을 열고, “All Users”(모든 사용자) 블레이드로 이동하고, “Source of authority”(인증 원본) 열을 추가하고, 사용자가 나타나고 “Windows Server AD”에서 오는 것으로 제대로 표시되는지 확인합니다. | [Azure AD management portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)(Azure AD 관리 포털) |

### <a name="considerations"></a>고려 사항

1. [여기](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)에서 암호 해시 동기화의 보안 고려 사항을 살펴보세요.  파일럿 프로덕션 사용자에 대한 암호 해시 동기화가 옵션이 아닌 경우 다음 대안을 고려합니다.
   * 프로덕션 도메인에서 테스트 사용자를 만듭니다. 다른 계정을 동기화하지 않는지 확인합니다.
   * UAT 환경으로 이동합니다.
2.  페더레이션을 적용하려면 POC를 넘어서 페더레이션된 솔루션을 온-프레미스 ID 공급자와 연결할 때 비용이 발생한다는 것을 이해하고 이 비용을 찾고 있는 혜택과 비교하는 것이 좋습니다.
    * 중요한 경로에 있으므로 고가용성에 맞게 설계해야 합니다.
    * 용량 계획에 필요한 온-프레미스 서비스입니다.
    * 모니터링/유지 관리/패치에 필요한 온-프레미스 서비스입니다.

참고 항목: [Office 365 ID 및 Azure Active Directory 이해 - 페더레이션 ID](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>브랜딩

예상 완료 시간: 15분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 자산(이미지, 로고 등). 최고의 시각화를 위해 자산에 권장 크기가 있는지 확인합니다. | [Azure Active Directory에서 로그인 페이지에 회사 브랜딩 추가](active-directory-branding-custom-signon-azure-portal.md) |
| 선택 사항: 환경에 ADFS 서버가 있는 경우 서버에 액세스하여 웹 테마를 사용자 지정합니다. | [AD FS user sign-in customization](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization)(AD FS 사용자 로그인 사용자 지정) |
| 최종 사용자 로그인 환경을 실행할 클라이언트 컴퓨터 |  |
| 선택 사항: 환경의 유효성을 검사할 모바일 장치 |  |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Azure AD 관리 포털로 이동합니다. | [Azure AD Management Portal - Company Branding](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding)(Azure AD 관리 포털 - 회사 브랜딩) |
| 로그인 페이지에 대한 자산을 업로드합니다(대표 로고, 작은 로고, 레이블 등). 선택적으로 AD FS가 있는 경우 동일한 자산을 ADFS 로그인 페이지에 맞춥니다. | [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가: 사용자 지정 가능한 요소](customize-branding.md) |
| 변경 내용이 완전히 적용될 때까지 몇 분 정도 기다립니다. |  |
| POC 사용자 자격 증명으로 https://myapps.microsoft.com에 로그인 |  |
| 브라우저의 모양과 느낌을 확인합니다. | [로그인 및 액세스 패널 페이지에 회사 브랜딩 추가](customize-branding.md) |
| 필요한 경우 다른 장치에서 모양과 느낌을 확인합니다. |  |

### <a name="considerations"></a>고려 사항

사용자 지정 후에도 이전 모양과 느낌이 남아 있으면 브라우저 클라이언트 캐시를 비우고 작업을 다시 시도합니다.

## <a name="group-based-licensing"></a>그룹 기반 라이선싱

예상 완료 시간: 10분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 모든 POC 사용자가 보안 그룹에 속함(클라우드 또는 온-프레미스) | [Azure Active Directory에서 그룹 만들기 및 구성원 추가](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Azure AD 관리 포털에서 라이선스 블레이드로 이동합니다. | [Azure AD Management Portal: Licensing](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products)(Azure AD 관리 포털: 라이선싱) |
| POC 사용자가 포함된 보안 그룹에 라이선스를 할당합니다. | [Azure Active Directory에서 사용자의 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>고려 사항

문제가 있는 경우 [Azure Active Directory에서 라이선스 관리를 위해 그룹을 사용하여 시나리오, 제한 사항 및 알려진 문제](active-directory-licensing-group-advanced.md)로 이동합니다.

## <a name="saas-federated-sso-configuration"></a>SaaS 페더레이션 SSO 구성

예상 완료 시간: 60분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 사용 가능한 SaaS 응용 프로그램의 테스트 환경. 이 가이드에서는 ServiceNow를 예제로 사용합니다.<br/>테스트 인스턴스를 사용하여 기존 데이터 품질 및 매핑을 탐색할 때 마찰을 최소화하는 것이 좋습니다. | 테스트 인스턴스를 가져오는 과정을 시작하려면 https://developer.servicenow.com/app.do#!/home으로 이동 |
| ServiceNow 관리 콘솔에 대한 관리자 권한 | [자습서: ServiceNow와 Azure Active Directory 통합](active-directory-saas-servicenow-tutorial.md) |
| 응용 프로그램을 할당할 대상 사용자 집합. PoC 사용자가 포함된 보안 그룹을 사용하는 것이 좋습니다. <br/>그룹을 만들 수 없는 경우 사용자를 PoC에 대한 응용 프로그램에 직접 할당합니다. | [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Microsoft 문서에서 모든 행위자에게 자습서를 공유합니다.  | [자습서: ServiceNow와 Azure Active Directory 통합](active-directory-saas-servicenow-tutorial.md) |
| 작업 모임을 설정하고 각 행위자와 함께 자습서 단계를 따릅니다. | [자습서: ServiceNow와 Azure Active Directory 통합](active-directory-saas-servicenow-tutorial.md) |
| 필수 구성 요소에서 식별된 그룹에게 앱을 할당합니다. POC의 범위에 조건부 액세스가 있으면 나중에 다시 방문하여 MFA 등을 추가할 수 있습니다. <br/>이 작업은 프로비전 프로세스에서 시작됩니다(구성된 경우). |  [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md) <br/>[Azure Active Directory에서 그룹 만들기 및 구성원 추가](active-directory-groups-create-azure-portal.md) |
| Azure AD 관리 포털을 사용하여 갤러리에서 ServiceNow 응용 프로그램을 추가합니다.| [Azure AD management Portal: Enterprise Applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview)(Azure AD 관리 포털: 엔터프라이즈 응용 프로그램) <br/>[Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| ServiceNow 앱의 "Single Sign-On" 블레이드에서 "SAML 기반 로그인"을 사용하도록 설정합니다. |  |
| ServiceNow URL을 통해 “로그온 URL” 및 “식별자” 필드를 입력합니다.<br/>“새 인증서 활성화” 상자를 선택하고<br/>설정을 저장합니다. |  |
| 패널 아래쪽에서 “ServiceNow 구성” 블레이드를 열어 ServiceNow를 구성하기 위한 사용자 지정된 지침을 확인합니다. |  |
| 지침에 따라 ServiceNow를 구성합니다. |  |
| ServiceNow 앱의 “프로비전” 블레이드에서 “자동” 프로비전을 사용하도록 설정합니다. | [새 Azure Portal에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](active-directory-enterprise-apps-manage-provisioning.md) |
| 프로비전이 완료되는 동안 몇 분 정도 기다립니다.  그 사이에 프로비전 보고서를 확인할 수 있습니다. |  |
| 액세스할 수 있는 테스트 사용자로 https://myapps.microsoft.com/에 로그인 | [액세스 패널이란?](active-directory-saas-access-panel-introduction.md) |
| 방금 만든 응용 프로그램의 타일을 클릭합니다. 액세스 권한을 확인합니다. |  |
| 필요한 경우 응용 프로그램 사용 현황 보고서를 확인할 수 있습니다. 약간의 대기 시간이 있으므로 보고서에 트래픽이 표시될 때까지 잠시 기다려야 합니다. | [Azure Active Directory 포털의 로그인 활동 보고서: 관리되는 응용 프로그램의 사용량](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory 보고서 보존 정책](active-directory-reporting-retention.md) |

### <a name="considerations"></a>고려 사항

1. 위의 [자습서](active-directory-saas-servicenow-tutorial.md)는 이전 Azure AD 관리 환경을 참조합니다. 그러나 PoC는 [빠른 시작](active-directory-enterprise-apps-whats-new-azure-portal.md#quickstart-get-going-with-your-new-application-right-away) 환경을 기반으로 합니다.
2. 대상 응용 프로그램이 갤러리에 없으면 “사용자 고유 앱 가져오기”를 사용할 수 있습니다. 참고 항목: [Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능: 한 곳에서 사용자 지정 응용 프로그램 추가](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS 암호 SSO 구성

예상 완료 시간: 15분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| SaaS 응용 프로그램에 대한 테스트 환경. 암호 SSO의 예로는 HipChat 및 Twitter가 있습니다. 다른 응용 프로그램의 경우 HTML 로그인 폼이 포함된 페이지의 정확한 URL이 필요합니다. | [Microsoft Azure Marketplace의 Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Microsoft Azure Marketplace의 HipChat](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| 응용 프로그램에 대한 테스트 계정. | [Twitter 가입](https://twitter.com/signup?lang=en)<br/>[Sign Up for Free: HipChat](https://www.hipchat.com/sign_up)(무료 가입: HipChat) |
| 응용 프로그램을 할당할 대상 사용자 집합. 사용자가 포함된 보안 그룹을 사용하는 것이 좋습니다. | [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md) |
| Internet Explorer, Chrome 또는 Firefox용 액세스 패널 확장을 배포할 컴퓨터에 대한 로컬 관리자 권한 | [Access Panel Extension for IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)(IE용 액세스 패널 확장)<br/>[Access Panel Extension for Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)(Chrome용 액세스 패널 확장)<br/>[Firefox용 액세스 패널 확장](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 브라우저 확장을 설치합니다. | [Access Panel Extension for IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)(IE용 액세스 패널 확장)<br/>[Access Panel Extension for Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)(Chrome용 액세스 패널 확장)<br/>[Access Panel Extension for Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409)(Firefox용 액세스 패널 확장) |
| 갤러리에서 응용 프로그램을 구성합니다. | [Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능: 새롭고 개선된 응용 프로그램 갤러리](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| 암호 SSO를 구성합니다. | [새 Azure Portal에서 엔터프라이즈 앱에 대한 Single Sign-On 관리: 암호 기반 로그온](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| 필수 구성 요소에서 식별된 그룹에게 앱을 할당합니다. | [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md) |
| 액세스할 수 있는 테스트 사용자로 https://myapps.microsoft.com/에 로그인 |  |
| 방금 만든 응용 프로그램의 타일을 클릭합니다. | [액세스 패널이란?: ID 프로비전 없는 암호 기반 SSO](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| 응용 프로그램 자격 증명을 제공합니다. | [액세스 패널이란?: ID 프로비전 없는 암호 기반 SSO](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| 브라우저를 닫고 로그인을 반복합니다. 이때 사용자는 응용 프로그램에 원활하게 액세스할 수 있어야 합니다. |  |
| 필요한 경우 응용 프로그램 사용 현황 보고서를 확인할 수 있습니다. 약간의 대기 시간이 있으므로 보고서에 트래픽이 표시될 때까지 잠시 기다려야 합니다. | [Azure Active Directory 포털의 로그인 활동 보고서: 관리되는 응용 프로그램의 사용량](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory 보고서 보존 정책](active-directory-reporting-retention.md) |

### <a name="considerations"></a>고려 사항

대상 응용 프로그램이 갤러리에 없으면 “사용자 고유 앱 가져오기”를 사용할 수 있습니다. 참고 항목: [Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능: 한 곳에서 사용자 지정 응용 프로그램 추가](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 다음 요구 사항을 고려하세요.
   * 응용 프로그램에 알려진 로그인 URL이 있어야 합니다.
   * 로그인 페이지에 브라우저 확장이 자동으로 채워질 수 있는 하나 이상의 텍스트 필드가 포함된 HTML 폼이 있어야 합니다. 적어도 사용자 이름 및 암호를 포함해야 합니다.

## <a name="saas-shared-accounts-configuration"></a>SaaS 고유 계정 구성

예상 완료 시간: 30분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 사전에 대상 응용 프로그램 및 정확한 로그인 URL 목록 제공. 예를 들어 Twitter를 사용할 수 있습니다. | [Microsoft Azure Marketplace의 Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Sign up for Twitter](https://twitter.com/signup?lang=en)(Twitter 가입) |
| 이 SaaS 응용 프로그램에 대한 공유 자격 증명. | [Azure AD를 사용한 계정 공유](active-directory-sharing-accounts.md)<br/>[이제 미리 보기에서 Facebook, Twitter 및 LinkedIn에 대해 Azure AD 자동화된 암호 롤오버! - Enterprise Mobility 및 Security 블로그](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| 같은 계정에 액세스할 두 명 이상의 팀원에 대한 자격 증명. 이 팀원은 보안 그룹에 속해야 합니다. | [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md) |
| Internet Explorer, Chrome 또는 Firefox용 액세스 패널 확장을 배포할 컴퓨터에 대한 로컬 관리자 권한 | [Access Panel Extension for IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)(IE용 액세스 패널 확장)<br/>[Access Panel Extension for Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)(Chrome용 액세스 패널 확장)<br/>[Firefox용 액세스 패널 확장](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 브라우저 확장을 설치합니다. | [Access Panel Extension for IE](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)(IE용 액세스 패널 확장)<br/>[Access Panel Extension for Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)(Chrome용 액세스 패널 확장)<br/>[Access Panel Extension for Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409)(Firefox용 액세스 패널 확장) |
| 갤러리에서 응용 프로그램을 구성합니다. | [Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능: 새롭고 개선된 응용 프로그램 갤러리](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| 암호 SSO를 구성합니다. | [새 Azure Portal에서 엔터프라이즈 앱에 대한 Single Sign-On 관리: 암호 기반 로그온](active-directory-enterprise-apps-manage-sso.md#password-based-sign-on) |
| 필수 구성 요소에서 식별된 그룹에게 자격 증명을 할당할 때 앱을 할당합니다. | [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md) |
| **같은 공유 계정**으로 앱에 액세스하는 서로 다른 사용자로 로그인합니다.  |  |
| 필요한 경우 응용 프로그램 사용 현황 보고서를 확인할 수 있습니다. 약간의 대기 시간이 있으므로 보고서에 트래픽이 표시될 때까지 잠시 기다려야 합니다. | [Azure Active Directory 포털의 로그인 활동 보고서: 관리되는 응용 프로그램의 사용량](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Azure Active Directory 보고서 보존 정책](active-directory-reporting-retention.md) |


### <a name="considerations"></a>고려 사항

대상 응용 프로그램이 갤러리에 없으면 “사용자 고유 앱 가져오기”를 사용할 수 있습니다. 참고 항목: [Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능: 한 곳에서 사용자 지정 응용 프로그램 추가](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 다음 요구 사항을 고려하세요.
   * 응용 프로그램에 알려진 로그인 URL이 있어야 합니다.
   * 로그인 페이지에 브라우저 확장이 자동으로 채워질 수 있는 하나 이상의 텍스트 필드가 포함된 HTML 폼이 있어야 합니다. 적어도 사용자 이름 및 암호를 포함해야 합니다.

## <a name="app-proxy-configuration"></a>앱 프록시 구성

예상 완료 시간: 20분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 사용자가 전역 관리자인 Microsoft Azure AD 기본 또는 프리미엄 구독 및 Azure AD 디렉터리 | [Azure Active Directory 버전](active-directory-whatis.md) |
| 원격 액세스를 사용하도록 구성할 온-프레미스에서 호스트된 웹 응용 프로그램 |  |
| 응용 프로그램 프록시 커넥터를 설치할 수 있는 Windows Server 2012 R2 또는 Windows 8.1 이상을 실행하는 서버 | [Azure AD 응용 프로그램 프록시 커넥터 이해](manage-apps/application-proxy-connectors.md) |
| 방화벽이 경로에 있는 경우 커넥터가 응용 프로그램 프록시에 HTTPS(TCP) 요청을 할 수 있도록 방화벽이 열려 있는지 확인합니다. | [Azure Portal에서 응용 프로그램 프록시 사용: 응용 프로그램 프록시 필수 구성 요소](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| 조직에서 프록시 서버를 사용하여 인터넷에 연결하려면 구성하는 방법에 대한 세부 내용은 기존 온-프레미스 프록시 서버로 작업 블로그 게시물을 살펴보세요. | [기존 온-프레미스 프록시 서버 작업](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 서버에 커넥터를 설치합니다. | [Azure Portal에서 응용 프로그램 프록시 사용: 커넥터 설치 및 등록](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| 온-프레미스 응용 프로그램을 Azure AD에 응용 프로그램 프록시 응용 프로그램으로 게시합니다. | [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md) |
| 테스트 사용자를 할당합니다. | [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시: 테스트 사용자 추가](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| 필요한 경우 사용자에 대한 Single Sign-On 환경을 구성합니다. | [Azure AD 응용 프로그램 프록시를 사용하여 Single Sign-On 제공](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| MyApps 포털에 할당된 사용자로 로그인하여 앱을 테스트합니다. | https://myapps.microsoft.com |

### <a name="considerations"></a>고려 사항

1. 커넥터를 회사 네트워크에 포함하는 것이 좋지만 클라우드에 포함하면 성능이 향상되는 경우도 있습니다. 참고 항목: [Azure Active Directory 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항](manage-apps/application-proxy-network-topology.md)
2. 보안에 대한 자세한 내용과 아웃바운드 연결만 유지 관리하는 방식으로 특별히 안전한 원격 액세스 솔루션을 제공하는 방법은 [Azure AD 응용 프로그램 프록시를 사용하여 앱에 원격으로 액세스하는 경우 보안 고려 사항](manage-apps/application-proxy-security.md)을 참조하세요.

## <a name="generic-ldap-connector-configuration"></a>일반 LDAP 커넥터 구성

예상 완료 시간: 60분

> [!IMPORTANT]
> 이 작업은 FIM/MIM 사용 경험이 요구되는 고급 구성입니다. 프로덕션에 사용되는 경우 이 구성에 대한 질문이 있으면 [프리미어 지원](https://support.microsoft.com/premier)을 참고하는 것이 좋습니다.

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 설치 및 구성된 Azure AD Connect | 문서 블록: [디렉터리 동기화 - 암호 해시 동기화](#directory-synchronization--password-hash-sync-phs--new-installation) |
| ADLDS 인스턴스 모임 요구 사항 | [일반 LDAP 커넥터 기술 참조: 일반 LDAP 커넥터의 개요](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| 사용자가 사용 중인 작업 목록 및 이러한 작업과 연결된 특성 | [Azure AD Connect 동기화: Azure Active Directory에 동기화된 특성](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 일반 LDAP 커넥터를 추가합니다. | [일반 LDAP 커넥터 기술 참조: 새 커넥터 만들기](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| 생성된 커넥터에 대한 실행 프로필을 만듭니다(전체 가져오기, 델타 가져오기, 전체 동기화, 델타 동기화, 내보내기) | [Create a Management Agent Run Profile](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)(관리 에이전트 실행 프로필 만들기)<br/> [Azure AD Connect Sync Service Manager에서 커넥터 사용](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| 전체 가져오기 프로필을 실행하고 커넥터 공간에 개체가 있는지 확인합니다. | [Search for a Connector Space Object](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)(커넥터 공간 개체 검색)<br/>[Azure AD Connect Sync Service Manager에서 커넥터 사용: 커넥터 공간 검색](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Metaverse의 개체에 작업에 필요한 특성이 포함되도록 동기화 규칙을 만듭니다. | [Azure AD Connect 동기화: 기본 구성 변경에 대한 모범 사례: 동기화 규칙 변경](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect 동기화: 선언적 프로비전 이해](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect Sync: 선언적 프로비전 식 이해](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| 전체 동기화 주기를 시작합니다. | [Azure AD Connect 동기화: Scheduler: Scheduler 시작](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| 문제 발생 시 문제 해결을 수행합니다. | [Azure AD와 동기화되지 않는 개체 문제 해결](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| LDAP 사용자가 응용 프로그램에 로그인하고 액세스할 수 있는지 확인합니다. | https://myapps.microsoft.com |

### <a name="considerations"></a>고려 사항

> [!IMPORTANT]
> 이 작업은 FIM/MIM 사용 경험이 요구되는 고급 구성입니다. 프로덕션에 사용되는 경우 이 구성에 대한 질문이 있으면 [프리미어 지원](https://support.microsoft.com/premier)을 참고하는 것이 좋습니다.

## <a name="groups---delegated-ownership"></a>그룹 - 위임된 소유권

예상 완료 시간: 10분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| SaaS 응용 프로그램(페더레이션 SSO 또는 암호 SSO)이 이미 구성됨 | 문서 블록: [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration) |
| #1에서 응용 프로그램에 대한 액세스 권한이 할당된 클라우드 그룹이 식별됨 | 문서 블록: [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration) <br/>[Azure Active Directory에서 그룹 만들기 및 구성원 추가](active-directory-groups-create-azure-portal.md) |
| 그룹 소유자에 대한 자격 증명을 사용할 수 있음 | [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md) |
| 앱에 액세스하는 정보 근로자에 대한 자격 증명이 식별됨 | [액세스 패널이란?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 응용 프로그램에 대한 액세스 권한이 부여된 그룹을 식별하고 해당 그룹의 소유자를 구성합니다.| [Azure Active Directory에서 그룹의 설정 관리](active-directory-groups-settings-azure-portal.md) |
| 그룹 소유자로 로그인하여 액세스 패널의 [그룹] 탭에서 그룹 멤버 자격을 확인합니다. | [Azure Active Directory Groups Management page](https://account.activedirectory.windowsazure.com/r/#/groups)(Azure Active Directory 그룹 관리 페이지) |
| 테스트할 정보 근로자를 추가합니다. |  |
| 정보 근로자로 로그인하여 타일을 사용할 수 있는지 확인합니다. | [액세스 패널이란?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>고려 사항

응용 프로그램의 프로비전이 사용되는 경우 정보 근로자로 응용 프로그램에 액세스하기 전에 프로비전이 완료될 때까지 몇 분 정도 기다려야 할 수 있습니다.

## <a name="saas-and-identity-lifecycle"></a>SaaS 및 ID 수명 주기

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| SaaS 응용 프로그램(페더레이션 SSO 또는 암호 SSO)이 이미 구성됨 | 문서 블록: [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration) |
| #1에서 응용 프로그램에 대한 액세스 권한이 할당된 클라우드 그룹이 식별됨 | 문서 블록: [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration) <br/>[Azure Active Directory에서 그룹 만들기 및 구성원 추가](active-directory-groups-create-azure-portal.md) |
| 앱에 액세스하는 정보 근로자에 대한 자격 증명이 식별됨 | [액세스 패널이란?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 앱이 할당된 그룹에서 사용자를 제거합니다. | [Azure Active Directory 테넌트의 사용자에 대한 그룹 멤버 자격 관리](active-directory-groups-members-azure-portal.md) |
| 프로비전이 해제되는 동안 몇 분 정도 기다립니다. | [Azure AD에서 SaaS 앱 사용자를 자동으로 프로비전: 자동 프로비전은 어떻게 수행되나요?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| 별도의 브라우저 세션에서 정보 근로자로 My Apps 포털에 로그인하고 타일이 없는지 확인합니다. | http://myapps.microsoft.com |


### <a name="considerations"></a>고려 사항

휴가자에 대한 PoC 시나리오 및/또는 휴가 시나리오를 추정해 보세요. 사용자가 온-프레미스 AD에서 사용하지 않도록 설정되거나 제거된 경우에는 더 이상 SaaS 응용 프로그램에 로그인할 방법이 없습니다.

## <a name="self-service-access-to-application-management"></a>응용 프로그램 관리에 대한 셀프 서비스 액세스

예상 완료 시간: 10분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 보안 그룹의 일부로 응용 프로그램에 대한 액세스 요청할 POC 사용자 식별 | 문서 블록: [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration) |
| 배포된 대상 응용 프로그램 | 문서 블록: [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Azure AD 관리 포털의 엔터프라이즈 응용 프로그램 블레이드로 이동합니다. | [Azure AD Management Portal: Enterprise Applications](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)(Azure AD 관리 포털: 엔터프라이즈 응용 프로그램) |
| 셀프 서비스를 사용하여 필수 구성 요소에서 응용 프로그램 구성 | [Azure Active Directory의 새로운 엔터프라이즈 응용 프로그램 관리 기능: 셀프 서비스 응용 프로그램 액세스 구성](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| 정보 근로자로 My Apps 포털에 로그인합니다. | http://myapps.microsoft.com |
| 페이지 위쪽에서 “+앱 추가” 단추를 확인합니다. 이 단추를 사용하여 앱에 액세스합니다. |  |

### <a name="considerations"></a>고려 사항

선택된 응용 프로그램에 프로비전 요구 사항이 있을 수 있으므로 앱으로 바로 이동하면 오류가 발생할 수 있습니다. 선택된 응용 프로그램이 Azure Ad를 통해 프로비전을 지원하고 프로비전이 구성되어 있으면 이를 사용하여 종단 간 작동하는 전체 흐름을 표시할 수 있습니다. 추가 권장 사항은 [SaaS 페더레이션 SSO 구성](#saas-federated-sso-configuration)에 대한 문서 블록을 참조하세요.

## <a name="self-service-password-reset"></a>셀프 서비스 암호 재설정

예상 완료 시간: 15분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 테넌트에서 셀프 서비스 암호 관리 사용. | [IT 관리자에 대한 Azure Active Directory 암호 재설정](active-directory-passwords-update-your-own-password.md) |
| 온-프레미스에서 암호를 관리하도록 암호 쓰기 저장 사용. 이 작업에는 특정 Azure AD Connect 버전이 필요합니다. | [암호 쓰기 저장 필수 구성 요소](authentication/howto-sspr-writeback.md) |
| 이 기능을 사용할 PoC 사용자를 식별하고 보안 그룹의 구성원인지 확인. 사용자는 기능을 완전히 소개할 수 있는 관리자가 아닌 사용자여야 합니다. | [사용자 지정: Azure AD 암호 관리: 암호 재설정에 대한 액세스 제한](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Azure AD 관리 포털: 암호 재설정으로 이동합니다. | [Azure AD Management Portal: Password Reset](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)(Azure AD 관리 포털: 암호 재설정) |
| 암호 재설정 정책을 확인합니다. POC를 위해 전화 통화 및 Q&A를 사용할 수 있습니다. 액세스 패널에 로그인하는 데 등록이 필요하도록 설정하는 것이 좋습니다. |  |
| 로그아웃하고 정보 근로자로 로그인합니다. |  |
| 2단계에서 구성한 셀프 서비스 암호 재설정 데이터를 제공합니다. | https://aka.ms/ssprsetup |
| 브라우저를 닫습니다. |  |
| 4단계에서 사용한 정보 근로자로 로그인 프로세스를 다시 시작합니다. |  |
| 암호를 재설정합니다. | [고유 암호 업데이트: 내 암호 재설정](active-directory-passwords-update-your-own-password.md) |
| 새 암호로 Azure AD 및 온-프레미스 리소스에 로그인해 보세요. |  |

### <a name="considerations"></a>고려 사항

1. Azure AD Connect를 업그레이드할 때 문제가 발생하면 클라우드 계정에 사용하거나 별도의 환경에 대한 데모로 만들어 보세요.
2. 관리자에게 다른 정책이 있는데 관리자 계정을 사용하여 암호를 재설정하면 PoC가 손상되고 혼동을 일으킬 수 있습니다. 재설정 작업을 테스트할 때 일반 사용자 계정을 사용해야 합니다.


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>전화로 Azure Multi-Factor Authentication

예상 완료 시간: 10분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| MFA를 사용할 POC 사용자 식별  |  |
| MFA 챌린지를 위한 수신 상태가 좋은 전화  | [Azure Multi-Factor Authentication 정의](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Azure AD 관리 포털에서 “사용자 및 그룹” 블레이드로 이동합니다. | [Azure AD Management Portal: Users and groups](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/)(Azure AD 관리 포털: 관리 포털) |
| “모든 사용자” 블레이드를 선택합니다. |  |
| 위쪽 막대에서 “Multi-Factor Authentication” 단추를 선택합니다. | Azure MFA 포털에 대한 직접 URL: https://aka.ms/mfaportal |
| “사용자” 설정에서 PoC 사용자를 선택하고 MFA에 대해 사용하도록 설정합니다. | [Azure Multi-Factor Authentication의 사용자 상태](authentication/howto-mfa-userstates.md) |
| PoC 사용자로 로그인하고 증명 프로세스를 진행합니다.  |  |

### <a name="considerations"></a>고려 사항

1. 이 문서 블록의 PoC 단계에서는 로그인할 때마다 사용자에 대한 MFA를 명시적으로 설정합니다. 더 많은 대상 시나리오에서 MFA를 적용하는 ID 보호 및 조건부 액세스와 같은 다른 도구가 있습니다. POC에서 프로덕션으로 이동할 때 이를 고려할 수 있습니다.
2. 이 문서 블록의 PoC 단계에서는 편의를 위해 전화 통화를 MFA 방법으로 명시적으로 사용합니다. POC에서 프로덕션으로 전환할 때 가능하면 항상 [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md)와 같은 응용 프로그램을 두 번째 요소로 사용하는 것이 좋습니다.
참고 항목: [DRAFT NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)(DRAFT NIST 특별 간행물 800-63B)

## <a name="mfa-conditional-access-for-saas-applications"></a>SaaS 응용 프로그램에 대한 MFA 조건부 액세스

예상 완료 시간: 10분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 정책을 적용할 PoC 사용자 식별. 조건부 액세스 정책의 범위를 지정하려면 이러한 사용자가 보안 그룹에 있어야 합니다. | [SaaS 페더레이션된 SSO 구성](#saas-federated-sso-configuration) |
| SaaS 응용 프로그램이 이미 구성됨 |  |
| PoC 사용자가 응용 프로그램에 할당되어 있음 |  |
| POC 사용자에 대한 자격 증명을 사용할 수 있음 |  |
| POC 사용자가 MFA에 등록됨. 수신 상태가 좋은 전화 사용 | https://aka.ms/ssprsetup |
| 내부 네트워크의 장치. 내부 주소 범위에 구성된 IP 주소 | 사용자 IP 주소 찾기: https://www.bing.com/search?q=what%27s+my+ip |
| 외부 네트워크의 장치(이동 통신 사업자의 모바일 네트워크를 사용하는 전화 가능) |  |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Azure AD 관리 포털: 조건부 액세스 블레이드로 이동합니다. | [Azure AD Management Portal: Conditional Access](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)(Azure AD 관리 포털: 조건부 액세스) |
| 조건부 액세스 정책을 만듭니다.<br/>- “사용자 및 그룹”에서 PoC 사용자를 대상으로 지정합니다.<br/>- “클라우드 앱”에서 PoC 응용 프로그램을 대상으로 지정합니다.<br/>- “조건” -> “위치”에서 신뢰할 수 있는 위치를 제외한 모든 위치를 대상으로 지정합니다. **참고:** 신뢰할 수 있는 IP는 [MFA Portal](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)(MFA 포털)에서 구성됩니다.<br/>- “권한 부여”에서 다단계 인증이 필요합니다. | [Azure Active Directory에서 조건부 액세스 시작: 정책 구성 단계](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| 회사 네트워크 내부에서 응용 프로그램에 액세스합니다. | [Azure Active Directory에서 조건부 액세스 시작: 정책 테스트](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| 공용 네트워크에서 응용 프로그램에 액세스합니다. | [Azure Active Directory에서 조건부 액세스 시작: 정책 테스트](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>고려 사항

페더레이션을 사용 중이면 온-프레미스 IdP(ID 공급자)를 사용하여 클레임을 통해 내부/외부 회사 네트워크 상태를 알릴 수 있습니다. 대규모 조직에서 평가 및 관리하기 복잡할 수 있는 IP 주소 목록을 관리할 필요 없이 이 기술을 사용할 수 있습니다. 이 경우 “네트워크 로밍” 시나리오(사용자가 내부 네트워크에서 로그인하고 로그인된 동안 커피숍 등의 위치 전환)에 대한 계정이 필요하고 의미를 이해해야 합니다. 참고 항목: [Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안 유지: 페더레이션 사용자를 위한 신뢰할 수 있는 IP](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>PIM(Privileged Identity Management)

예상 완료 시간: 15분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| PIM의 POC에 포함될 전역 관리자 식별 | [Azure AD Privileged Identity Management 시작](active-directory-privileged-identity-management-getting-started.md) |
| 보안 관리자가 될 전역 관리자 식별 | [Azure AD Privileged Identity Management 시작](active-directory-privileged-identity-management-getting-started.md)<br/> [Azure Active Directory PIM의 다른 관리자 역할](active-directory-privileged-identity-management-roles.md) |
| 선택 사항: 전역 관리자에게 PIM에서 메일 알림을 실행할 메일 권한이 있는지 확인합니다. | [Azure AD Privileged Identity Management란?: 역할 활성화 설정 구성](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| https://portal.azure.com에 GA(전역 관리자)로 로그인하고 PIM 블레이드를 부트스트랩합니다. 이 단계를 수행하는 전역 관리자는 보안 관리자로 시드됩니다.  이 행위자 GA1을 호출해 보겠습니다. | [Azure AD Privileged Identity Management에서 보안 마법사 사용](active-directory-privileged-identity-management-security-wizard.md) |
| 전역 관리자를 식별하고 영구에서 적격으로 이동합니다. 분명한 설명을 위해 이 관리자는 1단계에서 사용된 관리자와 달라야 합니다. 이 행위자 GA2를 호출해 보겠습니다. | [Azure AD Privileged Identity Management: 사용자 역할을 추가 또는 제거하는 방법](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Azure AD Privileged Identity Management란?: 역할 활성화 설정 구성](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| 이제 GA2로 https://portal.azure.com에 로그인하고 “사용자 설정”을 변경해 봅니다. 일부 옵션이 회색으로 표시되는 것을 알 수 있습니다. | |
| 새 탭 및 3단계와 같은 세션에서 https://portal.azure.com로 이동하고 PIM 블레이드를 대시보드에 추가합니다. | [Azure AD Privileged Identity Management 역할을 활성화하거나 비활성화하는 방법: Privileged Identity Management 응용 프로그램 추가](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| 전역 관리자 역할에 대한 활성화 요청 | [Azure AD Privileged Identity Management 역할을 활성화하거나 비활성화하는 방법: 역할 활성화](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| GA2가 MFA에 등록한 적이 없는 경우에는 Azure MFA에 대한 등록이 필요합니다. |  |
| 3단계의 원래 탭으로 돌아가서 브라우저에서 [새로 고침] 단추를 클릭합니다. 이제 “사용자 설정”을 변경할 수 있습니다. | |
| 필요한 경우 전역 관리자에 대한 메일을 사용하도록 설정하면 GA1 및 GA2의 받은 편지함을 확인하고 활성화되는 역할에 대한 알림을 확인할 수 있습니다. |  |
| 8 감사 기록을 확인하고 보고서를 검토하여 GA2의 권한 상승이 표시되는지 확인합니다. | [Azure AD Privileged Identity Management란?: 역할 활동 검토](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>고려 사항

이 기능은 Azure AD Premium P2 및/또는 EMS E5에 포함됩니다.

## <a name="discovering-risk-events"></a>위험 이벤트 검색

예상 완료 시간: 20분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| Tor 브라우저가 다운로드 및 설치된 장치 | [Download Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads)(Tor 브라우저 다운로드) |
| 로그인을 수행할 POC 사용자 권한 | [Azure Active Directory Identity Protection 플레이 북](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| Tor 브라우저를 엽니다. | [Download Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads)(Tor 브라우저 다운로드) |
| POC 사용자 계정으로 https://myapps.microsoft.com에 로그인 | [Azure Active Directory ID 보호 플레이 북: 위험 이벤트 시뮬레이트](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| 5~7분 정도 기다립니다. |  |
| 전역 관리자로 https://portal.azure.com에 로그인하고 ID 보호 블레이드 열기 | https://aka.ms/aadipgetstarted |
| 위험 이벤트 블레이드를 엽니다. “익명 IP 주소에서 로그인” 아래에 있는 항목을 확인해야 합니다.  | [Azure Active Directory Identity Protection 플레이 북: 위험 이벤트 시뮬레이트](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>고려 사항

이 기능은 Azure AD Premium P2 및/또는 EMS E5에 포함됩니다.

## <a name="deploying-sign-in-risk-policies"></a>로그인 위험 정책 배포

예상 완료 시간: 10분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| Tor 브라우저가 다운로드 및 설치된 장치 | [Download Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads)(Tor 브라우저 다운로드) |
| 로그인 테스트를 수행할 POC 사용자 권한 |  |
| POC 사용자가 MFA에 등록됨. 수신 상태가 좋은 전화를 사용해야 함 | 문서 블록: [전화로 Azure Multi-Factor Authentication](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| 전역 관리자로 https://portal.azure.com에 로그인하고 ID 보호 블레이드 열기 | https://aka.ms/aadipgetstarted |
| 다음과 같이 로그인 위험 정책을 사용하도록 설정합니다.<br/>- 할당 대상: POC 사용자<br/>- 조건: 로그인 위험 중간 이상(익명 위치에서 로그인을 중간 위험 수준으로 간주함)<br/>- 컨트롤: MFA 필요 | [Azure Active Directory ID 보호 플레이 북: 로그인 위험](active-directory-identityprotection-playbook.md) |
| Tor 브라우저를 엽니다. | [Download Tor Browser](https://www.torproject.org/projects/torbrowser.html.en#downloads)(Tor 브라우저 다운로드) |
| PoC 사용자 계정으로 https://myapps.microsoft.com에 로그인 |  |
| MFA 챌린지를 확인합니다. | [Azure AD ID 보호를 사용하는 로그인 환경: 위험한 로그인 복구](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>고려 사항

이 기능은 Azure AD Premium P2 및/또는 EMS E5에 포함됩니다. 위험 이벤트에 대한 자세한 내용은 [Azure Active Directory 위험 이벤트](active-directory-reporting-risk-events.md)를 참조하세요.

## <a name="configuring-certificate-based-authentication"></a>인증 기반 인증서 구성

예상 완료 시간: 20분

### <a name="pre-requisites"></a>필수 조건

| 필수 구성 요소 | 리소스 |
| --- | --- |
| 엔터프라이즈 PKI에서 사용자 인증서가 프로비전된 장치(Windows, iOS 또는 Android) | [사용자 인증서 배포](https://msdn.microsoft.com/library/cc770857.aspx) |
| ADFS를 통해 페더레이션된 Azure AD 도메인 | [Azure AD Connect 및 페더레이션](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Active Directory 인증서 서비스 개요](https://technet.microsoft.com/library/hh831740.aspx)|
| iOS 장치의 경우 Microsoft Authenticator 앱이 설치됨 | [Microsoft Authenticator 앱 시작](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>단계

| 단계 | 리소스 |
| --- | --- |
| ADFS에서 “인증서 인증”을 사용하도록 설정합니다. | [Configure Authentication Policies: To configure primary authentication globally in Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2)(인증 정책 구성: Windows Server 2012 R2에서 기본 인증을 전역으로 구성하려면) |
| 선택 사항: Azure AD에서 Exchange Active Sync 클라이언트에 대해 인증서 인증을 사용하도록 설정합니다. | [Azure Active Directory에서 인증서 기반 인증 시작](active-directory-certificate-based-authentication-get-started.md) |
| 액세스 패널로 이동하고 사용자 인증서를 사용하여 인증합니다. | https://myapps.microsoft.com |

### <a name="considerations"></a>고려 사항

이 배포의 주의 사항에 대한 자세한 내용은 [ADFS: Certificate Authentication with Azure AD & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)(ADFS: Azure AD 및 Office 365를 사용하는 인증서 인증)를 참조하세요.



> [!NOTE]
> 사용자 인증서 소유는 보호되어야 합니다. 장치를 관리하거나 스마트 카드의 경우 PIN을 사용합니다.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
