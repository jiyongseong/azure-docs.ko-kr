---
title: Azure Active Directory에서 그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 안전하게 마이그레이션하는 방법 | Microsoft Docs
description: '그룹 기반 라이선스를 사용하여 서로 다른 제품 라이선스(예: Office 365 Enterprise E1 및 E3) 간에 사용자를 마이그레이션하는 데 권장되는 프로세스에 대해 설명합니다.'
services: active-directory
keywords: Azure AD 라이선스
documentationcenter: ''
author: piotrci
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2018
ms.author: piotrci
ms.openlocfilehash: 068457044af7af7a55bdbcc4043da3028a68b2d0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-safely-migrate-users-between-product-licenses-by-using-group-based-licensing"></a>그룹 기반 라이선스를 사용하여 제품 라이선스 간에 사용자를 안전하게 마이그레이션하는 방법

이 문서에서는 그룹 기반 라이선스를 사용할 때 제품 라이선스 간에 사용자를 이동하는 데 권장되는 방법에 대해 설명합니다. 이 방법의 목적은 마이그레이션 중에 서비스 또는 데이터 손실이 없도록 하는 것입니다. 사용자가 제품 간에 원활하게 전환해야 합니다. 마이그레이션 프로세스의 다음 두 가지 변형에 대해 설명합니다.

-   Office 365 Enterprise E3 및 Office 365 Enterprise E5 간 마이그레이션과 같이 충돌하는 서비스 계획을 포함하지 않는 제품 라이선스 간의 단순 마이그레이션

