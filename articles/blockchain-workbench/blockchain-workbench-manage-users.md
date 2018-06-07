---
title: Azure Blockchain Workbench의 사용자 관리
description: Azure Blockchain Workbench에서 사용자를 관리하는 방법.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: c17a406a0fd6f1172599ac0379c6afc139403148
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Azure Blockchain Workbench의 사용자 관리

Azure Blockchain Workbench에는 사용자 컨소시엄의 일부인 사용자 및 조직을 위한 사용자 관리가 포함됩니다.

## <a name="prerequisites"></a>필수 조건

Blockchain Workbench 배포가 필요합니다. 배포에 대한 자세한 내용은 [Azure Blockchain Workbench 배포](blockchain-workbench-deploy.md)를 참조하세요.

## <a name="add-azure-ad-users"></a>Azure AD 사용자 추가

Azure Blockchain Workbench는 인증, 액세스 제어 및 역할에 Azure AD(Azure Active Directory)를 사용합니다. Blockchain Workbench Azure AD 테넌트의 사용자는 인증을 수행하고 Blockchain Workbench를 사용할 수 있습니다. 상호 작용하고 작업을 수행하도록 관리자 응용 프로그램 역할에 사용자를 추가합니다.

Blockchain Workbench 사용자는 Azure AD 테넌트에 존재해야 응용 프로그램 및 역할에 할당할 수 있습니다. Azure AD에 사용자를 추가하려면 다음 단계를 사용합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  오른쪽 위 모서리에서 사용자의 계정을 선택하고 Blockchain Workbench에 연결된 Azure AD 테넌트로 전환합니다.
3.  **Azure Active Directory > 사용자**를 선택합니다. 디렉터리에 사용자의 목록이 표시됩니다.
4.  디렉터리에 사용자를 추가하려면 **새 사용자**를 선택합니다. 외부 사용자의 경우 **새 게스트 사용자**를 선택합니다.

    ![새 사용자](media/blockchain-workbench-manage-users/add-ad-user.png)

5.  새 사용자에 대한 필수 필드를 입력합니다. **만들기**를 선택합니다.

[Azure AD](../active-directory/add-users-azure-active-directory.md) 문서에서 Azure AD 내에서 사용자를 관리하는 방법에 대한 자세한 내용을 알아보세요.

## <a name="manage-blockchain-workbench-administrators"></a>Blockchain Workbench 관리자 관리

사용자가 디렉터리에 추가되면 다음 단계에서는 Blockchain Workbench 관리자가 될 사용자를 선택합니다. **관리자** 그룹의 사용자는 Blockchain Workbench의 **관리자 응용 프로그램 역할**에 연결됩니다. 관리자는 사용자를 추가 또는 제거하고, 특정 시나리오에 사용자를 할당하며, 새 응용 프로그램을 만들 수 있습니다.

Azure AD 디렉터리에서 **관리자** 그룹에 사용자를 추가하려면 다음을 수행합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.
2.  오른쪽 위 모서리에서 사용자의 계정을 선택하여 사용자가 Blockchain Workbench에 연결된 Azure AD 테넌트에 있는지 확인합니다.
3.  **Azure Active Directory > 엔터프라이즈 응용 프로그램**을 선택합니다.
4.  Blockchain Workbench에 대한 Azure AD 클라이언트 응용 프로그램 선택
    
    ![모든 엔터프라이즈 응용 프로그램 등록](media/blockchain-workbench-manage-users/select-blockchain-client-app.png)

5.  **사용자 및 그룹 > 사용자 추가**를 선택합니다.
6.  **할당 추가**에서 **사용자**를 선택합니다. 관리자로 추가하려는 사용자를 선택하거나 검색합니다. 선택이 끝나면 **선택**을 클릭합니다.

    ![할당 추가](media/blockchain-workbench-manage-users/add-user-assignment.png)

9.  **역할**이 **관리자**로 설정되었는지 확인합니다.
10. **할당**을 선택합니다. 추가된 사용자가 할당된 관리자 역할이 있는 목록에 표시됩니다.

    ![Blockchain 클라이언트 응용 프로그램 사용자](media/blockchain-workbench-manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Blockchain Workbench 구성원 관리

Blockchain Workbench 응용 프로그램을 사용하여 사용자 컨소시엄의 일부인 사용자 및 조직을 관리합니다. 응용 프로그램 및 역할에 사용자를 추가하거나 제거할 수 있습니다.

브라우저에서 [Blockchain Workbench를 열고](blockchain-workbench-deploy.md#blockchain-workbench-web-url) 관리자로 로그인합니다.

![Blockchain Workbench](media/blockchain-workbench-manage-users/blockchain-workbench-applications.png)

### <a name="managing-members-in-applications"></a>응용 프로그램의 구성원 관리

구성원은 각 응용 프로그램에 추가됩니다. 구성원은 계약을 시작하거나 작업을 수행하는 응용 프로그램 역할을 하나 이상 가질 수 있습니다.

응용 프로그램에 구성원을 추가하려면 **응용 프로그램** 창에서 응용 프로그램 타일을 선택합니다.

![응용 프로그램 선택](media/blockchain-workbench-manage-users/blockchain-workbench-select-application.png)

> [!NOTE]
> 선택한 응용 프로그램에 연결된 구성원의 수는 구성원 타일에 반영됩니다.

#### <a name="add-member-to-application"></a>응용 프로그램에 구성원 추가

1. 현재 구성원의 목록을 표시하려면 구성원 타일을 선택합니다.
2. **구성원 추가**를 선택합니다.

    ![구성원 추가](media/blockchain-workbench-manage-users/application-add-members.png)

3. 사용자의 이름을 검색합니다.  Blockchain Workbench 테넌트에 있는 Azure AD 사용자만 나열됩니다. 사용자를 찾을 수 없는 경우 [Azure AD 사용자를 추가](#add-azure-ad-users)해야 합니다.

    ![구성원 추가](media/blockchain-workbench-manage-users/find-user.png)

4. 드롭다운 메뉴에서 **역할**을 선택합니다.

    ![역할 구성원 선택](media/blockchain-workbench-manage-users/application-select-role.png)

5. **추가**를 선택하여 관련된 역할이 있는 구성원을 응용 프로그램에 추가합니다.

#### <a name="remove-member-from-application"></a>응용 프로그램에서 구성원 제거

1. 현재 구성원의 목록을 표시하려면 구성원 타일을 선택합니다.
2. 제거하려는 사용자의 경우 역할 드롭다운에서 **제거**를 선택합니다.

    ![구성원 제거](media/blockchain-workbench-manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>역할 변경 또는 추가

1. 현재 구성원의 목록을 표시하려면 구성원 타일을 선택합니다.
2. 변경하려는 사용자의 경우 드롭다운 목록을 클릭하고 새 역할을 선택합니다.

    ![역할 변경](media/blockchain-workbench-manage-users/application-change-role.png)

## <a name="next-steps"></a>다음 단계

[Azure Blockchain Workbench에서 블록체인 응용 프로그램 만들기](blockchain-workbench-create-app.md)
