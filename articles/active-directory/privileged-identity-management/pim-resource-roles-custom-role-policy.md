---
title: Azure 리소스에 대한 Privileged Identity Management 설정을 대상으로 사용자 지정 역할 사용 | Microsoft Docs
description: PIM을 사용하여 Azure 리소스에 대해 사용자 지정 역할을 사용하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: e3df34e761f17aa3c1949af390e57360d84a304f
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32148943"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>Privileged Identity Management 설정을 대상으로 사용자 지정 역할 사용

다른 사용자에게 더 많은 자율성을 제공하면서 엄격한 PIM(Privileged Identity Management) 설정을 역할의 일부 멤버에게 적용해야 할 수도 있습니다. Azure 구독에서 실행될 응용 프로그램 개발을 지원하기 위해 조직이 일부 계약 직원을 채용하는 시나리오를 가정합니다.

리소스 관리자로서 직원이 승인 없이 액세스할 수 있도록 하려고 합니다. 그러나 조직의 리소스에 대한 액세스를 요청할 때 모든 계약 당사자는 승인을 받아야 합니다.

다음 섹션에 설명된 단계를 수행하여 Azure 리소스 역할에 대해 대상 PIM 설정을 지정합니다.

## <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

리소스에 대한 사용자 지정 역할을 만들려면 [Azure 역할 기반 액세스 제어의 사용자 지정 역할 만들기](../role-based-access-control-custom-roles.md)에 설명된 단계를 따릅니다.

사용자 지정 역할을 만들 때 복제하려는 기본 제공 역할을 쉽게 기억할 수 있도록 설명이 포함된 이름을 사용합니다.

> [!NOTE]
> 사용자 지정 역할이 복제하려는 기본 제공 역할의 복제본이 되고, 해당 범위는 기본 제공 역할과 일치하도록 합니다.

## <a name="apply-pim-settings"></a>PIM 설정 적용

역할을 Azure Portal의 테넌트에서 생성한 후 **Privileged Identity Management - Azure 리소스** 창으로 이동합니다. 역할을 적용하는 리소스를 선택합니다.

!["Privileged Identity Management - Azure 리소스" 창](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

이러한 역할의 멤버에 적용해야 하는 [PIM 역할 설정을 구성](pim-resource-roles-configure-role-settings.md)합니다.

마지막으로 이러한 설정으로 대상으로 할 멤버의 고유 그룹에 [역할을 할당](pim-resource-roles-assign-roles.md)합니다.

## <a name="next-steps"></a>다음 단계

[구독 소유자 및 액세스 검토](pim-resource-roles-perform-access-review.md)
