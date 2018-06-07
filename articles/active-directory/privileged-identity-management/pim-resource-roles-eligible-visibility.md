---
title: Privileged Identity Management에서 Azure에 대한 적격 할당 및 리소스 표시 유형 | Microsoft Docs
description: PIM을 사용할 때 리소스 역할에 멤버를 적격으로 할당하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4804d930a98192d64245784058920eeba7d30212
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149989"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Privileged Identity Management를 사용하여 적격 할당 및 리소스 표시 유형

Azure 리소스 역할에 대한 PIM(Privileged Identity Management)은 중요한 Azure 리소스가 있는 조직에 대한 향상된 보안을 제공합니다. 리소스 관리자는 PIM을 사용하여 리소스 역할에 멤버를 적격으로 할당할 수 있습니다. 다음 섹션에서 Azure 리소스 역할에 대한 다른 할당 형식 및 할당 상태에 대해 자세히 알아봅니다. 

## <a name="assignment-types"></a>할당 형식

Azure 리소스에서 PIM은 두 개의 고유한 할당 형식을 제공합니다.

- 적격
- Active

적격 할당에는 역할을 사용하는 작업을 수행하기 위해 역할의 멤버가 필요합니다. 작업은 Multi-Factor Authentication 검사를 성공하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

활성 할당에는 역할을 사용하는 작업을 수행하기 위해 멤버가 필요하지 않습니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

## <a name="assignment-duration"></a>할당 기간

리소스 관리자는 역할에 대해 PIM 설정을 구성할 때 각 할당 형식에 대한 두 가지 옵션에서 선택할 수 있습니다. 멤버가 PIM의 역할에 할당된 경우 이러한 옵션은 최대 기본 기간이 됩니다. 

관리자는 이러한 할당 형식 중 하나를 선택할 수 있습니다.

- 영구 적격 할당 허용
- 영구 활성 할당 허용

또는 관리자는 이러한 할당 형식 중 하나를 선택할 수 있습니다.

- 적격 할당 만료 기준 시간
- 활성 할당 만료 기준 시간

리소스 관리자가 **영구 적격 할당 허용** 또는 **영구 활성 할당 허용**을 선택하는 경우 리소스에 멤버를 할당하는 모든 관리자는 영구 멤버 자격을 할당할 수 있습니다.

리소스 관리자가 **적격 할당 만료 기준 시간** 또는 **활성 할당 만료 기준 시간**을 선택하는 경우 리소스 관리자는 모든 할당에 지정된 시작 및 종료 날짜를 포함하도록 요구하여 할당 수명 주기를 제어합니다.

> [!NOTE] 
> 리소스 관리자는 지정된 종료 날짜가 있는 모든 할당을 갱신할 수 있습니다. 또한 멤버는 [할당을 확장 또는 갱신](pim-resource-roles-renew-extend.md)하도록 셀프 서비스 요청을 시작할 수 있습니다.


## <a name="assignment-states"></a>할당 상태

Azure 리소스에서 PIM에는 **내 역할**, **역할** 및 PIM의 **멤버** 보기에서 **활성 역할** 탭에 표시되는 두 개의 고유한 할당 상태가 있습니다. 상태는 다음과 같습니다.

- 할당됨
- 활성화됨

**활성 역할**에 나열된 멤버 자격을 볼 때 **STATE** 열의 값을 사용하여 **할당됨**이 활성인 사용자와 적격 할당이 **활성화됨**이고 현재 활성인 사용자 간의 차이점을 구분할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Privileged Identity Manager의 역할 할당](pim-resource-roles-assign-roles.md)
