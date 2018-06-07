---
title: Azure AD 셀프 서비스 암호 재설정 개요 | Microsoft Docs
description: Azure AD 셀프 서비스 암호 재설정으로 조직에서 어떤 작업을 수행할 수 있나요?
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 77d26d860462b1a82315bf1385bccf62047e3da3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866671"
---
# <a name="azure-ad-self-service-password-reset-for-the-it-professional"></a>IT 전문가를 위한 Azure AD 셀프 서비스 암호 재설정

Azure Active Directory(Azure AD) 셀프 서비스 암호 재설정(SSPR)을 사용하면 사용자가 필요한 경우 어디서나 암호를 직접 재설정할 수 있습니다. 또한, 관리자는 사용자 암호가 재설정되는 방식을 제어할 수 있습니다. 사용자는 암호를 재설정하기 위해 지원 센터에 문의하지 않아도 됩니다. Azure AD SSPR에는 다음이 포함됩니다.

* **셀프 서비스 암호 변경**: 암호를 알고 있는 사용자가 암호를 변경하려는 경우 사용합니다.
* **셀프 서비스 암호 재설정**: 사용자가 로그인할 수 없어 다음과 같은 검증된 인증 방법 중 하나 이상을 사용하여 암호를 재설정하려는 경우 사용합니다.
   * 검증된 휴대폰으로 문자 메시지 전송.
   * 검증된 휴대폰 또는 사무실 전화로 전화.
   * 검증된 보조 메일 계정으로 메일 보내기.
   * 본인 확인 질문에 답변하기.
* **셀프 서비스 계정 잠금 해제**: 사용자가 암호를 사용하여 로그인할 수 없고 계정이 차단되었을 때 관리자 개입 없이 인증 방법을 사용하여 계정의 잠금을 해제하려는 경우 사용합니다.

> [!VIDEO https://www.youtube.com/embed/hc97Yx5PJiM]

## <a name="why-choose-azure-ad-sspr"></a>Azure AD SSPR을 선택해야 하는 이유

Azure AD SSPR을 사용하면 다음과 같은 이점이 제공됩니다.

* 지원 센터가 지원하는 암호 재설정은 IT 조직 지원 통화 중 약 20%를 차지하는데, 이러한 **비용을 절감**할 수 있습니다. 
* 최종 사용자가 암호 문제를 스스로 해결할 수 있도록 지원함으로써 **최종 사용자 환경을 개선**하고  **업무량을 경감**할 수 있습니다. 사용자가 지원 센터에 전화를 걸거나 지원 요청을 제출할 필요가 없어집니다.
* **이동성 공급** - 사용자가 어디에서든 암호를 재설정할 수 있습니다
* 보안 정책에 대한 **제어를 유지**할 수 있습니다. 관리자가 현재 가지고 있는 정책을 지속적으로 시행할 수 있습니다.

[빠른 시작 설명서](quickstart-sspr.md)를 참고하여 Azure AD SSPR을 시작하고 사용자가 암호를 스스로 재설정할 수 있도록 구성하세요.

## <a name="azure-ad-sspr-availability"></a>Azure AD SSPR 사용 가능 여부

Azure AD SSPR은 사용 중인 구독에 따라 다음과 같이 세 가지 등급으로 제공됩니다.

* **Azure AD Free**: 클라우드 전용 관리자가 암호를 스스로 재설정할 수 있습니다.
* **Azure AD Basic** 또는 **유료 Office 365 구독**: 클라우드 전용 사용자가 암호를 스스로 재설정할 수 있습니다.
* **Azure AD Premium**: 클라우드 전용, 페더레이션, 통과 인증 또는 암호 해시 동기화 사용자를 비롯한 모든 사용자와 관리자가 암호를 스스로 재설정할 수 있습니다. 온-프레미스 암호는 비밀번호 쓰기 저장을 사용하도록 설정해야 합니다.

## <a name="azure-ad-pricing-sla-updates-and-roadmap"></a>Azure AD 가격 책정, SLA, 업데이트 및 로드맵

다음 사이트에서 라이선스, 가격, 향후 계획에 대해 자세히 알아보세요.

* [Azure AD 가격 정보](https://azure.microsoft.com/pricing/details/active-directory/)
* [Office 365 가격](https://products.office.com/compare-all-microsoft-office-products?tab=2)
* [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)
* [Microsoft Online Services에 대한 서비스 수준 계약](http://go.microsoft.com/fwlink/?LinkID=272026&clcid=0x409)
* [Azure 업데이트](https://azure.microsoft.com/updates/)
* [Azure 로드맵](https://www.microsoft.com/cloud-platform/roadmap-recently-available)

## <a name="next-steps"></a>다음 단계

* SSPR을 시작할 준비가 되었나요? [Azure AD 셀프 서비스 암호 재설정을 설정](quickstart-sspr.md)해 보세요.
* [출시 가이드](howto-sspr-deployment.md)의 지침을 참고하여 사용자를 위한 성공적인 SSPR 배포를 계획해 보세요.
* [암호 재설정 또는 변경](../active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../active-directory-passwords-reset-register.md)
