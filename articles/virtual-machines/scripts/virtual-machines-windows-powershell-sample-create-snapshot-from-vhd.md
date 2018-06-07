---
title: Azure PowerShell 스크립트 샘플 - VHD에서 스냅숏을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - VHD에서 스냅숏을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기
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
ms.openlocfilehash: 7148d28038986d3ac7f88dd57f937942b0d29bfc
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "31600243"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>PowerShell을 사용하여 VHD에서 스냅숏을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기

이 스크립트는 같은 구독 또는 다른 구독의 저장소 계정에 VHD 파일의 스냅숏을 만듭니다. 이 스크립트를 사용하여 특수화된(일반화되지 않은/sysprep을 실행하지 않은) VHD를 스냅숏으로 가져온 다음 스냅숏을 사용하여 약간의 시간 동안 같은 관리 디스크를 여러 개 만듭니다. 또한 이 스크립트를 사용하여 데이터 VHD를 스냅숏으로 가져온 다음 스냅숏을 사용하여 약간의 시간 동안 관리 디스크를 여러 개 만듭니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure PowerShell 모듈 버전 4.0 이상을 실행해야 합니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 다른 구독의 VHD에서 관리 디스크를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | 디스크 만들기에 사용되는 디스크 구성을 만듭니다. 저장소 형식, 위치, 부모 VHD가 저장된 저장소 계정의 리소스 ID 및 부모 VHD의 VHD URI를 포함합니다. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | 매개 변수로 전달된 디스크 구성, 디스크 이름 및 리소스 그룹 이름을 사용하여 디스크를 만듭니다. |

## <a name="next-steps"></a>다음 단계

[스냅숏에서 관리 디스크 만들기](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[관리 디스크를 OS 디스크로 연결하여 가상 머신 만들기](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
