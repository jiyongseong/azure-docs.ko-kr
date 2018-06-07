---
title: HPC 팩 클러스터에 버스트 노드 추가 | Microsoft Docs
description: 클라우드 서비스에서 실행되는 작업자 역할 인스턴스를 추가하여 요청에 따라 HPC Pack 클러스터를 확장하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: eee9183321f21676271c8a9c7e023c80c4daf554
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Azure의 HPC 팩 클러스터에 주문형 "버스트" 노드 추가
Azure에서 [Microsoft HPC 팩](https://technet.microsoft.com/library/cc514029) 클러스터를 설정하는 경우 미리 구성된 계산 노드 VM을 유지하지 않고 클러스터 용량을 신속하게 확장 또는 축소할 수 있는 방법을 원할 것입니다. 이 문서에서는 "버스트" 노드(클라우드 서비스에서 실행되는 작업자 역할 인스턴스)를 Azure의 헤드 노드에 계산 리소스로 추가하는 방법을 보여줍니다. 

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![버스트 노드][burst]

이 문서의 단계를 통해 Azure 노드를 클라우드 기반 HPC 팩 헤드 노드 VM에 빠르게 추가하여 테스트 또는 개념 증명 배포를 수행할 수 있습니다. 전체적인 단계는 "Azure로 버스트"하여 온-프레미스 HPC 팩 클러스터에 클라우드 계산 용량을 추가하는 단계와 동일합니다. 자습서를 보려면 [Microsoft HPC 팩을 사용하여 하이브리드 계산 클러스터 설정](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)을 참조하세요. 프로덕션 배포에 대한 자세한 지침과 고려 사항을 보려면 [Microsoft HPC 팩을 사용하여 Azure로 버스트](https://technet.microsoft.com/library/gg481749.aspx)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Azure VM에 배포된 HPC Pack 헤드 노드** - 독립 실행형 헤드 노드 VM 또는 더 큰 클러스터의 일부인 VM을 사용할 수 있습니다. 독립 실행형 헤드 노드를 만들려면 [Azure VM에서 HPC Pack 헤드 노드 배포](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조합니다. 자동화된 HPC Pack 클러스트 배포 옵션에 대한 자세한 내용은 [Microsoft HPC Pack을 사용하여 Azure에서 Windows HPC 클러스터를 만들고 관리하는 옵션](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
  
  > [!TIP]
  > [HPC 팩 IaaS 배포 스크립트](hpcpack-cluster-powershell-script.md) 를 사용하여 Azure에 클러스터를 만들 경우 자동 배포에 Azure 버스트 노드를 포함할 수 있습니다. 해당 문서에서 예제를 참조하세요.
  > 
  > 
* **Azure 구독** - Azure 노드를 추가하려는 경우 헤드 노드 VM을 배포하는 데 사용하는 것과 동일한 구독 또는 다른 구독을 하나 이상 선택할 수 있습니다.
* **코어 할당량** - 멀티 코어 크기를 사용하여 여러 Azure 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 합니다.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>1단계: Azure 노드에 대한 클라우드 서비스 및 저장소 계정 만들기
Azure Portal 또는 동급의 도구를 사용하여 Azure 노드를 배포하는 데 필요한 다음 리소스를 구성합니다.

* 새 Azure 클라우드 서비스(클래식)
* 새 Azure Storage 계정(클래식)

> [!NOTE]
> 구독에서 기존 클라우드 서비스를 다시 사용하지 마세요. 
> 
> 

**고려 사항**

* 만들려는 각 Azure 노드 템플릿에 대해 별도의 클라우드 서비스를 구성합니다. 하지만 여러 노드 템플릿에 대해 동일한 저장소 계정을 사용할 수 있습니다.
* 동일한 Azure 지역에서 배포하는 데 사용할 저장소 계정과 클라우드 서비스를 찾는 것이 좋습니다.

## <a name="step-2-configure-an-azure-management-certificate"></a>2단계: Azure 관리 인증서 구성
Azure 노드를 계산 리소스로 추가하려면 헤드 노드에 관리 인증서를 추가하고 해당 인증서를 배포에 사용한 Azure 구독에 업로드해야 합니다.

이 시나리오의 경우 HPC 팩에서 설치한 **기본 HPC Azure 관리 인증서** 를 선택하고 헤드 노드에 자동으로 구성할 수 있습니다. 이 인증서는 테스트 목적 및 개념 증명 배포에 사용됩니다. 이 인증서를 사용하려면 헤드 노드 VM에서 구독으로 C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer 파일을 업로드합니다. [Azure Portal](https://portal.azure.com)에 인증서를 업로드하려면:

1. **구독** > *your_subscription_name*을 클릭합니다.

2. **관리 인증서** > **업로드**를 클릭합니다.

관리 인증서 구성에 대한 추가 옵션을 보려면 [Azure 버스트 배포를 위한 Azure 관리 인증서 구성 시나리오](http://technet.microsoft.com/library/gg481759.aspx)를 참조하세요.

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>단계 3: 클러스터에 Azure 노드 배포
이 시나리오에서 Azure 노드를 추가 및 시작하는 단계는 온-프레미스 헤드 노드의 단계와 일반적으로 동일합니다. 자세한 내용은 [Microsoft HPC 팩을 사용하여 Azure 노드를 배포하는 단계](https://technet.microsoft.com/library/gg481758.aspx)에서 다음 섹션을 참조하세요.

* Azure 노드 템플릿 만들기
* Windows HPC 클러스터에 Azure 노드 추가
* Azure 노드 시작(프로비전)

노드를 추가 및 시작하면 클러스터 작업을 실행하는 데 사용할 수 있습니다.

Azure 노드를 배포할 때 문제가 발생할 경우 [Microsoft HPC Pack을 사용하여 Azure 노드 배포 시 문제 해결](http://technet.microsoft.com/library/jj159097.aspx)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 버스트 노드에 계산 집약적 인스턴스 크기를 사용하려는 경우 [고성능 계산 VM 크기](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)의 고려 사항을 참조하세요.
* Azure 컴퓨팅 리소스를 클러스터 워크로드에 따라 자동으로 증가 또는 축소하려는 경우 [HPC Pack 클러스터에서 Azure 계산 리소스를 자동으로 증가 및 축소](hpcpack-cluster-node-autogrowshrink.md)를 참조하세요.

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
