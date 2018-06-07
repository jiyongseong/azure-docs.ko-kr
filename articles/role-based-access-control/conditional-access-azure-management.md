---
title: Azure Active Directory에서 조건부 액세스로 Azure Management에 대한 액세스 관리
description: Azure AD에서 조건부 액세스를 사용하여 Azure Management에 대한 액세스를 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: dcf701c15ee20b7d9aeea65f3d06b794006a5202
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>조건부 액세스로 Azure Management에 대한 액세스 관리

Azure Active Directory(Azure AD)의 조건부 액세스는 사용자가 지정한 특정 조건을 기반으로 클라우드 앱에 대한 액세스를 제어합니다. 액세스를 허용하려면 정책의 요구 사항에 대한 부합 여부를 기준으로 액세스를 허용 또는 차단하는 조건부 액세스 정책을 만듭니다. 

일반적으로 조건부 액세스를 사용하여 클라우드 앱에 대한 액세스를 제어합니다. 특정 조건(예: 로그인 위험, 위치 또는 장치)을 기준으로 Azure Management에 대한 액세스를 제어하고 다단계 인증 등의 요구 사항을 시행하는 정책을 설정할 수도 있습니다.

Azure Management에 대한 정책을 만들려면 정책을 적용할 앱을 선택할 때 **클라우드 앱**에서 **Microsoft Azure Management**를 선택합니다.

![Azure 관리에 대한 조건부 액세스](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

만든 정책은 클래식 Azure Portal, Azure Resource Manager 공급자, 클래식 Service Management API 및 Azure PowerShell 등을 비롯한 모든 Azure Management 끝점에 적용됩니다.

> [!CAUTION]
> Azure Management에 대한 액세스를 관리하는 정책을 설정하기 전에 조건부 액세스가 어떻게 작동하는지 이해해야 합니다. 포털에 대한 자신의 액세스를 차단할 수 있는 조건은 만들지 않아야 합니다.

조건부 액세스의 설정 및 사용에 대한 자세한 내용은 [Azure Active Directory의 조건부 액세스](../active-directory/active-directory-conditional-access-azure-portal.md)를 참조하세요.