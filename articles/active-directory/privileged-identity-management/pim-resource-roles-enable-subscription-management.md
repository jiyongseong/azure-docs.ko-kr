---
title: Azure 리소스에 대한 Privileged Identity Management - 구독 관리 설정 | Microsoft Docs
description: 전역 관리자가 테넌트에서 구독을 관리할 수 있는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: e164d8adaf5df63dba31bb6aa8e56f768741479c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200878"
---
# <a name="enable-subscription-management-in-your-tenant"></a>테넌트에서 구독 관리 사용

디렉터리의 전역 관리자는 테넌트의 일부 구독 리소스에 대한 기본 액세스 권한이 없을 수 있습니다. 이 문서에서는 테넌트의 모든 구독에 대한 액세스 권한을 자신에게 직접 제공하는 단계를 설명합니다. 또한 액세스 권한을 받은 후에도 조직에서 요구하는 보안 제어를 계속 준수하는 권장 방법을 제공합니다.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>내 디렉터리에서 관리 구독을 사용할 수 있는 사용자는 누구인가요?

전역 관리자 역할에 할당된 각 사용자는 다음 단계를 수행하여 구독 관리를 설정해야 합니다. 스스로 구독 관리를 설정하면 리소스 액세스 권한이 필요한 다른 전역 관리자를 추가할 수 있습니다. 전역 관리자 역할의 모든 멤버에게 액세스 권한을 설정하는 디렉터리 설정은 없습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

전역 관리자 역할의 적격 또는 활성 멤버인 계정으로 Azure Portal에 로그인합니다. 계정이 적격 전역 관리자인 경우 다음 단계로 넘어가기 전에 먼저 역할을 활성화해야 합니다.

## <a name="access-the-azure-active-directory-admin-center"></a>Azure Active Directory 관리 센터에 액세스

전역 관리자로 Azure Portal에 로그인했으므로 Azure 구독에 대한 액세스 권한을 제공하는 설정을 편집할 수 있습니다. Azure AD(Azure Active Directory) 관리 센터를 찾아 **속성**을 선택합니다.

![속성이 강조 표시된 Azure AD 관리 센터의 스크린샷](media/azure-pim-resource-rbac/aad_properties.png)

속성 목록의 **전역 관리자는 Azure 구독을 관리할 수 있습니다**에서 **예**를 선택합니다.

![토글이 [예]로 설정된 속성 페이지의 스크린샷](media/azure-pim-resource-rbac/aad_properties_save.png)

이제 계정은 테넌트에 있는 모든 구독 리소스의 사용자 액세스 관리자 역할에 자동으로 추가됩니다.

## <a name="browse-to-azure-ad-pim"></a>Azure AD PIM으로 이동

 여기서 Azure AD PIM(Privileged Identity Management)으로 이동합니다. **관리** 아래에서 **Azure 리소스**를 선택합니다.

![Azure 리소스가 강조 표시된 PIM 스크린샷](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>리소스 관리 및 검색

조직이 이미 Azure AD PIM을 사용하여 Azure AD에서 관리자를 보호하는 경우 블레이드가 로드될 때 구독 목록이 나타납니다.

![블레이드에 구독 목록이 표시된 PIM 스크린샷](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> 리소스가 보이지 않으면 다음 사항을 확인합니다.
>- 전역 관리자 역할이 만료되지 않았습니다. 
>- 조직에 Azure 구독이 있습니다.

![리소스 목록이 비어 있는 PIM 스크린샷](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>할당 구성

목록에서 구독을 선택하고, 사용자 자신(또는 멤버인 사용자 그룹)을 리소스의 적격 소유자로 할당합니다. 
[역할을 할당하는 방법에 대해 자세히 참고하세요](pim-resource-roles-assign-roles.md).

다음 단계를 진행하기 전에 각 리소스에 대해 이 프로세스를 반복합니다.

## <a name="clean-up-standing-access"></a>고정 액세스 정리

조직에서 중요한 구독에 대한 적격 할당이 있으므로 디렉터리 속성에서 옵션을 사용하지 않도록 설정하여 고정 액세스를 정리할 수 있습니다.

![토글이 [아니요]로 설정된 속성 페이지의 스크린샷](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>다음 단계

[리소스 검색](pim-resource-roles-discover-resources.md)

[역할 설정 구성](pim-resource-roles-configure-role-settings.md)