-   Office 365 Enterprise E1 및 Office 365 Enterprise E3 간 마이그레이션과 같이 충돌하는 서비스 계획을 포함하는 제품 간의 보다 복잡한 마이그레이션 충돌에 대한 자세한 내용은 [충돌하는 서비스 계획](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans) 및 [동시에 할당될 수 없는 서비스 계획](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-product-and-service-plan-reference#service-plans-that-cannot-be-assigned-at-the-same-time)을 참조하세요.

이 문서에는 마이그레이션 및 확인 단계를 수행하는 데 사용할 수 있는 샘플 PowerShell 코드가 포함되어 있습니다. 이 코드는 수동으로 단계를 수행할 수 없는 대규모 작업에 특히 유용합니다.

## <a name="before-you-begin"></a>시작하기 전에
마이그레이션을 시작하기 전에, 모든 사용자를 마이그레이션하기 위해 특정 가정이 참인지 확인하는 것이 중요합니다. 일부 사용자에 대해 가정이 참이 아니면 얼마 동안 마이그레이션이 실패할 수 있습니다. 결과적으로, 일부 사용자가 서비스 또는 데이터에 액세스하지 못할 수 있습니다. 다음과 같은 가정을 확인해야 합니다.

-   그룹 기반 라이선스를 사용하여 사용자에게 *소스 라이선스*가 할당되어 있습니다. 기존 제품의 라이선스가 단일 소스 그룹에서 상속되며 직접 할당된 것이 아닙니다.

    >[!NOTE]
    >라이선스가 직접 할당된 경우 *대상 라이선스*의 적용을 차단할 수 있습니다. [직접 및 그룹 라이선스 할당](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-advanced#direct-licenses-coexist-with-group-licenses)에 대해 자세히 알아보세요. [PowerShell 스크립트](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-ps-examples#check-if-user-license-is-assigned-directly-or-inherited-from-a-group)를 사용하여 사용자에게 직접 라이선스가 있는지 확인하는 것이 좋습니다.

-   대상 제품에 사용 가능한 라이선스가 충분합니다. 충분한 라이선스가 없으면 일부 사용자가 *대상 라이선스*를 얻지 못할 수 있습니다. [사용 가능한 라이선스 수를 확인](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products)할 수 있습니다.

-   *대상 라이선스*와 충돌할 수 있거나 *소스 라이선스*를 제거하지 못하게 하는 다른 할당된 제품 라이선스 없습니다. Workplace Analytics 또는 Project Online과 같이 다른 제품에 대해 종속성이 있는 추가 기능 제품이 라이선스를 예로 들 수 있습니다.

-   사용자 환경에서 그룹이 관리되는 방식을 이해합니다. 예를 들어, 온-프레미스에서 그룹을 관리하고 Azure AD Connect 통해 Azure AD(Azure Active Directory)와 동기화할 경우, 온-프레미스 시스템을 사용하여 사용자를 추가/제거합니다. 그룹 기반 라이선스에 의해 변경 내용이 Azure AD와 동기화되고 선택되는 데 시간이 소요됩니다. Azure AD 동적 그룹 멤버 자격을 사용하는 경우 대신 해당 특성을 수정하여 사용자를 추가/제거합니다. 그러나 전체 마이그레이션 프로세스는 동일합니다. 유일한 차이점은 그룹 구성원 자격에 대해 사용자를 추가/제거하는 방법입니다.

## <a name="migrate-users-between-products-that-dont-have-conflicting-service-plans"></a>충돌하는 서비스 계획이 없는 제품 간 사용자 마이그레이션
마이그레이션 목표는 그룹 기반 라이선스를 사용하여 사용자 라이선스를 *소스 라이선스*(이 예제에서는 Office 365 Enterprise E3)에서 *대상 라이선스*(이 예제에서는 Office 365 Enterprise E5)로 변경하는 것입니다. 이 시나리오의 두 제품에는 충돌하는 서비스 계획이 포함되어 있지 않으므로 충돌 없이 동시에 완전히 할당할 수 있습니다. 마이그레이션 중에 사용자가 서비스 또는 데이터에 대한 액세스 권한을 잃지 않습니다. 마이그레이션은 소규모 "일괄 처리"로 수행됩니다. 각 일괄 처리에 대한 결과가 유효한지 확인하고, 프로세스 동안 발생할 수 있는 모든 문제의 범위를 최소화할 수 있습니다. 전반적인 프로세스는 다음과 같습니다.

1.  사용자가 소스 그룹의 멤버이며 해당 그룹에서 *소스 라이선스*를 상속합니다.

2.  멤버 없이 *대상 라이선스*로 대상 그룹을 만듭니다.

3.  대상 그룹에 사용자 배치를 추가합니다. 그룹 기반 라이선스는 변경 내용을 선택하고 *대상 라이선스*를 할당합니다. 이 프로세스는 배치 크기 및 테넌트의 기타 활동에 따라 오래 걸릴 수 있습니다.

4.  사용자 배치가 그룹 기반의 라이선스에 의해 완전히 처리되는지 확인합니다. 각 사용자에게 *대상 라이선스*가 할당되어 있는지 확인합니다. 사용자가 다른 제품과 충돌 또는 라이선스 부족과 같은 오류 상태로 종료되지 않았는지 확인합니다. 오류에 대한 자세한 내용은 [Active Directory 라이선스 그룹 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal)을 참조하세요.

5.  이제 사용자에게 *소스 라이선스* 및 *대상 라이선스*가 둘 다 할당되었습니다.

6.  소스 그룹에서 동일한 사용자 배치를 제거합니다. 그룹 기반 라이선스는 변경에 대응하고, *소스 라이선스*가 사용자에게서 제거됩니다.

7.  후속 사용자 배치에 대해 이 프로세스를 반복합니다.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Azure Portal을 사용하여 단일 사용자 마이그레이션
단일 사용자를 마이그레이션하는 방법에 대한 간단한 연습입니다.

**1단계**: 사용자에게 그룹에서 상속된 *소스 라이선스*가 있습니다. 라이선스에 대한 직접 할당은 없습니다.

![그룹에서 상속된 소스 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithSourceLicenseInherited.png)

**2단계**: 사용자가 대상 그룹에 추가되고, 그룹 기반 라이선스가 변경을 처리합니다. 이제 사용자에게는 그룹에서 할당된 *소스 라이선스* 및 *대상 라이선스*가 둘 다 있습니다.

![그룹에서 상속된 소스 및 대상 라이선스가 모두 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithBothSourceAndTargetLicense.png)

**3단계**: 사용자가 소스 그룹에서 추가되고, 그룹 기반 라이선스가 변경을 처리합니다. 이제 사용자에게는 *대상 라이선스*만 있습니다.

![그룹에서 상속된 대상 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithTargetLicenseAssigned.png)

### <a name="automate-migration-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 마이그레이션 자동화
다음 코드 조각에서는 대규모 작업에 대한 마이그레이션 프로세스를 자동화하는 방법을 보여 줍니다.

> [!NOTE]
> 이 샘플 코드는 이 문서의 [마지막 섹션](#powershell-automation-of-migration-and-verification-steps)에 포함된 PowerShell 함수를 사용합니다.

```
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com','MigrationUser2@tailspinonline.com'

############### NON-CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:ENTERPRISEPACK'      # <-- This is the Office 365 Enterprise E3 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPREMIUM'   # <-- This is the Office 365 Enterprise E5 product.

if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will assign the target license $targetSkuId to all users"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceandTargetLicensePresent} 'STEP 2: Checking if all users still have the source license and now also have the target license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**샘플 출력(두 사용자의 마이그레이션)**

```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPREMIUM licenses available (13) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.

STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will assign the target license TailspinOnline:ENTERPRISEPREMIUM to all users
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and now also have the target license from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:ENTERPRISEPACK.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 2

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

## <a name="migrate-users-between-products-that-have-conflicting-service-plans"></a>충돌하는 서비스 계획이 있는 제품 간 사용자 마이그레이션
마이그레이션 목표는 그룹 기반 라이선스를 사용하여 사용자 라이선스를 *소스 라이선스*(이 예제에서는 Office 365 Enterprise E1)에서 *대상 라이선스*(이 예제에서는 Office 365 Enterprise E3)로 변경하는 것입니다. 이 시나리오의 두 제품에 충돌하는 서비스 계획이 포함되어 있으므로 사용자를 원활하게 마이그레이션하기 위해 충돌을 해결해야 합니다. 이러한 충돌에 대한 자세한 내용은 [Active Directory 라이선스 그룹 문제 해결: 충돌하는 서비스 계획](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal#conflicting-service-plans)을 참조하세요. 마이그레이션 중에 사용자가 서비스 또는 데이터에 대한 액세스 권한을 잃지 않습니다. 마이그레이션은 소규모 "일괄 처리"로 수행됩니다. 각 일괄 처리에 대한 결과가 유효한지 확인하고, 프로세스 동안 발생할 수 있는 모든 문제의 범위를 최소화할 수 있습니다. 전반적인 프로세스는 다음과 같습니다.

1.  사용자가 소스 그룹의 멤버이며 해당 그룹에서 *소스 라이선스*를 상속합니다.

2.  멤버 없이 *대상 라이선스*로 대상 그룹을 만듭니다.

3.  대상 그룹에 사용자 배치를 추가합니다. 그룹 기반 라이선스는 변경 내용을 선택하고 *대상 라이선스*를 할당하려고 합니다. 두 제품의 서비스 간 충돌 때문에 할당이 실패합니다. 그룹 기반 라이선스는 이러한 실패를 각 사용자의 오류로 기록합니다. 이 프로세스는 배치 크기 및 테넌트의 기타 활동에 따라 오래 걸릴 수 있습니다.

4.  사용자 배치가 그룹 기반의 라이선스에 의해 완전히 처리되는지 확인합니다. 각 사용자에게 기록된 충돌 오류가 있는지 확인합니다. 일부 사용자가 예상치 않은 오류 상태로 종료되지 않았는지 확인합니다. 오류에 대한 자세한 내용은 [Active Directory 라이선스 그룹 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-problem-resolution-azure-portal)을 참조하세요.

5.  여전히 사용자에게 *소스 라이선스* 및 *대상 라이선스*에 대한 충돌 오류가 있습니다. 사용자에게 *대상 라이선스*는 아직 할당되지 않았습니다.

6.  소스 그룹에서 동일한 사용자 배치를 제거합니다. 그룹 기반 라이선스는 변경에 대응하고, *소스 라이선스*가 각 사용자에게서 제거됩니다. 충돌 오류가 동시에 제거되고(오류를 유발하는 다른 제품 라이선스가 없는 경우) *대상 라이선스*가 할당됩니다. 이 프로세스를 수행하면 전환 중에 서비스 또는 데이터가 손실되지 않습니다.

7.  후속 사용자 배치에 대해 이 프로세스를 반복합니다.

### <a name="migrate-a-single-user-by-using-the-azure-portal"></a>Azure Portal을 사용하여 단일 사용자 마이그레이션
단일 사용자를 마이그레이션하는 방법에 대한 간단한 연습입니다.

**1단계**: 사용자에게 그룹에서 상속된 *소스 라이선스*가 있습니다. 라이선스에 대한 직접 할당은 없습니다.

![그룹에서 상속된 소스 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithSourceLicenseInheritedConflictScenario.png)

**2단계**: 사용자가 대상 그룹에 추가되고, 그룹 기반 라이선스가 변경을 처리합니다. 사용자에게 여전히 *소스 라이선스*가 있으므로 *대상 라이선스*는 충돌로 인해 오류 상태가 됩니다.

![그룹에서 상속된 소스 라이선스 및 오류 상태인 대상 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithSourceLicenseAndTargetLicenseInConflict.png)

**3단계**: 사용자가 소스 그룹에서 추가되고, 그룹 기반 라이선스가 변경을 처리합니다. *대상 라이선스*가 사용자에게 적용됩니다.

![그룹에서 상속된 대상 라이선스가 있는 사용자](media/active-directory-licensing-group-product-migration/UserWithTargetLicenseAssignedConflictScenario.png)


### <a name="automate-migration-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 마이그레이션 자동화
다음 코드 조각에서는 대규모 작업에 대한 마이그레이션 프로세스를 자동화하는 방법을 보여 줍니다.

> [!NOTE]
> 이 샘플 코드는 이 문서의 [마지막 섹션](#powershell-automation-of-migration-and-verification-steps)에 포함된 PowerShell 함수를 사용합니다.

```
# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that the target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId
```

**샘플 출력(두 사용자의 마이그레이션)**

```
Verifying initial assumptions:
Enough TailspinOnline:ENTERPRISEPACK licenses available (61) for users: 2.
migrationuser@tailspinonline.com                OK
migrationuser2@tailspinonline.com               OK
Checks passed for all 2 users.
STEP 1: Adding users to the target group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe. This will put target license TailspinOnline:ENTERPRISEPACK in conflict state with the source license TailspinOnline:STANDARDPACK
Adding user MigrationUser@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe
Adding user MigrationUser2@tailspinonline.com to group bcf279d1-5ad5-46a5-b469-4b8a552aa2fe

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 2: Checking if all users still have the source license and are in conflict state for license from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.

STEP 3: Removing users from the source group b82c04f0-ce30-4ff1-bac7-735d92d83036. This will remove the source license TailspinOnline:STANDARDPACK and remove the conflict on target license TailspinOnline:ENTERPRISEPACK which will become assigned.
Removing user MigrationUser@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036
Removing user MigrationUser2@tailspinonline.com from group b82c04f0-ce30-4ff1-bac7-735d92d83036

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 1

MigrationUser@tailspinonline.com                Expected state: NO
MigrationUser2@tailspinonline.com               Expected state: NO

Total users checked: 2. In expected state: 0. Not yet: 2
Not all users are in expected state. Waiting 60 seconds to try again.

STEP 4: Checking if all users have source license removed and target license assigned from target group. Check iteration: 3

MigrationUser@tailspinonline.com                Expected state: YES
MigrationUser2@tailspinonline.com               Expected state: YES

Total users checked: 2. In expected state: 2. Not yet: 0
Check passed for all users. Exiting check loop.
```

<h2 id="powershell-automation-of-migration-and-verification-steps">마이그레이션을 자동화하고 확인하기 위한 PowerShell 코드 실행</h2>

이 섹션에는 이 문서에 설명된 스크립트를 실행하는 데 필요한 PowerShell 코드가 포함되어 있습니다.

>[!WARNING]
>이 코드는 데모 용도의 예로 제공됩니다. 사용자 환경에서 사용하려는 경우, 먼저 작은 규모로 코드를 테스트하거나 별도의 테스트 테넌트에서 테스트하는 것이 좋습니다. 환경의 특정 요구에 맞게 코드를 조정해야 할 수도 있습니다.

코드를 실행하려면 [Azure AD PowerShell v1.0 라이브러리](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)의 명령을 사용합니다. 스크립트를 실행하기 전에 `connect-msolservice` cmdlet을 실행하여 테넌트에 로그인합니다.

```
# BEGIN: Helper functions that are used in the scripts.

# GetUserObject function
# Retrieve a user object based on the ObjectId or UserPrincipalName.
function GetUserObject
{
    [OutputType([Microsoft.Online.Administration.User])]
    Param([object]$userId)

    $userIdType = $userId.GetType()

    if($userIdType -eq [Guid])
    {
        return Get-MsolUser -ObjectId $userId
    }
    elseif($userIdType -eq [string])
    {
        return Get-MsolUser -UserPrincipalName $userId
    }
    else
    {
        throw "User Id type must be a Guid or a string (UserPrincipalName). The user id type was: $($userIdType.Name)"
    }
}

# GetGuidUserId function
# Get a Guid objectId for a user, even when a UserPrincipal string is passed in.
# Guid ids are needed for group membership manipulation, where UPNs cannot be used.
function GetGuidUserId
{
    [OutputType([Guid])]
    Param([object]$userId)

    [Guid]$guidId = [Guid]::Empty
    if($userId.GetType() -eq [String])
    {
        $user = GetUserObject $userId
        return $user.ObjectId
    }
    elseif($userId.GetType() -eq [Guid])
    {
        return $userId
    }
    else
    {
        throw "UserId type must be a Guid or a string (UserPrincipalName). The user id type was: $($userId.GetType().Name)"
    }
}

# AddUsersToGroup function
# Add a collection of users to a group.
# Note: This function fails if a user is already a member of the specified group.
function AddUsersToGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Adding user $userId to group $groupId"
        Add-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# RemoveUsersFromGroup function
# Remove a collection of users from a group.
# Note: This function fails if a user is not a member of the specified group.
function RemoveUsersFromGroup
{
    Param([object[]]$userIds, [Guid]$groupId)

    foreach($userId in $userIds)
    {
        $fetchId = GetGuidUserId $userId
        Write-Host "Removing user $userId from group $groupId"
        Remove-MsolGroupMember -GroupObjectId $groupId -GroupMemberType User -GroupMemberObjectId $fetchId
    }
}

# GetUserLicense function
# Return the license object that corresponds to the skuId.
# Return NULL if no object is found.
function GetUserLicense
{
    [OutputType([Microsoft.Online.Administration.UserLicense])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    # Look for the specific license SKU in all of the licenses that are assigned to the user.
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

# IsLicenseAssignedToUser function
# Check if the specific SKU license is assigned to the user,
#    regardless of how the license is assigned (directly or via GBL).
function IsLicenseAssignedToUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [string]$skuId)

    $license = GetUserLicense $user $skuId

    return ($license -ne $null)
}

# GetObjectIdsAssigningLicense function
function GetObjectIdsAssigningLicense
{
    [OutputType([Guid[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        return [Guid[]]$license.GroupsAssigningLicense
    }
    return [Array]::CreateInstance([Guid],0)
}

# UserHasLicenseAssignedFromThisGroup function
# Return TRUE if the user inherits the license from the specific group.
# Note: This function returns true only if the license is successfully assigned from the group.
#       If the license is in an error state, the function return false.
function UserHasLicenseAssignedFromThisGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    [Guid[]]$objectsAssigningLicense = GetObjectIdsAssigningLicense $user $skuId

    # GroupsAssigningLicense contains a collection of object IDs for assigning the license.
    # This could be a group object or a user object (contrary to what the name suggests).
    foreach ($assignmentSource in $objectsAssigningLicense)
    {
        # If the collection contains at least one ID that doesn't match the user ID, the license is inherited from a group.
        # Note: The license might also be assigned directly, in addition to being inherited.
        if ($assignmentSource -ieq $groupId)
        {
            return $true
        }
    }
    return $false
}

# GetErrorsForLicense function
# Return error objects for a specific license.
function GetErrorsForLicense
{
    [OutputType([Microsoft.Online.Administration.IndirectLicenseError[]])]
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    [Microsoft.Online.Administration.IndirectLicenseError[]] $errorObjects = $user.IndirectLicenseErrors | Where {"$($_.AccountSku.AccountName):$($_.AccountSku.SkuPartNumber)" -ieq $skuId}

    if($errorObjects -eq $null)
    {
        #there are no errors at all
        return [Array]::CreateInstance([Microsoft.Online.Administration.IndirectLicenseError],0)
    }

    return $errorObjects
}

# GetErrorForLicenseFromGroup function
# Return an error label that's associated with a specific license that's inherited from a specific group.
# Return $null if there's no error.
function GetErrorForLicenseFromGroup
{
    [OutputType([string])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId)


    # There are some errors. Check if any of the errors are associated with the group.
    foreach($licenseError in GetErrorsForLicense $user $skuId)
    {
        if($licenseError.ReferencedObjectId -eq $groupId)
        {
            return $licenseError.Error
        }
    }
    return $null
}

# IsExpectedLicenseStateForGroup function
# Check if the license is in an expected state for a given group.
# If expectedError is set to a value, the function checks if the license is in the specific error state for the group.
# If expectedError is NULL, the function checks if the license is successfully assigned from the group.
function IsExpectedLicenseStateForGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$groupId, [string]$skuId, [string]$expectedError)

    # The license is expected to be fully assigned from the group and not in an error state.
    if([string]::IsNullOrEmpty($expectedError))
    {
        # Check if the assigned license is inherted from the expected group and without an error on it.
        return (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
    }
    # The license is expected to be in the specific error state on the specific group.
    else
    {
        $error = GetErrorForLicenseFromGroup $user $groupId $skuId
        return ($error -ieq $expectedError)
    }
}

# VerifySourceLicensePresentAndTargetLicenseInConflictState function
# Detect if the licenses are in a specific state where the source license is assigned, but the target license is in a conflict state.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       The conflict state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceLicensePresentAndTargetLicenseInConflictState
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if the user still has the source license. If not, abort the script because something is seriously wrong.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is in conflict, as expected.
    $conflictError = 'MutuallyExclusiveViolation'
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $conflictError)
}

# VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup function
# Detect if the licenses are in a specific state where the source license isn't present,
#    but the target license is correctly assigned.
# Note: If the source license is gone and the target license isn't present,
#       the function throws an exception to abort the script.
#       Something went wrong and the user may have lost access to services.
function VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # Check if user has the source license completely removed, which is a prerequisite to the target license eventually kicking in.
    if(IsLicenseAssignedToUser $user $sourceSkuId)
    {
        return $false
    }

    # Check if the user has the target license at all. If not, abort the script because something is seriously wrong.
    if(-Not (IsLicenseAssignedToUser $user $targetSkuId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $targetSkuId assigned, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    # Check if the target license is assigned from the expected target group, and no longer in an error state.
    return (IsExpectedLicenseStateForGroup $user $targetGroupId $targetSkuId $null)
}

# VerifySourceandTargetLicensePresent function
# Detect if the licenses are in the specific state where the source license is assigned and the target license is also assigned.
# Note: If the source license is gone, the function throws an exception to abort the script.
#       This state can signify that something went wrong with the migration steps and the user lost access to services.
function VerifySourceandTargetLicensePresent
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    #check user still has source license - if not, abort because something is seriously wrong
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        throw "User $($user.UserPrincipalName) ($($user.ObjectId)) does not have the expected license $sourceSkuId from source group $sourceGroupId, which may result in loss of access and data. This is unexpected and should be investigated. Aborting execution."
    }
    #check if the target license is also assigned from the target group
    return (UserHasLicenseAssignedFromThisGroup $user $targetSkuId $targetGroupId)
}

# VerifyAssumptionsForUser function
# Verify basic assumptions that should be true for a user before we execute the migration process.
# The function prints details about the verification steps.
# Return TRUE if all of the assumptions are true.
function VerifyAssumptionsForUser
{
    [OutputType([bool])]
    Param([Microsoft.Online.Administration.User]$user,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    $userName = $user.UserPrincipalName
    # 1. The user has the source license assigned from the source group.
    if(-Not (UserHasLicenseAssignedFromThisGroup $user $sourceSkuId $sourceGroupId))
    {
        Write-Host "$userName does not have source license $sourceSkuId assigned from source group $sourceGroupId."
        return $false
    }

    # 2. The user does't have the same source license assigned from another group at the same time,
    #    and the user doesn't have the source license assigned directly.
    [Guid[]]$otherObjectsAssigningLicense = GetObjectIdsAssigningLicense $user $sourceSkuId | Where {$_ -ne $sourceGroupId}
    foreach($otherObject in $otherObjectsAssigningLicense)
    {
        if($otherObject -eq $user.ObjectId)
        {
            Write-Host "$userName has source license assigned directly to the user."
        }
        else
        {
            Write-Host "$username has source license assigned from an additional unexpected group $otherObject."
        }
    }
    if($otherObjectsAssigningLicense -and $otherObjectsAssigningLicense.Count -gt 0)
    {
        return $false
    }

    # 3. The user doesn't have the target license assigned.
    if(IsLicenseAssignedToUser $user $targetSkuId)
    {
        Write-Host "$userName already has target license assigned."
        return $false
    }

    # 4. The user doesn't have the target license in an error state from some groups.
    [Microsoft.Online.Administration.IndirectLicenseError[]]$licenseErrors = GetErrorsForLicense $user $targetSkuId
    foreach($licenseError in $licenseErrors)
    {
        Write-Host "$userName has target license in error state $($licenseError.Error) from unexpected group $($licenseError.ReferencedObjectId)."
    }
    if($licenseErrors -and $licenseErrors.Count -gt 0)
    {
        return $false
    }

    Write-Host "$userName`t`tOK"
    return $true
}

# VerifyAssumptions function
# Check if all of the users to be migrated are in a correct state.
function VerifyAssumptions
{
    [OutputType([bool])]
    Param([object[]]$userIds, [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    Write-Host "Verifying initial assumptions:"

    # Check if there are enough target licenses for all of the users.
    $skuState = Get-MsolAccountSku | Where {$_.AccountSkuId -ieq $targetSkuId}

    if($skuState -eq $null)
    {
        Write-Host "Target license SKU $targetSkuId does not exist in the tenant at all."
        return $false
    }

    $availableLicenses = $skuState.ActiveUnits - $skuState.ConsumedUnits

    if($userIds.Count -gt $availableLicenses)
    {
        Write-Host "Not enough licenses for all users. User count: $($userIds.Count), licenses available: $availableLicenses"
        return $false
    }
    else
    {
        Write-Host "Enough $targetSkuId licenses available ($availableLicenses) for users: $($userIds.Count)."
    }

    # Check if each user to be migrated is in an expected state.
    $usersOK = 0
    $usersNotOK = 0
    foreach($userId in $userIds)
    {
        $user = GetUserObject $userId
        if(VerifyAssumptionsForUser $user $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId)
        {
            ++$usersOK
        }
        else
        {
            ++$usersNotOK
        }
    }
    if($usersNotOK -gt 0)
    {
        Write-Host "Checks passed for $usersOK users, but failed for $usersNotOK users."
        return $false
    }
    Write-Host "Checks passed for all $usersOK users."
    return $true
}

# ExecuteVerificationLoop function
# Execute a verification function (passed in as a delegate by using $checkFunction) for each user.
# The function tracks how many users passed/failed verification.
# The function repeats the verification loop until all of the users have passed the check.
#   The loop may never terminate if some users never reach the expected state.
#   If the loop doesn't terminate, you should investigate to determine the cause.
# Note: If the verification function fails with an exception,
#       such as the function detects an unexpected user state,
#       the loop terminates and investigation into the user state is needed.
function ExecuteVerificationLoop
{
    Param([System.Management.Automation.ScriptBlock]$checkFunction, [string]$consoleMessage, [object[]]$userIds,  [Guid]$sourceGroupId, [string]$sourceSkuId, [Guid]$targetGroupId, [string]$targetSkuId)

    # How long to wait until the loop is retried.
    $sleepIntervalSeconds = 60
    $retryIteration = 1

    While($true)
    {
        Write-Host "`n$consoleMessage. Check iteration: $retryIteration`n"

        $usersInExpectedState = 0
        $usersNotYet = 0

        foreach ($userId in $userIds)
        {
            $user = GetUserObject $userId
            if($checkFunction.InvokeReturnAsIs($user, $sourceGroupId, $sourceSkuId, $targetGroupId, $targetSkuId))
            {
                Write-Host "$userId`t`tExpected state: YES"
                ++$usersInExpectedState
            }
            else
            {
                Write-Host "$userId`t`tExpected state: NO"
                ++$usersNotYet
            }
        }

        Write-Host "`nTotal users checked: $($userIds.Count). In expected state: $usersInExpectedState. Not yet: $usersNotYet"

        if($usersNotYet -eq 0)
        {
            Write-Host "Check passed for all users. Exiting check loop."
            return
        }

        ++$retryIteration
        Write-Host "Not all users are in expected state. Waiting $sleepIntervalSeconds seconds to try again."
        Start-Sleep -Seconds $sleepIntervalSeconds
    }
}
# END: Helper functions that are used in the script.

# BEGIN: Execute the script.

# Enable strict execution mode.
Set-StrictMode -Version latest
# Stop the script when the first exception is thrown.
$ErrorActionPreference = "Stop"

# A batch of users that we want to migrate in this iteration.
# The batch can be specified as an array of User Principal Names (string) or ObjectIds (Guid).
# Note: The batch can be loaded from a text file that represents a larger batch of users that we want to migrate.
[string[]]$usersToMigrate = 'MigrationUser@tailspinonline.com', 'MigrationUser2@tailspinonline.com'

############### CONFLICTING LICENSES SCENARIO ################

# The source group and source license to remove the user from.
$sourceGroupId = [Guid]'b82c04f0-ce30-4ff1-bac7-735d92d83036'
$sourceSkuId = 'TailspinOnline:STANDARDPACK'             # <-- This is the Office 365 Enterprise E1 product.

# The target group and target license to assign to the user.
$targetGroupId = [Guid]'bcf279d1-5ad5-46a5-b469-4b8a552aa2fe'
$targetSkuId = 'TailspinOnline:ENTERPRISEPACK'           # <-- This is the Office 365 Enterprise E3 product.

# Assumptions before migration:
# 1. Users are already in the source group and they have the source license assigned from that group.
# 2. Users don't have the same source license assigned from another group at the same time,
#    and they don't have the source license assigned directly.
#    IMPORTANT: If Assumption 2 isn't true, removing users from the source group in STEP 3
#               won't result in the target license being correctly applied.
# 3. There are enough available licenses to assign a target license to all of the users that are being migrated.
if(-Not (VerifyAssumptions $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId))
{
    throw "Some users did not pass validation checks. See the output for details. Aborting migration process."
}

Write-Host "STEP 1: Adding users to the target group $targetGroupId. This will put target license $targetSkuId in conflict state with the source license $sourceSkuId"
AddUsersToGroup $usersToMigrate $targetGroupId

# Verify that the target license shows up in the conflict state for each user on the list.
# This step runs in a loop, forever, until all of the users are in the expected state.
# Group-based licensing (GBL) can take some time to reflect the changes on users.
# As a result, the loop should terminate after a period of time that's dependent on the size of the user collection.
# Note: If the loop hasn't terminated after a long period of time, stop the script.
#       Inspect the users that are reported as not yet in the expected state.
#       Verify that the users are not blocked for some other reason.
ExecuteVerificationLoop ${function:VerifySourceLicensePresentAndTargetLicenseInConflictState} 'STEP 2: Checking if all users still have the source license and are in conflict state for license from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# Now it's safe to remove the users from the source group.
Write-Host "STEP 3: Removing users from the source group $sourceGroupId. This will remove the source license $sourceSkuId and remove the conflict on target license $targetSkuId which will become assigned."
RemoveUsersFromGroup $usersToMigrate $sourceGroupId

# Verify that target license is now active on each user and the source license is removed.
ExecuteVerificationLoop ${function:VerifySourceLicenseRemovedAndTargetLicenseAssignedFromGroup} 'STEP 4: Checking if all users have source license removed and target license assigned from target group' $usersToMigrate $sourceGroupId $sourceSkuId $targetGroupId $targetSkuId

# END: Execute the script.
```

## <a name="next-steps"></a>다음 단계

다음 문서에서 그룹을 통한 라이선스 관리에 대한 기타 시나리오를 알아보세요.

* [Azure Active Directory에서 그룹에 대한 라이선스 문제 식별 및 해결](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory의 그룹 기반 라이선싱에 대한 PowerShell 예제](active-directory-licensing-ps-examples.md)
* [Azure Active Directory 그룹 기반 라이선스 추가 시나리오](active-directory-licensing-group-advanced.md)
