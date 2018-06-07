---
title: Azure에서 VM 이름 확인을 위해 내부 DNS 사용 | Microsoft Docs
description: Azure에서 VM 이름 확인을 위해 내부 DNS 사용
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
ms.openlocfilehash: 73ecbb5bfcc99eb4507cac24b0539a04c3d8f521
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="using-internal-dns-for-vm-name-resolution-on-azure"></a>Azure에서 VM 이름 확인을 위해 내부 DNS 사용

이 문서에서는 가상 NIC 카드(VNic) 및 DNS 레이블 이름을 사용하여 Linux VM에 대해 정적 내부 DNS 이름을 설정하는 방법을 보여 줍니다. 정적 DNS 이름은 이 문서에서 사용되는 Jenkins 빌드 서버 또는 Git 서버와 같은 영구 인프라 서비스에 사용됩니다.

요구 사항은 다음과 같습니다.

* [Azure 계정](https://azure.microsoft.com/pricing/free-trial/)
* [SSH 공용 및 개인 키 파일](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#quick-commands) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](static-dns-name-resolution-for-linux-on-azure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="quick-commands"></a>빠른 명령

태스크를 빠르게 완료해야 하는 경우 다음 섹션에서 필요한 명령에 대해 자세히 알아보세요. 각 단계에 대한 보다 자세한 내용 및 상황 설명은 [여기서부터](#detailed-walkthrough) 문서 끝까지 참조하세요.  

사전 요구 사항: SSH 인바운드를 사용하는 NSG, 리소스 그룹, VNet, 서브넷입니다.

### <a name="create-a-vnic-with-a-static-internal-dns-name"></a>정적 내부 DNS 이름으로 VNic 만들기

`-r` cli 플래그는 VNic에 대한 정적 DNS 이름을 제공하는 DNS 레이블을 설정하기 위한 것입니다.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>VNet, NSG에 VM 배포 및 VNic 연결

`-N`은 Azure에 배포하는 동안 새 VM에 VNic를 연결합니다.

```azurecli
azure vm create jenkins \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-F myVNet \
-j mySubnet \
-N jenkinsVNic
```

## <a name="detailed-walkthrough"></a>자세한 연습

Azure에서 전체 CiCd(지속적인 통합 및 지속적인 배포) 인프라는 특정 서버가 정적이거나 서버의 수명이 길어야 합니다.  VNet(Virtual Network) 및 NSG(네트워크 보안 그룹)와 같은 Azure 자산이 정적이고 거의 배포되지 않은 수명이 긴 리소스인 것이 좋습니다.  VNet을 배포하면 인프라에 어떤 부정적인 영향을 주지 않고 새 배포에서 다시 사용할 수 있습니다.  이 정적 네트워크에 Git 리포지토리 서버 및 Jenkins 자동화 서버를 추가하면 개발 또는 테스트 환경에 CiCd를 제공합니다.  

내부 DNS 이름은 Azure Virtual Network 내에서만 확인할 수 있습니다.  DNS 이름은 내부용이므로 외부 인터넷에서 확인할 수 없으며 인프라에 대한 추가 보안을 제공합니다.

_모든 예제를 고유한 이름으로 바꿉니다._

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 이 연습에서 만드는 모든 항목을 구성하는 데 필요합니다.  Azure 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>VNet 만들기

첫 번째 단계는 VM을 시작할 VNet을 빌드하는 것입니다.  VNet은 이 연습을 위한 서브넷을 포함합니다.  Azure VNet에 대한 자세한 내용은 [가상 네트워크 만들기](../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하세요.

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>NSG 만들기

서브넷은 기존 네트워크 보안 그룹을 기초로 빌드되므로 서브넷 전에 NSG를 구축합니다.  Azure NSG는 네트워크 계층에서 방화벽과 동일합니다.  Azure NSG에 대한 자세한 내용은 [Azure CLI에서 NSG를 만드는 방법](../../virtual-network/tutorial-filter-network-traffic-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>인바운드 SSH 허용 규칙 추가

Linux VM은 인터넷에서 액세스를 해야 하므로 인바운드 포트 22 트래픽이 Linux VM의 포트 22에 대한 네트워크를 통해 전달되도록 허용하는 규칙이 필요합니다.

```azurecli
azure network nsg rule create inboundSSH \
--resource-group myResourceGroup \
--nsg-name myNSG \
--access Allow \
--protocol Tcp \
--direction Inbound \
--priority 100 \
--source-address-prefix * \
--source-port-range * \
--destination-address-prefix 10.10.0.0/24 \
--destination-port-range 22
```

## <a name="add-a-subnet-to-the-vnet"></a>VNet에 서브넷 추가

VNet 내의 VM은 서브넷에 있어야 합니다.  각 VNet에는 여러 개의 서브넷이 있을 수 있습니다.  서브넷을 만들고 서브넷을 NSG와 연결하여 서브넷에 방화벽을 추가합니다.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

이제 서브넷은 VNet 내에 추가되고 NSG와 NSG 규칙과 연결됩니다.

## <a name="creating-static-dns-names"></a>정적 DNS 이름 만들기

Azure는 매우 유연하지만 VM 이름 확인을 위해 DNS 이름을 사용하려면 DNS 레이블 지정을 사용하여 VNic(가상 네트워크 카드)로 만들어야 합니다.  VNic는 다른 VM에 연결하여 다시 사용할 수 있으므로 중요합니다. 그렇기 때문에 VM이 임시일 수 있는 반면 VNic는 고정 리소스로 유지됩니다.  VNic에 DNS 레이블 지정을 사용하여 VNet의 다른 VM에서 간단한 이름 확인을 사용할 수 있습니다.  확인 가능한 이름을 사용하면 다른 VM에서 DNS 이름 `Jenkins`로 자동화 서버에 액세스하거나 `gitrepo`로 Git 서버에 액세스할 수 있습니다.  VNic를 만들고 이전 단계에서 만든 서브넷에 연결합니다.

```azurecli
azure network nic create jenkinsVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet \
-r jenkins
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet 및 NSG에 VM 배포

이제 VNet, 해당 VNet 내부의 서브넷 및 NSG가 SSH에 대한 포트 22를 제외한 모든 인바운드 트래픽을 차단하여 서브넷을 보호하는 방화벽의 역할을 하게 됩니다.  이제 이 기존 네트워크 인프라 내에 VM을 배포할 수 있습니다.

Azure CLI 및 `azure vm create` 명령을 사용하여 기존 Azure 리소스 그룹, VNet, 서브넷 및 VNic에 Linux VM을 배포합니다.  전체 VM을 배포하기 위해 CLI를 사용하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 전체 Linux 환경 만들기](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

```azurecli
azure vm create jenkins \
--resource-group myResourceGroup myVM \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name jenkinsVNic
```

기존 리소스를 호출하기 위해 CLI 플래그를 사용하여 Azure에서 기존 네트워크 내에 VM을 배포하도록 지시합니다.  다시 말해, VNet 및 서브넷이 배포되면 Azure 지역 내에서 정적 또는 영구적으로 리소스로 유지할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
