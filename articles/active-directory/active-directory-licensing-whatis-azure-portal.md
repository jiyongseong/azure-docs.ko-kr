---
title: Azure Active Directory의 그룹 기반 라이선스란? | Microsoft Docs
description: Azure Active Directory 그룹 기반 라이선스, 작동 방법 및 모범 사례에 대한 설명
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/29/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 81d2916709f4d9e88faebc198940ad905705125a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Azure Active Directory에서 그룹 기반 라이선스 기본

Office 365, Enterprise Mobility + Security, Dynamics CRM 및 기타 유사한 제품과 같은 Microsoft 유료 클라우드 서비스를 사용하려면 라이선스가 필요합니다. 이러한 라이선스는 해당 서비스에 액세스해야 하는 각 사용자에게 할당됩니다. 라이선스를 관리하기 위해 관리자는 관리 포털(Office 또는 Azure) 및 PowerShell cmdlet 중 하나를 사용합니다. Azure AD(Azure Active Directory)는 모든 Microsoft 클라우드 서비스에 대한 ID 관리를 지원하는 기본 인프라입니다. Azure AD는 사용자에 대한 라이선스 할당 상태에 대한 정보를 저장합니다.

지금까지 개별 사용자 수준에서만 라이선스를 할당할 수 있었기 때문에 대규모 관리가 어려워질 수 있습니다. 예를 들어 조직이나 부서에 가입하거나 탈퇴하는 사용자와 같이 조직의 변경 내용에 따라 사용자 라이선스를 추가하거나 제거하려면 관리자는 종종 복잡한 PowerShell 스크립트를 작성해야 합니다. 이 스크립트는 클라우드 서비스를 개별적으로 호출합니다.

이러한 문제를 해결하기 위해 이제는 Azure AD에 그룹 기반 라이선스가 포함됩니다. 그룹에 제품 라이선스를 하나 이상 할당할 수 있습니다. Azure AD는 그룹의 모든 멤버에게 라이선스가 할당되도록 합니다. 그룹에 참가하는 새 멤버에게는 적절한 라이선스가 할당됩니다. 멤버가 그룹을 떠날 때 해당 라이선스가 제거됩니다. 이렇게 하면 사용자 기준으로 조직 및 부서 구조에 변경 내용을 반영하기 위해 PowerShell을 통해 라이선스 관리를 자동화할 필요가 없습니다.

>[!NOTE]
>이 기능은 현재 공개 미리 보기로 제공됩니다. 변경 내용을 되돌리거나 제거할 준비를 해야 합니다. 이 기능은 공개 미리 보기 동안 모든 Azure AD(Azure Active Directory) 구독에서 사용할 수 있습니다. 그러나 기능이 일반 공급되면 일부 기능에는 하나 이상의 Azure Active Directory Premium 라이선스가 필요할 수도 있습니다.

## <a name="features"></a>기능

다음은 그룹 기반 라이선스 기능의 주요 특징입니다.

- Azure AD의 보안 그룹에 라이선스를 할당할 수 있습니다. Azure AD Connect를 사용하여 보안 그룹을 온-프레미스에서 동기화할 수 있습니다. 또한 Azure AD(클라우드 전용 그룹이라고도 함)에서 보안 그룹을 직접 만들거나 Azure AD 동적 그룹 기능을 통해 자동으로 만들 수 있습니다.

- 그룹에 제품 라이선스를 할당하면 관리자는 제품에서 서비스 계획을 하나 이상 사용하지 않도록 설정할 수 있습니다. 일반적으로 조직에서 제품에 포함된 서비스를 아직 사용할 준비가 되지 않은 경우에 이 작업이 수행됩니다. 예를 들어 관리자는 Office 365를 부서에 할당할 수 있지만 Yammer 서비스를 일시적으로 사용하지 않도록 설정할 수 있습니다.

- 사용자 수준 라이선스를 필요로 하는 모든 Microsoft Clouds Services는 지원됩니다. 여기에는 모든 Office 365 제품, Enterprise Mobility + Security 및 Dynamics CRM이 포함됩니다.

- 그룹 기반 라이선스는 현재 [Azure Portal](https://portal.azure.com)을 통해서만 사용할 수 있습니다. Office 365 포털과 같이 주로 사용자 및 그룹 관리를 위해 다른 관리 포털을 사용하는 경우에 이 작업을 계속 수행할 수 있습니다. 그러나 그룹 수준에서 라이선스를 관리하려면 Azure Portal을 사용해야 합니다.

- Azure AD는 그룹 멤버 자격 변경으로 인해 발생하는 라이선스 수정을 자동으로 관리합니다. 일반적으로 라이선스 수정은 멤버 자격 변경 후 수분 내에 효과가 발생합니다.

- 사용자는 지정된 라이선스 정책을 사용하는 여러 그룹의 멤버가 될 수 있습니다. 또한 사용자는 그룹 외부에서 직접 할당된 일부 라이선스를 보유할 수도 있습니다. 결과 사용자 상태는 모든 할당된 제품 및 서비스 라이선스의 조합입니다.

- 어떤 경우에는 사용자에게 라이선스를 할당할 수 없습니다. 예를 들어 테넌트에서 사용할 수 있는 라이선스가 충분하지 않거나 충돌하는 서비스를 동시에 할당했을 수 있습니다. 관리자는 Azure AD가 그룹 라이선스를 완전하게 처리할 수 없는 사용자에 대한 정보에 액세스할 수 있습니다. 그런 다음 해당 정보에 따라 수정 작업을 수행할 수 있습니다.

- 공개 미리 보기 동안 그룹 기반 라이선스 관리를 사용하려면 Azure AD Basic 또는 Premium Edition에 대한 유료 또는 평가판 구독이 테넌트에 필요합니다.

## <a name="your-feedback-is-welcome"></a>피드백이 있으시면 언제든지 보내주세요.

피드백 또는 기능 요청이 있는 경우 [이 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing)을 사용하여 공유해주세요.

## <a name="next-steps"></a>다음 단계

그룹 기반 라이선스를 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Active Directory에서 라이선스 시작](active-directory-licensing-get-started-azure-portal.md)
* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)
