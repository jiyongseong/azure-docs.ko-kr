---
title: Azure의 Linux HPC Pack 클러스터 옵션 | Microsoft Docs
description: Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Linux HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다.
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 281867e30c78c7ed36ac739c8ae1a902463199cd
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166457"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-linux-hpc-workloads-in-azure"></a>HPC Pack을 사용하여 Azure에서 Linux HPC 워크로드의 클러스터를 만들고 관리하기 위한 옵션
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

이 문서에서는 HPC 팩을 사용하여 Linux 워크로드를 실행하는 Azure 옵션에 대해 집중적으로 설명합니다. 또한 [HPC 팩을 사용하여 Windows HPC 워크로드](../windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 실행할 때 사용 가능한 옵션도 있습니다.

## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM의 HPC Pack 클러스터 및 VM 확장 집합
### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿
* (GitHub) [HPC 팩 2016 업데이트 1 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC 팩 2012 R2 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2012R2)
* (빠른 시작) [Linux 계산 노드가 있는 HPC 팩 2012 R2 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="azure-vm-images"></a>Azure VM 이미지
Azure에서 자신만의 HPC 팩 클러스터를 구축하려면 [Azure Marketplace에서 HPC 팩 이미지](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22)를 찾습니다.

## <a name="hpc-pack-2012-r2-cluster-in-classic-deployment-model"></a>클래식 배포 모델의 HPC 팩 2012 R2 클러스터
* [HPC 팩 IaaS 배포 스크립트를 사용하여 Linux HPC 클러스터 만들기](../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure에서 HPC 팩 클러스터의 Linux 컴퓨터 노드 시작](classic/hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](classic/hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](classic/hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [자습서: Azure의 Linux RDMA 클러스터에서 Microsoft HPC 팩으로 STAR-CCM+ 실행](classic/hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

## <a name="job-submisssion"></a>작업 제출
* [Azure에서 HPC 팩 클러스터에 작업 제출](../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




