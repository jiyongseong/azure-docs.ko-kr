---
title: Azure 가상 머신 확장 집합을 사용하여 자동으로 OS 업그레이드 | Microsoft Docs
description: 확장 집합의 VM 인스턴스에서 OS를 자동으로 업그레이드하는 방법을 알아봅니다
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 28a9b3d68037aac0c1198da4232c045487b01174
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Azure 가상 머신 확장 집합 자동 OS 업그레이드

자동 OS 이미지 업그레이드는 모든 VM을 최신 OS 이미지로 자동 업그레이드하는 Azure 가상 머신 확장 집합의 미리 보기 기능입니다.

자동 OS 업그레이드의 특징은 다음과 같습니다.

- 구성이 완료되면 이미지 게시자가 게시한 최신 OS 이미지가 사용자 개입 없이 확장 집합에 자동으로 적용됩니다.
- 판매자가 새 플랫폼 이미지를 게시할 때마다 인스턴스 배치를 롤링 방식으로 업그레이드합니다.
- 응용 프로그램 상태 프로브와 통합됩니다(선택 사항이지만 보안을 위해 강력하게 권장).
- 모든 VM 크기에 사용할 수 있습니다.
- Windows 및 Linux 플랫폼 이미지에 사용할 수 있습니다.
- 언제든지 자동 업그레이드를 옵트아웃할 수 있습니다(OS 업그레이드를 수동으로 시작 가능).
- VM의 OS 디스크는 최신 이미지 버전을 사용하여 만든 새 OS 디스크로 교체됩니다. 구성된 확장 및 사용자 지정 데이터 스크립트가 실행되고 지속형 데이터 디스크는 유지됩니다.


## <a name="preview-notes"></a>미리 보기 정보 
미리 보기 버전은 다음과 같은 제한 사항이 적용됩니다.

