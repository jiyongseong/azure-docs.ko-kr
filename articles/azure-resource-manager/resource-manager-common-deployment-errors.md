---
title: 일반적인 Azure 배포 오류 해결 | Microsoft Docs
description: Azure Resource Manager를 사용하여 Azure에 리소스를 배포할 때 발생하는 일반적인 오류를 해결하는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: 배포 오류 Azure 배포, Azure에 배포
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3ecc1a9557c7854a0771decb3cc7f7597bcd87dd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결

이 문서에서는 발생할 수 있는 일반적인 Azure 배포 오류 중 일부에 대해 설명하고 오류를 해결하기 위한 정보를 제공합니다. 배포 오류에 대한 오류 코드를 찾을 수 없는 경우 [오류 코드](#find-error-code) 찾기를 참조하세요.

## <a name="error-codes"></a>오류 코드

| 오류 코드 | 해결 방법 | 자세한 정보 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 저장소 계정에 대한 명명 제한 사항을 따릅니다. | [저장소 계정 이름 오류 해결](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | 사용 가능한 저장소 계정 속성을 확인합니다. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | 클러스터나 지역에 사용할 수 있는 리소스가 없거나 요청한 VM 크기를 지원할 수 없습니다. 나중에 요청을 다시 시도하거나 다른 VM 크기를 요청합니다. | [Linux의 프로비전 및 할당 문제](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [Windows의 프로비전 및 할당 문제](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) 및 [할당 문제 해결](../virtual-machines/windows/allocation-failure.md)|
| AnotherOperationInProgress | 동시 작업이 완료될 때까지 기다립니다. | |
| AuthorizationFailed | 계정 또는 서비스 주체가 배포를 완료하는 데 충분한 권한이 없습니다. 계정이 속한 역할 및 배포 범위에 대한 액세스 권한을 확인합니다. | [Azure 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md) |
| BadRequest | Resource Manager에서 예상한 것과 일치하지 않는 배포 값을 보냈습니다. 문제 해결에 도움이 되는 내부 상태 메시지를 확인합니다. | [템플릿 참조](/azure/templates/) 및 [지원되는 위치](resource-manager-templates-resources.md#location) |
| 충돌 | 리소스의 현재 상태에 허용되지 않는 작업을 요청하고 있습니다. 예를 들어 디스크 크기 조정은 VM을 만들거나 VM의 할당을 취소할 때만 허용됩니다. | |
| DeploymentActive | 이 리소스 그룹에 대한 동시 배포가 완료될 때까지 기다립니다. | |
| DeploymentFailed | DeploymentFailed 오류는 해결하는 데 필요한 세부 정보를 제공하지 않는 일반 오류입니다. 자세한 정보를 제공하는 오류 코드에 대해서는 오류 세부 정보를 살펴봅니다. | [오류 코드 찾기](#find-error-code) |
| DeploymentQuotaExceeded | 리소스 그룹당 800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. Azure CLI의 경우 [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) 또는 PowerShell에서 [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment)를 사용하여 기록에서 항목을 삭제할 수 있습니다. 배포 기록에서 항목을 삭제해도 배포 리소스에는 영향을 주지 않습니다. | |
| DnsRecordInUse | DNS 레코드 이름은 고유해야 합니다. 다른 이름을 제공하거나 기존 레코드를 수정합니다. | |
| ImageNotFound | VM 이미지 설정을 확인합니다. |  |
| InUseSubnetCannotBeDeleted | 리소스를 업데이트하려고 할 때 이 오류가 발생할 수 있지만, 해당 리소스를 삭제한 다음 만들면 요청이 처리됩니다. 변경되지 않은 모든 값을 지정해야 합니다. | [리소스 업데이트](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 해당 테넌트에 대한 액세스 토큰을 가져옵니다. 계정이 속한 테넌트의 토큰만 가져올 수 있습니다. | |
| InvalidContentLink | 아마 사용할 수 없는 중첩된 템플릿에 연결하려고 했을 것입니다. 중첩된 템플릿에 제공된 URI를 다시 한 번 확인합니다. 저장소 계정에 해당 템플릿이 있는 경우 액세스 가능한 URI인지 확인합니다. SAS 토큰을 전달해야 합니다. | [연결된 템플릿](resource-group-linked-templates.md) |
| InvalidParameter | 리소스에 대해 제공한 값 중 하나가 예상 값과 일치하지 않습니다. 이 오류는 다양한 조건으로 인해 발생할 수 있습니다. 예를 들어 암호가 완전하지 않거나 Blob 이름이 올바르지 않을 수 있습니다. 오류 메시지를 확인하여 수정해야 하는 값을 결정합니다. | |
| InvalidRequestContent | 배포 값에 예기치 않은 값이 포함되었거나 필요한 값이 누락되었습니다. 리소스 종류에 대한 값을 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidRequestFormat | 배포를 실행할 때 디버그 로깅을 사용하도록 설정하고 요청 내용을 확인합니다. | [디버그 로깅](#enable-debug-logging) |
| InvalidResourceNamespace | **type** 속성에 지정한 리소스 네임스페이스를 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidResourceReference | 리소스가 아직 없거나 잘못 참조되었습니다. 종속성을 추가해야 하는지 확인합니다. **reference** 함수를 사용할 때 시나리오에 필요한 매개 변수가 포함되었는지 확인합니다. | [종속성 오류 해결](resource-manager-not-found-errors.md) |
| InvalidResourceType | **type** 속성에 지정한 리소스 종류를 확인합니다. | [템플릿 참조](/azure/templates/) |
| InvalidSubscriptionRegistrationState | 리소스 공급자에 구독을 등록합니다. | [등록 오류 해결](resource-manager-register-provider-errors.md) |
| InvalidTemplate | 템플릿 구문에 오류가 있는지 확인합니다. | [잘못된 템플릿 오류 해결](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | 불필요한 종속성을 제거합니다. | [순환 종속성 해결](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | 계정이 배포하는 리소스 그룹과 동일한 테넌트에 속하는지 확인합니다. | |
| LinkedInvalidPropertyId | 리소스에 대한 리소스 ID가 올바르게 해석되지 않습니다. 구독 ID, 리소스 그룹 이름, 리소스 종류, 부모 리소스 이름(필요한 경우) 및 리소스 이름을 포함하여 리소스 ID에 필요한 모든 값을 제공했는지 확인합니다. | |
| LocationRequired | 리소스에 대한 위치를 제공합니다. | [위치 설정](resource-manager-templates-resources.md#location) |
| MismatchingResourceSegments | 중첩되는 리소스의 이름 및 형식에 세그먼트 수가 올바른지 확인하십시오. | [리소스 세그먼트 해결](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | 리소스 공급자 등록 상태 및 지원되는 위치를 확인합니다. | [등록 오류 해결](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | 리소스 공급자에 구독을 등록합니다. | [등록 오류 해결](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | 리소스 공급자 등록 상태를 확인합니다. | [등록 오류 해결](resource-manager-register-provider-errors.md) |
| NotFound | 종속 리소스를 부모 리소스와 병렬로 배포하려고 시도했을 수 있습니다. 종속성을 추가해야 하는지 확인합니다. | [종속성 오류 해결](resource-manager-not-found-errors.md) |
| OperationNotAllowed | 배포에서 구독, 리소스 그룹 또는 지역에 대한 할당량을 초과하는 작업을 시도하고 있습니다. 가능하면 배포를 수정하여 할당량 내에서 유지합니다. 그렇지 않은 경우 할당량 변경을 요청하는 것이 좋습니다. | [할당량 오류 해결](resource-manager-quota-errors.md) |
| ParentResourceNotFound | 자식 리소스를 만들기 전에 부모 리소스가 있는지 확인합니다. | [부모 리소스 오류 해결](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | 지정된 IP 주소에는 Azure에 필요한 주소 범위가 포함됩니다. 예약된 범위를 방지하도록 IP 주소를 변경합니다. | [IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | 지정된 IP 주소가 서브넷 범위를 벗어났습니다. 서브넷 범위에 속하는 IP 주소로 변경합니다. | [IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | 일부 속성이 배포된 리소스에서 변경될 수 없습니다. 리소스를 업데이트할 때 변경이 허용되는 속성으로 제한합니다. | [리소스 업데이트](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | 배포 중에 수행하려는 작업을 금지하는 리소스 정책이 구독에 포함되어 있습니다. 해당 작업을 차단하는 정책을 찾습니다. 가능하면 정책의 제한 사항을 충족하도록 배포를 수정합니다. | [정책 오류 해결](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | 예약된 이름을 포함하지 않는 리소스 이름을 제공합니다. | [예약된 리소스 이름](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 삭제가 완료될 때까지 기다립니다. | |
| ResourceGroupNotFound | 배포에 대한 대상 리소스 그룹의 이름을 확인합니다. 구독에 이 이름이 이미 있어야 합니다. 구독 컨텍스트를 확인합니다. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | 배포에서 확인할 수 없는 리소스를 참조합니다. **reference** 함수를 사용할 때 시나리오에 필요한 매개 변수가 포함되었는지 확인합니다. | [참조 오류 해결](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | 배포에서 구독, 리소스 그룹 또는 지역에 대한 할당량을 초과하는 리소스를 만들고 있습니다. 가능하면 인프라를 수정하여 할당량 내에서 유지합니다. 그렇지 않은 경우 할당량 변경을 요청하는 것이 좋습니다. | [할당량 오류 해결](resource-manager-quota-errors.md) |
| SkuNotAvailable | 선택한 위치에서 사용할 수 있는 SKU(예: VM 크기)를 선택합니다. | [SKU 오류 해결](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | 저장소 계정에 고유한 이름을 제공합니다. | [저장소 계정 이름 오류 해결](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | 저장소 계정에 고유한 이름을 제공합니다. | [저장소 계정 이름 오류 해결](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | 사용하려는 구독, 리소스 그룹 및 저장소 계정 이름을 확인합니다. | |
| SubnetsNotInSameVnet | 가상 머신에는 하나의 가상 네트워크만 있을 수 있습니다. 여러 NIC를 배포할 때는 모두 동일한 가상 네트워크에 속해야 합니다. | [여러 NIC](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | 불필요한 종속성을 제거합니다. | [순환 종속성 해결](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | 단일 배포에 대한 리소스 그룹의 수를 줄입니다. | [크로스 리소스 그룹 배포](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>오류 코드 찾기

두 가지 유형의 오류가 발생할 수 있습니다.

* 유효성 검사 오류
* 배포 오류

유효성 검사 오류는 배포 전에 확인할 수 있는 시나리오에서 발생합니다. 유효성 검사 오류에는 템플릿의 구문 오류나 구독 할당량을 초과하는 리소스를 배포하려는 구문 오류가 포함됩니다. 배포 오류는 배포 프로세스 중 발생하는 조건에서 발생합니다. 배포 오류에는 병렬로 배포 중인 리소스에 액세스하려는 시도가 포함됩니다.

두 가지 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 두 가지 오류 유형 모두 [활동 로그](resource-group-audit.md)에 나타납니다. 하지만 배포가 시작된 것은 아니므로 유효성 검사 오류는 배포 기록에 나타나지 않습니다.

### <a name="validation-errors"></a>유효성 검사 오류

포털을 통해 배포할 때 값을 제출한 후 유효성 검사 오류가 표시됩니다.

![포털 유효성 검사 오류 표시](./media/resource-manager-common-deployment-errors/validation-error.png)

메시지를 선택하여 세부 정보를 확인합니다. 다음 이미지에서 **InvalidTemplateDeployment** 오류와 정책에서 배포를 차단했음을 나타내는 메시지를 볼 수 있습니다.

![유효성 검사 세부 정보 표시](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>배포 오류

작업이 유효성 검사를 통과했지만, 배포 중에 실패하는 경우 알림에 오류가 표시됩니다. 알림을 선택합니다.

![알림 오류](./media/resource-manager-common-deployment-errors/notification.png)

배포에 대한 세부 정보가 표시됩니다. 옵션을 선택하여 오류에 대한 자세한 정보를 찾습니다.

![배포 실패](./media/resource-manager-common-deployment-errors/deployment-failed.png)

오류 메시지 및 오류 코드가 표시됩니다. 두 개의 오류 코드가 있습니다. 첫 번째 오류 코드(**DeploymentFailed**)는 오류를 해결하는 데 필요한 세부 정보를 제공하지 않는 일반 오류입니다. 두 번째 오류 코드(**StorageAccountNotFound**)는 필요한 세부 정보를 제공합니다. 

![오류 세부 정보](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>디버그 로깅 활성화

무엇이 잘못되었는지 알려면 요청 및 응답에 대한 정보가 더 필요한 경우가 있습니다. PowerShell 또는 Azure CLI를 사용하여, 배포 중에 추가 정보가 기록되도록 요청할 수 있습니다.

- PowerShell

   PowerShell에서 **DeploymentDebugLogLevel** 매개 변수를 All, ResponseContent 또는 RequestContent로 설정합니다.

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   요청 내용을 다음 cmdlet으로 검토합니다.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   또는 응답 내용을 다음으로 검토합니다.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   이 정보를 통해 템플릿의 값이 잘못 설정되었는지 확인할 수 있습니다.

- Azure CLI

   다음 명령을 사용하여 배포 작업을 검토합니다.

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 중첩된 템플릿

   중첩된 템플릿에 대한 디버그 정보를 기록하려면 **debugSetting** 요소를 사용합니다.

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```

## <a name="create-a-troubleshooting-template"></a>문제 해결 템플릿 만들기

경우에 따라 템플릿 문제를 해결하는 가장 쉬운 방법은 일부를 테스트해보는 것입니다. 간소화된 템플릿을 만들어 오류를 일으키는 것으로 생각되는 부분에 집중할 수 있습니다. 예를 들어, 리소스를 참조할 때 오류가 발생한다고 가정합니다. 전체 템플릿을 처리하는 대신, 문제를 일으킬 수 있는 부분만 반환하는 템플릿을 만듭니다. 이렇게 하면 템플릿 함수를 바르게 사용하여, 올바른 매개 변수를 전달하고 원하는 리소스를 가져오고 있는지 확인하는 데 도움이 됩니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

또는 종속성을 잘못 설정하는 것과 관련되었다고 생각되는 배포 오류가 발생한다고 가정해보세요. 간소화된 템플릿을 분리하여 템플릿을 테스트합니다. 먼저 단일 리소스(예: SQL Server)를 배포하는 템플릿을 만듭니다. 리소스가 올바르게 정의된 것이 확실하면 종속되는 리소스(예: SQL Database)를 추가합니다. 이러한 두 리소스가 올바르게 정의되어 있으면 다른 종속 리소스(예: 감사 정책)를 추가합니다. 각 테스트 배포 사이에 리소스 그룹을 삭제하여 종속성을 적절히 테스트합니다.


## <a name="next-steps"></a>다음 단계
* 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](resource-group-audit.md)를 참조하세요.
* 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](resource-manager-deployment-operations.md)를 참조하세요.
