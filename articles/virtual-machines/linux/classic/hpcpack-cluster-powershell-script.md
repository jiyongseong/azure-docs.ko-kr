---
title: Linux HPC 팩 클러스터를 배포하기 위한 PowerShell 스크립트 | Microsoft Docs
description: PowerShell 스크립트를 실행하여 Azure 가상 머신에 Linux HPC Pack 2012 R2 클러스터 배포
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>HPC Pack IaaS 배포 스크립트를 사용하여 Linux HPC(고성능 컴퓨팅) 클러스터 만들기
HPC Pack IaaS 배포 PowerShell 스크립트를 실행하여 Azure 가상 머신에 완전한 Linux 워크로드용 HPC Pack 2012 R2 클러스터를 배포합니다. 이 클러스터는 Windows Server 및 Microsoft HPC Pack을 실행하는 Active Directory 가입 헤드 노드와 HPC Pack에서 지원하는 Linux 배포판 중 하나를 실행하는 계산 노드로 구성됩니다. Azure에서 Windows 워크로드용 HPC 팩 클러스터를 배포하려면 [HPC 팩 IaaS 배포 스크립트를 사용하여 Windows HPC 클러스터 만들기](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.
> [!IMPORTANT] 
> 이 문서에 설명된 PowerShell 스크립트는 클래식 배포 모델을 사용하여 Azure에 Microsoft HPC Pack 2012 R2 클러스터를 만듭니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> 또한 이 문서에 설명된 스크립트는 HPC Pack 2016을 지원하지 않습니다. HPC Pack 2012 R2 및 HPC 팩 2016에 대한 Resource Manager 템플릿에 대한 정보는 [Azure에서 HPC Pack 클러스터 배포 옵션](../hpcpack-cluster-options.md)을 참조하세요.


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>예제 구성 파일
다음 구성 파일은 도메인 컨트롤러 및 도메인 포리스트를 만들고 HPC Pack 클러스터(로컬 데이터베이스를 사용하는 1개 헤드 노드 및 10개 Linux 계산 노드)를 배포합니다. 모든 클라우드 서비스는 동아시아 위치에서 직접 생성됩니다. Linux 계산 노드는 2개 클라우드 서비스와 2개 저장소 계정에 만들어집니다(즉, *MyLnxCNService01* 및 *mylnxstorage01*의 *MyLnxCN-0001*에서 *MyLnxCN-0005*까지와 *MyLnxCNService02* 및 *mylnxstorage02*의 *MyLnxCN-0006*에서 *MyLnxCN-0010*까지). 계산 노드는 OpenLogic CentOS 버전 7.0 Linux 이미지에서 만들어집니다. 

구독 이름과 계정 및 서비스 이름을 고유한 값으로 대체합니다.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>문제 해결
* **"VNet이 없음" 오류**. HPC 팩 IaaS 배포 스크립트를 실행하여 하나의 구독으로 Azure에 여러 클러스터를 배포할 경우 하나 이상의 배포가 실패하고 “VNet *VNet\_Name* 없음” 오류가 표시됩니다.
  이 오류가 발생할 경우 실패한 배포에 대해 스크립트를 다시 실행합니다.
* **Azure 가상 네트워크에서 인터넷에 액세스할 수 없는 문제**. 배포 스크립트를 사용하여 새 도메인 컨트롤러로 HPC Pack 클러스터를 만들거나 헤드 노드 VM을 도메인 컨트롤러로 수동으로 승격할 경우 Azure 가상 네트워크의 VM에서 인터넷으로 연결할 때 문제가 발생할 수 있습니다. 이러한 문제는 전달자 DNS 서버가 도메인 컨트롤러에 자동으로 구성되어 있고 이 전달자 DNS 서버가 올바르게 확인되지 않을 경우 발생할 수 있습니다.
  
    이 문제를 해결하려면 도메인 컨트롤러에 로그인하고 전달자 구성 설정을 제거하거나 유효한 전달자 DNS 서버를 구성합니다. 그러려면 서버 관리자에서 **도구** > **DNS**를 클릭하여 DNS 관리자를 연 다음 **전달자**를 두 번 클릭합니다.

## <a name="next-steps"></a>다음 단계
* 지원되는 Linux 배포판, 데이터 이동, Linux 계산 노드를 사용하여 HPC 팩 클러스터에 작업을 제출하는 방법에 대한 자세한 내용은 [Azure의 HPC 팩 클러스터에서 Linux 계산 노드 시작](hpcpack-cluster.md)을 참조하세요.
* 스크립트를 사용하여 클러스터를 만들고 Linux HPC 워크로드를 실행하는 자습서는 다음 항목을 참조하세요.
  * [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](hpcpack-cluster-namd.md)
  * [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 OpenFOAM 실행](hpcpack-cluster-openfoam.md)
  * [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 STAR-CCM+ 실행](hpcpack-cluster-starccm.md)

