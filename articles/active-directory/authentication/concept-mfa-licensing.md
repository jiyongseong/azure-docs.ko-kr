---
title: Azure MFA 버전 및 사용 계획 | Microsoft Docs
description: Multi-Factor Authentication 클라이언트 및 사용 가능한 다양한 방법과 버전에 대해 설명합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 08/25/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: ec1a3e7e3e8969e161854a9f386d4b2e767ee48a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 획득 방법

계정을 보호하려는 경우 두 단계 인증 과정이 조직 간에 표준화되어야 합니다. 이 기능은 리소스에 대해 액세스 권한이 있는 관리자 계정에 대해 특히 중요합니다. 따라서 Microsoft는 Office 365 및 Azure 관리자에 대해 기본적인 2단계 인증 기능을 추가 비용 없이 제공합니다. 관리자를 위한 기능을 업그레이드하거나 두 단계 인증을 나머지 사용자에게 확장하려면 Azure Multi-Factor Authentication을 구매할 수 있습니다. 

이 문서에서는 관리자에게 제공되는 버전과 전체 Azure MFA 버전 간의 차이를 설명합니다. 전체 Azure MFA 제품을 배포할 준비가 되었으면 뒷부분의 섹션에서 구현 옵션과 Microsoft에서 사용량을 계산하는 방법을 설명합니다.


>[!IMPORTANT]
>이 문서는 Azure Multi-Factor Authentication을 구입하는 여러 가지 방법을 이해하는 데 도움이 됩니다. 가격 책정 및 대금 청구에 대한 자세한 내용은 항상 [Multi-Factor Authentication 가격 페이지](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)를 참조하세요.

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 사용 가능한 버전

다음 표에서는 세 가지 버전의 Multi-Factor Authentication 간 차이점을 설명합니다.

