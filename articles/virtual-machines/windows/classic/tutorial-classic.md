---
title: Azure Portal에서 VM 만들기 | Microsoft Docs
description: Azure 포털에서 Windows 가상 머신만들기
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5fd2128ff436d3211f41c7dfdcc4c2b8aabd0eb0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Azure 포털에서 Windows를 실행하는 가상 머신 만들기
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [PowerShell: 클래식 배포](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. **Azure Portal**을 통해 [Resource Manager 배포 모델을 사용하여 이러한 단계를 수행하는 방법](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 알아봅니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

이 자습서에서는 Azure Portal에서 Windows를 실행하는 Azure VM(가상 컴퓨터)을 만드는 방법을 보여 줍니다. 한 예로, Windows Server 이미지를 사용할 것이지만, 해당 아미지는 Azure가 제공하는 여러 이미지 중 하나일 뿐입니다. 참고: 이미지 선택은 구독에 따라 달라집니다. 예를 들어 Windows 데스크톱 이미지는 MSDN 구독자가 사용할 수 있습니다.

이 섹션에서는 Azure Portal에서 **대시보드**를 사용하여 가상 컴퓨터를 선택하고 만드는 방법을 보여 줍니다.

[사용자 고유의 이미지](createupload-vhd.md)를 사용하여 VM을 만들 수도 있습니다. 이 방법 및 다른 방법에 대한 자세한 내용은 [Windows 가상 머신을 만드는 다양한 방법](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

## <a id="createvirtualmachine"> </a>가상 컴퓨터 만들기
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>다음 단계
* Azure Portal에서 [Resource Manager 배포 모델을 사용하여 VM을 만드는 방법](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 알아봅니다.
* 가상 머신에 로그온합니다. 지침은 [Windows Server를 실행하는 가상 머신에 로그온](connect-logon.md)을 참조하세요.
* 데이터를 저장할 디스크를 연결합니다. 빈 디스크와 데이터가 포함된 디스크를 모두 연결할 수 있습니다. 지침은 [클래식 배포 모델을 사용하여 만든 Windows 가상 컴퓨터에 데이터 디스크 연결](attach-disk.md)을 참조하세요.
