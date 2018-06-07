---
title: 역할을 활성화하거나 비활성화하는 방법 | Microsoft Docs
description: Azure Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID에 대한 역할을 활성화하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 02/14/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: c12ba7ed076d531c75b5f9a473d124b94a6f0362
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-activate-or-deactivate-roles-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 역할을 활성화하거나 비활성화하는 방법
Azure AD(Active Directory) Privileged Identity Management를 사용하면 기업이 Azure AD 및 기타 Microsoft Online Services(Office 365 또는 Microsoft Intune 등)에서 권한 있는 리소스에 대한 액세스를 관리하는 방법을 간소화합니다.  

관리 역할에 대해 적격이 되었다면 권한이 필요한 작업을 수행해야 하는 경우 해당 역할을 활성화할 수 있다는 의미입니다. 예를 들어 경우에 따라 Office 365 기능을 관리하는 경우 조직의 권한 있는 역할 관리자는 해당 역할이 다른 서비스에도 또한 영향을 주므로 사용자를 영구적인 전역 관리자로 만들지 못할 수 있습니다. 대신 Exchange Online 관리자와 같은 Azure AD 역할에 대한 자격을 줍니다. 이러한 권한이 필요한 경우 해당 역할을 활성화하도록 요청하고 미리 결정된 기간 동안 제어할 수 있는 관리자 권한을 부여받습니다.

이 문서는 Azure AD PIM(Privileged Identity Management)에서 해당 역할을 활성화해야 하는 관리자를 위해 작성되었습니다. 사용 권한이 필요할 때 역할을 활성화하고 완료 시 역할을 비활성화하는 단계를 안내합니다. 또한 권한 있는 역할 관리자가 역할(미리 보기)을 활성화하는 데 승인이 필요할 수 있습니다. [PIM 승인 워크플로](./privileged-identity-management/azure-ad-pim-approval-workflow.md)에 대한 자세한 내용은 여기를 참조하세요.

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management 응용 프로그램 추가
다른 포털 또는 PowerShell에서 작동하려는 경우에도 [Azure 포털](https://portal.azure.com/) 에서 Azure AD Privileged Identity Management 응용 프로그램을 사용하여 역할 활성화를 요청합니다. Azure 포털에 Azure AD Privileged Identity Management 응용 프로그램이 없는 경우 다음 단계에 따라 시작합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. Azure 포털의 오른쪽 위에서 사용자 이름을 선택하고 작동할 디렉터리를 선택합니다.
3. **모든 서비스**를 선택하고 필터 텍스트 상자를 사용하여 **Azure AD Privileged Identity Management**를 검색합니다.
4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.

## <a name="activate-a-role"></a>역할 활성화
역할을 사용해야 하는 경우 Azure AD Privileged Identity Management 응용 프로그램의 왼쪽 탐색 열에서 **My Roles**(내 역할) 탐색 옵션을 선택하여 활성화를 요청할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/) 에 로그인하고 Azure AD Privileged Identity Management 타일을 선택합니다.
2. **My Roles**(내 역할)를 선택합니다. 페이지 맨 위에 있는 그룹화에 할당된 적격 역할 목록이 나타납니다.
3. 활성화할 역할을 선택합니다.
4. **활성화**를 선택합니다. **역할 활성화 요청** 블레이드가 나타납니다.
5. 일부 역할은 Multi-factor Authentication (MFA)가 있어야 역할을 활성화할 수 있습니다. 세션당 한 번만 인증해야 합니다.
   
    ![역할 활성화 전에 MFA 사용하여 확인합니다 - 스크린샷][2]
6. 텍스트 필드에 활성화 요청 이유를 입력합니다.  일부 역할은 문제 티켓 번호를 제공해야 합니다.
7. **확인**을 선택합니다.  역할에서 승인을 요청하지 않는 경우 역할이 활성화된 상태이며 해당 역할이 활성 역할 목록(적격 역할 할당 목록 바로 아래)에 표시됩니다. [역할에서 활성화하는 데 승인이 필요](./privileged-identity-management/azure-ad-pim-approval-workflow.md)한 경우 요청이 승인 보류 중임을 알려주는 알림 메시지가 브라우저 오른쪽 상단 모서리에 간단하게 표시됩니다.

    ![보류 중인 요청 알림 - 스크린샷][3]

## <a name="deactivate-a-role"></a>역할 비활성화
역할이 활성화된 후 시간 제한(적격 기간)에 도달하면 자동으로 비활성화됩니다.

먼저 관리 작업을 완료한 경우 Azure AD Privileged Identity Management 응용 프로그램에서 수동으로 역할을 비활성화할 수도 있습니다.  **My Roles**(내 역할)를 선택하고, **Active role assignments**(활성 역할 할당) 그룹화에서 사용이 완료된 역할을 선택하고, **비활성화**를 선택합니다.  

## <a name="cancel-a-pending-request"></a>보류 중인 요청 취소
승인이 필요한 역할을 활성화할 필요가 없는 경우 언제든 보류 중인 요청을 취소할 수 있습니다. Azure AD Privileged Identity Management 응용 프로그램의 왼쪽 탐색 열에서 **My Roles**(내 역할) 탐색 옵션을 선택하기만 하면 됩니다.

1. [Azure 포털](https://portal.azure.com/) 에 로그인하고 Azure AD Privileged Identity Management 타일을 선택합니다.
2. **My Roles**(내 역할)를 선택합니다. 페이지 맨 위에 있는 그룹화에 할당된 적격 역할 목록이 나타납니다.
3. 원하는 역할을 선택합니다.
4. 역할 활성화 세부 정보 블레이드에서 **Activation is pending approval**(활성화가 승인 보류 중임) 배너를 선택합니다.
5. **보류 중인 승인** 블레이드 맨 위에서 **취소**를 선택합니다.

   ![보류 중인 요청 취소 스크린샷][4]

## <a name="next-steps"></a>다음 단계
Azure AD Privileged Identity Management에 대한 자세한 정보는 다음 링크를 참조하세요.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
[2]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_activation_MFA.png
[3]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Toast2.png
[4]: ./media/active-directory-privileged-identity-management-how-to-activate-role/PIM_Request_Pending_Banner_Cancel.png
