---
title: Windows Virtual Machines 개요 | Microsoft Docs
description: Azure에서 Windows 가상 머신 만들기 및 관리에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ff709d021c9d4201301edd9890f1e4a94f555313
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Azure에서의 Windows 가상 머신 개요

Azure Virtual Machines(VM)는 Azure에서 제공하는 여러 유형의 [확장성 있는 주문형 컴퓨팅 리소스](../../app-service/choose-web-site-cloud-service-vm.md) 중 하나입니다. 일반적으로 컴퓨팅 환경에서 다른 선택 옵션에서 제공하는 것보다 더 많이 제어해야 하는 경우에 가상 컴퓨터를 선택합니다. 이 문서에서는 VM을 만들기 전에 고려해야 하는 요구 사항, 만드는 방법 및 관리하는 방법에 대해 설명합니다.

Azure VM은 가상 컴퓨터를 실행하는 실제 하드웨어를 구입 및 유지 관리하지 않고도 가상화의 유연성을 제공합니다. 하지만 가상 컴퓨터에서 실행하는 소프트웨어의 구성, 패치 및 설치와 같은 작업을 수행하여 VM을 계속 유지 관리할 필요가 있습니다.

Azure 가상 머신은 다양한 방식으로 사용할 수 있습니다. 일부 사례:

* **개발 및 테스트** – Azure VM은 응용 프로그램의 코딩과 테스트에 필요한 특정 구성을 갖춘 컴퓨터를 만드는 쉽고 빠른 방법을 제공합니다.
* **클라우드의 응용 프로그램** – 응용 프로그램에 대한 수요가 변동할 수 있으므로 Azure의 VM에서 응용 프로그램을 실행하는 것이 경제적입니다. 필요할 경우 여분의 VM에 대해 비용을 지불하고, 그렇지 않은 경우에는 해당 VM을 종료합니다.
* **확장된 데이터 센터** – Azure 가상 네트워크의 가상 머신은 조직의 네트워크에 쉽게 연결할 수 있습니다.

응용 프로그램에서 사용하는 VM의 수는 요구 사항을 충족하는 데 필요한 만큼 늘리거나 줄일 수 있습니다.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM을 만들기 전의 고려 사항
Azure에서 응용 프로그램 인프라를 구축하는 경우에는 언제나 다양한 [디자인 고려 사항](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)이 있습니다. VM의 이러한 양상으로 인해 시작하기 전에 다음 항목을 중요하게 고려해야 합니다.

* 응용 프로그램 리소스 이름
* 리소스가 저장되어 있는 위치
* VM 크기
* 만들 수 있는 VM의 최대 수
* VM에서 실행하는 운영 체제
* 시작 이후의 VM 구성
* VM에 필요한 관련 리소스

### <a name="naming"></a>이름 지정
가상 머신에는 할당된 [이름](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)과 운영 체제의 일부로 구성된 컴퓨터 이름이 있습니다. VM 이름은 최대 15자로 제한됩니다.

Azure를 사용하여 운영 체제 디스크를 만드는 경우 컴퓨터 이름과 가상 머신 이름은 동일합니다. 이전에 구성된 운영 체제를 포함하고 있는 [사용자 고유의 이미지를 업로드하여 사용하고](upload-generalized-managed.md) 이 이미지를 사용하여 가상 머신을 만드는 경우 이름이 다를 수 있습니다. 사용자 고유의 이미지 파일을 업로드하면 운영 체제의 컴퓨터 이름과 가상 머신 이름을 동일하게 지정하는 것이 좋습니다.

