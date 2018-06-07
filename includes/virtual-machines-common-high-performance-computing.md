---
title: 포함 파일
description: 포함 파일
services: virtual-machines-linux, virtual-machines-windows
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 05/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32a438d393077cfe4cb7f6ee62f3a01edfce0571
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34152226"
---
조직에는 대규모 컴퓨팅 요구 사항이 있습니다. 이러한 큰 계산 작업에는 엔지니어링 설계 및 분석, 재무 위험 계산, 이미지 렌더링, 복잡한 모델링, 몬테카를로 시뮬레이션 등이 포함됩니다. 

Azure 클라우드를 사용하여 병렬 일괄 처리 작업에서 기존의 HPC 시뮬레이션에 이르기까지 계산 집약적인 Linux 및 Windows 작업을 효율적으로 실행합니다. 선택한 계산 서비스, 그리드 관리자, Marketplace 솔루션 및 공급 업체 호스트(SaaS) 응용 프로그램을 사용하여 Azure 인프라에서 HPC 및 배치 워크로드를 실행합니다. Azure는 작업을 배포하고 수천 대의 VM 또는 코어로 규모를 조정한 후 더 적은 리소스가 필요한 경우 규모를 축소하는 유연한 솔루션을 제공합니다. 



## <a name="solution-options"></a>솔루션 옵션



