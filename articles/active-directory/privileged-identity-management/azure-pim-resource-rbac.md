---
title: Azure PIM 리소스 RBAC 개요 | Microsoft Docs
description: 용어 및 알림을 포함하여 PIM의 RBAC 기능에 대한 개요를 살펴봅니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: billmath
ms.openlocfilehash: edf22ea0cfe60cb734b4339363d50af050466000
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="pim-for-azure-resources"></a>Azure Resource용 PIM

Azure Active Directory PIM(Privileged Identity Management)을 사용하면 이제 조직 내에서 Azure Resource에 대한 액세스를 관리, 제어 및 모니터링할 수 있습니다. 여기에는 구독, 리소스 그룹 및 Virtual Machines도 포함됩니다. Azure Portal 내에서 Azure RBAC(역할 기반 액세스 제어) 기능을 활용하는 모든 리소스는 Azure AD PIM에서 제공해야 하는 매우 유용한 보안 및 수명 주기 관리 기능 모두와 Azure AD 역할에 곧 제공될 중요한 새 기능 몇 가지를 활용할 수 있습니다. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>리소스 관리자에 유용한 Azure Resource용 PIM

- 관리하는 Azure 리소스에 대해 역할이 할당된 사용자 및 그룹 보기
- 주문형 "Just-In-Time" 액세스를 통해 구독, 리소스 그룹 등과 같은 리소스 관리
- 새 시간 범위 할당 설정을 통해 할당된 사용자/그룹 리소스 액세스 자동 만료
- 빠른 작업 또는 대기 중(on-call) 일정에 대한 임시 리소스 액세스 권한 할당
- 모든 기본 제공 또는 사용자 지정 역할에서 리소스 액세스에 대한 Multi-Factor Authentication 적용 
- 사용자의 활성 세션 동안 리소스 액세스와 상관 관계가 있는 리소스 활동에 대한 보고서 얻기
- 새 사용자 또는 그룹에 리소스 액세스 권한이 할당될 때 및 적격 할당을 활성화할 때 경고 받기

Azure AD PIM에서는 기본 제공 Azure 리소스 역할뿐만 아니라 사용자 지정(RBAC) 역할(다음을 포함하지만 이에 국한되지 않음)도 관리할 수 있습니다.

- 소유자
- 사용자 액세스 관리자
- 참가자
- 보안 관리자
- 보안 관리자 등

