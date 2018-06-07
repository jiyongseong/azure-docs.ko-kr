---
title: Azure AD 액세스 검토용 프로그램 및 컨트롤 관리 | Microsoft Docs
description: 조직의 각 거버넌스, 위험 관리 및 준수 이니셔티브에 대한 추가 프로그램을 만들어 Azure Active Directory 액세스 검토를 컨트롤로 수집하고 구성할 수 있습니다.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: billmath
ms.openlocfilehash: a3fb812623b490e27907f63c1f7c6610ae754fb8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192883"
---
# <a name="manage-programs-and-their-controls"></a>프로그램 및 해당 컨트롤 관리 

Azure AD(Azure Active Directory)에는 그룹 구성원 및 응용 프로그램 액세스에 대한 액세스 검토가 포함됩니다. 이러한 컨트롤 예제를 통해 조직의 그룹 구성원 자격 및 응용 프로그램에 대한 액세스 권한이 있는 사용자를 확인할 수 있습니다. 조직에서 이러한 컨트롤을 사용하여 거버넌스, 위험 관리 및 준수 요구 사항을 효율적으로 처리할 수 있습니다.

## <a name="create-and-manage-programs-and-their-controls"></a>프로그램 및 해당 컨트롤 만들기 및 관리
액세스 검토를 프로그램으로 구성하여 다양한 용도로 추적하고 수집하는 방법을 간소화할 수 있습니다. 각 액세스 검토를 프로그램에 연결할 수 있습니다. 그런 다음, 감사자에게 제공할 보고서를 준비하면 특정 이니셔티브 범위의 액세스 검토에 초점을 맞출 수 있습니다.  프로그램 및 액세스 검토 결과가 전역 관리자, 보안 관리자 또는 보안 판독기 역할의 사용자에게 표시됩니다.

프로그램 목록을 보려면 [액세스 검토 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)로 이동하고 **프로그램**을 선택합니다.

**기본 프로그램**은 항상 존재합니다. 전역 관리자 역할에 속하는 경우 추가 프로그램을 만들 수 있습니다. 예를 들어 준수 이니셔티브 또는 비즈니스 목표 각각에 대한 프로그램을 하나씩 선택할 수 있습니다.

프로그램이 더 이상 필요하지 않고 연결된 컨트롤이 없는 경우 해당 프로그램을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [그룹의 멤버 또는 응용 프로그램에 액세스에 대한 액세스 검토 만들기](active-directory-azure-ad-controls-create-access-review.md)
- [액세스 검토 결과 검색](active-directory-azure-ad-controls-retrieve-access-review.md)
