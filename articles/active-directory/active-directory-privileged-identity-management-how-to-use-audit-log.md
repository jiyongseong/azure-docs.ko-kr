---
title: Azure AD Privileged Identity Management에서 감사 로그를 사용하는 방법 | Microsoft Docs
description: Azure 권한 있는 ID 관리 확장에서 감사 로그를 사용하는 방법을 알아봅니다.
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
ms.openlocfilehash: 20fd9c5ee90947cc2d3816a0590d4780408baa2f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="using-the-audit-log-in-pim"></a>PIM에서 감사 로그 사용
Azure Privileged Identity Management(PIM) 감사 로그를 사용하여 지정된 기간 내의 모든 사용자 할당 및 활성화를 볼 수 있습니다. 테넌트에서 관리자, 최종 사용자 및 동기화 작업을 비롯한 활동의 전체 감사 기록을 보려는 경우 [Azure Active Directory 액세스 및 사용 보고서](active-directory-view-access-usage-reports.md)

## <a name="navigate-to-the-audit-log"></a>감사 로그로 이동
[Azure Portal](https://portal.azure.com) 대시보드에서 **Azure AD Privileged Identity Management** 앱을 선택합니다. 여기에서 PIM 대시보드에 **권한 있는 역할 관리** > **감사 기록**을 클릭하여 감사 로그에 액세스합니다.

## <a name="the-audit-log-graph"></a>감사 로그 그래프
감사 로그를 사용하여 전체 활성화, 일일 최대 활성화, 일일 평균 활성화를 선 그래프로 보도록 사용할 수 있습니다.  감사 기록에 역할이 둘 이상인 경우 역할별로 데이터를 필터링할 수도 있습니다.

**시간**, **작업** 및 **역할** 단추를 사용하여 로그를 정렬합니다.

## <a name="the-audit-log-list"></a>감사 로그 목록
감사 로그 목록의 열은 다음과 같습니다.

* **요청자** - 역할 활성화 또는 변경을 요청한 사용자입니다.  값이 "Azure 시스템"인 경우 Azure 감사 로그에서 자세한 내용을 확인합니다.
* **사용자** - 활성화되어 있거나 역할에 할당된 사용자입니다.
* **역할** - 사용자가 할당하거나 활성화한 역할입니다.
* **작업** - 요청자가 수행하는 작업입니다. 여기에는 할당, 할당 해제, 활성화 또는 비활성화가 포함될 수 있습니다.
* **Time** - 작업이 발생한 시간.
* **Reasoning** - 활성화 중에 reason(이유) 필드에 입력한 텍스트가 있는 경우 여기에 표시됩니다.
* **만료** - 역할의 활성화에만 관련이 있습니다.

## <a name="filter-the-audit-log"></a>감사 로그 필터링
**필터** 단추를 클릭하여 감사 로그에 표시되는 정보를 필터링할 수 있습니다.  **Update chart parameters blade** (차트 매개 변수 업데이트 블레이드)가 표시됩니다.

필터를 설정한 후 **업데이트** 를 클릭하여 로그의 데이터를 필터링합니다.  데이터가 바로 표시되지 않으면 페이지를 새로 고칩니다.

### <a name="change-the-date-range"></a>날짜 범위 변경
**오늘**, **지난 주**, **지난 달**, **사용자 지정** 단추를 사용하여 감사 로그의 시간 범위를 변경합니다.

**사용자 지정** 단추를 선택하면 로그에 대해 날짜 범위를 지정하도록 **시작** 날짜 필드와 **끝** 날짜 필드가 제공됩니다.  MM/DD/YYYY 형식으로 날짜를 입력하거나 **달력** 아이콘을 클릭하여 달력에서 날짜를 선택할 수 있습니다.

### <a name="change-the-roles-included-in-the-log"></a>로그에 포함된 역할 변경
로그에 포함시키거나 제외한 각 역할의 옆에 있는 **역할** 확인란을 선택 또는 선택 해제합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