>[!NOTE]
소유자 또는 사용자 액세스 관리자 역할에 할당된 사용자 또는 그룹 구성원 및 Azure AD에서 구독 관리를 사용할 수 있는 전역 관리자는 리소스 관리자입니다. 이러한 관리자는 Azure Resources용 PIM을 사용하여 역할을 할당하고, 역할 설정을 구성하며, 액세스를 검토할 수 있습니다. [Azure 리소스에 대한 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 목록을 참조하세요.

## <a name="tasks"></a>작업

PIM은 왼쪽 탐색 메뉴의 [작업] 섹션에서 역할을 활성화하고 보류 중인 활성화/요청 및 [Azure AD 디렉터리 역할](azure-ad-pim-approval-workflow.md)에 대해 보류 중인 승인을 보는 데 편리한 액세스를 제공하고, 보류 중인 응답을 검토합니다.

개요 진입점에서 작업 메뉴 항목 중 하나에 액세스하면 Azure AD 디렉터리 역할과 Azure Resource 역할 둘 다에 대한 결과가 결과 보기에 포함되어 있습니다. 

![](media/azure-pim-resource-rbac/role-settings-details.png)

내 역할에는 Azure AD 디렉터리 역할 및 Azure Resource 역할에 대한 활성 및 적격 역할 할당 목록이 포함되어 있습니다.

## <a name="activate-roles"></a>역할 활성화

Azure Resources에 대한 역할 활성화에서는 적격 역할 구성원이 미래의 날짜/시간으로 활성화를 예약하고 관리자가 구성한 최대 기간 내에서 특정 활성화를 선택할 수 있는 새로운 환경을 소개하고 있습니다. [여기서 Azure AD 역할 활성화](../active-directory-privileged-identity-management-how-to-activate-role.md)에 대해 자세히 알아봅니다.

![](media/azure-pim-resource-rbac/contributor.png)

[활성화] 메뉴에서 원하는 시작 날짜와 시간을 입력하여 해당 역할을 활성화합니다. 선택적으로 활성화 기간(역할이 활성 상태로 유지되는 기간)을 줄이고 필요한 경우 근거를 입력합니다. 그런 다음 [활성화]를 클릭합니다.

시작 날짜와 시간이 수정되지 않는다면 해당 역할이 몇 초 이내에 활성화됩니다. [내 역할] 페이지에서 활성화 배너 메시지에 대한 큐에서 대기 중인 역할이 표시됩니다. 이 메시지를 지우려면 [새로 고침] 단추를 클릭합니다.

![](media/azure-pim-resource-rbac/my-roles.png)

활성화가 미래의 날짜와 시간으로 예약되면 보류 중인 요청이 왼쪽 탐색 메뉴의 [보류 중인 요청] 탭에 표시됩니다. 역할 활성화가 더 이상 필요하지 않은 경우 사용자는 페이지의 오른쪽에 있는 [취소] 단추를 클릭하여 해당 요청을 취소할 수 있습니다.

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>Azure 리소스 검색 및 관리

Azure Resource에 대한 역할을 검색하고 관리하려면 왼쪽 탐색 메뉴의 관리 탭에서 Azure Resources를 선택합니다. 페이지 위쪽의 필터 또는 검색 표시줄을 사용하여 리소스를 찾습니다.

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>리소스 대시보드

[관리자 보기] 대시보드에는 4가지 기본 구성 요소가 있습니다. 지난 7일 동안의 리소스 역할 활성화에 대한 그래픽 표현입니다. 이 데이터에는 선택한 리소스에 대한 범위가 지정되어 있으며 가장 일반적인 역할(소유자, 참가자, 사용자 액세스 관리자) 및 결합된 모든 역할에 대한 활성화가 표시되어 있습니다.

활성화 그래프의 오른쪽에는 사용자 및 그룹 모두에 대한 할당 유형별 역할 할당 분포를 표시하는 두 개의 차트가 있습니다. 차트 조각을 선택하면 값이 백분율로 변경됩니다(또는 그 반대로).

![](media/azure-pim-resource-rbac/admin-view.png)

차트 아래에는 지난 30일 동안 새 역할 할당이 있는 사용자 및 그룹의 수(왼쪽)와 총 할당 수로 정렬된 역할 목록(내림차순)이 표시됩니다.

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>역할 할당 관리

관리자는 왼쪽 탐색 영역에서 [역할] 또는 [구성원]을 선택하여 역할 할당을 관리할 수 있습니다. [역할]을 선택하면 관리자가 관리 작업 범위를 특정 역할로 지정할 수 있으며, [구성원]에서는 해당 리소스에 대한 모든 사용자 및 그룹의 역할 할당을 표시합니다.

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
활성화가 보류 중인 역할이 있는 경우 구성원 자격을 볼 때 알림 배너가 페이지 위쪽에 표시됩니다.

## <a name="assign-roles"></a>역할 할당

역할에 사용자 또는 그룹을 할당하려면 [역할] 보기에서 역할을 선택하거나 [구성원] 보기의 작업 모음에서 [추가]를 클릭합니다.

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
[구성원] 탭에서 사용자 또는 그룹을 추가하는 경우 사용자 또는 그룹을 선택하기 전에 먼저 [추가] 메뉴에서 역할을 선택해야 합니다.

![](media/azure-pim-resource-rbac/select-role.png)

디렉터리에서 사용자 또는 그룹을 선택합니다.

![](media/azure-pim-resource-rbac/choose.png)

드롭다운 메뉴에서 적절한 할당 유형을 선택합니다. 

**Just-In-Time 할당:** 지정된 시간 동안 또는 무기한(역할 설정에서 구성한 경우)으로 사용자 또는 그룹 구성원에게 역할에 대해 적합하지만 비영구적인 액세스 권한을 제공합니다. 

**직접 할당:** 사용자 또는 그룹 구성원이 역할 할당을 활성화할 필요가 없습니다(영구적 액세스라고 함). 작업이 완료될 때 액세스할 필요가 없는 대기 중 이동(on-call shift) 또는 시간이 중요한 활동과 같은 단기 사용에 대해 직접 할당을 사용하는 것이 좋습니다.

![](media/azure-pim-resource-rbac/membership-settings.png)

할당 유형 드롭다운 아래의 확인란을 사용하면 할당이 영구적이어야 하는지(Just-In-Time 할당 활성화가 영구적으로 적합한지/직접 할당이 영구적 활성인지)를 지정할 수 있습니다. 특정 할당 기간을 지정하려면 확인란의 선택을 취소하고 시작 및/또는 종료 날짜/시간 필드를 수정합니다.

>[!NOTE]
다른 관리자가 역할 설정에서 각 할당 유형에 대해 최대 할당 기간을 지정한 경우에는 해당 확인란을 수정할 수 없습니다.

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>활성화 및 Azure 리소스 활동 보기

특정 사용자가 다양한 리소스에서 수행한 작업을 확인해야 하는 경우 적격 사용자에 대해 지정된 활성화 기간과 관련된 Azure 리소스 활동을 검토할 수 있습니다. [구성원] 보기 또는 특정 역할의 구성원 목록에서 사용자를 선택하여 시작합니다. 결과에서는 Azure Resources의 날짜별 사용자 작업 및 동일한 기간 동안의 최근 역할 활성화를 그래픽으로 표시합니다.

![](media/azure-pim-resource-rbac/user-details.png)

특정 역할 활성화를 선택하면 역할 활성화 세부 정보와 해당 사용자가 활성화된 동안 발생한 해당 Azure 리소스 활동이 표시됩니다.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>기존 할당 수정

사용자/그룹 세부 정보 보기에서 기존 할당을 수정하려면 페이지 위쪽의 작업 모음에서 [설정 변경]을 선택합니다. 할당 유형을 [Just-In-Time 할당] 또는 [직접 할당]으로 변경합니다.

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>구독에 액세스 권한이 있는 사용자 검토

[구독]에서 역할 할당을 검토하려면 왼쪽 탐색 영역에서 [구성원] 탭을 선택하거나, 역할을 선택한 다음 구성원을 검토할 특정 역할을 선택합니다. 

작업 모음에서 [검토]를 선택하여 기존 액세스 검토를 보고 [추가]를 선택하여 새 검토를 만듭니다.

![](media/azure-pim-resource-rbac/owner.png)

[액세스 검토에 대해 자세히 알아보기](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
검토는 현재 구독 리소스 종류에 대해서만 지원됩니다.

## <a name="configure-role-settings"></a>역할 설정 구성

역할 설정 구성은 PIM 환경에서 할당에 적용되는 기본값을 정의합니다. 리소스에 대해 이러한 설정을 정의하려면 왼쪽 탐색 영역에서 [역할 설정] 탭을 선택하거나 모든 역할의 작업 모음에서 역할 설정 단추를 선택하여 현재 옵션을 살펴봅니다.

페이지 위쪽의 작업 모음에서 [편집]을 클릭하면 각 설정을 수정할 수 있습니다.

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

설정 변경 내용은 마지막으로 업데이트된 날짜/시간 및 설정을 변경한 관리자를 포함하여 역할 설정 페이지에 기록됩니다.

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>리소스 감사

리소스 감사에서는 리소스에 대한 모든 역할 활동에 대한 보기를 제공합니다. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 정보를 필터링할 수 있습니다.
![](media/azure-pim-resource-rbac/last-day.png) 또한 빠른 액세스도 제공하여 사용자의 활동 세부 정보를 볼 수 있습니다. 보기에서 모든 "역할 활성화" 작업은 특정 요청자의 리소스 활동에 대한 링크입니다.
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>Just Enough Administration

Azure 리소스용 PIM을 사용하면 리소스 역할 할당에 대한 JEA(Just Enough Administration) 모범 사례를 쉽게 적용할 수 있습니다. Azure 구독 또는 리소스 그룹에 할당된 사용자 및 그룹 구성원은 축소된 범위에서 기존 역할 할당을 활성화할 수 있습니다. 

검색 페이지에서 관리해야 하는 종속 리소스를 찾습니다.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

왼쪽 탐색 메뉴에서 [내 역할]을 선택하고 활성화할 적절한 역할을 선택합니다. 아래와 같이 리소스 그룹이 아닌 구독에 역할이 할당되었으므로 할당 유형이 [상속됨]입니다.

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 기본 제공 역할](../../role-based-access-control/built-in-roles.md)
- [여기서 Azure AD 역할 활성화](../active-directory-privileged-identity-management-how-to-activate-role.md)에 대해 자세히 알아보세요.
- [PIM 승인 워크플로](azure-ad-pim-approval-workflow.md)
