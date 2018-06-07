---
title: Azure의 리소스 액세스 이해 | Microsoft Docs
description: 이 항목에서는 구독 관리자를 사용하여 전체 Azure Portal에서 리소스 액세스를 제어하는 방법에 대한 개념을 설명합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: rolyon
ms.custom: it-pro;
ms.openlocfilehash: b993721a614f380aa1d5748bc925bd3bc9151841
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="understanding-resource-access-in-azure"></a>Azure의 리소스 액세스 이해

Azure의 액세스 제어는 결제 관점에서 시작합니다. [Azure 계정 센터](https://account.azure.com)를 방문하여 액세스하는 Azure 계정의 소유자는 계정 관리자(AA)입니다. 구독은 결제를 위한 컨테이너이지만 보안 경계로서의 역할도 합니다. 각 구독에는 [Azure Portal](https://portal.azure.com/)을 사용하여 해당 구독에서 Azure 리소스를 추가, 제거 및 수정할 수 있는 SA(서비스 관리자)가 있습니다. 새 구독의 기본 SA는 AA이지만 AA가 Azure 계정 센터에서 SA를 변경할 수 있습니다.

<br><br>![Azure 계정][1]

또한 구독은 디렉터리와 연결되어 있습니다. 디렉터리는 사용자 집합을 정의합니다. 디렉터리를 만든 직장 또는 학교의 사용자이거나 외부 게스트 사용자일 수 있습니다. 구독은 서비스 관리자(SA) 또는 CA(공동 관리자)로 할당된 해당 디렉터리 사용자의 하위 집합에서 액세스할 수 있습니다. 유일한 예외는 레거시의 이유로 Microsoft 계정(이전의 Windows Live ID)이 디렉터리에 없는 SA 또는 CA로 할당될 수 있다는 점입니다.

<br><br>![Azure의 Access Control][2]

Microsoft 계정을 사용하여 로그인된 SA는 Azure Portal 내 기능을 통해 구독이 연결된 디렉터리를 변경할 수 있습니다. 이 작업은 해당 구독의 액세스 제어에 영향을 줍니다.

<br><br>![간단한 사용자 로그인 흐름][3]

단순한 경우 조직(예: Contoso)은 요금 청구를 적용하고 동일한 집합의 구독에 대해 액세스 제어합니다. 즉, 디렉터리는 단일 Azure 계정에 의해 소유된 구독에 연결됩니다. Azure Portal에 로그인하면 사용자는 리소스의 두 가지 컬렉션(이전 그림에서 주황색으로 표시)을 볼 수 있습니다.

* 사용자 계정이 있는 디렉터리 (원본 또는 외래 사용자로 추가). 로그인에 사용되는 디렉터리는 이 계산과 관련이 없으므로 디렉터리는 로그인 위치에 관계없이 항상 표시됩니다.
* (SA 또는 CA인 경우) 사용자가 액세스 할 수 있는 로그인 AND로 사용되는 디렉터리와 연결된 구독의 일부인 리소스.

<br><br>![여러 구독 및 디렉터리가 있는 사용자][4]

여러 디렉터리에 구독이 있는 사용자는 구독 필터를 사용하여 Azure Portal의 현재 컨텍스트를 전환할 수 있습니다. 내부적으로 이렇게 하면 다른 디렉터리에 별도로 로그인하게 되지만, single sign-on (SSO)을 사용하여 원활하게 수행할 수 있습니다.

구독 간에 리소스를 이동하는 등의 작업은 구독의 단일 디렉터리 보기의 결과로 더 어려울 수 있습니다. 리소스 전송을 수행하려면 **설정**에서 구독 페이지의 **디렉터리 편집** 명령을 처음 사용하여 동일한 디렉터리에 구독을 연결해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
* Azure 구독에 대한 관리자를 변경하는 방법에 대해 자세히 알아보려면 [Azure 관리자 역할을 추가 또는 변경하는 방법](../billing/billing-add-change-azure-subscription-administrator.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](../active-directory/active-directory-how-subscriptions-associated-directory.md)
* Azure AD에서 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../active-directory/active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/rbac-and-directory-admin-roles/IC707931.png
[2]: ./media/rbac-and-directory-admin-roles/IC707932.png
[3]: ./media/rbac-and-directory-admin-roles/IC707933.png
[4]: ./media/rbac-and-directory-admin-roles/IC707934.png
