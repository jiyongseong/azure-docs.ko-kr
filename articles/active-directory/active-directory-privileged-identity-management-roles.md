---
title: Azure AD Privileged Identity Management의 역할 | Microsoft Docs
description: Azure 권위 있는 ID 관리 확장을 사용하여 권한 있는 ID에 사용되는 역할을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 03/04/2018
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 464b8c3433f6fbfb9e29f57ee96dd6d5e8f2109a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="different-administrative-role-in-azure-active-directory-pim"></a>Azure Active Directory PIM의 다른 관리자 역할
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

조직의 사용자를 Azure AD의 다른 관리 역할에 할당할 수 있습니다. 이러한 역할 할당은 사용자를 추가 또는 제거하거나 서비스 설정을 변경하는 등 사용자가 Azure AD, Office 365, 기타 Microsoft Online Services, 연결된 응용 프로그램에서 수행할 수 있는 작업을 제어합니다.  

> [!IMPORTANT]
> Microsoft는 Azure Portal에서 [Azure AD 관리 센터](https://aad.portal.azure.com)를 사용하여 Azure AD를 관리하는 것을 권장합니다.

전역 관리자는 `Add-MsolRoleMember` 및 `Remove-MsolRoleMember`와 같은 PowerShell cmdlet을 사용하거나 또는 [Azure Active Directory에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md)에 설명된 대로 포털을 통해 사용자가 **영구적으로** Azure AD에서 역할에 할당되도록 업데이트할 수 있습니다.

Azure AD Privileged Identity Management(PIM)는 Azure AD에서 사용자에 대한 권한 있는 액세스의 정책을 관리합니다. PIM은 Azure AD에서 사용자에게 하나 이상의 역할을 할당하고, 다른 사용자를 역할에 영구적으로 할당하거나 또는 역할에 대해 적격이 되도록 할당할 수 있습니다. 사용자가 역할에 영구적으로 할당되거나 적격 역할 할당을 활성화하는 경우 해당 역할에 할당된 권한으로 Azure Active Directory, Office 365 및 기타 응용 프로그램을 관리할 수 있습니다.

영구 및 적격 역할 할당을 비교했을 때 이 둘을 통해 다른 사람에게 주어진 액세스에는 차이가 없습니다. 유일한 차이는 사람들이 그 액세스를 항상 필요로 하지 않는다는 점입니다. 그런 사람들은 역할에 대해 적격이 되도록 하며, 언제라도 필요할 때 켜고 끄도록 할 수 있습니다.

## <a name="roles-managed-in-pim"></a>PIM에서 관리되는 역할
Privileged Identity Management를 사용하면 사용자를 다음과 같이 일반적인 관리자 역할에 할당할 수 있습니다.

* **전역 관리자** (회사 관리자라고도 함)는 모든 관리 기능에 액세스할 수 있습니다. 조직에는 전역 관리자가 두 개 이상 있을 수 있습니다. Office 365를 자동으로 구입하기 위해 등록한 사람은 전역 관리자가 됩니다.
* **권한 있는 역할 관리자** 는 Azure AD PIM을 관리하고 다른 사용자에 대한 역할 할당을 업데이트합니다.  
* **대금 청구 관리자** : 구입하고, 구독을 관리하고, 지원 티켓을 관리하고, 서비스 상태를 모니터링 합니다.
* **암호 관리자** : 암호를 재설정하고, 서비스 요청을 관리하고, 서비스 상태를 모니터링합니다. 암호 관리자는 사용자의 암호 재설정으로 제한됩니다.
* **서비스 관리자** : 서비스 요청을 관리하고 서비스 상태를 모니터링합니다.
  
  > [!NOTE]
  > Office 365를 사용하는 경우 사용자에게 서비스 관리자 역할을 할당하기 전에 먼저 Exchange Online 등의 서비스에 사용자 관리 권한을 할당합니다.
  > 
  > 
* **사용자 관리 관리자** 는 암호를 다시 설정하고, 서비스 상태를 모니터링하고, 사용자 계정과 사용자 그룹 및 서비스 요청을 관리합니다. 사용자 관리 관리자는 전역 관리자를 삭제하거나 다른 관리자 역할을 만들거나 청구, 전역 및 서비스 관리를 위해 암호를 재설정할 수 없습니다.
* **Exchange 관리자** 는 Exchange 관리 센터(EAC)를 통해 Exchange Online에 대한 관리 액세스 권한을 보유하고 Exchange Online에서 거의 모든 태스크를 수행할 수 있습니다.
* **SharePoint 관리자(미리 보기)** 는 SharePoint Online 관리 센터를 통해 SharePoint Online에 대한 관리 액세스 권한을 보유하고 SharePoint Online에서 거의 모든 태스크를 수행할 수 있습니다. 현재, 이 역할은 미리 보기로 제공되고 있습니다. 적격 사용자는 PIM에서 정품 인증을 한 후 SharePoint 내에서 이 역할을 사용할 때 지연이 발생할 수 있습니다.
* **비즈니스용 Skype 관리자** 는 비즈니스용 Skype 관리 센터를 통해 비즈니스용 Skype에 대한 관리 액세스 권한을 보유하고 비즈니스용 Skype Online에서 거의 모든 태스크를 수행할 수 있습니다.

[Azure AD에서 관리자 역할 할당](active-directory-assign-admin-roles-azure-portal.md) 및 [Office 365에서 관리자 역할 할당](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504)에 대한 자세한 내용을 보려면 이 문서를 읽으세요.

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


PIM에서는 사용자가 [필요할 때 역할을 활성화](active-directory-privileged-identity-management-how-to-add-role-to-user.md)할 수 있도록 [이러한 역할을 사용자에게 할당](active-directory-privileged-identity-management-how-to-activate-role.md)할 수 있습니다.

다른 사용자가 PIM에 액세스하여 관리할 수 있도록 하려는 경우 PIM에 필요한 사용자 역할은 [PIM에 대한 액세스 권한을 제공하는 방법](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)에 자세히 설명되어 있습니다.

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>PIM에서 관리되지 않는 역할
위에서 언급한 것을 제외하고 Exchange Online 또는 SharePoint Online 내에 있는 역할은 Azure AD에 표시되지 않으므로 PIM에서 볼 수 없습니다. 이러한 Office 365 서비스에서 세분화된 역할 할당 변경에 대한 자세한 내용은 [Office 365의 사용 권한](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d)을 참조하세요.

Azure 구독 및 리소스 그룹도 Azure AD에 표시되지 않습니다. Azure 구독을 관리하려면 [Azure 관리자 역할을 추가하거나 변경하는 방법](../billing/billing-add-change-azure-subscription-administrator.md)을 참조하고, Azure RBAC에 대한 자세한 내용은 [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>사용자 역할 및 로그인
일부 Microsoft 서비스 및 응용 프로그램의 경우 사용자를 역할에 할당하는 방법만으로 사용자를 관리자로 지정하지 못할 수 있습니다.

Azure Portal에 액세스하려면 사용자가 Azure 구독을 관리할 필요가 없는 경우에도 사용자가 서비스 관리자이거나 Azure 구독에서 공동 관리자여야 합니다.  예를 들어 Azure AD의 구성 설정을 관리하려면 사용자는 Azure AD의 전역 관리자이면서 Azure 구독에서 구독 공동 관리자여야 합니다.  Azure 구독에 사용자를 추가하는 방법을 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../billing/billing-add-change-azure-subscription-administrator.md)을 참조하세요.

Microsoft Online Services에 액세스하려면 서비스 포털을 열거나 관리 작업을 수행하기 전에 사용자에게 라이선스도 할당되어야 합니다.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Azure AD에서 사용자에게 라이선스 할당
1. 전역 관리자 계정 또는 공동 관리자 계정을 사용하여 [Azure Portal](http://portal.azure.com)에 로그인합니다.
3. Azure AD를 선택하고, 이에 연결된 라이선스가 있는 작업할 디렉터리를 선택합니다.
4. 왼쪽에서 **라이선스**를 선택합니다. 사용 가능한 라이선스 목록이 나타납니다.
5. 배포하려는 라이선스가 포함된 라이선스 계획을 선택합니다.
6. **사용자 할당**을 선택합니다.
7. 라이선스를 할당하려는 사용자를 선택합니다.
8. **할당** 단추를 클릭합니다.  이제 사용자가 Azure에 로그인할 수 있습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

