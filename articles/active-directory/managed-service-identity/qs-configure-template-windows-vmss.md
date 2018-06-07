---
title: 템플릿을 사용하여 Azure 가상 머신 확장 집합에서 MSI 구성
description: Azure Resource Manager 템플릿을 사용하여 Azure VMSS에서 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: f7c5d063bfb287de9afe808395b951ecb161da69
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930615"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>템플릿을 사용하여 VMSS 관리 서비스 ID 구성

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 서비스 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure Resource Manager 배포 템플릿을 사용하여 Azure VMSS에서 다음과 같은 관리 서비스 ID 작업을 수행하는 방법을 알아봅니다.
- Azure VMSS에서 시스템 할당 ID를 사용 및 사용하지 않도록 설정
- Azure VMSS에서 사용자 할당 ID 추가 및 제거

## <a name="prerequisites"></a>필수 조건

- 관리 서비스 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 ID의 차이점](overview.md#how-does-it-work)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 다음을 비롯한 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다.

   - [Azure Marketplace의 사용자 지정 템플릿](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)을 사용하여 템플릿을 처음부터 만들거나 기존의 공통 템플릿 또는 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 기반으로 템플릿을 만듭니다.
   - [원본 배포](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) 또는 [배포의 현재 상태](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
   - 로컬 [JSON 편집기(예: VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)를 사용하는 경우 PowerShell 또는 CLI를 사용하여 템플릿을 업로드하고 배포합니다.
   - Visual Studio [Azure 리소스 그룹 프로젝트](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 사용하여 템플릿을 만들고 배포합니다.  

선택한 옵션에 관계 없이 초기 배포 및 재배포 시 템플릿 구문은 동일합니다. 기존 또는 새 VM에서 동일한 방식으로 MSI를 사용합니다. 또한 기본적으로 Azure Resource Manager는 배포에 대해 [증분 업데이트](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)를 수행합니다.

## <a name="system-assigned-identity"></a>시스템 할당 ID

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 시스템 할당 ID를 사용하거나 사용하지 않도록 설정합니다.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Azure VMSS 생성 중에나, 기존 VMSS에 대해 시스템 할당 ID를 사용하도록 설정

1. 템플릿을 편집기에 로드하고 `resources` 섹션 내에서 원하는 `Microsoft.Compute/virtualMachineScaleSets` 리소스를 찾습니다. 사용 중인 편집기와 템플릿을 편집 중인 배포(새 배포 또는 기존 배포)에 따라 실제 리소스는 다음 스크린샷과 약간 다를 수 있습니다.
   
   ![템플릿의 스크린샷 - VM 찾기](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. 시스템 할당 ID를 사용하도록 설정하려면 동일한 수준의 `"identity"` 속성을 `"type": "Microsoft.Compute/virtualMachineScaleSets"` 속성으로 추가합니다. 다음 구문을 사용합니다.

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (선택 사항) 가상 머신 확장 집합 MSI 확장을 `extensionsProfile` 요소로 추가합니다. 이 단계는 Azure IMDS(Instance Metadata Service) ID를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다.  다음 구문을 사용합니다.

   >[!NOTE] 
   > 다음 예제에서는 Windows 가상 머신 확장 집합 확장(`ManagedIdentityExtensionForWindows`)이 배포되고 있다고 가정합니다. 또한 `"name"` 및 `"type"` 요소에 `ManagedIdentityExtensionForLinux`를 대신 사용하여 Linux에 대해 구성할 수도 있습니다.
   >

   ```JSON
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
   ```

4. 작업을 완료하면 템플릿은 다음과 같이 표시됩니다.

   ![업데이트 이후 템플릿의 스크린샷](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 ID를 사용하지 않도록 설정

> [!NOTE]
> 가상 머신에서 관리 서비스 ID를 사용하지 않도록 설정하는 기능은 현재 지원되지 않습니다. 그 동안 시스템 할당 및 사용자 할당 ID를 전환할 수 있습니다.

시스템 할당 ID는 더 이상 필요하지 않고 사용자 할당 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우:

- 템플릿을 편집기에 로드하고 ID 형식을 `'UserAssigned'`로 변경

## <a name="user-assigned-identity"></a>사용자 할당 ID

이 섹션에서는 Azure Resource Manager 템플릿을 사용하여 Azure VMSS에 사용자 할당 ID를 할당합니다.

> [!Note]
> Azure Resource Manager 템플릿을 사용하여 사용자 할당 ID를 만들려면 [사용자 할당 ID 만들기](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity)를 참조하세요.

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Azure VMSS에 사용자 할당 ID 할당

1. `resources` 요소 아래에 다음 항목을 추가하여 사용자 할당 ID를 VMSS에 할당합니다.  `<USERASSIGNEDIDENTITY>`를 직접 만든 사용자 할당 ID의 이름으로 바꿔야 합니다.

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. (선택 사항) `extensionProfile` 요소 아래에 다음 항목을 추가하여 관리 ID 확장을 VMSS에 추가합니다. 이 단계는 Azure IMDS(Instance Metadata Service) ID 엔드포인트를 사용하여 토큰을 검색할 수도 있으므로 선택 사항입니다. 다음 구문을 사용합니다.
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
                    "properties": {
                        "publisher": "Microsoft.ManagedIdentity",
                        "type": "ManagedIdentityExtensionForWindows",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "port": 50342
                        },
                        "protectedSettings": {}
                    }
                }
   ```
3.  완료되면 템플릿은 다음과 같이 표시됩니다.
   
      ![사용자 할당 ID의 스크린샷](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>다음 단계

- MSI에 대한 광범위한 관점은 [관리 서비스 ID 개요](overview.md)를 참조하세요.

