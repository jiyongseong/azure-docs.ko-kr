---
title: 퀵 스타트 - PowerShell로 첫 번째 Azure Container Instances 컨테이너 만들기
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Azure Container Instances에 Windows 컨테이너를 배포합니다
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: quickstart
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4a1d338304dbd5e2845768b7bf0273eed23af0ec
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>빠른 시작: Azure Container Instances에서 첫 번째 컨테이너 만들기

Azure Container Instances를 사용하면 가상 머신을 프로비전하거나 상위 수준 서비스를 도입하지 않고도 Azure에서 Docker 컨테이너를 쉽게 만들고 관리할 수 있습니다. 이 빠른 시작에서는 Azure에서 Windows 컨테이너를 만들고, 이를 FQDN(정규화된 도메인 이름)으로 인터넷에 공개합니다. 이 작업은 단일 명령으로 완료됩니다. 곧 브라우저에서 응용 프로그램이 실행됩니다.

![Azure Container Instances를 사용하여 배포된 앱이 브라우저에 표시됨][qs-powershell-01]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에는 Azure PowerShell 모듈 버전 5.5 이상이 필요합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup][New-AzureRmResourceGroup]을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>컨테이너 만들기

[New-AzureRmContainerGroup][New-AzureRmContainerGroup] cmdlet에 이름, Docker 이미지 및 Azure 리소스 그룹을 제공하여 컨테이너를 만들 수 있습니다. 필요에 따라 DNS 이름 레이블이 있는 컨테이너를 인터넷에 공개할 수 있습니다.

다음 명령을 실행하여 IIS(인터넷 정보 서비스)가 실행되는 Nano Server 컨테이너를 시작합니다. `-DnsNameLabel` 값은 인스턴스를 만드는 Azure 지역 내에서 고유해야 합니다. 따라서 고유성을 유지하기 위해 이 값을 수정해야 할 수도 있습니다.

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

몇 초 안에 요청에 대한 응답을 받습니다. 처음에는 컨테이너가 **만드는 중** 상태이지만 1~2분 안에 시작됩니다. [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] cmdlet을 사용하여 배포 상태를 확인할 수 있습니다.

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

컨테이너의 프로비전 상태, FQDN(정규화된 도메인 이름) 및 IP 주소가 cmdlet의 출력에 표시됩니다.

```console
PS Azure:\> Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

컨테이너의 **ProvisioningState**가 `Succeeded`(성공)로 전환되면 브라우저에서 `Fqdn`으로 이동합니다.

![Azure Container Instances를 사용하여 배포된 IIS가 브라우저에 표시됨][qs-powershell-01]

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 작업을 완료했으면 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] cmdlet을 사용하여 컨테이너를 제거합니다.

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 공용 Docker Hub 리포지토리의 이미지로 Azure 컨테이너 인스턴스를 만들었습니다. 컨테이너 이미지를 직접 빌드하고 개인 Azure 컨테이너 레지스트리의 Azure Container Instances에 배포하려면 Azure Container Instances 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
