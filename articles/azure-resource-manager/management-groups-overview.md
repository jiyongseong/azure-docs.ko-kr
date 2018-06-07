---
title: Azure 관리 그룹으로 리소스 구성 | Microsoft Docs
description: 관리 그룹 및 사용 방법에 대해 알아봅니다.
author: rthorn17
manager: rithorn
editor: ''
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: rithorn
ms.openlocfilehash: 53de4afb42e9ea5b7845a9c862dc1e06c6de36df
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="organize-your-resources-with-azure-management-groups"></a>Azure 관리 그룹으로 리소스 구성 

조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 조건을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 조건을 자동으로 상속합니다. 관리 그룹은 어떤 형식의 구독을 사용하든 관계 없이 대규모의 엔터프라이즈급 관리를 제공합니다.

관리 그룹 기능은 공개 미리 보기에서 사용할 수 있습니다. 관리 그룹을 사용하려면 [Azure Portal](https://portal.azure.com)에 로그인하고 **모든 서비스** 섹션에서 **관리 그룹**을 검색합니다. 

예를 들어, VM(가상 머신) 생성에 사용 가능한 지역을 제한하는 정책을 관리 그룹에 적용할 수 있습니다. 이 정책은 해당 지역에만 VM을 만들 수 있도록 허용하는 방식으로 그 지역에 속한 모든 관리 그룹, 구독 및 리소스에 적용됩니다.

## <a name="hierarchy-of-management-groups-and-subscriptions"></a>관리 그룹 및 구독의 계층 구조 

리소스를 통합 정책 및 액세스 관리를 위한 계층 구조로 구성하는 유연한 관리 그룹 및 구독 구조를 만들 수 있습니다. 다음 다이어그램은 부서에서 구성하는 관리 그룹 및 구독으로 구성된 계층의 예를 보여줍니다.    

![트리](media/management-groups/MG_overview.png)

부서별로 그룹화된 계층을 만들면 *상속*하는 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md) 역할을 해당 관리 그룹에 속한 부서에 할당할 수 있습니다. 관리 그룹을 사용하면 역할을 한 번만 할당하면 되므로 워크로드를 줄이고 오류 위험을 낮출 수 있습니다. 

### <a name="important-facts-about-management-groups"></a>관리 그룹에 대한 중요 한 사실
- 단일 디렉터리에서 지원할 수 있는 관리 그룹 수는 10,000개입니다. 
- 관리 그룹 트리에서 지원할 수 있는 최대 깊이 수준은 6입니다.
    - 이 제한에는 루트 수준 또는 구독 수준이 포함되지 않습니다.
- 각 관리 그룹은 하나의 부모만 지원할 수 있습니다.
- 각 관리 그룹은 여러 자식을 가질 수 있습니다. 

### <a name="preview-subscription-visibility-limitation"></a>미리 보기 구독 가시성 제한 
현재 미리 보기 내에서 액세스를 상속받은 구독을 볼 수 없도록 제한됩니다. 액세스는 구독에 상속되지만 Azure Resource Manager를 통해 상속 액세스 권한을 아직 처리할 수 없습니다.  

REST API를 사용하여 구독에 대한 정보를 가져오면 액세스할 수 있는 경우와 같은 세부 정보를 반환하지만 Azure Portal 및 Azure Powershell 내에서 구독은 표시되지 않습니다. 

이 항목은 현재 처리 중이며 "일반 공급"으로 발표된 관리 그룹보다 우선하여 해결될 예정입니다.  

### <a name="cloud-solution-providercsp-limitation-during-preview"></a>미리 보기 중 CSP(클라우드 솔루션 공급자) 제한 
현재 CSP(클라우드 솔루션 공급자) 파트너에 대해 고객의 디렉터리 내에서 고객의 관리 그룹을 만들거나 관리할 수 없도록 제한돼 있습니다.  
이 항목은 현재 처리 중이며 "일반 공급"으로 발표된 관리 그룹보다 우선하여 해결될 예정입니다.


## <a name="root-management-group-for-each-directory"></a>각 디렉터리에 대한 루트 관리 그룹

각 디렉터리에는 "루트" 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 루트 관리 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. 디렉터리 관리자는 처음에 이 루트 그룹의 소유자가 되도록 [자신을 승격해야 합니다](../role-based-access-control/elevate-access-global-admin.md). 관리자는 그룹의 소유자가 되면 계층 구조를 관리할 다른 디렉터리 사용자 또는 그룹에 모든 RBAC 역할을 할당할 수 있습니다.  

### <a name="important-facts-about-the-root-management-group"></a>루트 관리 그룹에 대한 중요한 사실
- 기본적으로 Azure Active Directory ID에 루트 관리 그룹의 이름 및 ID가 제공됩니다. 표시 이름은 언제든지 Azure Portal 내에서 다른 이름을 표시하도록 업데이트할 수 있습니다. 
- 모든 구독 및 관리 그룹은 디렉터리 내의 한 루트 관리 그룹까지 접을 수 있습니다.  
    - 전역 관리가 가능하도록 디렉터리의 모든 항목이 루트 관리 그룹까지 접히게 하는 것이 좋습니다.  
    - 공개 미리 보기 기간에는 디렉터리 내의 모든 구독이 자동으로 루트의 자식이 되지는 않습니다.   
    - 공개 미리 보기 기간에는 새 구독이 기본적으로 루트 관리 그룹에 자동 지정되지 않습니다. 
- 루트 관리 그룹은 다른 관리 그룹과는 다르게 이동하거나 삭제할 수 없습니다. 
  
## <a name="management-group-access"></a>관리 그룹 액세스

Azure 관리 그룹은 모든 리소스 액세스 및 역할 정의를 위한 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 지원합니다. 이러한 권한은 해당 계층 구조에 있는 자식 리소스에 상속됩니다.   

[기본 제공 RBAC 역할](../role-based-access-control/overview.md#built-in-roles)을 관리 그룹에 할당할 수 있지만, 다음과 같은 네 가지 역할이 주로 사용됩니다. 
- **소유자** 는 액세스 권한을 다른 사용자에게 위임할 수 있는 권한을 포함하여 모든 리소스에 대한 전체 액세스 권한을 보유합니다. 
- **참가자**는 모든 유형의 Azure 리소스를 만들고 관리할 수 있지만 다른 사용자에게 액세스 권한을 부여할 수 없습니다.
- **리소스 정책 참가자**는 리소스의 디렉터리에 정책을 만들고 관리할 수 있습니다.     
- **읽기 권한자** 는 기존 Azure 리소스를 볼 수 있습니다. 


## <a name="next-steps"></a>다음 단계 
관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요. 
- [관리 그룹을 만들어 Azure 리소스 구성](management-groups-create.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](management-groups-manage.md)
- [Azure PowerShell 모듈 설치](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [REST API 사양 검토](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Azure CLI 확장 설치](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)

