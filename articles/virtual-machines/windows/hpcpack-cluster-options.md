---
title: Azure의 Windows HPC 팩 클러스터 옵션 | Microsoft Docs
description: Microsoft HPC Pack을 사용하여 Azure 클라우드에서 Windows HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하기 위한 옵션에 대해 알아봅니다.
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165760"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>HPC Pack을 사용하여 Azure에서 Windows HPC 워크로드의 클러스터를 만들고 관리하기 위한 옵션
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

이 문서에서는 Windows 워크로드를 실행하는 HPC 팩 클러스터를 만드는 Azure 옵션에 대해 집중적으로 설명합니다. 또한 [Linux HPC 워크로드](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 실행하는 HPC 팩 클러스터를 생성하는 옵션도 있습니다.


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM의 HPC Pack 클러스터 및 VM 확장 집합
### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿
* (GitHub) [HPC 팩 2016 업데이트 1 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC 팩 2012 R2 클러스터 템플릿](https://github.com/MsHpcPack/HPCPack2012R2)
* (빠른 시작) [HPC 팩 2012 R2 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* (빠른 시작) [사용자 지정 계산 노드 이미지가 있는 HPC 팩 2012 R2 클러스터 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM 이미지
Azure에서 자신만의 HPC 팩 클러스터를 구축하려면 [Azure Marketplace에서 HPC 팩 이미지](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22)를 찾습니다.


### <a name="tutorials"></a>자습서
* [자습서: Azure에서 HPC 팩 2016 클러스터 배포](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure에서 Azure Active Directory로 HPC Pack 2016 클러스터 관리](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>클래식 배포 모델에서 HPC 팩 2012 R2 클러스터 배포
* [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [자습서: Azure에서 Excel 및 SOA 워크로드를 실행할 HPC 팩 클러스터 시작](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure에서 HPC 팩 클러스터의 컴퓨터 노드 관리](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [HPC 팩 클러스터에서 Azure 계산 리소스 확장 및 축소](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [MPI 응용 프로그램을 실행하기 위해 HPC Pack을 사용하여 Windows RDMA 클러스터 설정](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>HPC 팩 2012 R2에서 Azure에 버스트
* [Microsoft HPC 팩을 사용하여 Azure 작업자 인스턴스에 버스트](https://technet.microsoft.com/library/gg481749.aspx)
* [HPC 팩을 사용하여 Azure Batch로 버스트](https://technet.microsoft.com/library/mt612877.aspx)
* [자습서: Azure에서 HPC 팩을 사용하여 하이브리드 클러스터 설정](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>작업 제출

* [Azure에서 HPC 팩 클러스터에 작업 제출](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






