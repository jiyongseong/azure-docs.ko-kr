---
title: Azure CLI 샘플 | Microsoft Docs
description: Azure CLI 샘플
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5df71f1b662defa9103df4b10a86f67bf5ee6e4b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Linux 가상 머신에 대한 Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| | |
|---|---|
|**가상 머신 만들기**||
| [가상 머신 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | 최소한의 구성으로 Linux 가상 머신을 만듭니다. |
| [완벽히 구성된 가상 컴퓨터 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | 리소스 그룹, 가상 머신 및 모든 관련된 리소스를 만듭니다.|
| [고가용성 가상 머신 만들기](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | 고가용성의 부하가 분산된 구성으로 여러 가상 머신을 만듭니다. |
| [VM 만들기 및 구성 스크립트 실행](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 머신을 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 NGINX를 설치합니다. |
| [WordPress가 설치된 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 머신을 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 WordPress를 설치합니다. |
| [관리되는 OS 디스크에서 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | 기존 관리되는 디스크를 OS 디스크로 연결하여 가상 머신을 만듭니다. |
| [스냅숏에서 VM 만들기](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | 먼저 스냅숏에서 관리되는 디스크를 만들고 새 관리되는 디스크를 OS 디스크로 연결하여 스냅숏에서 가상 머신을 만듭니다. |
|**저장소 관리**||
| [VHD에서 관리 디스크 만들기](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | 특수화된 VHD의 관리 디스크를 OS 디스크로 만들거나 데이터 VHD의 관리 디스크를 데이터 디스크로 만듭니다.  |
| [스냅숏에서 관리 디스크 만들기](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | 스냅숏에서 관리 디스크를 만듭니다. |
| [동일하거나 다른 구독으로 관리 디스크 복사](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 부모 관리 디스크와 같은 지역에 있지만 동일하거나 다른 구독으로 관리 디스크를 복사합니다. 
| [저장소 계정에 VHD로 스냅샷 내보내기](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) | 다른 지역의 저장소 계정에 관리 스냅숏을 VHD로 내보냅니다. |
| [동일하거나 다른 구독으로 스냅숏 복사](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 부모 스냅숏과 같은 지역에 있지만 동일하거나 다른 구독으로 스냅숏을 복사합니다. |
|**네트워크 가상 머신**||
| [가상 머신 간의 네트워크 트래픽 보안](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | 두 개의 가상 머신, 모든 관련된 리소스 및 내부 및 외부 NSG(네트워크 보안 그룹)를 만듭니다. |
|**가상 머신 보호**||
| [VM 및 데이터 디스크 암호화](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Azure Key Vault, 암호화 키 및 서비스 주체를 만든 다음 VM을 암호화합니다. |
|**가상 머신 모니터링**||
| [Operations Management Suite를 사용하여 VM 모니터링](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | 가상 머신을 만들고 Operations Management Suite 에이전트를 설치하고 OMS 작업 영역에서 VM을 등록합니다.  |
|**가상 머신 문제 해결**||
| [VM 운영 체제 디스크 문제 해결](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | 두 번째 VM의 데이터 디스크처럼 첫 번째 VM에서 운영 체제 디스크를 탑재합니다. |
| | |
