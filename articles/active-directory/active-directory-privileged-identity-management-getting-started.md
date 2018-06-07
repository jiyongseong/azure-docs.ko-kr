---
title: Azure AD Privileged Identity Management 시작 | Microsoft Docs
description: Azure 포털에서 Azure Active Directory Privileged Identity Management 응용 프로그램을 사용하여 권한 있는 ID를 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: users-groups-roles
ms.topic: get-started-article
ms.workload: identity
ms.date: 09/17/2017
ms.author: curtand
ms.custom: pim
ms.openlocfilehash: 7137d7ba53c055c869bd967a04085d0c65aec683
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="start-using-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 시작

Azure Active Directory(AD) Privileged Identity Management를 사용하여 조직 내에서 액세스를 관리, 제어 및 모니터링할 수 있습니다. 여기에는 Azure 리소스, Azure AD 및 다른 Microsoft 온라인 서비스(예: Office 365 또는 Microsoft Intune)의 리소스에 대한 액세스가 포함됩니다.

이 문서에서는 Azure AD PIM 앱을 Azure 포털 대시보드에 추가하는 방법을 알려줍니다.

## <a name="add-the-privileged-identity-management-application"></a>Privileged Identity Management 응용 프로그램 추가

Azure AD Privileged Identity Management를 사용하기 전에 응용 프로그램을 Azure 포털 대시보드에 추가해야 합니다.

1. 해당 디렉터리의 전역 관리자로 [Azure 포털](https://portal.azure.com/) 에 로그인합니다.
2. 조직에 둘 이상의 디렉터리가 있는 경우 Azure 포털의 오른쪽 위에서 사용자 이름을 선택합니다. PIM을 사용할 디렉터리를 선택합니다.
3. **모든 서비스**를 선택하고 필터 텍스트 상자를 사용하여 **Azure AD Privileged Identity Management**를 검색합니다.
4. **대시보드에 고정** 옵션을 선택하고 **만들기**를 클릭합니다. Privileged Identity Management 응용 프로그램이 열립니다.

디렉터리에서 Azure AD Privileged Identity Management를 사용하는 첫 번째 사용자인 경우 디렉터리에 **보안 관리자** 및 **권한 있는 역할 관리자** 역할이 자동으로 할당됩니다. 권한 있는 역할 관리자만 사용자의 Azure AD 디렉터리 역할 할당을 관리할 수 있습니다. 또한 [보안 마법사](active-directory-privileged-identity-management-security-wizard.md)를 실행하도록 선택할 수도 있습니다. 초기 검색 및 할당 경험을 안내합니다.

## <a name="navigate-to-your-tasks"></a>태스크로 이동합니다.

Azure AD Privileged Identity Management를 설정하면 응용 프로그램을 열 때마다 탐색 블레이드가 표시됩니다. 이 블레이드를 사용하여 사용자 ID 관리 작업을 수행합니다.

![PIM의 최상위 태스크 - 스크린샷](./media/active-directory-privileged-identity-management-getting-started/PIM_Tasks_New.png)

- **내 역할**은 사용자에게 할당된 적합하며 활성 상태인 역할 목록을 표시합니다. 여기서 할당된 적합한 역할을 활성화할 수 있습니다.
- **요청 승인(미리 보기)** 은 승인하도록 지정된 디렉터리에 있는 사용자가 적합한 Azure AD 디렉터리 역할을 활성화하기 위한 요청 목록을 표시합니다. [자세한 정보](./privileged-identity-management/azure-ad-pim-approval-workflow.md)
- **보류 중인 요청(미리 보기)** 은 적합한 역할 할당을 활성화하기 위한 보류 중인 요청을 표시합니다.
- **액세스 검토**는 자신 또는 다른 사용자에 대한 액세스를 검토하는지와 관계 없이 완료하도록 지정된 활성 액세스 검토를 나열합니다.
- 왼쪽 탐색 메뉴의 관리 섹션 아래에 있는 **Azure AD 디렉터리 역할**은 권한 있는 역할 관리자가 역할 할당을 관리하고, 역할 활성화 설정을 변경하고, 액세스 검토 등을 시작하는 대시보드입니다. 이 대시보드는 권한 있는 역할 관리자가 아닌 사용자에게 비활성화됩니다. 이러한 사용자는 [내 보기]라는 특수한 대시보드에 액세스할 수 있습니다. [내 보기] 대시보드는 전체 테넌트가 아닌 대시보드에 액세스하는 사용자에 대한 정보만 표시합니다.
- 왼쪽 탐색 메뉴의 관리 섹션 아래에 있는 **Azure 리소스 역할(미리 보기)** 은 선택한 역할 할당이 있는 구독 리소스 목록을 표시합니다. 

## <a name="next-steps"></a>다음 단계
[Azure AD Privileged Identity Management 개요](active-directory-privileged-identity-management-configure.md) 에는 조직의 관리 액세스 권한을 관리할 수 있는 방법에 대한 자세한 정보가 포함되어 있습니다.

[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-configure/PIM_EnablePim.png
