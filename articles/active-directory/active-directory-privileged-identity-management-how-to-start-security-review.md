---
title: 액세스 검토를 시작하는 방법 | Microsoft Docs
description: Azure Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID에 대해 액세스 검토를 만드는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 05/04/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: 1923890e01b351f884837e14b5e2ca736ec687ce
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management의 액세스 검토를 시작하는 방법
사용자가 더 이상 필요 없는 권한 있는 액세스를 가진 경우 "오래된" 역할 할당이 됩니다. 오래된 역할 할당과 관련된 위험을 줄이기 위해 권한 있는 역할 관리자는 사용자에게 부여된 역할을 정기적으로 검토해야 합니다. 이 문서에서 Azure AD PIM(Privileged Identity Management)에서 액세스 검토를 시작하는 단계를 설명합니다.

## <a name="start-an-access-review"></a>액세스 검토 시작
> [!NOTE]
> Azure Portal의 대시보드에 PIM 응용 프로그램을 추가하지 않았으면 [Azure Privileged Identity Management 시작](active-directory-privileged-identity-management-getting-started.md)
> 
> 

PIM 응용 프로그램 기본 페이지에서 액세스 검토를 시작하는 방법은 세 가지가 있습니다.

* **액세스 검토** > **추가**
* **역할** > **검토** 단추
* 역할 목록에서 검토할 특정 역할 선택 > **검토** 단추

**검토** 단추를 클릭하면 **액세스 검토 시작** 블레이드가 나타납니다. 이 블레이드에서 이름 및 시간 제한을 사용해 검토를 구성하고, 검토할 역할을 선택하고, 검토를 수행할 사용자를 결정합니다.

![액세스 검토 시작 - 스크린 샷][1]

### <a name="configure-the-review"></a>검토 구성
액세스 검토를 만들려면 이름을 지정하고 시작 및 종료 날짜를 설정해야 합니다.

![검토 구성 - 스크린 샷][2]

사용자가 완료할 수 있도록 검토 기간이 충분히 긴지 확인해야 합니다. 검토를 종료 날짜 전에 마친다면 언제나 검토를 일찍 중지할 수 있습니다.

### <a name="choose-a-role-to-review"></a>검토할 역할 선택
각 검토는 오직 하나의 역할에 집중합니다. 특정 역할 블레이드에서 액세스 검토를 시작하지 않는 경우 지금 역할을 선택해야 합니다.

1. **역할 멤버 자격 검토**
   
    ![역할 멤버 자격 검토 - 스크린샷][3]
2. 목록에서 역할 하나를 선택 합니다.

### <a name="decide-who-will-perform-the-review"></a>검토를 수행할 사용자를 결정합니다.
검토를 수행하는 데 세 가지 옵션이 있습니다. 검토를 다른 사람에게 완료하도록 할당할 수 있습니다, 사용자가 직접 수행하거나 각 사용자가 자신의 액세스를 검토하도록 할 수 있습니다.

1. **검토자 선택**
   
    ![검토자 선택 - 스크린 샷][4]
2. 옵션 중 하나를 선택하십시오.
   
   * **검토자 선택**: 액세스가 필요한 사용자를 알 수 없는 경우 이 옵션을 사용합니다. 이 옵션을 사용하여 리소스 소유자 또는 관리자 그룹에게 검토를 완료하도록 할당할 수 있습니다.
   * **Me**: 액세스 검토가 작동하는 방법을 미리 보고자 하거나 그러지 못하는 사람들을 위해 검토하려는 경우 유용합니다.
   * **멤버 자체 검토**: 사용자에게 자신의 역할 할당을 검토하게 하려면 이 옵션을 사용합니다.

### <a name="start-the-review"></a>검토를 시작합니다.
마지막으로 사용자가 자신의 액세스를 승인한다면 그 이유를 제공하도록 요구하는 옵션이 있습니다. 원하면 검토에 대한 설명을 추가하고 **시작**을 선택합니다.

사용자에게 대기 중인 액세스 검토가 있다는 것을 알려주어야 하고, [액세스 검토를 수행하는 방법](active-directory-privileged-identity-management-how-to-perform-security-review.md)을 보여 줍니다.

## <a name="manage-the-access-review"></a>액세스 검토 관리
액세스 검토 섹션에서 검토자가 Azure AD PIM 대시보드에서 검토를 완료하는 진행률을 추적할 수 있습니다. 액세스 권한은 [검토가 완료](active-directory-privileged-identity-management-how-to-complete-review.md)될 때까지 디렉터리에서 변경되지 않습니다.

검토 기간이 끝날 때까지는 사용자에게 검토를 완료하거나, 또는 액세스 검토 섹션에서 검토를 일찍 중지하도록 알릴 수 있습니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>PIM 목차
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
