---
title: "Azure AD 조인에 대한 사용 시나리오와 배포 고려 사항 | Microsoft Docs"
description: "관리자가 최종 사용자(직원, 학생, 다른 사용자)를 위해 Azure AD 조인을 설정하는 방법을 설명합니다. 또한 Azure AD 조인을 사용하는 데 대한 다양한 실제 시나리오에 대해서 설명합니다."
services: active-directory
documentationcenter: 
author: femila
manager: mtillman
editor: 
tags: azure-classic-portal
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: markvi
ms.openlocfilehash: 173ad6f07699ca6bfa534dedc053663bba571382
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="usage-scenarios-and-deployment-considerations-for-azure-ad-join"></a>Azure AD 연결에 대한 사용 시나리오와 배포 고려 사항
## <a name="usage-scenarios-for-azure-ad-join"></a>Azure AD 연결에 대한 사용 시나리오
### <a name="scenario-1-businesses-largely-in-the-cloud"></a>시나리오 1: 클라우드에서 주로 업무 처리
Azure Active Directory 연결(Azure AD 연결)은 현재 클라우드에서 업무용 ID를 작동 및 관리하거나 곧 클라우드로 전환하려는 경우에 유용할 수 있습니다. Azure AD에서 만든 계정을 사용하여 Windows 10에 로그인할 수 있습니다. 사용자는 [FRX(첫 실행 경험) 프로세스](active-directory-azureadjoin-user-frx.md)를 사용하거나 [설정 메뉴](active-directory-azureadjoin-user-upgrade.md)에서 Azure AD에 연결하여 Azure AD에 컴퓨터를 연결할 수 있습니다.  또한 사용자는 브라우저나 Office 응용 프로그램에서 SSO(Single-Sign-On)를 사용하여 Office 365와 같은 클라우드 리소스에 편리하게 액세스할 수 있습니다.

### <a name="scenario-2-educational-institutions"></a>시나리오 2: 교육 기관
교육 기관에는 일반적으로 두 가지 사용자 유형인 교직원 및 학생이 있습니다. 교직원은 조직에서 장기 멤버로 간주됩니다. 교직원에 대한 온-프레미스 계정을 만드는 것이 바람직합니다. 하지만 학생은 조직의 단기 멤버이며 Azure AD에서 자신의 계정을 관리할 수 있습니다. 즉, 디렉터리 규모를 온-프레미스로 저장하는 대신 클라우드에 푸시할 수 있습니다. 또한 학생들은 자신의 Azure AD 계정으로 Windows에 로그인하고 Office 응용 프로그램에서 Office 365 리소스에 액세스할 수 있습니다.

### <a name="scenario-3-retail-businesses"></a>시나리오 3: 소매 기업
소매 기업에는 비정규직 근로자가 있고 장기 직원이 있습니다. 일반적으로 장기적인 정규직 직원에 대해서는 온-프레미스 계정을 만든 후 도메인에 연결된 컴퓨터를 사용합니다. 하지만 비정규직 근로자는 조직의 단기 멤버이므로 사용자 라이선스를 보다 쉽게 이동할 수 있도록 이들의 계정을 관리하는 것이 좋습니다. Office 365 라이선스를 사용하여 클라우드에 이러한 사용자의 계정을 만들면 이러한 사용자는 Azure AD 계정을 사용하여 Windows 및 Office 응용 프로그램에 로그인할 수 있으며 사용자가 조직을 떠난 후에 해당 라이선스의 이동을 보다 쉽게 관리할 수 있습니다.

### <a name="scenario-4-additional-scenarios"></a>시나리오 4: 시나리오
위에서 설명한 이점 외에도, 사용자의 장치를 Azure AD에 연결하여 간소화된 연결 환경, 효율적인 장치 관리, 자동 모바일 장치 관리 등록 및 Azure AD/온-프레미스 리소스에 대한 Single Sign-On 등에 따른 많은 이점을 얻을 수 있습니다.  

## <a name="deployment-considerations-for-azure-ad-join"></a>Azure AD 연결에 대한 배포 고려 사항
### <a name="enable-your-users-to-join-a-company-owned-device-directly-to-azure-ad"></a>사용자가 Azure AD에 직접 회사 소유의 장치를 연결하도록 허용
기업에서는 파트너 회사 및 조직에 클라우드 전용 계정을 제공할 수 있습니다. 이러한 파트너는 Single Sign-On을 사용하여 회사 앱 및 리소스에 쉽게 액세스할 수 있습니다. 이 시나리오는 주로 인증을 위해 Azure AD에 의존하는 Office 365 또는 SaaS 앱과 같은 클라우드 리소스에 액세스하는 사용자에게 해당됩니다.

### <a name="prerequisites"></a>필수 조건
**엔터프라이즈 수준(관리자)**

* Azure Active Directory를 사용하는 Azure 구독  

**사용자 수준**

* Windows 10(Professional 및 Enterprise 버전)

### <a name="administrator-tasks"></a>관리자 작업
* [장치 등록 설정](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>사용자 작업
* [설치하는 동안 Azure AD로 새 Windows 10 장치 설정](active-directory-azureadjoin-user-frx.md)
* [설정 메뉴에서 Azure AD으로 Windows 10 장치 설정](active-directory-azureadjoin-user-upgrade.md)
* [개인 Windows 10 장치를 조직에 연결](active-directory-azureadjoin-personal-device.md)

## <a name="enable-byod-in-your-organization-for-windows-10"></a>조직의 BYOD를 Windows 10에서 사용하도록 설정
사용자 및 직원이 개인 Windows 장치(BYOD)를 사용하여 회사 앱 및 리소스에 액세스하도록 설정할 수 있습니다. 사용자는 안전하고 호환되는 방식으로  리소스에 액세스하기 위해 개인 Windows 장치에 자신의 Azure AD 계정(회사 또는 학교 계정)을 추가할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
**엔터프라이즈 수준(관리자)**

* Azure AD 구독

**사용자 수준**

* Windows 10(Professional 및 Enterprise 버전)

### <a name="administrator-tasks"></a>관리자 작업
* [장치 등록 설정](active-directory-azureadjoin-setup.md)

### <a name="user-tasks"></a>사용자 작업
* [개인 Windows 10 장치를 조직에 연결](active-directory-azureadjoin-personal-device.md)

## <a name="additional-information"></a>추가 정보
* [엔터프라이즈를 위한 Windows 10: 작업에 장치를 사용하는 방법](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 조인을 통해 클라우드 기능을 Windows 10 장치로 확장](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport를 통해 암호 없이 ID 인증](active-directory-azureadjoin-passport.md)
* [Azure AD 조인에 대한 사용 시나리오에 대해 알아보기](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 환경용 Azure AD에 도메인 가입된 장치 연결](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD 조인 설정](active-directory-azureadjoin-setup.md)

