---
title: Azure Service Fabric 클러스터 크기 조정 | Microsoft Docs
description: 이 자습서에서는 Service Fabric 클러스터 크기를 신속하게 조정하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/06/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: e80fad4d0bddff89ff4dda7feed90fc622369ee9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-scale-a-service-fabric-cluster"></a>자습서: Service Fabric 클러스터 크기 조정

이 자습서는 이 시리즈의 두 번째 파트로, 기존 클러스터를 확장 및 축소하는 방법을 보여 줍니다. 이 내용을 완료하면 클러스터를 크기 조정하는 방법과 남은 리소스를 정리하는 방법을 알게 됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 클러스터 노드 수 읽기
> * 클러스터 노드 추가(규모 확장)
> * 클러스터 노드 제거(규모 감축)

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 또는 [Linux 클러스터](service-fabric-tutorial-create-vnet-and-linux-cluster.md) 만들기
> * 클러스터 규모 확장 또는 규모 감축
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * [Service Fabric을 사용하여 API Management 배포](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>필수 조건
이 자습서를 시작하기 전에:
- Azure 구독이 없는 경우 [평가판 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- [Azure PowerShell 모듈 버전 4.1 이상](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 또는 [Azure CLI 2.0](/cli/azure/install-azure-cli)을 설치합니다.
- Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 또는 [Linux 클러스터](service-fabric-tutorial-create-vnet-and-linux-cluster.md) 만들기
- Windows 클러스터를 배포하는 경우 Windows 개발 환경을 설정합니다. [Visual Studio 2017](http://www.visualstudio.com), **Azure 개발**, **ASP.NET 및 웹 개발** 및 **.NET Core 플랫폼 간 개발** 워크로드를 설치합니다.  그런 후 [.NET 개발 환경](service-fabric-get-started.md)을 설정합니다.
- Linux 클러스터를 배포하는 경우 [Linux](service-fabric-get-started-linux.md) 또는 [MacOS](service-fabric-get-started-mac.md)에서 Java 개발 환경을 설정합니다.  [Service Fabric CLI](service-fabric-cli.md)를 설치합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인
Azure 명령을 실행하기 전에 Azure 계정에 로그인하고 구독을 선택합니다.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>클러스터에 연결

자습서의 이 부분을 성공적으로 완료하려면 Service Fabric 클러스터와 가상 머신 확장 집합(클러스터 호스팅)을 모두 연결해야 합니다. 가상 머신 확장 집합은 Azure에서 Service Fabric을 호스팅하는 Azure 리소스입니다.

클러스터에 연결하는 경우 클러스터에서 정보를 쿼리할 수 있습니다. 클러스터를 사용하여 클러스터에서 인식할 수 있는 노드를 알 수 있습니다. 다음 코드에서 클러스터에 연결하는 경우 이 시리즈의 첫 번째 파트에서 작성된 것과 동일한 인증서를 사용합니다. `$endpoint` 및 `$thumbprint` 변수를 실제 값으로 설정해야 합니다.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

이제 연결되었으며 명령을 사용하여 클러스터에 있는 각 노드의 상태를 가져올 수 있습니다. PowerShell의 경우 `Get-ServiceFabricClusterHealth` 명령을 사용하고, **sfctl**의 경우 `sfctl cluster select` 명령을 사용합니다.

## <a name="scale-out"></a>확장

규모를 확장할 때 확장 집합에 더 많은 가상 컴퓨터 인스턴스를 추가합니다. 이러한 인스턴스는 Service Fabric 에서 사용하는 노드가 됩니다. Service Fabric은 확장 집합에 더 많은 인스턴스가 추가되고(규모 확장) 자동으로 반응할 때를 압니다. 다음 코드는 이름별로 확장 집합을 가져오고 확장 집합의 **용량**을 1단위로 늘립니다.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

이 코드는 용량을 6으로 설정합니다.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>규모 감축

규모 감축은 더 낮은 **용량** 값을 사용하는 것을 제외하고 규모 확장과 동일합니다. 확장 집합을 규모 감축하는 경우 확장 집합에서 가상 머신 인스턴스를 제거합니다. 일반적으로 Service Fabric은 어떤 일이 발생했는지 알지 못하며 노드가 누락되었다고 생각합니다. 그러면 Service Fabric은 클러스터의 비정상 상태를 보고합니다. 잘못된 상태를 방지하려면 노드가 사라질 것으로 예상한다는 것을 서비스 패브릭에 알려야 합니다.

### <a name="remove-the-service-fabric-node"></a>서비스 패브릭 노드 제거

> [!NOTE]
> 이 부분만 *Bronze* 내구성 계층에 적용됩니다. 내구성에 자세한 내용은 [Service Fabric 클러스터 용량 계획][durability]을 참조하세요.

가상 머신 확장 집합의 규모를 감축할 때 확장 집합(대부분의 경우)은 마지막으로 만든 가상 머신 인스턴스를 제거합니다. 따라서 마지막으로 만들고 일치하는 서비스 패브릭 노드를 찾아야 합니다. 서비스 패브릭 노드에서 가장 큰 `NodeInstanceId` 속성 값을 확인하여 이 마지막 노드를 찾을 수 있습니다. 아래의 코드 예제는 노드 인스턴스별로 정렬하고 가장 큰 ID 값을 가진 인스턴스에 대한 세부 정보를 반환합니다. 

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

서비스 패브릭 클러스터는 이 노드가 제거될 것임을 알아야 합니다. 수행해야 하는 세 가지 단계가 있습니다.

1. 더 이상 데이터가 복제되지 않도록 노드를 사용하지 않도록 설정합니다.  
PowerShell: `Disable-ServiceFabricNode`  
sfcli: `sfctl node disable`

2. 노드를 중지하여 서비스 패브릭 런타임이 완전히 종료되고 앱이 종료 요청을 받도록 하십시오.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfcli: `sfctl node transition --node-transition-type Stop`

2. 클러스터에서 노드를 제거합니다.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfcli: `sfctl node remove-state`

이 세 단계가 노드에 적용된 후에는 확장 집합에서 제거할 수 있습니다. [bronze][durability] 외의 내구성 계층을 사용하는 경우 확장 집합 인스턴스가 제거될 때 이러한 단계가 완료됩니다.

다음 코드 블록은 마지막으로 만든 노드를 가져오고 클러스터에서 해당 노드를 사용하지 않도록 설정하고 중지 및 제거합니다.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300
    
    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }
    
    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

아래 **sfctl** 코드에서 `sfctl node list --query "sort_by(items[*], &name)[-1].name"` 명령을 사용하여 마지막으로 만든 노드의 **node-name** 값을 가져옵니다.

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> 다음 **sfctl** 쿼리를 사용하여 각 단계의 상태를 확인합니다.
>
> **비활성화 상태 확인**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **중지 상태 확인**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>확장 집합 규모 감축

이제 클러스터에서 서비스 패브릭 노드가 제거되었으며 가상 머신 확장 집합을 규모 감축할 수 있습니다. 아래 예제에서는 확장 집합 용량이 1식 줄어듭니다.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

이 코드는 용량을 5로 설정합니다.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 클러스터 노드 수 읽기
> * 클러스터 노드 추가(규모 확장)
> * 클러스터 노드 제거(규모 감축)


이제 다음 자습서로 넘어가서 클러스터 런타임을 업그레이드하는 방법을 알아보겠습니다.
> [!div class="nextstepaction"]
> [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
