---
title: "Azure 템플릿을 사용하여 Azure VM에 사용자 할당 MSI를 구성하는 방법"
description: "Azure Resource Manager 템플릿을 사용하여 Azure VM에서 사용자 할당 MSI(관리 서비스 ID)를 구성하기 위한 단계별 지침을 제공합니다."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e01e4c397e0d0a19280a32fc1e8341b57b47e4eb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Azure 템플릿을 사용하여 VM에 사용자 할당 MSI(관리 서비스 ID) 구성

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

관리 서비스 ID는 Azure Active Directory에서 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure Resource Manager 배포 템플릿을 사용하여 Azure VM에 대해 사용자 할당 MSI를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Azure VM 생성 중에/기존 VM에 대해 MSI를 사용하도록 설정

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, Azure Resource Manager 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 다음을 비롯한 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다.

   - [Azure Marketplace의 사용자 지정 템플릿](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)을 사용하여 템플릿을 처음부터 만들거나 기존의 공통 템플릿 또는 [빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 기반으로 템플릿을 만듭니다.
   - [원본 배포](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) 또는 [배포의 현재 상태](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
   - 로컬 [JSON 편집기(예: VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md)를 사용하는 경우 PowerShell 또는 CLI를 사용하여 템플릿을 업로드하고 배포합니다.
   - Visual Studio [Azure 리소스 그룹 프로젝트](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 사용하여 템플릿을 만들고 배포합니다.  

선택한 옵션에 관계 없이 초기 배포 및 재배포 시 템플릿 구문은 동일합니다. 사용자 할당 MSI를 만들고 새로운 또는 기존 VM에 할당하는 것은 동일한 방식으로 수행됩니다. 또한 기본적으로 Azure Resource Manager는 배포에 대해 [증분 업데이트](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)를 수행합니다.

1. Azure에 로컬로 또는 Azure Portal을 통해 로그인하든지 상관없이 MSI 및 VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 구독 및 리소스에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다(예: “소유자” 역할).

2. 템플릿을 편집기에 로드한 후 `resources` 섹션 내에서 원하는 `Microsoft.Compute/virtualMachines` 리소스를 찾습니다. 사용 중인 편집기와 템플릿을 편집 중인 배포(새 배포 또는 기존 배포)에 따라 실제 리소스는 다음 스크린샷과 약간 다를 수 있습니다.

   >[!NOTE] 
   > 이 예제에서는 `vmName`, `storageAccountName`, `nicName` 등을 템플릿에 정의한 것으로 가정합니다.
   >

   ![템플릿의 스크린샷 - VM 찾기](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. `"type": "Microsoft.Compute/virtualMachines"` 속성과 동일한 수준에서 `"identity"` 속성을 추가합니다. 다음 구문을 사용합니다.

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 그런 후에 VM MSI 확장을 `resources` 요소로 추가합니다. 다음 구문을 사용합니다.

   >[!NOTE] 
   > 다음 예제에서는 Windows VM 확장(`ManagedIdentityExtensionForWindows`)을 배포한다고 가정합니다. 또한 `"name"` 및 `"type"` 요소에 `ManagedIdentityExtensionForLinux`를 대신 사용하여 Linux에 대해 구성할 수도 있습니다.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

5. 작업을 완료하면 템플릿은 다음과 같이 표시됩니다.

   ![업데이트 이후 템플릿의 스크린샷](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Azure VM에서 MSI 제거

MSI가 더 이상 필요하지 않은 VM이 있는 경우 다음을 수행합니다.

1. Azure에 로컬로 로그인하든지 또는 Azure Portal을 통해 로그인하든지 상관없이 VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다. 또한 계정이 VM에 대한 쓰기 권한을 부여하는 역할에 속해야 합니다(예: "Virtual Machine 참여자" 역할).

2. 앞 섹션에서 추가한 두 요소 VM의 `"identity"` 속성 및 `"Microsoft.Compute/virtualMachines/extensions"` 리소스를 제거합니다.

## <a name="related-content"></a>관련 콘텐츠

- MSI에 대한 광범위한 관점은 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.

