---
title: Azure Active Directory에서 그룹에 라이선스 할당 | Microsoft Docs
description: Azure Active Directory 그룹 기반 라이선스를 사용하여 사용자에게 라이선스를 할당하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 091750d7cadd460ac46d3293951bf4423383c3c5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Azure Active Directory에서 그룹 멤버 자격별로 사용자에게 라이선스 할당

이 문서에서는 Azure Active Directory (Azure AD)에서 사용자 그룹에 제품 라이선스를 할당하고 올바르게 허가를 받았는지 확인하는 과정을 설명합니다.

이 예에서는 테넌트에 **HR 부서**라는 보안 그룹이 포함되어 있습니다. 이 그룹에는 인사 부서의 모든 멤버(약 1,000명의 사용자)이 포함되어 있습니다. 사용자가 부서 전체에 Office 365 Enterprise E3 라이선스를 할당하려고 합니다. 제품에 포함된 Yammer Enterprise 서비스는 해당 부서에서 사용할 준비를 갖출 때까지 일시적으로 사용하지 않도록 설정해야 합니다. 또한 동일한 사용자 그룹에 Enterprise Mobility + Security 라이선스를 배포하려고 합니다.

> [!NOTE]
> 일부 Microsoft 서비스는 모든 위치에서 사용할 수 없습니다. 사용자에게 라이선스를 할당하려면 먼저 관리자가 해당 사용자에 대해 사용 위치 속성을 지정해야 합니다.

> 그룹 라이선스 할당의 경우 사용 위치가 지정되지 않은 사용자는 디렉터리의 위치를 상속합니다. 여러 위치에 사용자가 있는 경우 항상 Azure AD에서 사용자 만들기 흐름의 일부로(예: AAD Connect 구성을 통해) 사용 위치를 설정하는 것이 좋습니다. 이렇게 하면 라이선스 할당의 결과가 항상 정확하고 허용되지 않은 위치에서 사용자에게 서비스가 제공되지 않습니다.

## <a name="step-1-assign-the-required-licenses"></a>1단계: 필요한 라이선스 할당