### <a name="locations"></a>위치
Azure에서 만든 리소스는 모두 전 세계의 여러 [지리적 지역](https://azure.microsoft.com/regions/)에 걸쳐 배포됩니다. 일반적으로 VM을 만들 때 지역을 **위치**라고 합니다. VM의 경우 가상 하드 디스크가 저장되는 위치가 지정됩니다.

아래 표에서는 사용할 수 있는 위치 목록을 가져올 수 있는 몇 가지 방법을 보여 줍니다.

| 방법 | 설명 |
| --- | --- |
| Azure portal |VM을 만들 때 목록에서 위치를 선택합니다. |
| Azure PowerShell |[Get AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) 명령을 사용합니다. |
| REST API |[위치 나열](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations) 작업을 사용합니다. |
| Azure CLI |[az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az_account_list_locations) 작업을 사용합니다. |

### <a name="vm-size"></a>VM 크기
사용할 VM의 [크기](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)는 실행하려는 워크로드를 기준으로 결정됩니다. 그런 다음 선택하는 크기는 처리 성능, 메모리 및 저장소 용량 등의 요소를 결정합니다. Azure는 다양한 크기를 제공하여 다양한 유형의 사용을 지원합니다.

Azure는 VM의 크기와 운영 체제에 기반한 [시간당 가격](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)을 청구합니다. 부분 시간의 경우 Azure는 사용 시간(분)에 대해서만 청구합니다. 저장소는 가격이 책정되며 개별적으로 청구됩니다.

### <a name="vm-limits"></a>VM 제한
구독에는 프로젝트에 대해 많은 수의 VM을 배포하는 데 영향을 줄 수 있는 기본 [할당량 제한](../../azure-subscription-service-limits.md)이 있습니다. 구독별 기준으로 현재 제한은 지역당 20대의 VM입니다. [증가를 요구하는 지원 티켓을 제출](../../azure-supportability/resource-manager-core-quotas-request.md)하면 한도가 늘어날 수 있습니다.

### <a name="operating-system-disks-and-images"></a>운영 체제 디스크 및 이미지
가상 머신은 [VHD(가상 하드 디스크)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 사용하여 해당 OS(운영 체제) 및 데이터를 저장합니다. VHD는 OS를 설치하도록 선택할 수 있는 이미지에도 사용됩니다. 

Azure에서는 다양한 버전과 종류의 Windows Server 운영 체제에서 사용할 수 있도록 많은 [마켓플레이스 이미지](https://azure.microsoft.com/marketplace/virtual-machines/)를 제공합니다. Marketplace 이미지는 이미지 게시자, 제안, SKU 및 버전(대개 최신으로 지정된 버전)으로 식별됩니다. 

아래 표에서는 이미지에 대한 정보를 찾을 수 있는 몇 가지 방법을 보여 줍니다.

| 방법 | 설명 |
| --- | --- |
| Azure portal |사용할 이미지를 선택할 때 사용자에 적합한 값이 자동으로 지정됩니다. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher) -Location "location"<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer) -Location "location" -Publisher "publisherName"<BR>[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) -Location "location" -Publisher "publisherName" -Offer "offerName" |
| REST API |[이미지 게시자 나열](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[이미지 제안 나열](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[이미지 SKU 나열](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| Azure CLI |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list_publishers) --location "location"<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list_offers) --location "location" --publisher "publisherName"<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_skus) --location "location" --publisher "publisherName" --offer "offerName"|

[사용자 고유의 이미지를 업로드하고 사용하도록](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account) 선택할 수 있습니다. 이렇게 하는 경우 게시자 이름, 제안 및 SKU는 사용되지 않습니다.

### <a name="extensions"></a>확장
VM [확장](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)은 배포 후 구성 및 자동화 작업을 통해 VM 추가 기능을 제공합니다.

다음과 같은 일반 작업은 확장을 사용하여 수행할 수 있습니다.

* **사용자 지정 스크립트 실행** – [사용자 지정 스크립트 확장](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하면 VM을 프로비전할 때 스크립트를 실행하여 VM에 워크로드를 구성할 수 있습니다.
* **구성 배포 및 관리** – [PowerShell DSC(필요한 상태 구성) 확장](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하면 구성과 환경을 관리하도록 VM에 DSC를 설정할 수 있습니다.
* **진단 데이터 수집** – [Azure 진단 확장](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 사용하면 응용 프로그램의 상태를 모니터링하는 데 사용할 수 있는 진단 데이터를 수집하도록 VM을 구성할 수 있습니다.

### <a name="related-resources"></a>관련 리소스
이 표에 있는 리소스는 VM에서 사용하며, VM을 만들 때 이미 존재하거나 만들어야 합니다.

| 리소스 | 필수 | 설명 |
| --- | --- | --- |
| [리소스 그룹](../../azure-resource-manager/resource-group-overview.md) |예 |VM은 리소스 그룹에 포함되어야 합니다. |
| [Storage 계정](../../storage/common/storage-create-storage-account.md) |예 |가상 하드 디스크를 저장하기 위해 VM에 저장소 계정이 필요합니다. |
| [가상 네트워크](../../virtual-network/virtual-networks-overview.md) |예 |VM은 가상 네트워크의 구성원이어야 합니다. |
| [공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |아니오 |원격으로 액세스하기 위해 VM에 할당된 공용 IP 주소가 있을 수 있습니다. |
| [네트워크 인터페이스](../../virtual-network/virtual-network-network-interface.md) |예 |네트워크에서 통신하기 위해 VM에 네트워크 인터페이스가 필요합니다. |
| [데이터 디스크](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |아니오 |VM은 저장소 기능을 확장하기 위해 데이터 디스크를 포함할 수 있습니다. |

## <a name="how-do-i-create-my-first-vm"></a>첫 번째 VM을 만드는 방법
VM을 만들기 위한 몇 가지 옵션이 있습니다. 선택 옵션은 속해 있는 환경에 따라 달라집니다. 

아래 표에서는 VM을 만들기 시작할 때 가져올 수 있는 정보를 제공합니다.

| 방법 | 문서 |
| --- | --- |
| Azure portal |[포털에서 Windows를 실행하는 가상 머신 만들기](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 템플릿 |[리소스 관리자 템플릿을 사용하여 Windows 가상 머신 만들기](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 클라이언트 SDK |[C#를 사용하여 Azure 리소스 배포](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST API |[VM 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure CLI |[Azure CLI를 사용하여 VM 만들기](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

문제가 결코 발생하지 않기를 바라지만 때로는 몇몇 문제가 발생하기도 합니다. 이러한 상황이 발생하면 [Azure에서 Windows 가상 머신을 만들 때 발생하는 Resource Manager 배포 문제 해결](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 있는 정보를 확인하세요.

## <a name="how-do-i-manage-the-vm-that-i-created"></a>만든 VM을 관리하는 방법
VM은 스크립팅 지원을 통해 브라우저 기반 포털, 명령줄 도구로 관리하거나 REST API를 통해 직접 관리할 수 있습니다. 수행 가능한 몇 가지 일반 관리 작업으로 VM 관련 정보 가져오기, VM에 로그인, 가용성 관리 및 백업 만들기가 있습니다.

### <a name="get-information-about-a-vm"></a>VM에 대한 정보 가져오기
아래 표에서는 VM에 대한 정보를 가져올 수 있는 몇 가지 방법을 보여 줍니다.

| 방법 | 설명 |
| --- | --- |
| Azure portal |허브 메뉴에서 **Virtual Machines**를 클릭하고 목록에서 VM을 선택합니다. VM에 대한 블레이드에서 개요 정보, 설정 값 및 모니터링 메트릭에 액세스할 수 있습니다. |
| Azure PowerShell |PowerShell을 사용하여 VM을 관리하는 방법에 대한 자세한 내용은 [Azure PowerShell 을 사용하여 Windows VM 만들기 및 관리](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. |
| REST API |[VM 가져오기 정보](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) 작업을 사용하여 VM에 대한 정보를 가져옵니다. |
| 클라이언트 SDK |C#를 사용한 VM 관리에 대한 내용은 [Azure Resource Manager 및 C#를 사용하여 Azure Virtual Machines 관리](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. |
| Azure CLI |Azure CLI를 사용하여 VM을 관리하는 방법에 대한 내용은 [Azure CLI 참조](https://docs.microsoft.com/cli/azure/vm)를 참조하세요. |

### <a name="log-on-to-the-vm"></a>VM에 로그인
Azure 포털의 연결 단추를 사용하여 [RDP(원격 데스크톱) 세션을 시작합니다](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 원격 연결을 사용하려고 할 때 가끔 문제가 발생할 수도 있습니다. 이러한 상황이 발생하면 [Windows를 실행하는 Azure 가상 머신에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 있는 도움말 정보를 확인하세요.

### <a name="manage-availability"></a>가용성 관리
응용 프로그램의 [높은 가용성을 보장](manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법을 이해하는 것이 중요합니다. 이렇게 구성하면 여러 VM을 만들어 하나 이상 실행되도록 합니다.

99.95 VM SLA(서비스 수준 계약)에 적합한 배포가 되도록 [가용성 집합](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 내에서 워크로드를 실행하는 VM을 둘 이상 배포해야 합니다. 이렇게 구성하면 VM이 여러 오류 도메인 간에 분산되고, 다양한 유지 관리 창을 사용하는 호스트에 배포됩니다. 전체 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)는 Azure의 보장된 가용성에 대해 전반적으로 설명합니다.

### <a name="back-up-the-vm"></a>VM 백업
[Recovery Services 자격 증명 모음](../../backup/backup-introduction-to-azure-backup.md)은 Azure Backup 및 Azure Site Recovery 서비스 모두에서 데이터와 자산을 보호하는 데 사용됩니다. Recovery Services 자격 증명 모음을 사용하면 [PowerShell을 통해 Resource Manager 배포 VM에 대한 백업을 배포하고 관리할 수 있습니다](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>다음 단계
* Linux VM으로 작업할 계획이면 [Azure 및 Linux](../linux/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 살펴봅니다.
* [Azure 인프라 연습 예제](infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 인프라를 설정하는 관련 지침에 대해 알아봅니다.
