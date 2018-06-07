---
title: Azure 스택 Linux 이미지를 추가 합니다.
description: 자세한 내용은 Azure 스택에 Linux 이미지를 추가 하는 방법입니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 64a860bc925b9c7499363c1fe39d03df88a9a51d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="add-linux-images-to-azure-stack"></a>Azure 스택 Linux 이미지를 추가 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 Marketplace에 Linux 기반 이미지를 추가 하 여 Azure 스택에 Linux 가상 컴퓨터 (Vm)를 배포할 수 있습니다. 마켓플레이스 관리를 통해 Azure 스택에 Linux 이미지를 추가 하는 가장 쉬운 방법은 됩니다. 이러한 이미지가 준비 하 고 Azure 스택와의 호환성에 대 한 테스트 합니다.

## <a name="marketplace-management"></a>마켓플레이스 관리

Azure 마켓플레이스에서 Linux 이미지를 다운로드 하려면 문서에 설명 된 절차를 따르세요. 사용자가 Azure 스택에 제공 하려는 Linux 이미지를 선택 합니다. 

[Azure에서 Azure 스택에 마켓플레이스 항목을 다운로드](azure-stack-download-azure-marketplace-item.md)합니다.

이러한 이미지를 자주 업데이트 됩니다 되므로 마켓플레이스 관리를 최신 상태로 유지 하는 빈도 확인 하세요.

## <a name="prepare-your-own-image"></a>사용자 고유의 이미지를 준비 합니다.

 가능한 경우 항상 준비 되어 Azure 스택에 대 한 테스트는 마켓플레이스 관리를 통해 사용할 수 있는 이미지를 다운로드 합니다. 
 
 Azure Linux 에이전트 (일반적으로 호출 `WALinuxAgent` 또는 `walinuxagent`)가 필요한 모든 버전의 에이전트는 Azure 스택에 작동 합니다. 버전 2.2.18 또는 이상을 사용 해야 사용자 고유의 이미지를 만듭니다. [클라우드 init](https://cloud-init.io/) Azure 스택에이 이번에 지원 되지 않습니다.

 다음 지침을 사용 하 여 사용자 고유의 Linux 이미지를 준비할 수 있습니다.

   * [CentOS 기반 배포](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
   * [SLES 및 openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
   * [Ubuntu Server](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>마켓플레이스 이미지를 추가 합니다.
 
에 따라 [시장에 이미지를 추가](azure-stack-add-vm-image.md)합니다. 다음 사항을 확인는 `OSType` 로 설정 된 `Linux`합니다.

마켓플레이스 이미지를 추가한 후 마켓플레이스 항목이 만들어지고 사용자가 Linux 가상 컴퓨터를 배포할 수 있습니다.
