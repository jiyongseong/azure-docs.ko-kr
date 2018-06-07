---
title: Azure AD 디렉터리에 기존 Azure 구독을 추가하는 방법 | Microsoft Docs
description: Azure AD 디렉터리에 기존 구독을 추가하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 6664a2b9bb6375314090bf4dae9190c7d52565d2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법

이 문서에서는 Azure 구독과 Azure Active Directory(Azure AD) 간의 관계와 같은 정보 및 Azure AD 디렉터리에 기존 구독을 추가하는 방법을 다룹니다. Azure 구독은 Azure AD와 트러스트 관계가 있습니다. 즉, 사용자, 서비스 및 장치를 인증하는 디렉터리를 신뢰합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 각 구독은 하나의 디렉터리만 신뢰합니다. 

구독이 디렉터리와 갖는 트러스트 관계는 구독이 Azure의 다른 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다릅니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 Azure AD 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하고 새 구독을 사용하여 디렉터리를 관리할 수 있습니다.

모든 사용자는 자신을 인증하는 단일 홈 디렉터리를 가지고 있지만 다른 디렉터리의 게스트가 될 수도 있습니다. Azure AD에서 사용자 계정이 멤버 또는 게스트인 디렉터리만 볼 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

* 구독에 대해 RBAC 소유자 액세스 권한이 있는 계정으로 로그인해야 합니다.
* 구독이 연결된 현재 디렉터리 및 추가하려는 디렉터리에 존재하는 계정으로 로그인해야 합니다. 다른 디렉터리에 대한 액세스 권한을 얻는 방법에 대해 알아보려면 [Azure Active Directory 관리자가 B2B 공동 작업 사용자를 추가하는 방법](active-directory-b2b-admin-add-users.md)을 참조하세요.
* 이 기능은 CSP(MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P) 및 Microsoft Imagine(MS-AZR-0144P) 구독에 사용할 수 없습니다.

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Azure AD 디렉터리에 기존 구독을 연결하려면

1. 로그인하고 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 구독을 선택합니다.
2. **디렉터리 변경**을 클릭합니다.

    ![디렉터리 변경 단추를 보여 주는 스크린샷](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. 경고를 검토합니다. 구독 디렉터리가 변경되면 액세스 권한이 할당된 모든 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 사용자 및 모든 구독 관리자의 액세스 권한이 손실됩니다.
4. 디렉터리를 선택합니다.

    ![디렉터리 변경 UI를 보여 주는 스크린샷](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. **변경**을 클릭합니다.
6. 성공! 디렉터리 전환기를 사용하여 새 디렉터리로 이동합니다. 모든 항목이 제대로 표시되는 데 최대 10분이 걸릴 수 있습니다.

    ![디렉터리 변경 성공 알림을 보여 주는 스크린샷](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![전환기를 보여주는 스크린샷](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


갖고 있는 모든 Azure 키 자격 증명 모음도 구독 이동의 영향을 받으므로 작업을 다시 시작하기 전에 [키 자격 증명 모음 테넌트 ID를 변경](../key-vault/key-vault-subscription-move-fix.md)해야 합니다.

구독 디렉터리 변경은 서비스 수준 작업입니다. 구독 청구 소유권에는 영향을 미치지 않으며 계정 관리자는 [계정 센터](https://account.azure.com/subscriptions)를 사용하여 서비스 관리자를 계속 변경할 수 있습니다. 원래 디렉터리를 삭제하려면 구독 청구 소유권을 새 계정 관리자로 이전해야 합니다. 청구 소유권 이전에 대해 자세히 알아보려면 [Azure 구독의 소유권을 다른 계정으로 이전](../billing/billing-subscription-transfer.md)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 새 Azure AD 디렉터리를 무료로 만드는 방법에 대해 자세히 알아보려면 [Azure Active Directory 테넌트를 얻는 방법](develop/active-directory-howto-tenant.md)을 참조하세요.
* Azure 구독에 대한 청구 소유권 이전에 대해 자세히 알아보려면 [Azure 구독의 소유권을 다른 계정으로 이전](../billing/billing-subscription-transfer.md)을 참조하세요.
* Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure AD에서 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
