---
title: Azure Active Directory에서 사용자 라이선스 사용자를 그룹 기반 라이선스로 마이그레이션 | Microsoft Docs
description: Azure Active Directory를 사용하여 개별 라이선스 사용자를 그룹 기반 라이선스로 전환하는 방법
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 01/14/2018
ms.author: curtand
ms.custom: seohack1
ms.openlocfilehash: ed95ba1329ba3052fab3d81327d23475e9902d8a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Azure Active Directory에서 라이선스에 대해 라이선스 사용자를 그룹에 추가하는 방법

개별 사용자에게 라이선스를 할당하기 위해 "직접 할당"을 통해, 즉 PowerShell 스크립트 또는 기타 도구를 사용하여 조직의 사용자에게 기존 라이선스가 배포되었을 수 있습니다. 조직에서 그룹 기반 라이선스를 사용하여 라이선스를 관리하려는 경우 기존 솔루션을 그룹 기반 라이선스로 원활하게 대체하기 위한 마이그레이션 계획이 필요하게 됩니다.

가장 중요한 고려 사항은 그룹 기반 라이선스로의 마이그레이션으로 인해 사용자에게 현재 할당된 라이선스가 일시적으로 손실되는 상황을 피해야 한다는 것입니다. 사용자가 서비스 및 해당 데이터에 액세스하지 못하는 일이 없도록 라이선스를 제거할 수 있는 프로세스는 피해야 합니다.

## <a name="recommended-migration-process"></a>권장 마이그레이션 프로세스

1. 사용자에 대한 라이선스 할당 및 제거를 관리하는 기존 자동화(예: PowerShell) 기능이 있을 것입니다. 이러한 기능은 실행 상태로 둡니다.

2. 새 라이선스 그룹을 만들고(또는 사용할 기존 그룹 결정) 필요한 모든 사용자가 구성원으로 추가되어 있는지 확인합니다.

3. 이러한 그룹에 필요한 라이선스를 할당합니다. 여기서의 기존 자동화(예: PowerShell)가 해당 사용자에게 적용하는 동일한 라이선스 상태가 반영되도록 하는 것이 목표입니다.

4. 해당 그룹의 모든 사용자에게 라이선스가 적용되었는지 확인합니다. 이를 위해 각 그룹의 처리 상태를 확인하고 감사 로그를 확인합니다.

  - 라이선스 세부 정보를 확인하여 개별 사용자를 부분적으로 확인할 수 있습니다. 이를 통해 이러한 사용자에게 동일한 라이선스가 "직접" 할당되고 그룹에서 "상속"되었음을 알 수 있습니다.

  - PowerShell 스크립트를 실행하여 [사용자에게 라이선스가 할당된 방법을 확인](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses)할 수 있습니다.

  - 동일한 제품 라이선스가 사용자에게 직접 방법과 그룹을 통한 방법으로 모두 할당되어도 해당 사용자에게는 라이선스가 하나만 사용됩니다. 따라서 추가 라이선스에 대해 마이그레이션을 수행할 필요가 없습니다.

5. 오류 상태의 사용자가 있는지 각 그룹을 확인하여 실패한 라이선스 할당이 없는지 확인합니다. 자세한 내용은 [그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)을 참조하세요.

6. 원래의 직접 할당을 제거하는 것을 고려합니다. 이러한 작업을 점차적으로 “단계별”로 진행하면서 사용자의 일부에 대한 결과를 모니터링할 수 있습니다.

  사용자에 대한 원래의 직접 할당을 그대로 둘 수 있지만 사용자가 라이선스 그룹을 나가게 되어도 원래 라이선스가 계속 유지되며 이러한 상황을 원치 않을 수 있습니다.

## <a name="an-example"></a>예제

우리 조직에는 1000명의 사용자가 있습니다. 모든 사용자에게는 EMS(Enterprise Mobility + Security) 라이선스가 필요합니다. 200명의 사용자는 재무 부서에 속하며 Office 365 Enterprise E3 라이선스가 필요합니다. 온-프레미스에서 PowerShell 스크립트가 실행되고 있으며 사용자가 들어오고 나갈 때 라이선스를 추가 및 제거합니다. 이 스크립트를 그룹 기반 라이선스로 바꾸어 Azure AD에서 라이선스를 자동으로 관리하려고 합니다.

마이그레이션 프로세스는 다음과 같을 수 있습니다.

1. Azure Portal을 사용하여 Azure AD의 **모든 사용자** 그룹에 EMS 라이선스를 할당합니다. 모든 필수 사용자가 포함된 **재무 부서** 그룹에 E3 라이선스를 할당합니다.

2. 각 그룹의 모든 사용자에 대한 라이선스 할당이 완료되었는지 확인합니다. 각 그룹에 대한 블레이드로 이동하고 **라이선스**를 선택한 후 **라이선스** 블레이드 맨 위에서 처리 상태를 확인합니다.

  - 처리가 완료되었으면 "최신 라이선스 변경 내용이 모든 사용자에게 할당됨"이 표시됩니다.

  - 해당 사용자의 라이선스가 성공적으로 할당되지 않았을 수 있다는 알림은 위쪽에 표시됩니다. 일부 사용자의 라이선스가 부족한가요? 일부 사용자에게 충돌하는 라이선스 SKU가 할당되어 상속 그룹이 라이선스를 할당하지 못하나요?

3. 일부 사용자를 검토하여 직접 및 그룹 라이선스가 모두 할당되었는지 확인합니다. 사용자에 대한 블레이드로 이동한 후 **라이선스**를 선택하고 라이선스 상태를 검사합니다.

  - 다음은 마이그레이션 중에 예상되는 사용자 상태입니다.

      ![예상되는 사용자 상태](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  이 경우 사용자에게 직접 및 상속된 라이선스가 모두 있는 것입니다. **EMS** 및 **E3**가 둘 다 할당되어 있는 것을 볼 수 있습니다.

  - 각 라이선스를 선택하여 사용하도록 설정된 서비스에 대한 세부 정보를 확인합니다. 이를 통해 직접 및 그룹 라이선스가 해당 사용자의 정확히 동일한 서비스 계획을 사용하도록 설정하는지 확인할 수 있습니다.

      ![서비스 계획 확인](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. 직접 및 그룹 라이선스가 동일한 것인지 확인한 후에 사용자에게서 직접 라이선스를 제거할 수 있습니다. 포털에 있는 개별 사용자에게서 라이선스를 제거하여 테스트한 다음 자동화 스크립트를 실행하여 대량으로 제거할 수 있습니다. 포털을 통해 같은 사용자에게서 직접 라이선스를 제거하는 예제는 다음과 같습니다. 라이선스 상태는 변경되지 않지만 직접 할당은 더 이상 표시되지 않습니다.

  ![직접 라이선스 제거](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>다음 단계

그룹을 통한 라이선스 관리에 대한 기타 시나리오에 대해 자세히 알아보려면 이 문서를 읽어 보세요.

* [Azure Active Directory에서 그룹에 라이선스 할당](active-directory-licensing-group-assignment-azure-portal.md)
* [Azure Active Directory의 그룹 기반 라이선스란?](active-directory-licensing-whatis-azure-portal.md)
* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)
