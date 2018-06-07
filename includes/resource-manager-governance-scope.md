---
title: "포함 파일"
description: "포함 파일"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 4f5dbbd1da8a221fb5f8b9641e016829987db538
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
항목을 만들기 전에 범위 개념을 검토해 보겠습니다. Azure는 네 가지 관리 수준인 관리 그룹, 구독, 리소스 그룹 및 리소스를 제공합니다. [관리 그룹](../articles/billing/billing-enterprise-mgmt-group-overview.md)은 미리 보기 릴리스에 포함되어 있습니다. 다음 그림은 세 가지 계층의 예를 보여 줍니다.

![범위](./media/resource-manager-governance-scope/scope-levels.png)

이러한 범위 수준에서 관리 설정을 적용합니다. 선택한 수준은 설정이 적용되는 범위를 결정합니다. 하위 수준은 상위 수준의 설정을 상속합니다. 구독에 설정을 적용하면 해당 설정이 구독의 모든 리소스 그룹 및 리소스에 적용됩니다. 리소스 그룹에 설정을 적용하면 해당 설정이 리소스 그룹 및 모든 리소스에 적용됩니다. 그러나 다른 리소스 그룹에는 해당 설정이 적용되지 않습니다.

일반적으로 중요한 설정은 상위 수준에서 적용하고, 프로젝트별 요구 사항은 하위 수준에서 적용하는 것이 좋습니다. 예를 들어, 조직의 모든 리소스가 특정 지역에 배포되도록 하려고 할 경우 이러한 요구 사항을 달성하기 위해 허용되는 위치를 지정하는 정책을 구독에 적용합니다. 조직의 다른 사용자가 새 리소스 그룹 및 리소스를 추가하게 되면 허용되는 위치에 자동으로 적용됩니다. 
