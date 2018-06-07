---
title: 빠른 시작 - PowerShell을 사용하여 Azure에서 개인 Docker 레지스트리 만들기
description: Azure에서 PowerShell을 사용하여 개인 Docker 컨테이너 레지스트리를 만드는 방법을 빠르게 알아봅니다.
services: container-registry
author: marsma
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/08/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 282cd4bc9256fc483014b53626c02106d0de236a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="quickstart-create-an-azure-container-registry-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Container Registry 만들기

Azure Container Registry는 관리되는 Docker 컨테이너 레지스트리 서비스로, Docker 컨테이너 이미지를 빌드, 저장 및 제공하는 데 사용됩니다. 이 빠른 시작에서는 PowerShell을 사용하여 Azure 컨테이너 레지스트리를 만드는 방법을 알아봅니다. 레지스트리를 만든 후에는 컨테이너 이미지를 레지스트리에 푸시하고, 레지스트리의 컨테이너를 ACI(Azure Container Instances)에 배포합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서는 Azure PowerShell 모듈 버전 5.7.0 이상이 필요합니다. 설치된 버전을 확인하려면 `Get-Module -ListAvailable AzureRM` 명령을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

또한 Docker가 로컬에 설치되어 있어야 합니다. Docker는 [macOS][docker-mac], [Windows][docker-windows] 및 [Linux][docker-linux] 시스템용 패키지를 제공합니다.

Azure Cloud Shell에는 필요한 Docker 구성 요소(`dockerd` 데몬) 중 일부가 없기 때문에 이 빠른 시작에 Cloud Shell을 사용할 수 없습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Connect-AzureRmAccount][Connect-AzureRmAccount] 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

Azure에 인증되었으면 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 명령을 사용하여 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리적 컨테이너입니다.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>컨테이너 레지스트리 만들기

다음으로 [New-AzureRMContainerRegistry][New-AzureRMContainerRegistry] 명령을 사용하여 새 리소스 그룹에 컨테이너 레지스트리를 만듭니다.

레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 다음은 "myContainerRegistry007"이라는 레지스트리를 만드는 예제입니다. 명령에서 *myContainerRegistry007*을 원하는 대로 바꾸고 명령을 실행하여 레지스트리를 만듭니다.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-registry"></a>레지스트리에 로그인

컨테이너 이미지를 푸시하고 끌어오려면 레지스트리에 로그인해야 합니다. 먼저 [Get-AzureRmContainerRegistryCredential][Get-AzureRmContainerRegistryCredential] 명령을 사용하여 레지스트리의 관리자 자격 증명을 가져옵니다.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

다음으로 [docker login][docker-login]을 실행하여 로그인합니다.

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

로그인이 성공하면 `Login Succeeded`가 반환됩니다.

```console
PS Azure:\> $creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
Login Succeeded
```

## <a name="push-image-to-registry"></a>레지스트리에 이미지 푸시

레지스트리에 로그인했으니, 이제 컨테이너 이미지를 레지스트리로 푸시할 수 있습니다. 레지스트리로 푸시할 수 있는 이미지를 가져오려면 Docker 허브에서 공용 [aci-helloworld][aci-helloworld-image] 이미지를 끌어옵니다. [aci-helloworld][aci-helloworld-github] 이미지는 Azure Container Instances 로고를 표시하는 정적 HTML 페이지를 제공하는 작은 Node.js 응용 프로그램입니다.

```powershell
docker pull microsoft/aci-helloworld
```

이미지를 가져오면 다음과 비슷한 출력이 표시됩니다.

```console
PS Azure:\> docker pull microsoft/aci-helloworld
Using default tag: latest
latest: Pulling from microsoft/aci-helloworld
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
3be0618266da: Pull complete
9e232827e52f: Pull complete
b53c152f538f: Pull complete
Digest: sha256:a3b2eb140e6881ca2c4df4d9c97bedda7468a5c17240d7c5d30a32850a2bc573
Status: Downloaded newer image for microsoft/aci-helloworld:latest
```

Azure 컨테이너 레지스트리로 이미지를 푸시하려면 레지스트리의 FQDN(정규화된 도메인 이름)을 사용하여 이미지에 태그를 지정해야 합니다. Azure 컨테이너 레지스트리의 FQDN은 *\<registry-name\>.azurecr.io* 형식입니다.

