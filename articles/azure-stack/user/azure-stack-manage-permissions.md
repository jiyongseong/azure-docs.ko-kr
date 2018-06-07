---
title: Azure 스택에 사용자별 리소스에 대 한 사용 권한 관리 | Microsoft Docs
description: 서비스 관리자 또는 테 넌 트 RBAC 사용 권한을 관리 하는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4f9354426ba584b26213f8a104c14122a831a453
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Azure Stack Role-Based 액세스 제어를 사용한 리소스에 대 한 액세스 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 지원 동일한 역할 기반 액세스 제어 (RBAC) [액세스 관리에 대 한 보안 모델](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) Microsoft Azure를 사용 하 합니다. RBAC는 사용자, 그룹 또는 구독, 리소스 및 서비스에 대 한 응용 프로그램 액세스 관리를 사용할 수 있습니다.

## <a name="basics-of-access-management"></a>액세스 관리의 기본 사항

역할 기반 액세스 제어는 사용자 환경을 보호 하는 데 사용할 수 있는 세분화 된 액세스 제어를 제공 합니다. 사용자가 특정 범위에서 RBAC 역할을 할당 하 여 필요한 정확한 권한을 제공 합니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스 될 수 있습니다. 읽기는 [Azure 포털에서 역할 기반 액세스 제어](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview) 문서 액세스 관리에 대 한 더 자세한 정보를 얻을 수 있습니다.

### <a name="built-in-roles"></a>기본 제공 역할

Azure 스택 모든 리소스 종류에 적용할 수 있는 세 가지 기본 역할에 있습니다.

* **소유자** 리소스에 대 한 액세스를 비롯 한 모든 사항을 관리할 수 있습니다.
* **참가자** 리소스에 대 한 액세스를 제외한 모든 사항을 관리할 수 있습니다.
* **판독기** 모든 것을 볼 수 있지만 변경할 수 없습니다.

### <a name="resource-hierarchy-and-inheritance"></a>리소스 계층 구조 및 상속

Azure 스택 다음과 같은 리소스 계층 구조에 있습니다.

* 각 구독 디렉터리 하나에 속합니다.
* 각 리소스 그룹은 하나의 구독에 속해 있습니다.
* 각 리소스는 한 리소스 그룹에 속해 있습니다.

부모 범위에서 부여 되는 액세스 하위 범위에서 상속 됩니다. 예: 

* 구독 범위에서 Azure AD 그룹에 읽기 권한자 역할을 할당합니다. 해당 그룹의 멤버는 구독에서 모든 리소스 그룹 및 리소스를 볼 수 있습니다.
* 리소스 그룹 범위에서 응용 프로그램에 참가자 역할을 할당합니다. 응용 프로그램 구독에서 다른 리소스 그룹 하지 있지만 해당 리소스 그룹에 있는 모든 형식의 리소스를 관리할 수 있습니다.

### <a name="assigning-roles"></a>역할 할당

사용자에 게 둘 이상의 역할을 할당할 수 있습니다 및 각 역할은 서로 다른 범위와 연결 될 수 있습니다. 예: 

* 구독-1로 TestUser-A의 읽기 역할을 할당합니다.
* TestUser-A 소유자 역할 TestVM-1을 할당 합니다.

Azure [역할 할당](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal) 문서 보기, 할당 및 역할을 삭제 하는 방법에 대 한 자세한 정보를 제공 합니다.

### <a name="resource-hierarchy-and-inheritance"></a>리소스 계층 구조 및 상속

Azure 스택 다음과 같은 리소스 계층 구조에 있습니다.

* 각 구독 디렉터리 하나에 속합니다.
* 각 리소스 그룹은 하나의 구독에 속해 있습니다.
* 각 리소스는 한 리소스 그룹에 속해 있습니다.

부모 범위에서 부여 되는 액세스 하위 범위에서 상속 됩니다. 예: 

* 할당 된 **판독기** 구독 범위에서 Azure AD 그룹에는 역할입니다. 해당 그룹의 멤버는 구독에서 모든 리소스 그룹 및 리소스를 볼 수 있습니다.
* 할당 된 **참가자** 응용 프로그램 리소스 그룹 범위에서 역할입니다. 응용 프로그램 구독에서 다른 리소스 그룹 하지 있지만 해당 리소스 그룹에 있는 모든 형식의 리소스를 관리할 수 있습니다.

### <a name="assigning-roles"></a>역할 할당

사용자에 게 둘 이상의 역할을 할당할 수 있습니다 및 각 역할은 서로 다른 범위와 연결 될 수 있습니다. 예: 

* 구독-1로 TestUser-A의 읽기 역할을 할당합니다.
* TestUser-A 소유자 역할 TestVM-1을 할당 합니다.

Azure [역할 할당](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-portal) 문서 보기, 할당 및 역할을 삭제 하는 방법에 대 한 자세한 정보를 제공 합니다.

## <a name="set-access-permissions-for-a-user"></a>사용자에 대한 액세스 권한을 설정합니다

다음 단계에서는 사용자의 권한을 구성 하는 방법에 설명 합니다.

1. 관리하려는 리소스에 대한 소유자 권한이 있는 계정으로 로그인합니다.
2. 왼쪽의 탐색 창에서 **리소스 그룹**을 선택합니다.
3. 에 사용 권한을 설정 하려는 리소스 그룹의 이름을 선택 합니다.
4. 리소스 그룹의 탐색 창에서 선택 **액세스 제어 (IAM)** 합니다. **액세스 제어** 보기 리소스 그룹에 대 한 액세스 권한이 있는 항목을 나열 합니다. 이러한 결과 필터링 하 고 권한을 추가 하거나 제거 하려면 메뉴 모음을 사용 하 여 수 있습니다.
5. 에 **액세스 제어** 메뉴 모음에서 **+ 추가**합니다.
6. **사용 권한을 추가**:

   * 할당 하려는 역할을 선택 된 **역할** 드롭 다운 목록입니다.
   * 할당 하려는 리소스 선택는 **액세스 권한을 할당할** 드롭 다운 목록입니다.
   * 액세스 권한을 부여할 디렉터리에서 사용자, 그룹 또는 응용 프로그램을 선택합니다. 표시 이름, 전자 메일 주소 및 개체 식별자를 사용하여 디렉터리를 검색할 수 있습니다.

7. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

[서비스 주체 만들기](azure-stack-create-service-principals.md)