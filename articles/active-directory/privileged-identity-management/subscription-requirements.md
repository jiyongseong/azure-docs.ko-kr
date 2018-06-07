---
title: Privileged Identity Management 구독 - Azure | Microsoft Docs
description: 테넌트에서 Azure AD Privileged Identity Management를 관리하고 사용하기 위한 구독 및 라이선스 요구 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: barclayn
ms.custom: pim
ms.openlocfilehash: 9e49ddb8fca9ce193c92f27c307942d5f3d3d2fd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Azure Active Directory Privileged Identity Management 구독 요구 사항

Azure AD Privileged Identity Management는 Azure AD의 Premium P2 버전의 일부로 사용할 수 있습니다. P2의 다른 기능과 Premium P1과 비교한 결과에 대한 자세한 내용은 [Azure Active Directory 버전](../active-directory-editions.md)을 참조하세요.

>[!NOTE]
Azure AD(Azure Active Directory) Privileged Identity Management가 미리 보기로 제공될 때는 테넌트가 서비스를 시도할 때 라이선스가 확인되지 않았습니다.  현재 Azure AD Privileged Identity Management가 일반 공급되므로 2016년 12월부터는 테넌트에서 Privileged Identity Management를 사용하려면 평가판 또는 유료 구독이 있어야 합니다.
  

## <a name="confirm-your-trial-or-paid-subscription"></a>평가판 또는 유료 구독 확인

조직에 평가판 또는 유료 구독이 있는지 잘 모를 경우 확실 하지 않은 Windows PowerShell V1용 Azure Active Directory 모듈에 포함된 명령을 사용하여 테넌트에 구독이 있는지 여부를 확인할 수 있습니다. 
1. PowerShell 창을 엽니다.
2. `Connect-MsolService`를 입력하여 테넌트의 사용자로 인증을 받습니다.
3. `Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`을 입력합니다.

이 명령은 테넌트의 구독 목록을 검색합니다. 반환되는 줄이 없으면 Azure AD Premium P2 평가판을 구하거나 Azure AD Premium P2 구독 또는 EMS E5 구독이 구매해야 Azure AD Privileged Identity Management를 사용할 수 있습니다.  평가판을 구하고 Azure AD Privileged Identity Management 사용을 시작하려면 [Azure AD Privileged Identity Management 시작](../active-directory-privileged-identity-management-getting-started.md)을 읽어보세요.

이 명령이 SkuPartNumber가 "AAD_PREMIUM_P2"이거나 "EMSPREMIUM"이고 IsTrial이 "True"인 줄을 반환하는 경우 Azure AD Premium P2 평가판이 테넌트에 있는 것입니다.  구독 상태를 사용하도록 설정하지 않고 Azure AD Premium P2 또는 EMS E5 구독을 구매하지 않은 경우 Azure AD Privileged Identity Management를 계속 사용하려면 Azure AD Premium P2 구독 또는 EMS E5 구독을 구입해야 합니다.

Azure AD Premium P2는 [Microsoft 기업계약](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx), [오픈 볼륨 라이선스 프로그램](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) 및 [클라우드 솔루션 공급자](https://partner.microsoft.com/en-US/cloud-solution-provider) 프로그램을 통해 구입할 수 있습니다. 또한 Azure 및 Office 365 구독자는 Azure AD Premium P2를 온라인으로 구입할 수도 있습니다.  Azure AD Premium 가격 책정 및 온라인 주문 방법에 대한 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management를 테넌트에서 사용할 수 없음

다음과 같은 경우 Azure AD Privileged Identity Management를 테넌트에서 더 이상 사용할 수 없습니다.
- 조직이 미리 보기 상태이며 Azure AD Premium P2 구독 또는 EMS E5 구독을 구입하지 않은 상태에서 Azure AD Privileged Identity Management를 사용하고 있었습니다.
- 조직이 만료된 Azure AD Premium P2 또는 EMS E5 평가판을 보유하고 있습니다.
- 조직이 구입한 구독이 만료되었습니다.

Azure AD Premium P2 구독 또는 EMS E5 구독이 만료되거나 미리 보기의 Azure AD Privileged Identity Management를 사용하던 조직이 Azure AD Premium P2 또는 EMS E5 구독을 구입하지 않았습니다.

- Azure AD 역할에 대한 영구 역할 할당은 영향을 받지 않습니다.
- 사용자가 권한 있는 역할을 활성화하거나, 권한 있는 액세스를 관리하거나, 권한 있는 역할의 액세스 검토를 수행하기 위해 더 이상 Azure Portal의 Azure AD Privileged Identity Management 확장과 Azure AD Privileged Identity Management의 Graph API cmdlet 및 PowerShell 인터페이스를 사용할 수 없습니다.
- 사용자가 더 이상 권한 있는 역할을 활성화할 수 없으므로 Azure AD 역할의 적격 역할 할당이 제거됩니다.
- Azure AD 역할에 대해 진행 중인 모든 액세스 검토가 종료되고 Azure AD Privileged Identity Management 구성 설정이 제거됩니다.
- Azure AD Privileged Identity Management는 더 이상 역할 할당 변경에 대한 전자 메일을 전송하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD Privileged Identity Management 시작](../active-directory-privileged-identity-management-getting-started.md)
- [Azure AD Privileged Identity Management의 역할](../active-directory-privileged-identity-management-roles.md)