1. 관리자 계정으로 [**Azure Portal**](https://portal.azure.com)에 로그인합니다. 라이선스를 관리하려면 계정은 전역 관리자 역할 또는 사용자 계정 관리자여야 합니다.

2. 왼쪽 탐색 창에서 **모든 서비스**를 선택한 다음, **Azure Active Directory**를 선택합니다. 이 창을 즐겨찾기에 추가하거나 포털 대시보드에 고정할 수 있습니다.

3. **Azure Active Directory** 창에서 **라이선스**를 선택하여 테넌트의 모든 라이선스 대상 제품을 보고 관리할 수 있는 창을 엽니다.

4. **모든 제품**에서 제품 이름을 선택하여 Office 365 Enterprise E3 및 Enterprise Mobility + Security를 둘 다 선택합니다. 할당을 시작하려면 창 맨 위에 있는 **할당**을 선택합니다.

   ![모든 제품, 라이선스 할당](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. **라이선스 할당** 창에서 **사용자 및 그룹**을 클릭하여 **사용자 및 그룹** 창을 엽니다. 그룹 이름 *HR Department*를 검색하고 그룹을 선택한 다음, 창 맨 아래에서 **선택**을 클릭하여 확인합니다.

   ![그룹 선택](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. **라이선스 할당** 창에서 **할당 옵션(선택 사항)** 을 클릭하여 앞서 선택한 두 가지 제품에 포함된 모든 서비스 계획을 표시합니다. **Yammer Enterprise**를 찾은 후 **해제**하여 제품 라이선스에서 해당 서비스를 사용하지 않도록 설정합니다. **할당 옵션** 맨 아래에서 **확인**을 클릭하여 확인합니다.

   ![할당 옵션](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. 할당을 완료하려면 **라이선스 할당** 창에서 창 맨 아래에 있는 **할당**을 클릭합니다.

8. 오른쪽 위 모서리에 프로세스의 상태와 결과를 보여 주는 알림이 표시됩니다. 그룹에 대한 할당을 완료할 수 없으면(예를 들어 그룹의 기존 라이선스 때문에) 알림을 클릭하여 실패 세부 정보를 확인합니다.

이제 HR Department 그룹의 라이선스 템플릿이 지정되었습니다. Azure AD에서 해당 그룹의 모든 기존 멤버를 처리하기 위한 백그라운드 프로세스가 시작되었습니다. 이 초기 작업은 그룹의 현재 크기에 따라 약간 시간이 걸릴 수 있습니다. 다음 단계에서는 프로세스가 완료되었는지와 문제 해결을 위해 추가적인 관리가 필요한지를 확인하는 방법을 설명합니다.

> [!NOTE]
> 대체 위치인 Azure AD의 **사용자 및 그룹**에서 동일한 할당을 시작할 수 있습니다. **Azure Active Directory** > **사용자 및 그룹** > **모든 그룹**으로 이동합니다. 그 후 그룹을 찾아서 선택한 다음 **라이선스** 탭으로 이동합니다. 창 위쪽의 **할당** 단추를 클릭하면 라이선스 할당 창이 열립니다.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>2단계: 초기 할당이 완료되었는지 확인

1. **Azure Active Directory** > **사용자 및 그룹** > **모든 그룹**으로 이동합니다. 그런 다음 라이선스가 할당된 **HR Department** 그룹을 찾습니다.

2. **HR Department** 그룹 창에서 **라이선스**를 선택합니다. 그러면 사용자에게 라이선스가 완전히 할당되었는지, 확인해야 할 오류가 있는지 신속하게 확인합니다. 다음과 같은 정보가 제공됩니다.

   - 현재 그룹에 할당된 제품 라이선스의 목록입니다. 사용하도록 설정된 특정 서비스를 표시하고 변경을 수행할 항목을 선택합니다.

   - 그룹의 최신 라이선스 변경 상태(모든 사용자 구성원에 대해 변경 내용을 처리 중인지 아니면 처리가 완료되었는지).

   - 라이선스를 할당할 수 없어서 오류 상태인 사용자에 대한 정보.

   ![할당 옵션](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. 라이선스처리에 대한 자세한 내용은 **Azure Active Directory** > **사용자 및 그룹** > *그룹 이름* > **감사 로그**를 참조하세요. 다음 활동에 유의하세요.

   - 활동: **사용자에게 그룹 기반 라이선스 할당 시작**. 시스템이 그룹에 대한 라이선스 할당 변경을 선택하고 모든 사용자 멤버에게 적용하기 시작하면 로깅됩니다. 수행된 변경 내용에 대한 정보가 포함됩니다.

   - 활동: **사용자에게 그룹 기반 라이선스 할당 완료**. 시스템이 그룹의 모든 사용자에 대한 처리를 완료하면 로깅됩니다. 성공적으로 처리된 사용자 수와 그룹 라이선스를 할당하지 못한 사용자 수에 대한 요약 정보가 포함되어 있습니다.

   [이 섹션을 읽고](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) 감사 로그를 사용하여 그룹 기반 라이선스에 의한 변경 내용을 분석하는 방법에 대해 자세히 알아보세요.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>3단계: 라이선스 문제 확인 및 해결

1. **Azure Active Directory** > **사용자 및 그룹** > **모든 그룹**으로 이동한 후 라이선스가 할당된 **HR Department** 그룹을 찾습니다.
2. **HR Department** 그룹 창에서 **라이선스**를 선택합니다. 창 맨 위에 라이선스를 할당하지 못한 사용자가 10명이라는 알림이 표시됩니다. 이 알림을 클릭하면 이 그룹에서 라이선스 오류 상태인 모든 사용자 목록이 열립니다.
3. **할당 실패** 열에는 두 제품 라이선스 모두 사용자에게 할당하지 못했다는 내용이 표시됩니다. **상위 실패 원인** 열에는 실패의 원인이 포함되어 있습니다. 이 예에서는 **충돌하는 서비스 계획**이 실패의 원인입니다.

   ![할당 실패](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. 사용자를 선택하여 **라이선스** 창을 엽니다. 이 창에는 현재 사용자에게 할당된 모든 라이선스가 표시됩니다. 이 예에서는 사용자에게 **키오스크 사용자** 그룹에서 상속된 Office 365 Enterprise E1 라이선스가 있습니다. 이 라이선스는 시스템에서 **HR Department** 그룹에 적용하려고 하는 E3 라이선스와 충돌합니다. 결과적으로 해당 그룹의 어떠한 라이선스도 사용자에게 할당되지 않았습니다.

   ![사용자의 라이선스 보기](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. 이 충돌을 해결하기 위해 **키오스크 사용자** 그룹에서 해당 사용자를 제거합니다. Azure AD에서 변경 내용을 처리한 후에 **HR Department** 라이선스가 올바르게 할당됩니다.

   ![잘못 할당된 라이선스](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>다음 단계

그룹의 라이선스를 관리할 수 있는 기능 집합에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory에서 개별 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션하는 방법](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)
