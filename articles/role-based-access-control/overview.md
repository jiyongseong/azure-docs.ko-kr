---
title: RBAC를 통한 액세스 및 사용 권한 관리 - Azure RBAC | Microsoft Docs
description: Azure Portal에서 Azure 역할 기반 액세스 제어를 통해 액세스 관리를 시작합니다. 디렉터리에서 사용 권한을 할당하기 위해 역할 할당을 사용합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/02/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 4a964d127d4d45d10a1f9d39b585b8c59e30ae90
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>Azure Portal에서 역할 기반 Access Control 시작
보안 지향적 회사는 직원에게 정확히 필요한 권한을 제공하는 데 중점을 두어야 합니다. 권한이 너무 많으면 공격자에게 계정이 노출될 수 있고, 권한이 너무 적으면 직원이 업무를 효율적으로 수행할 수 없습니다. Azure 역할 기반 Access Control(RBAC)은 Azure에 대한 세밀한 액세스 관리를 제공하여 이 문제를 해결하도록 도와줍니다.

RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다. 예를 들어 RBAC를 사용하여 한 명의 직원은 구독의 가상 머신을 관리하도록 하고, 다른 직원은 동일한 구독 내에서 SQL 데이터베이스를 관리하도록 할 수 있습니다.

## <a name="basics-of-access-management-in-azure"></a>Azure에서 액세스 관리의 기본 사항
각각의 Azure 구독은 하나의 Azure AD(Active Directory) 디렉터리와 연결됩니다. 사용자, 그룹 및 해당 디렉터리에서 응용 프로그램은 Azure 구독에서 리소스를 관리할 수 있습니다. Azure 포털, Azure 명령줄 도구 또는 Azure 관리 API를 사용하여 이러한 액세스 권한을 할당합니다.

특정 범위에서 사용자, 그룹 및 응용 프로그램에 적절한 RBAC 역할을 할당하여 액세스 권한을 부여합니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다. 부모 범위에서 할당된 역할은 역할 내에 포함된 하위 항목에 대한 액세스를 부여합니다. 예를 들어 리소스 그룹에 액세스할 수 있는 사용자는 웹 사이트, 가상 머신 및 서브넷을 포함하여 그 안에 포함된 모든 리소스를 관리할 수 있습니다.

![Azure Active Directory 요소 간 관계 - 다이어그램](./media/overview/rbac_aad.png)

할당하는 RBAC 역할에 따라 해당 범위 내에서 사용자, 그룹 또는 응용 프로그램이 관리할 수 있는 리소스가 결정됩니다.

## <a name="built-in-roles"></a>기본 제공 역할
Azure RBAC에는 모든 리소스 유형에 적용되는 3가지 기본 역할이 있습니다.

* **소유자** 는 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다.
* **참여자** 는 모든 유형의 Azure 리소스를 만들고 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수 없습니다.
* **읽기 권한자** 는 기존 Azure 리소스를 볼 수 있습니다.

Azure의 나머지 RBAC 역할은 특정 Azure 리소스의 관리를 허용합니다. 예를 들어 Virtual Machine 참여자 역할을 사용하면 사용자가 가상 머신을 만들고 관리할 수 있습니다. 가상 머신이 연결된 가상 네트워크 또는 서브넷에 액세스할 권한을 부여하지 않습니다. 

[RBAC 기본 제공 역할](built-in-roles.md) 은 Azure에서 사용할 수 있는 역할을 나열합니다. 각 기본 제공 역할이 사용자에게 부여하는 작업 및 범위를 지정합니다. 더 많은 제어를 위해 사용자 고유의 역할을 정의하려는 경우 [Azure RBAC에서 사용자 지정 역할](custom-roles.md)을 빌드하는 방법을 참조하세요.

## <a name="resource-hierarchy-and-access-inheritance"></a>리소스 계층 구조 및 액세스 상속
* Azure에서 각 **구독** 은 하나의 디렉터리에만 속해 있습니다. 그러나 각 디렉터리는 하나 이상의 구독을 가질 수 있습니다.
* 각 **리소스 그룹** 은 하나의 구독에만 속해 있습니다.
* 각 **리소스** 는 하나의 리소스 그룹에만 속해 있습니다.

부모 범위에서 부여되는 액세스 권한은 자식 범위에서 상속됩니다. 예: 

* 구독 범위에서 Azure AD 그룹에 읽기 권한자 역할을 할당합니다. 해당 그룹의 멤버는 구독에서 모든 리소스 그룹 및 리소스를 볼 수 있습니다.
* 리소스 그룹 범위에서 응용 프로그램에 참가자 역할을 할당합니다. 해당 리소스 그룹에서 모든 종류의 리소스를 관리할 수 있지만 구독에서 다른 리소스 그룹을 관리할 수 없습니다.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC와 클래식 구독 관리자 비교
[클래식 구독 관리자와 공동 관리자](../billing/billing-add-change-azure-subscription-administrator.md)는 Azure 구독에 대한 모든 권한을 보유합니다. 이들은 [Azure Portal](https://portal.azure.com), Azure Resource Manager API 및 클래식 배포 모델 API를 사용하여 리소스를 관리할 수 있습니다. RBAC 모델에서는 클래식 관리자에게 구독 범위에서 소유자 역할이 할당됩니다.

Azure 포털 및 새 Azure Resource Manager API만이 Azure RBAC를 지원합니다. RBAC 역할이 할당된 사용자 및 응용 프로그램에서는 Azure 클래식 배포 모델 API를 사용할 수 없습니다.

## <a name="authorization-for-management-vs-data-operations"></a>관리를 위한 권한 부여와 데이터 작업 비교
Azure RBAC는 Azure 포털 및 Azure Resource Manager API에서 Azure 리소스의 관리 작업만을 지원합니다. Azure 리소스의 모든 데이터 수준 작업에 대한 권한을 부여할 수 있는 것은 아닙니다. 예를 들어 Storage 계정 관리 권한을 부여할 수 있지만 Storage 계정 내의 blob 또는 테이블에 대한 관리 권한은 부여할 수 없습니다. 마찬가지로 SQL 데이터베이스를 관리할 수 있지만 그 안의 테이블은 관리할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Portal에서 역할 기반 Access Control](role-assignments-portal.md)을 통해 시작합니다.
* [RBAC 기본 제공 역할](built-in-roles.md)
* [Azure RBAC에서 사용자 지정 역할](custom-roles.md)
