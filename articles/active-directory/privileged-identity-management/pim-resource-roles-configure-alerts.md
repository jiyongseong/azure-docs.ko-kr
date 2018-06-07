---
title: Privileged Identity Management를 사용하여 Azure 리소스에 대한 보안 경고 관리 | Microsoft Docs
description: PIM 보안 경고를 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c6c057541b3e3067de6331bab6ca9cccfa092710
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149188"
---
# <a name="manage-security-alerts-for-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management를 사용하여 Azure 리소스에 대한 보안 경고 관리
Azure 리소스에 대한 PIM(Privileged Identity Management)은 사용자의 환경에 의심스럽거나 안전하지 않은 활동이 있을 때 경고를 생성합니다. 경고가 트리거될 때 경고 페이지에 표시됩니다. 

![경고 페이지](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>경고 검토
경고를 선택하여 경고나 재구성 조언을 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

![경고 보고서](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alerts
| 경고 | 심각도 | 트리거 | 권장 사항 |
| --- | --- | --- | --- |
| **너무 많은 소유자를 리소스에 할당** |중간 |너무 많은 사용자가가 소유자 역할을 보유하고 있습니다. |목록에서 사용자를 검토하고 권한이 적은 역할에 다시 할당합니다. |
| **너무 많은 영구 소유자를 리소스에 할당** |중간 |너무 많은 사용자가 역할에 영구적으로 할당되었습니다. |목록에서 사용자를 검토하고 일부를 다시 할당하여 역할 사용을 위한 활성화를 요구합니다. |
| **만든 역할 중복** |중간 |여러 역할에는 동일한 조건이 있습니다. |이러한 역할 중 하나만 사용합니다. |


### <a name="severity"></a>심각도
* **높음**: 정책 위반으로 인해 즉각적인 조치를 요구합니다. 
* **보통**: 즉각적인 조치를 요구하지는 않지만 잠재적으로 정책이 위반될 수 있음을 나타냅니다.
* **낮음**: 즉각적인 조치를 요구하지는 않지만 정책 변경을 제안합니다.

## <a name="configure-security-alert-settings"></a>보안 경고 설정 구성
경고 페이지에서 **설정**으로 이동합니다.
![설정](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

환경 및 보안 목표로 작업하는 다양한 경고에서 설정을 사용자 지정합니다.
![설정 사용자 지정](media/azure-pim-resource-rbac/rbac-alert-settings.png)