- 자동 OS 업그레이드가 [OS SKU 4개](#supported-os-images)만 지원합니다. SLA 또는 보장이 없습니다. 미리 보기 기간에는 프로덕션 크리티컬 워크로드에 자동 업그레이드를 사용하지 않는 것이 좋습니다.
- Azure Disk Encryption(현재 미리 보기)은 현재 가상 머신 확장 집합 자동 OS 업그레이드를 지원하지 **않습니다**.


## <a name="register-to-use-automatic-os-upgrade"></a>자동 OS 업그레이드를 사용하도록 등록
자동 OS 업그레이드 기능을 사용하려면 다음과 같이 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 명령을 사용하여 미리 보기 공급자를 등록해야 합니다.

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

등록 상태가 *등록됨*으로 보고될 때까지 약 10분 정도 소요됩니다. [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 명령을 사용하여 현재 등록 상태를 확인할 수 있습니다. 등록했으면 다음과 같이 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 명령을 사용하여 *Microsoft.Compute* 공급자를 등록합니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

> [!NOTE]
> Service Fabric 클러스터에는 응용 프로그램 상태에 대한 고유의 개념이 있지만 Service Fabric이 없는 확장 집합은 부하 분산 장치 상태 프로브를 사용하여 응용 프로그램 상태를 모니터링합니다. 상태 프로브에 대한 공급자 기능을 등록하려면 다음과 같이 [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) 명령을 사용합니다.
>
> ```powershell
> Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
> ```
>
> 마찬가지로, 등록 상태가 *등록됨*으로 보고될 때까지 약 10분 정도 소요됩니다. [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) 명령을 사용하여 현재 등록 상태를 확인할 수 있습니다. 등록되었으면 다음과 같이 [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) 명령을 사용하여 *Microsoft.Network* 공급자를 등록합니다.
>
> ```powershell
> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
> ```

## <a name="portal-experience"></a>포털 환경
위의 등록 단계를 수행한 후 [Azure Portal](https://aka.ms/managed-compute)로 이동하여 확장 집합에서 자동 OS 업그레이드를 활성화하고 업그레이드의 진행 상태를 볼 수 있습니다.

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>지원되는 OS 이미지
현재는 특정 OS 플랫폼 이미지만 지원됩니다. 지금은 사용자가 직접 만든 사용자 지정 이미지를 사용할 수 없습니다. 플랫폼 이미지의 *version* 속성을 *latest*로 설정해야 합니다.

현재 지원되는 SKU는 다음과 같습니다(더 추가 예정).
    
| 게시자               | 제안         |  SKU               | 버전  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | 최신   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | 최신   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    | 최신   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | 최신   |



## <a name="application-health-without-service-fabric"></a>Service Fabric이 없는 응용 프로그램 상태
> [!NOTE]
> 이 섹션은 Service Fabric이 없는 확장 집합에만 적용됩니다. Service Fabric에는 응용 프로그램 상태에 대한 고유의 개념이 있습니다. Service Fabric과 함께 자동 OS 업그레이드를 사용할 때 새 OS 이미지는 Service Fabric에서 실행되는 서비스의 고가용성을 유지하기 위해 업데이트 도메인에 의해 업데이트 도메인에 롤아웃됩니다. Service Fabric 클러스터의 내구성 특성에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)를 참조하세요.

OS가 업그레이드되는 동안 확장 집합의 VM 인스턴스는 한 번에 하나의 일괄 처리 단위로 업그레이드됩니다. 업그레이드된 VM 인스턴스에서 고객 응용 프로그램의 상태가 정상인 경우에만 업그레이드를 계속 진행해야 합니다. 이러한 이유로 응용 프로그램이 확장 집합 OS 업그레이드 엔진에 상태 신호를 제공해야 합니다. OS를 업그레이드하는 동안 플랫폼은 VM 전원 상태 및 확장 프로비전 상태를 고려하여 업그레이드 후 VM 인스턴스가 정상 상태인지 확인합니다. VM 인스턴스의 OS를 업그레이드하는 동안 VM 인스턴스의 OS 디스크는 최신 이미지 버전에 따라 새 디스크로 교체됩니다. OS 업그레이드가 완료되면 구성된 확장이 이러한 VM에서 실행됩니다. VM의 모든 확장이 성공적으로 프로비전된 경우에만 응용 프로그램의 상태가 정상인 것으로 간주됩니다. 

또한 플랫폼에 응용 프로그램의 현재 상태에 대한 정확한 정보를 제공하도록 응용 프로그램 상태 프로브로 확장 집합을 구성*해야* 합니다. 응용 프로그램 상태 프로브는 상태 신호로 사용되는 사용자 지정 부하 분산 장치 프로브입니다. 확장 집합 VM 인스턴스에서 실행되는 응용 프로그램은 외부 HTTP 또는 TCP 요청에 응답하여 상태를 알릴 수 있습니다. 사용자 지정 부하 분산 장치 프로브에 대한 자세한 내용은 [부하 분산 장치 프로브 이해](../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.

여러 배치 그룹을 사용하도록 확장 집합을 구성한 경우 [표준 부하 분산 장치](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)를 사용하는 프로브를 사용해야 합니다.

### <a name="important-keep-credentials-up-to-date"></a>중요: 자격 증명을 최신 상태로 유지합니다.
예를 들어 VM 확장이 저장소 계정에 대한 SAS 토큰을 사용하도록 구성되면 확장 집합이 자격 증명을 사용하여 외부 리소스에 액세스하는 경우 자격 증명을 최신 상태로 유지하고 있는지 확인해야 합니다. 인증서 및 토큰을 포함한 자격 증명이 만료되는 경우 업그레이드에 실패하고 VM의 첫 번째 일괄 처리가 실패한 상태가 됩니다.

리소스 인증 오류 시 VM을 복구하고 자동 OS 업그레이드를 다시 설정하는 권장된 단계는 다음과 같습니다.

* 확장에 전달된 토큰(또는 다른 자격 증명)를 다시 생성합니다.
* 외부 엔터티에 연결된 VM 내에서 사용되는 자격 증명이 최신 상태인지 확인합니다.
* 새 토큰으로 확장 집합의 확장을 업데이트합니다.
* 업데이트된 확장 집합을 배포합니다. 그러면 실패한 인스턴스를 포함하여 모든 VM 인스턴스를 업데이트합니다. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>확장 집합의 응용 프로그램 상태 프로브로 사용자 지정 부하 분산 장치 구성
확장 집합 상태에 대한 부하 분산 장치 프로브를 명시적으로 *만들어야* 합니다. 기존 HTTP 검색 또는 TCP 프로브에 대한 동일한 끝점을 사용할 수 있지만, 상태 프로브에 기존의 부하 분산 장치 프로브와 다른 동작이 필요할 수 있습니다. 예를 들어 기존의 부하 분산 장치 프로브는 인스턴스의 부하가 너무 높으면 비정상 상태를 반환하기도 하는데, 이 동작은 OS를 자동으로 업그레이드하는 동안 인스턴스 상태를 확인하는 데에는 적합하지 않습니다. 검색 속도가 2분을 넘지 않도록 프로브를 구성하세요.

부하 분산 장치 프로브는 다음과 같이 확장 집합의 *networkProfile*에서 참조할 수 있으며 내부 또는 공용 부하 분산 장치에 연결할 수 있습니다.

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>구독에 OS 이미지 업그레이드 정책 적용
안전한 업그레이드를 위해 업그레이드 정책을 적용하는 것이 좋습니다. 이 정책을 적용하려면 구독에 응용 프로그램 상태 프로브가 필요할 수 있습니다. 다음 Azure Resource Manager 정책은 자동 OS 이미지 업그레이드 설정이 구성되지 않은 배포를 거부합니다.

1. 다음과 같이 [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition) 명령을 사용하여 기본 제공 Azure Resource Manager 정책을 가져옵니다.

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. 다음과 같이 [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment) 명령을 사용하여 구독에 정책을 할당합니다.

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>자동 업데이트 구성
자동 업그레이드를 구성하려면 확장 집합 모델 정의에서 *automaticOSUpgrade* 속성을 *true*로 설정해야 합니다. Azure PowerShell 또는 Azure CLI 2.0을 사용하여 이 속성을 구성할 수 있습니다.

다음 예제에서는 Azure PowerShell(4.4.1 이상)을 사용하여 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMSS*라는 확장 집합에 대해 자동 업그레이드를 구성합니다.

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


다음 예제에서는 Azure CLI(2.0.20 이상)를 사용하여 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMSS*라는 확장 집합에 대해 자동 업그레이드를 구성합니다.

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>자동 OS 업그레이드의 상태 확인
Azure PowerShell, Azure CLI 2.0 또는 REST API를 사용하여 확장 집합에서 수행된 가장 최근의 OS 업그레이드 상태를 확인할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell
다음 예제에서는 Azure PowerShell(4.4.1 이상)을 사용하여 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMSS*라는 확장 집합의 상태를 확인합니다.

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
다음 예제에서는 Azure CLI(2.0.20 이상)를 사용하여 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMSS*라는 확장 집합의 상태를 확인합니다.

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>REST API
다음 예제에서는 REST API를 사용하여 *myResourceGroup*이라는 리소스 그룹에 있는 *myVMSS*라는 확장 집합의 상태를 확인합니다.

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

GET 호출은 다음 예제 출력과 비슷한 속성을 반환합니다.

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>자동 OS 업그레이드 실행
응용 프로그램 상태 프로브의 사용을 확장하기 위해 확장 집합 OS 업그레이드에서 다음 단계를 실행합니다.

1. 비정상 인스턴스가 20%를 초과하면 업그레이드를 중지하고, 초과하지 않으면 계속 진행합니다.
2. 업그레이드할 그 다음 VM 인스턴스 일괄 처리, 즉, 인스턴스 수가 전체 인스턴트의 20%를 넘지 않는 일괄 처리를 식별합니다.
3. 그 다음 VM 인스턴스 일괄 처리의 OS를 업그레이드합니다.
4. 업그레이드된 인스턴스 중 비정상 인스턴스가 20%를 초과하면 업그레이드를 중지하고, 초과하지 않으면 계속 진행합니다.
5. Service Fabric 클러스터의 일부가 아닌 확장 집합의 경우 업그레이드는 프로브가 정상이 되도록 최대 5분을 기다린 다음, 즉시 다음 일괄 처리를 계속합니다. Service Fabric 클러스터의 일부인 확장 집합의 경우 확장 집합은 다음 일괄 처리로 이동하기 전에 30분 동안 기다립니다.
6. 업그레이드할 인스턴스가 남아 있으면 1단계로 이동하여 다음 일괄 처리를 진행하고, 그렇지 않으면 업그레이드가 완료됩니다.

확장 집합 OS 업그레이드 엔진은 모든 일괄 처리를 업그레이드하기 전에 전체 VM 인스턴스 상태를 검사합니다. 일괄 처리를 업그레이드하는 동안 Azure 데이터 센터에서 VM의 가용성에 영향을 줄 수 있는 다른 동시 계획된 유지 관리 또는 계획되지 않은 유지 관리가 발생할 수 있습니다. 따라서 20%가 넘는 인스턴스가 일시적으로 중지될 수 있습니다. 이 경우 현재 일괄 처리가 끝나면 확장 집합 업그레이드가 중지됩니다.


## <a name="deploy-with-a-template"></a>템플릿을 사용하여 배포

다음 템플릿을 사용하여 <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>자동 롤링 업그레이드 - Ubuntu 16.04-LTS</a> 자동 업그레이드를 사용하는 확장 집합을 배포할 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>다음 단계
확장 집합에 자동 OS 업그레이드를 사용하는 방법에 대한 더 많은 예제를 보려면 [미리 보기 기능에 대한 GitHub 리포지토리](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)를 참조하세요.