* **직접 솔루션**
    * Azure 가상 머신 또는 [가상 머신 확장 집합](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)에서 고유한 클러스터 환경을 설정합니다. 
    * 추가 용량을 위해 온-프레미스 클러스터를 전환하거나 Azure에서 새 클러스터를 배포합니다. 
    * Azure Resource Manager 템플릿을 사용하여 선행 [워크로드 관리자](#workload-managers), 인프라 및 [응용 프로그램](#hpc-applications)을 배포합니다. 
    * MPI 또는 GPU 워크로드에 대한 특수한 하드웨어 및 네트워크 연결을 포함하는 [HPC 및 GPU VM 크기](#hpc-and-gpu-sizes)를 선택합니다. 
    * I/O 사용량이 많은 워크로드에 [고성능 저장소](#hpc-storage)를 추가합니다.
* **하이브리드 솔루션**
    * 오프로드("버스트") 최대 워크로드에 대한 온-프레미스 솔루션을 Azure 인프라로 확장합니다.
    * 기존 [워크로드 관리자](#workload-manager)에서 주문형 클라우드 계산을 사용합니다.
    * MPI 또는 GPU 워크로드에 [HPC 및 GPU VM 크기](#hpc-and-gpu-sizes)를 활용합니다.
* **빅 컴퓨팅 솔루션을 서비스로 제공**
    * [Azure Batch](#azure-batch) 및 관련 [Azure 서비스](#related-azure-services)를 사용하여 사용자 지정 빅 컴퓨팅 솔루션 및 워크플로를 개발합니다.
    * [Altair](http://www.altair.com/), [Rescale](https://www.rescale.com/azure/) 및 [Cycle Computing](https://cyclecomputing.com/)(이제 [Microsoft와 조인됨](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))을 포함하여 공급 업체에서 Azure를 사용할 수 있는 엔지니어링 및 시뮬레이션 솔루션을 실행합니다.
    * Azure에서 호스팅된 서비스로 [Cray 슈퍼 컴퓨터](https://www.cray.com/solutions/supercomputing-as-a-service/cray-in-azure)를 사용합니다.
* **Marketplace 솔루션**
    * [Azure Marketplace](https://azuremarketplace.microsoft.com/)에서 제공되는 [HPC 응용 프로그램](#hpc-applications) 및 [솔루션](#marketplace-solutions)의 규모를 사용합니다. 
    


다음 섹션에서는 지원 기술에 대한 자세한 내용 및 설명서에 대한 링크를 제공합니다.



## <a name="marketplace-solutions"></a>Marketplace 솔루션

HPC용으로 설계된 Linux 및 Windows VM 이미지 및 솔루션은 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)를 방문하세요. 이러한 예로 다음이 포함됩니다.

* [RogueWave CentOS 기반 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
*  [TIBCO Grid Server Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Windows 및 Linux용 Azure 데이터 과학 VM](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)
* [D3View](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/xfinityinc.d3view-v5?tab=Overview)
* [UberCloud](https://azure.microsoft.com/search/marketplace/?q=ubercloud)
* [Lustre용 Intel Cloud Edition](https://azuremarketplace.microsoft.com/marketplace/apps/intel.lustre-cloud-edition-eval?tab=Overview)


 
## <a name="hpc-applications"></a>HPC 응용 프로그램

Azure에서 사용자 지정 또는 상용 HPC 응용 프로그램을 실행합니다. 이 섹션에서 일부 예제는 추가 VM 또는 계산 코어를 통해 효율적으로 확장하도록 벤치마킹됩니다. 솔루션 배포 준비는 [Azure Marketplace](https://marketplace.azure.com)를 방문합니다.

> [!NOTE]
> 클라우드에서 실행하기 위해 라이선스나 기타 제한 사항에 대한 상용 응용 프로그램을 공급 업체에 확인합니다. 일부 공급 업체는 종량제 라이선스를 제공합니다. 솔루션에 대한 라이선스 서버가 클라우드에 필요하거나 온-프레미스 라이선스 서버에 연결할 수 있습니다.

### <a name="engineering-applications"></a>엔지니어링 응용 프로그램


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB 분산 컴퓨팅 서버](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>그래픽 및 렌더링

* Azure Batch의 [Autodesk Maya, 3ds Max 및 Arnold](../articles/batch/batch-rendering-service.md) 

### <a name="ai-and-deep-learning"></a>AI 및 심층 학습

* 심층 학습 모델에 대한 [Batch AI](../articles/batch-ai/overview.md) 학습
* [Microsoft Cognitive 도구 키트](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [심층 학습 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [심층 학습용 Batch Shipyard 레시피](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)






## <a name="hpc-and-gpu-vm-sizes"></a>HPC 및 GPU VM 크기
Azure는 계산 집약적 작업에 맞게 설계된 크기를 포함하여 [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM에 다양한 크기를 제공합니다. 예를 들어 H16r 및 H16mr VM은 높은 처리량 백 엔드 RDMA 네트워크에 연결할 수 있습니다. 이 클라우드 네트워크는 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) 또는 Intel MPI에서 실행되는 긴밀하게 결합된 병렬 응용 프로그램의 성능을 향상시킬 수 있습니다. 

N 시리즈 VM은 AI(인공 지능) 학습 및 시각화를 포함한 계산 집약적 또는 그래픽 집약적 응용 프로그램을 위해 설계된 NVIDIA GPU를 특징으로 합니다. 

자세한 정보:

* [Linux](../articles/virtual-machines/linux/sizes-hpc.md) 및 [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VM에 대한 고성능 계산 크기 
* [Linux](../articles/virtual-machines/linux/sizes-gpu.md) 및 [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VM에 대한 사용 가능한 GPU 크기 

방법 배우기:

* [MPI 응용 프로그램을 실행하도록 Linux RDMA 클러스터 설정](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [MPI 응용 프로그램을 실행하기 위해 Microsoft HPC Pack을 사용하여 Windows RDMA 클러스터 설정](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Batch 풀에서 계산 집약적 VM 사용](../articles/batch/batch-pool-compute-intensive-sizes.md)



## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md)는 클라우드에서 대규모 병렬 및 HPC(고성능 컴퓨팅) 응용 프로그램을 효율적으로 실행하기 위한 플랫폼 서비스입니다. Azure Batch는 가상 머신의 관리되는 풀에서 실행되는 계산 집약적 작업을 예약하고, 작업 요구에 맞게 계산 리소스를 자동으로 크기 조정할 수 있습니다. 

SaaS 공급자 및 개발자는 Batch SDK 및 도구를 사용하여 HPC 응용 프로그램 또는 컨테이너 작업을 Azure에 통합하고, 데이터를 Azure로 스테이징하고, 작업 실행 파이프라인을 빌드할 수 있습니다. 

방법 배우기:

* [Batch를 사용하여 개발 시작](../articles/batch/quick-run-dotnet.md)
* [Azure Batch 샘플 코드 사용(영문)](https://github.com/Azure/azure-batch-samples)
* [Batch에서 낮은 우선 순위 VM 사용](../articles/batch/batch-low-pri-vms.md)
* [Batch Shipyard를 사용하여 컨테이너화된 HPC 작업 실행(영문)](https://github.com/Azure/batch-shipyard)
* [일괄 처리에서 병렬 R 워크로드 실행](https://github.com/Azure/doAzureParallel)
* [일괄 처리에서 주문형 Spark 작업 실행](https://github.com/Azure/aztk)

## <a name="workload-managers"></a>워크로드 관리자

Azure 인프라에서 실행할 수 있는 클러스터 및 워크로드 관리자의 예는 다음과 같습니다. Azure VM에서 독립 실행형 클러스터 또는 온-프레미스 클러스터에서 Azure VM에 대한 버스트를 만듭니다. 
* [Alces 비행 계산](https://azuremarketplace.microsoft.com/marketplace/apps/alces-flight-limited.alces-flight-compute-solo?tab=Overview)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [IBM Spectrum Symphony 및 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [PBS Pro](http://pbspro.org)
* [Microsoft HPC Pack](https://technet.microsoft.com/library/mt744885.aspx) - [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 및 [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM에서 실행할 옵션을 확인합니다. 



## <a name="hpc-storage"></a>HPC 저장소

대규모 Batch 및 HPC 작업에는 기존 클라우드 파일 시스템의 용량을 초과하는 데이터 저장 및 액세스에 대한 요구 사항이 있습니다. Azure에서 [Lustre](http://lustre.org/) 및 [BeeGFS](http://www.beegfs.com/content/)와 같은 병렬 파일 시스템 솔루션을 구현합니다.

자세한 정보:

* [Azure에서 병렬 가상 파일 시스템](https://azure.microsoft.com/resources/parallel-virtual-file-systems-on-microsoft-azure/)
* [Avere](http://www.averesystems.com/about-us/about-avere)(현재 [Microsoft와 연합](https://blogs.microsoft.com/blog/2018/01/03/microsoft-to-acquire-avere-systems-accelerating-high-performance-computing-innovation-for-media-and-entertainment-industry-and-beyond/))의 고성능 클라우드 저장소 솔루션


## <a name="related-azure-services"></a>관련 Azure 서비스

Azure 가상 머신, 가상 머신 확장 집합, Batch 및 관련 계산 서비스는 Azure HPC 솔루션 대부분의 기초가 됩니다. 그러나 이 솔루션에서는 다양한 Azure 관련 서비스를 이용할 수 있습니다. 다음은 목록의 일부입니다.

### <a name="storage"></a>Storage

* [Blob, 테이블 및 큐 저장소](../articles/storage/storage-introduction.md)
* [File Storage](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>데이터 및 분석
* [HDInsight](../articles/hdinsight/hadoop/apache-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Databricks](../articles/azure-databricks/what-is-azure-databricks.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="ai-and-machine-learning"></a>AI 및 기계 학습
* [Machine Learning Services](../articles/machine-learning/service/overview-what-is-azure-ml.md)
* [Batch AI](../articles/batch-ai/overview.md)
* [Genomics](../articles/genomics/overview-what-is-genomics.md)

### <a name="networking"></a>네트워킹
* [Virtual Network](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>컨테이너
* [컨테이너 서비스](../articles/container-service/dcos-swarm/container-service-intro.md)
* [AKS(Azure Kubernetes Service)](../articles/aks/intro-kubernetes.md)
* [컨테이너 레지스트리](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>고객 사례

Azure HPC 솔루션으로 비즈니스 문제를 해결한 고객은 다음과 같습니다.

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [EFS](https://customers.microsoft.com/story/efs-professionalservices-azure)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>다음 단계
* [엔지니어링 시뮬레이션](https://simulation.azure.com/), [렌더링](https://azure.microsoft.com/solutions/big-compute/rendering/), [금융 및 자본 시장](https://finance.azure.com/) 및 [유전체학](https://enterprise.microsoft.com/en-us/industries/health/genomics/)에 대한 빅 컴퓨팅 솔루션에 대해 자세히 알아봅니다.
* 최신 공지 사항은 [Microsoft HPC 및 Batch 팀 블로그](http://blogs.technet.com/b/windowshpc/) 및 [Azure 블로그](https://azure.microsoft.com/blog/tag/hpc/)를 참조하세요.

* 확장성 있는 Azure [Batch](https://azure.microsoft.com/services/batch/) 관리 서비스를 사용하여 기본 인프라를 관리하지 않고 계산 집약적 작업을 실행합니다. [자세한 정보](https://azure.microsoft.com/solutions/architecture/hpc-big-compute-saas/)



