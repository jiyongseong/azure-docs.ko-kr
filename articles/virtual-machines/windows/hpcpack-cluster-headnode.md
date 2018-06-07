---
title: Azure VM에서 HPC 팩 헤드 노드 만들기 | Microsoft Docs
description: Azure Portal 및 Resource Manager 배포 모델을 사용하여 Azure VM에 Microsoft HPC 팩 2012 R2 헤드 노드를 만드는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Marketplace 이미지를 사용하여 Azure VM에 HPC 팩 클러스터의 헤드 노드 만들기
Azure Marketplace 및 Azure Portal에서 [Microsoft HPC 팩 2012 R2 가상 머신 이미지](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)를 사용하여 HPC 클러스터의 헤드 노드를 만드는 방법을 보여 줍니다. 이 HPC 팩 VM 이미지는 HPC 팩 2012 R2 업데이트 3이 미리 설치된 Windows Server 2012 R2 Datacenter를 기준으로 합니다. Azure에서 HPC 팩의 개념 증명 배포에 대해 이 헤드 노드를 사용합니다. 그런 다음 계산 노드를 HPC 워크로드를 실행하는 클러스터에 추가할 수 있습니다.

> [!TIP]
> 헤드 노드와 계산 노드를 포함하는 전체 HPC 팩 2012 R2 클러스터를 Azure에 배포하려면 자동화된 방법을 사용하는 것이 좋습니다. 옵션에는 [HPC 팩 IaaS 배포 스크립트](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) 및 [Windows 워크로드에 대한 HPC 팩 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)와 같은 Resource Manager 템플릿이 포함됩니다. Resource Manager 템플릿은 [Microsoft HPC 팩 2016 클러스터](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates)에 사용할 수도 있습니다. 
> 
> 

## <a name="planning-considerations"></a>고려 사항 계획
다음 그림에 나와 있는 것처럼 Azure 가상 네트워크의 Active Directory 도메인에 HPC Pack 헤드 노드를 배포합니다.

![HPC 팩 헤드 노드][headnode]

* **Active Directory 도메인**: VM에서 HPC 서비스를 시작하기 전에 HPC 팩 2012 R2 헤드 노드를 Azure의 Active Directory 도메인에 연결해야 합니다. 이 문서에서 설명한 것처럼 개념 증명 배포의 경우 HPC 서비스를 시작하기 전에 도메인 컨트롤러인 헤드 노드에 대해 만드는 VM을 승격할 수 있습니다. 헤드 노드 VM을 연결하는 Azure에 별도의 도메인 컨트롤러 및 포리스트를 배포하는 방법도 있습니다.

* **배포 모델**: 새로운 배포는 대부분 Resource Manager 배포 모델을 사용하는 것이 좋습니다. 이 문서에서는 배포 모델을 사용하는 것으로 가정합니다.

* **Azure Virtual Network**: Resource Manager 배포 모델을 사용하여 헤드 노드를 배포하는 경우 Azure Virtual Network를 지정하거나 만듭니다. 헤드 노드를 기존 Active Directory 도메인에 가입하는 경우 가상 네트워크를 사용합니다. 나중에 클러스터에 계산 노드 VM을 추가해야 합니다.

## <a name="steps-to-create-the-head-node"></a>헤드 노드를 만드는 단계
다음은 Azure 포털에서 Resource Manager 배포 모델을 사용하여 HPC Pack 헤드 노드용 Azure VM을 만들기 위한 자세한 단계입니다. 

1. 별도 도메인 컨트롤러 VM으로 Azure에 새 Active Directory 포리스트를 만들려는 경우 [Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc)을 사용하는 방법이 있습니다. 간단한 개념 증명 배포의 경우 이 단계를 생략하고 헤드 노드 VM 자체를 도메인 컨트롤러로 구성해도 됩니다. 이 옵션은 이 문서의 뒷부분에서 설명합니다.
2. Azure Marketplace에서 [Windows Server 2012 R2의 HPC Pack 2012 R2 페이지](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)에서 **Virtual Machine 만들기**를 클릭합니다. 
3. 포털의 **Windows Server 2012 R2에 있는 HPC 팩 2012 R2** 페이지에서 **Resource Manager** 배포 모델을 선택한 다음 **만들기**를 클릭합니다.
   
    ![HPC 팩 이미지][marketplace]
4. 포털을 사용하여 설정을 구성하고 VM을 만듭니다. Azure를 처음 접하는 경우 자습서 [Azure 포털에서 Windows 가상 머신 만들기](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 따르세요. 개념 증명 배포의 경우 일반적으로 기본 설정 또는 권장 설정을 적용할 수 있습니다.
   
   > [!NOTE]
   > Azure에서 기존 Active Directory 도메인에 헤드 노드를 가입하려는 경우 VM을 만들 때 해당 도메인에 대한 가상 네트워크를 지정해야 합니다.
   > 
   > 
5. VM을 만든 다음 VM이 실행되면 원격 데스크톱을 사용하여 [VM에 연결합니다](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . 
6. 다음 옵션 중 하나를 선택하여 Active Directory 도메인 포리스트에 VM을 조인합니다.
   
   * 기존 도메인 포리스트가 있는 Azure 가상 네트워크에 VM을 만든 경우 표준 서버 관리자 또는 Windows PowerShell 도구를 사용하여 포리스트에 VM을 연결합니다. 그런 다음 다시 시작합니다.
   * 기존 도메인 포리스트가 없는 새 가상 네트워크에 VM을 만든 경우 VM을 도메인 컨트롤러로 승격합니다. 표준 단계를 사용하여 헤드 노드에 Active Directory Domain Services 역할을 설치하고 구성합니다. 자세한 단계를 보려면 [새 Windows Server 2012 Active Directory 포리스트 설치](https://technet.microsoft.com/library/jj574166.aspx)를 참조하세요.
7. VM이 실행되고 Active Directory 포리스트에 연결되면 다음과 같이 HPC Pack 서비스를 시작합니다.
   
    a. 로컬 관리자 그룹의 구성원인 도메인 계정을 사용하여 헤드 노드 VM에 연결합니다. 예를 들어 헤드 노드 VM을 만들 때 설정한 관리자 계정을 사용합니다.
   
    나. 기본 헤드 노드 구성의 경우 관리자 권한으로 Windows PowerShell을 시작하고 다음을 입력합니다.
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    HPC 팩 서비스가 시작하는 데 몇 분이 소요될 수 있습니다.
   
    추가 헤드 노드 구성 옵션을 보려면 `get-help HPCHNPrepare.ps1`을 입력합니다.

## <a name="next-steps"></a>다음 단계
* 이제 HPC 팩 클러스터의 헤드 노드를 사용할 수 있습니다. 예를 들어 HPC 클러스터 관리자를 시작하고 [배포할 일 모음](https://technet.microsoft.com/library/jj884141.aspx)을 완료합니다.
* 필요 시 클러스터 계산 용량을 늘리려면 클라우드 서비스에 [Azure 버스트 노드](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 추가합니다. 
* 클러스터에서 테스트 워크로드를 실행합니다. 예를 보려면 HPC 팩 [시작하기 가이드](https://technet.microsoft.com/library/jj884144)를 참조하세요.

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