완전한 이미지 태그로 변수를 채웁니다. 로그인 서버, 리포지토리 이름("aci-helloworld") 및 이미지 버전("v1")을 포함합니다.

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
```

이제 [docker tag][docker-tag]를 사용하여 이미지에 태그를 지정합니다.

```powershell
docker tag microsoft/aci-helloworld $image
```

마지막으로 레지스트리에 [docker push][docker-push]를 실행합니다.

```powershell
docker push $image
```

Docker 클라이언트가 이미지를 푸시하면 출력은 다음과 비슷합니다.

```console
PS Azure:\> docker push $image
The push refers to repository [myContainerRegistry007.azurecr.io/aci-helloworld]
31ba1ebd9cf5: Pushed
cd07853fe8be: Pushed
73f25249687f: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:565dba8ce20ca1a311c2d9485089d7ddc935dd50140510050345a1b0ea4ffa6e size: 1576
```

축하합니다! 방금 첫 번째 컨테이너 이미지를 레지스트리로 푸시했습니다.

## <a name="deploy-image-to-aci"></a>ACI에 이미지 배포

이제 이미지가 레지스트리에 있으니, 컨테이너를 Azure Container Instances에 직접 배포하고 Azure에서 실행되는 이미지를 살펴봅시다.

먼저 레지스트리 자격 증명을 *PSCredential*로 변환합니다. 컨테이너 인스턴스를 만드는 데 사용되는 `New-AzureRmContainerGroup` 명령에서 이 형식을 요구하기 때문입니다.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

그리고 컨테이너의 DNS 이름 레이블은 사용자가 만드는 Azure 지역 내에서 고유해야 합니다. 다음 명령을 실행하여 생성된 이름으로 변수를 채웁니다.

```powershell
$dnsname = "aci-demo-" + (Get-Random -Maximum 9999)
```

마지막으로 [New-AzureRmContainerGroup][New-AzureRmContainerGroup] 명령을 실행하여 이미지의 컨테이너를 CPU 코어 1개, 메모리 1GB인 레지스트리에 배포합니다.

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
```

Azure에서 컨테이너에 대한 세부 정보와 함께 초기 응답이 제공되고, 처음에는 상태가 "보류 중"일 것입니다.

```console
PS Azure:\> New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name "mycontainer" -Image $image -RegistryCredential $pscred -Cpu 1 -MemoryInGB 1 -DnsNameLabel $dnsname
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials : {myContainerRegistry007}
RestartPolicy            : Always
IpAddress                : 40.117.255.198
DnsNameLabel             : aci-demo-8751
Fqdn                     : aci-demo-8751.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Linux
Volumes                  :
State                    : Pending
Events                   : {}
```

상태를 모니터링하고 언제 실행되는지 확인하려면 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 명령을 몇 차례 실행합니다. 1분 안에 컨테이너가 시작됩니다.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

처음에는 컨테이너의 ProvisioningState가 *만드는 중*이고, 컨테이너가 가동되기 시작한 후에는 *성공* 상태로 바뀌는 것을 볼 수 있습니다.

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Creating
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
Succeeded
```

## <a name="view-running-application"></a>실행 중인 응용 프로그램 보기

ACI에 배포가 성공하고 컨테이너가 작동되면 브라우저에서 FQDN(정규화된 도메인 이름)으로 이동하여 Azure에서 실행 중인 앱을 살펴봅니다.

[Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] 명령을 사용하여 컨테이너의 FQDN을 가져옵니다.


```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
```

이 명령의 출력은 컨테이너 인스턴스의 FQDN입니다.

```console
PS Azure:\> (Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).Fqdn
aci-demo-8571.eastus.azurecontainer.io
```

FQDN을 얻었으면 브라우저에서 해당 FQDN으로 이동합니다.

![브라우저의 Hello World 앱][qs-psh-01-running-app]

축하합니다! Azure에서 응용 프로그램을 실행하는 컨테이너를 만들고, 개인 Azure 컨테이너 레지스트리의 컨테이너 이미지에서 직접 배포하였습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스가 더 이상 필요 없으면 [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] 명령을 사용하여 리소스 그룹, 컨테이너 레지스트리 및 컨테이너 인스턴스를 제거합니다.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure CLI를 사용하여 Azure Container Registry를 만들고, Azure Container Instances에서 해당 인스턴스를 시작했습니다. ACI에 대해 자세히 알아보기 위해 Azure Container Instances 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Container Instances 자습서](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[aci-helloworld-github]: https://github.com/Azure-Samples/aci-helloworld
[aci-helloworld-image]: https://hub.docker.com/r/microsoft/aci-helloworld/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzureRmAccount]: /powershell/module/azurerm.profile/connect-azurermaccount
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Get-AzureRmContainerRegistryCredential]: /powershell/module/azurerm.containerregistry/get-azurermcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[New-AzureRMContainerRegistry]: /powershell/module/containerregistry/New-AzureRMContainerRegistry
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-psh-01-running-app]: ./media/container-registry-get-started-powershell/qs-psh-01-running-app.png
