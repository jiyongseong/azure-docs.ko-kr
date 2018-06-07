---
title: Azure PowerShell 스크립트 샘플 - 동일한 또는 다른 구독에 있는 저장소 계정의 VHD 파일에서 관리 디스크 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 동일한 또는 다른 구독에 있는 저장소 계정의 VHD 파일에서 관리 디스크 만들기
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 6dfdd88f2f1e776fca69ffa53b2e424fe9d2c8ea
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>PowerShell을 사용하여 동일한 또는 다른 구독에 있는 저장소 계정의 VHD 파일에서 관리 디스크 만들기

이 스크립트는 같은 구독 또는 다른 구독의 저장소 계정에 VHD 파일의 관리 디스크를 만듭니다. 이 스크립트를 사용하여 관리 OS 디스크에 특수화된(일반화/sysprep되지 않음) VHD를 가져와 가상 머신을 만듭니다 또한 데이터 VHD를 관리되는 데이터 디스크로 가져오는 데 사용합니다. 

VHD 파일에서 단시간에 동일한 관리 디스크를 여러 개 만들지 마세요. Vhd 파일에서 관리 디스크를 만들기 위해 vhd 파일에서 Blob 스냅숏이 만들어진 후 관리 디스크를 만드는 데 사용됩니다. 1분에 하나의 Blob 스냅숏만 만들 수 있으므로 제한으로 인해 디스크 생성 오류가 발생합니다. 이 제한을 피하려면 [vhd 파일에서 관리 스냅숏](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 만든 후 관리 스냅숏을 사용하여 단시간에 여러 관리 디스크를 만듭니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈 버전 4.0 이상을 실행해야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 다른 구독의 VHD에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 디스크 만들기에 사용되는 디스크 구성을 만듭니다. 저장소 형식, 위치, 부모 VHD가 저장된 저장소 계정의 리소스 ID, 부모 VHD의 VHD URI를 포함합니다. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 매개 변수로 전달된 디스크 구성, 디스크 이름 및 리소스 그룹 이름을 사용하여 디스크를 만듭니다. |

## <a name="next-steps"></a>다음 단계

[관리 디스크를 OS 디스크로 연결하여 가상 머신 만들기](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