| 버전 | 설명 |
| --- | --- |
| Office 365용 Multi-Factor Authentication |이 버전은 Office 365 응용프로그램에서만 작동되며 Office 365 포털에서 관리됩니다. 관리자는 [2단계 인증을 사용하여 Office 365 리소스의 보안을 유지](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)할 수 있습니다. 이 버전은 Office 365 구독에 포함되어 있습니다. |
| Azure AD 관리자를 위한 Multi-Factor Authentication | Azure AD 테넌트에서 전역 관리자 역할이 할당된 사용자는 추가 비용 없이 Azure AD 전역 관리자 계정에 2단계 인증을 사용하도록 설정할 수 있습니다.|
| Azure Multi-Factor Authentication | 대개 "전체" 버전이라고 하는 Azure Multi-factor Authentication은 가장 다양한 기능을 제공합니다. [Azure Portal](https://portal.azure.com), 고급 보고, 다양한 온-프레미스 및 클라우드 응용 프로그램 지원을 통해 추가 구성 옵션을 제공합니다. Azure Multi-Factor Authentication은 [Azure Active Directory Premium 계획](https://www.microsoft.com/cloud-platform/azure-active-directory-features) 및 [Enterprise Mobility + Security 계획](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)에 포함되며 클라우드 또는 온-프레미스에 배포할 수 있습니다. |

## <a name="feature-comparison-of-versions"></a>버전 기능 비교
다음 표에서 다양한 버전의 Azure Multi-Factor Authentication에서 사용 가능한 기능의 목록을 제공합니다.

> [!NOTE]
> 다음은 각 Multi-Factor Authentication에 포함된 기능을 비교하는 테이블입니다. 완전한 Azure Multi-Factor Authentication 서비스를 사용하는 경우 [클라우드 또는 온-프레미스에서 MFA를 사용하는지](concept-mfa-whichversion.md)에 따라 일부 기능을 사용할 수 없을 수도 있습니다.


| 기능 | Office 365용 Multi-Factor Authentication | Azure AD 관리자를 위한 Multi-Factor Authentication | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| MFA를 사용하여 Azure AD 관리자 계정 보호 |● |● (Azure AD 전역 관리자 계정에만 해당) |● |
| 두 번째 단계로 모바일 앱 |● |● |● |
| 두 번째 단계로 전화 통화 |● |● |● |
| 두 번째 단계로 SMS |● |● |● |
| MFA를 지원하지 않는 클라이언트에 대한 앱 암호 |● |● |● |
| 확인 방법에 대한 관리자 제어 |● |● |● |
| MFA를 사용하여 비관리자 계정 보호 |● (Office 365 응용 프로그램만 해당) | |● |
| PIN 모드 | | |● |
| 사기 행위 경고 | | |● |
| MFA 보고서 | | |● |
| 일회성 바이패스 | | |● |
| 전화 통화에 대한 사용자 지정 인사말 | | |● |
| 전화 통화에 대한 사용자 지정 발신자 번호 | | |● |
| 신뢰할 수 있는 IP | | |● |
| 신뢰할 수 있는 장치에 대한 MFA 기억 |● |● |● |
| MFA SDK | | |● (사용되지 않음) | 
| 온-프레미스 응용 프로그램에 대한 MFA | | |● |

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Azure AD 관리자에 Azure Multi-Factor Authentication을 설정하는 방법
Azure AD 테넌트에서 전역 관리자 역할이 할당된 사용자는 추가 비용 없이 Azure AD 전역 관리자 계정에 2단계 인증을 사용하도록 설정할 수 있습니다. Microsoft 계정을 사용하는 경우 [여기](https://support.microsoft.com/en-us/help/12408/microsoft-account-about-two-step-verification)에서 다단계 인증을 등록할 수 있습니다. Microsoft 계정을 사용하지 않는 경우 글로벌 관리자에 대한 다단계 인증은 [여기](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)에서 설정합니다.

## <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 획득 방법
Azure Multi-Factor Authentication에서 제공하는 전체 기능을 사용하려는 경우 몇 가지 옵션이 있습니다.

### <a name="option-1---mfa-licenses"></a>옵션 1 - MFA 라이선스

Azure Multi-Factor Authentication 라이선스를 구매하여 Azure Active Directory에서 사용자에게 할당합니다. 

이 옵션을 사용하면 라이선스가 없는 일부 사용자를 위해 2단계 인증을 제공해야 하는 경우 Azure Multi-Factor Authentication 공급자만 만듭니다. 그렇지 않으면 두 번 청구될 수 있습니다.

### <a name="option-2---bundled-licenses-that-include-mfa"></a>옵션 2 - MFA를 포함하는 번들 라이선스

Azure Active Directory Premium, Enterprise Mobility + Security 등 Azure Multi-Factor Authentication이 포함되는 라이선스를 구매하여 Azure Active Directory의 사용자에게 할당합니다. 

이 옵션을 사용하면 라이선스가 없는 일부 사용자를 위해 두 단계 인증을 제공해야 하는 경우에만 Azure Multi-Factor Authentication 공급자를 만들어야 합니다. 그렇지 않으면 두 번 청구될 수 있습니다. 

### <a name="option-3---mfa-consumption-based-model"></a>옵션 3 - MFA 사용량 기반 모델

Azure 구독 내에서 Azure Multi-Factor Authentication 공급자를 만듭니다. Azure MFA 공급자는 모든 다른 Azure 리소스처럼 기업계약, Azure 약정 금액 또는 신용 카드에 대해 청구되는 Azure 리소스입니다. 이러한 공급자는 전체 Azure 구독에만 만들 수 있으며 $0 지출 한도를 포함하는 Azure 구독으로 제한되지 않습니다. 제한된 구독은 옵션 1 및 2처럼 라이선스를 활성화하는 경우 만들어집니다. 

Azure Multi-Factor Authentication 제공자를 사용하는 경우 Azure 구독을 통해 청구되는 두 가지 사용 모델을 사용할 수 있습니다.  

1. **활성화된 사용자별** - 규칙적으로 인증이 필요한 고정된 수의 직원에 대해 2단계 인증을 사용하려는 기업에 적합합니다. 사용자당 요금 청구는 Azure AD 테넌트 및 Azure MFA 서버에서 MFA에 대해 활성화된 사용자 수를 기반으로 합니다. 사용자가 Azure AD 및 Azure MFA 서버 모두에서 MFA에 대해 활성화되고 도메인 동기화(Azure AD Connect)가 사용하도록 설정된 경우 더 많은 사용자 집합을 계산합니다. 도메인 동기화를 사용할 수 없는 경우 Azure AD 및 Azure MFA 서버에서 MFA에 대해 활성화된 모든 사용자의 합계를 계산합니다. 대금 청구는 매일 계산되어 상거래 시스템에 보고됩니다. 

  > [!NOTE]
  > 대금 청구 예1: 오늘 MFA에 대해 5,000명의 사용자가 활성화되었습니다. MFA 시스템은 이 숫자를 31로 나누고 해당 날짜에 대해 161.29명의 사용자를 보고합니다. 내일은 15명의 사용자를 추가로 활성화하므로 MFA 시스템은 해당 날짜에 대해 161.77명의 사용자를 보고합니다. 청구 주기가 끝날 때까지 Azure 구독에 대해 대금 청구된 총 사용자 수는 약 5,000으로 합산됩니다. 
  >
  > 대금 청구 예2: 라이선스가 있는 사용자와 없는 사용자가 혼합되어 있으므로 사용자당 Azure MFA 공급자를 포함하여 차액을 메웁니다. 테넌트에는 4,500개의 Enterprise Mobility + Security 라이선스가 있지만 MFA에 대해 5,000명의 사용자가 활성화되어 있습니다. Azure 구독에는 500명의 사용자에 대해 대금이 청구되고 매일 16.13명의 사용자료 계산 및 보고됩니다. 

2. **인증당** - 비규칙적으로 인증이 필요한 큰 그룹의 사용자에 대해 2단계 인증을 사용하려는 기업에 적합합니다. 대금 청구는 인증의 성공 또는 거부 여부에 관계없이 2단계 인증 요청 수를 기반으로 합니다. 이 대금 청구는 10개 인증 팩의 Azure 사용량 명세서에 표시되며 매일 보고됩니다. 

  > [!NOTE]
  > 대금 청구 예3: 오늘, Azure MFA 서비스가 3,105개의 2단계 인증 요청을 수신했습니다. Azure 구독에는 310.5개 인증 팩에 대한 요금이 청구됩니다. 

Azure MFA 라이선스를 포함할 수 있지만 사용량 기반 구성에 따라 요금이 청구된다는 점을 고려해야 합니다. 인증당 Azure MFA 공급자를 설정했다면 라이선스가 있는 사용자가 수행했어도 2단계 인증 요청마다 요금이 청구됩니다. Azure AD 테넌트에 연결되지 않은 도메인에서 사용자당 Azure MFA 공급자를 설정하면 사용자에게 Azure AD에 대한 라이선스가 있어도 활성화된 사용자당 요금이 청구됩니다. 

## <a name="next-steps"></a>다음 단계

- 가격 책정에 대한 자세한 내용은 [Azure MFA 가격 책정](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)을 참조하세요.

- [클라우드 또는 온-프레미스](concept-mfa-whichversion.md)에서 Azure MFA를 배포할지 여부 선택
