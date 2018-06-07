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
ms.openlocfilehash: c04514218c7ed8dfd72b94345d2deb88e663fda1
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
[Azure 정책](/azure/azure-policy/)은 구독의 모든 리소스가 회사 표준을 충족하도록 하는 데 유용합니다. 승인된 그러한 리소스 종류 및 SKU로만 배포 옵션을 제한하여 비용을 줄이는 정책을 사용합니다. 리소스에 대한 규칙 및 동작을 정의하고, 해당 규칙이 배포하는 동안 자동으로 적용됩니다. 예를 들어 배포되는 리소스의 종류를 제어할 수 있습니다. 또는 리소스에 대한 승인된 위치를 제한할 수 있습니다. 일부 정책은 동작을 거부하고 일부 정책은 동작의 감사를 설정합니다.

정책은 RBAC(역할 기반 액세스 제어)에 보완적입니다. RBAC는 사용자 액세스를 중점적으로 다루며 기본 거부 및 명시적 허용 시스템입니다. 정책은 배포 도중 및 배포 후 리소스 속성에 중점을 둡니다. 이는 기본적으로 허용 및 명시적 거부 시스템입니다.

정책을 이해하기 위해 *정책 정의* 및 *정책 할당*이라는 두 가지 개념이 있습니다. 정책 정의는 적용하려는 관리 조건을 설명합니다. 정책 할당은 특정 범위에 대한 동작에 정책을 더합니다.

![정책 할당](./media/resource-manager-governance-policy/policy-concepts.png)

Azure는 수정 없이 사용할 수 있는 몇 가지 기본 제공 정책 정의를 제공합니다. 사용자는 자신의 범위에서 허용되는 값을 지정하는 매개 변수 값을 전달합니다. 기본 제공 정책 정의가 사용자의 요구 사항을 충족하지 않는 경우 [사용자 지정 정책 정의를 만들 수 있습니다](../articles/azure-policy/create-manage-policy.md).
