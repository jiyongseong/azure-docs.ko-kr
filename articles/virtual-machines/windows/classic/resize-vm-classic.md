---
title: 클래식 배포 모델에서 Windows VM 크기 조정 - Azure | Microsoft Docs
description: Azure Powershell을 사용하여 클래식 배포 모델에서 만든 Windows 가상 머신의 크기를 조정합니다.
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 6afc8125462a659d0892e7dbb53f1f71789815cc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>클래식 배포 모델에서 만든 Windows VM 크기 조정
이 문서에서는 Azure Powershell을 사용하여 클래식 배포 모델에서 만든 Windows VM의 크기를 조정하는 방법을 보여 줍니다.

VM의 크기를 조정하는 기능을 고려할 때, 가상 컴퓨터 크기 조정에 사용할 수 있는 크기의 범위를 제어하는 두 가지 개념이 있습니다. 첫 번째 개념은 VM이 배포된 지역입니다. 지역에 사용할 수 있는 VM 크기 목록은 Azure 지역 웹 페이지의 서비스 탭 아래에 있습니다. 두 번째 개념은 현재 VM을 호스팅하는 실제 하드웨어입니다. VM을 호스팅하는 물리적 서버는 일반적인 실제 하드웨어의 클러스터에서 함께 그룹화됩니다. VM 크기를 변경하는 방법은 현재 VM을 호스팅하는 하드웨어 클러스터에서 원하는 새 VM 크기를 지원하는지 여부에 따라 다릅니다.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 배포 모델에서 만든 VM의 크기를 조정](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수도 있습니다.

## <a name="add-your-account"></a>사용자 계정 추가
클래식 Azure 리소스와 함께 작동하도록 Azure PowerShell을 구성해야 합니다. 클래식 리소스를 관리하도록 Azure PowerShell을 구성하려면 다음 단계를 수행 합니다.

1. PowerShell 프롬프트에서 `Add-AzureAccount`를 입력하고 **Enter**를 클릭합니다. 
2. Azure 구독과 연관된 메일 주소를 입력하고 **계속**을 클릭합니다. 
3. 계정에 암호를 입력합니다. 
4. **로그인**을 클릭합니다. 

## <a name="resize-in-the-same-hardware-cluster"></a>동일한 하드웨어 클러스터에서 크기 조정
VM을 호스팅하는 하드웨어 클러스터에서 사용할 수 있는 크기로 VM 크기를 조정하려면 다음 단계를 수행합니다.

1. VM을 포함하는 클라우드 서비스를 호스팅하는 하드웨어 클러스터에서 사용 가능한 VM 크기를 나열하려면 다음 PowerShell 명령을 실행합니다.
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. 다음 명령을 실행하여 VM 크기를 조정합니다.
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>새 하드웨어 클러스터에서 크기 조정
VM을 호스팅하는 하드웨어 클러스터에서 사용할 수 없는 크기로 VM을 크기 조정하려면 클라우드 서비스와 클라우드 서비스의 모든 VM을 다시 만들어야 합니다. 각 클라우드 서비스는 단일 하드웨어 클러스터에서 호스트되므로 클라우드 서비스의 모든 VM은 하드웨어 클러스터에서 지원되는 크기여야 합니다. 다음 단계에서는 클라우드 서비스를 삭제한 후 다시 만들어 VM 크기를 조정하는 방법을 설명합니다.

1. 지역에서 사용할 수 있는 VM 크기를 나열하려면 다음 PowerShell 명령을 실행합니다. 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. 크기 조정할 VM을 포함하는 클라우드 서비스의 각 VM에 대한 모든 구성 설정을 기록해 둡니다. 
3. 각 VM에 대한 디스크를 유지하는 옵션을 선택하는 클라우드 서비스에서 모든 VM을 삭제합니다.
4. 원하는 VM 크기를 사용하여 크기를 조정하도록 VM을 다시 만듭니다.
5. 이제 클라우드 서비스를 호스팅하는 하드웨어 클러스터에서 사용할 수 있는 VM 크기를 사용하여 클라우드 서비스에 있는 다른 모든 VM을 다시 만듭니다.

새 VM 크기를 사용하여 클라우드 서비스를 삭제하고 다시 만드는 샘플 스크립트는 [여기](https://github.com/Azure/azure-vm-scripts)에서 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
* [Resource Manager 배포 모델에서 만든 VM 크기 조정](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

