---
title: StorSimple에 역할 기반 액세스 제어 사용 | Microsoft Docs
description: StorSimple 컨텍스트에서 Azure RBAC(역할 기반 액세스 제어)를 사용하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 674f4ec53300643450d8a576db6fcb50e86dd9d2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161161"
---
# <a name="role-based-access-control-for-storsimple"></a>StorSimple에 대한 역할 기반 액세스 제어

이 문서는 StorSimple 장치에 RBAC(역할 기반 액세스 제어)를 사용하는 방법에 대해 간략히 설명합니다. RBAC는 Azure에 대한 정밀 액세스 관리를 제공합니다. RBAC를 사용하여 모든 사람들에게 무제한 액세스 권한을 주는 대신 StorSimple 사용자가 작업을 수행하는 데 필요한 만큼의 액세스 권한을 부여합니다. Azure에서 액세스 관리의 기초에 대한 자세한 내용은 [Azure Portal에서 역할 기반 액세스 제어 시작](../role-based-access-control/overview.md)을 참조하세요.

이 문서는 Azure Portal에서 실행되는 업데이트 3.0 이상을 실행하는 StorSimple 8000 시리즈 장치에 적용됩니다.

## <a name="rbac-roles-for-storsimple"></a>StorSimple에 대한 RBAC 역할

RBAC는 역할을 기반으로 할당할 수 있습니다. 이 역할은 환경에서 사용할 수 있는 리소스를 기준으로 특정 권한 수준을 보장합니다. StorSimple 사용자는 기본 제공 또는 사용자 지정의 두 가지 유형 중에서 역할을 선택할 수 있습니다.

* **기본 제공 역할** - 기본 제공 역할은 owner, contributor, reader 또는 사용자 액세스 관리자가 있습니다. 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/overview.md#built-in-roles)을 참조하세요.

* **사용자 지정 역할** - 기본 제공 역할이 요구 사항에 맞지 않을 경우 StorSimple에 대해 사용자 지정 RBAC 역할을 만들 수 있습니다. 사용자 지정 RBAC 역할을 만들려면 기본 제공 역할을 시작하고 편집한 다음 환경에서 다시 가져옵니다. 역할의 다운로드 및 업로드는 Azure PowerShell 또는 Azure CLI를 사용하여 관리합니다. 자세한 내용은 [역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

Azure Portal에서 StorSimple 장치 사용자에 대해 사용할 수 있는 다른 역할을 보려면 StorSimple 장치 관리자 서비스로 이동한 다음 **액세스 제어(IAM) > 역할**로 이동하십시오.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>StorSimple 인프라 관리자에 대한 사용자 지정 역할 만들기

다음 예제에서는 사용자가 모든 리소스 범위를 볼 수 있지만 편집하거나 새 리소스 범위를 만들 수 없는 기본 제공 역할인 **Reader**부터 시작합니다. 그런 다음 이 역할을 확장해 새로운 사용자 지정 역할인 StorSimple 인프라 관리자를 만듭니다. 이 역할은 StorSimple 장치의 인프라를 관리할 수 있는 사용자에게 할당됩니다.

1. 관리자 권한으로 Windows PowerShell을 실행합니다.

2. Azure에 로그인합니다.

    `Connect-AzureRmAccount`

3. Reader 역할을 컴퓨터에서 JSON 템플릿으로 내보냅니다.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Visual Studio에서 JSON 파일을 엽니다. 일반적인 RBAC 역할은 세 가지 주요 섹션, 즉, **Actions**, **NotActions**, **AssignableScopes**으로 구성되어 있습니다.

    **Actions** 섹션에서는 이 역할에 대해 허용된 모든 작업이 나열됩니다. 각 작업은 리소스 공급자로부터 할당됩니다. StorSimple 인프라 관리자의 경우 `Microsoft.StorSimple` 리소스 공급자를 사용합니다.

    구독에 등록되어 있고 사용 가능한 모든 리소스 공급자를 보려면 PowerShell을 사용합니다.

    `Get-AzureRMResourceProvider`

    또한 리소스 공급자를 관리하려면 사용 가능한 모든 PowerShell cmdlet을 확인할 수 있습니다.

    **NotActions** 섹션에는 특정 RBAC 역할에 대해 제한된 모든 작업이 나열됩니다. 이 예제에서는 아무 작업도 제한되어 있지 않습니다.
    
    **AssignableScopes**에는 구독 ID가 나열됩니다. RBAC 역할에 사용된 명시적 구독 ID가 포함되어 있어야 합니다. 올바른 구독 ID를 지정하지 않은 경우 구독에 역할을 가져올 수 없습니다.

    위의 사항에 주의하면서 파일을 편집합니다.

    ```
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. 사용자 지정 RBAC 역할을 다시 환경으로 가져옵니다.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


이 역할은 이제 **액세스 제어** 블레이드의 역할 목록에 나타납니다.

![RBAC 역할 보기](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

자세한 내용은 [사용자 지정 역할](../role-based-access-control/custom-roles.md)로 이동하세요.

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>PowerShell을 통해 사용자 지정 역할 생성을 위한 샘플 출력

```
PS C:\WINDOWS\system32> Connect-AzureRmAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>사용자 지정 역할에 사용자 추가

역할 할당의 범위인 리소스, 리소스 그룹 또는 구독 내에서 액세스 권한을 부여합니다. 액세스 권한을 제공할 때에는 부모 노드에서 부여한 액세스 권한이 자식에게 상속된다는 점을 기억하세요. 자세한 내용을 보려면 [리소스 계층 및 액세스 권한 상속(영문)](../role-based-access-control/overview.md#resource-hierarchy-and-access-inheritance)으로 이동하세요.

1. **액세스 제어(IAM)** 로 이동합니다. 액세스 제어 블레이드에서 **+ 추가**를 선택합니다.

    ![RBAC 역할에 액세스 권한 추가](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. 할당하려는 역할을 선택합니다. 이 경우는 **StorSimple 인프라 관리자**입니다.

3. 액세스 권한을 부여할 디렉터리에서 사용자, 그룹 또는 응용 프로그램을 선택합니다. 표시 이름, 전자 메일 주소 및 개체 식별자를 사용하여 디렉터리를 검색할 수 있습니다.

4. **저장**을 선택하여 할당을 만듭니다.

    ![RBAC 역할에 사용 권한 추가](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

**사용자 추가** 알림은 진행 상태를 추적합니다. 사용자가 성공적으로 추가된 후 액세스 제어의 사용자 목록이 업데이트됩니다.

## <a name="view-permissions-for-the-custom-role"></a>사용자 지정 역할에 대한 사용자 권한 보기

이 역할을 만들면 Azure Portal에서 이 역할과 연결된 사용 권한을 볼 수 있습니다.

1. 이 역할과 관련된 사용 권한을 보려면 **액세스 제어(IAM) > 역할 > StorSimple 인프라 관리자**로 이동합니다. 이 역할의 사용자 목록이 표시됩니다.

2. StorSimple 인프라 관리자를 선택하고 **사용 권한**을 클릭합니다.

    ![StorSimple 인프라 관리자 역할의 사용 권한 보기](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. 이 역할과 연결된 사용 권한이 표시됩니다.

    ![StorSimple 인프라 관리자 역할에서 사용자 보기](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>다음 단계

[내부 및 외부 사용자에 대한 사용자 지정 역할 할당](../role-based-access-control/role-assignments-external-users.md) 방법에 대해 배웁니다.

