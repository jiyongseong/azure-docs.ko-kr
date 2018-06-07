---
title: Azure Virtual Machine PowerShell 샘플 | Microsoft Docs
description: Azure Virtual Machine PowerShell 샘플
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/30/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 27f926a9a5d30d28e2721ffa52695292f794a501
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell 샘플

다음 표에는 Windows 가상 머신을 만들고 관리하는 PowerShell 스크립트 샘플에 대한 링크가 제공됩니다.

| | |
|---|---|
|**가상 머신 만들기**||
| [가상 머신 빠르게 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 최소한의 프롬프트를 사용하여 리소스 그룹, 가상 머신 및 모든 관련된 리소스를 만듭니다.|
| [완벽히 구성된 가상 머신 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 리소스 그룹, 가상 머신 및 모든 관련된 리소스를 만듭니다.|
| [고가용성 가상 머신 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 고가용성의 부하가 분산된 구성으로 여러 가상 머신을 만듭니다.|
| [VM 만들기 및 구성 스크립트 실행](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 가상 머신을 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 IIS를 설치합니다. |
| [VM 만들기 및 DSC 구성 실행](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 가상 머신을 만들고 Azure DSC(필요한 상태 구성) 확장을 사용하여 IIS를 설치합니다. |
| [VHD 업로드 및 VM 만들기](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | 로컬 VHD 파일을 Azure에 업로드하고 VHD에서 이미지 만든 후 해당 이미지에서 VM을 만듭니다. |
| [관리되는 OS 디스크에서 VM 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 기존 관리되는 디스크를 OS 디스크로 연결하여 가상 머신을 만듭니다. |
| [스냅숏에서 VM 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 먼저 스냅숏에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅숏에서 가상 머신을 만듭니다. |
|**저장소 관리**||
| [동일하거나 다른 구독의 VHD에서 관리 디스크 만들기](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 동일하거나 다른 구독에서 특수화된 VHD의 관리 디스크를 OS 디스크로 만들거나 데이터 VHD의 관리 디스크를 데이터 디스크로 만듭니다.  |
| [스냅숏에서 관리 디스크 만들기](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 스냅숏에서 관리 디스크를 만듭니다. |
| [동일하거나 다른 구독으로 관리 디스크 복사](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 부모 관리 디스크와 같은 지역에 있지만 동일하거나 다른 구독으로 관리 디스크를 복사합니다. 
| [저장소 계정에 VHD로 스냅샷 내보내기](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 다른 지역의 저장소 계정에 관리 스냅숏을 VHD로 내보냅니다. |
| [VHD에서 스냅숏 만들기](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json) | VHD에서 스냅숏을 만들어 짧은 시간 동안 스냅숏에서 동일한 여러 관리 디스크를 만듭니다.  |
| [동일하거나 다른 구독으로 스냅숏 복사](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 부모 스냅숏과 같은 지역에 있지만 동일하거나 다른 구독으로 스냅숏을 복사합니다. |
|**가상 머신 보호**||
| [VM 및 데이터 디스크 암호화](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Azure Key Vault, 암호화 키 및 서비스 주체를 만든 다음 VM을 암호화합니다. |
|**가상 머신 모니터링**||
| [Operations Management Suite를 사용하여 VM 모니터링](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 가상 머신을 만들고 Operations Management Suite 에이전트를 설치하고 OMS 작업 영역에서 VM을 등록합니다.  |
| | |

