---
title: HPC 팩 클러스터의 Linux 계산 VM | Microsoft Docs
description: Azure에서 Linux HPC(고성능 컴퓨팅) 워크로드에 대한 HPC Pack 클러스터를 만들고 사용하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 57ad5d5d2e7e068f47d51408527f1f7553917279
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Azure에서 HPC Pack 클러스터의 Linux 계산 노드 시작
Windows Server를 실행하는 헤드 노드 및 지원되는 Linux 배포를 실행하는 여러 컴퓨터 노드를 포함하는 Azure의 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) 클러스터를 설정합니다. 클러스터의 Windows 헤드 노드와 Linux 노드 간에 데이터를 이동하는 옵션을 탐색합니다. Linux HPC 작업을 클러스터에 제출하는 방법에 대해 알아봅니다.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

다음 다이어그램은 만들고 작업하려는 HPC Pack 클러스터를 개략적으로 보여 줍니다.

![Linux 노드가 포함된 HPC Pack 클러스터][scenario]

Azure에서 Linux HPC 워크로드를 실행하기 위한 다른 옵션을 보려면 [배치 및 고성능 컴퓨팅에 대한 기술 리소스](../../../batch/big-compute-resources.md)를 참조하세요.

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Linux 계산 노드가 포함된 HPC Pack 클러스터 배포
이 문서에서는 Azure에서 Linux 계산 노드와 함께 HPC Pack 클러스터를 배포하기 위한 두 가지 옵션을 보여 줍니다. 두 방법 모두 HPC Pack과 함께 Windows Server의 Marketplace 이미지를 사용하여 헤드 노드를 만듭니다. 

* **Azure Resource Manager 템플릿** - Azure Marketplace의 템플릿 또는 커뮤니티의 빠른 시작 템플릿을 사용하여 Resource Manager 배포 모델에서 클러스터 만들기를 자동화합니다. 예를 들어 Azure Marketplace의 [Linux 워크로드에 대한 HPC Pack 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 템플릿은 Linux HPC 워크로드에 대한 완전한 HPC Pack 클러스터 인프라를 만듭니다.
* **PowerShell 스크립트** - [Microsoft HPC Pack IaaS 배포 스크립트](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)(**New-HpcIaaSCluster.ps1**)를 사용하여 클래식 배포 모델의 완전한 클러스터 배포를 자동화합니다. 이 Azure PowerShell 스크립트는 빠른 배포를 위해 Azure Marketplace의 HPC Pack VM 이미지를 사용하며 Linux 컴퓨터 노드 배포를 위한 포괄적인 구성 매개 변수 집합을 제공합니다.

Azure의 HPC Pack 클러스터 배포 옵션에 대한 자세한 내용은 [Microsoft HPC Pack을 사용하여 Azure에서 HPC(고성능 컴퓨팅) 클러스터를 만들고 관리하는 옵션](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

### <a name="prerequisites"></a>필수 조건
* **Azure 구독** - Azure Global 또는 Azure China 서비스의 구독을 사용할 수 있습니다. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.
* **코어 할당량** - 멀티 코어 VM 크기를 사용하여 여러 클러스터 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료로 온라인 고객 지원 요청을 개설합니다.
* **Linux 배포판** - 현재 HPC Pack은 컴퓨터 노드에 대한 다음 Linux 배포판을 지원합니다. 이러한 사용 가능한 배포판의 Marketplace 버전을 사용하거나 사용자 자체 버전을 제공할 수 있습니다.
  
  * **CentOS 기반**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux** 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12(Premium), SLES 12 SP1, SLES 12 SP1(Premium), SLES 12 for HPC, SLES 12 for HPC(Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > RDMA 지원 VM 크기 중 하나에서 Azure RDMA 네트워크를 사용하려면 Azure Marketplace에서 SUSE Linux Enterprise Server 12 HPC 또는 CentOS 기반 HPC 이미지를 지정합니다. 자세한 내용은 [고성능 계산 VM 크기](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
    > 
    > 

HPC Pack IaaS 배포 스크립트를 사용하여 클러스터를 배포하는 경우 추가 필수 조건은 다음과 같습니다.

* **클라이언트 컴퓨터** - 클러스터 배포 스크립트를 실행할 Windows 기반 클라이언트 컴퓨터가 필요합니다.
* **Azure PowerShell** - [Azure PowerShell(버전 0.8.10 이상)을 설치 및 구성](/powershell/azure/overview) 합니다.
* **HPC 팩 IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `.\New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인할 수 있습니다. 이 문서는 4.4.1 이상 버전의 스크립트를 기반으로 합니다.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>배포 옵션 1. Resource Manager 템플릿 사용
1. Azure Marketplace의 [Linux 워크로드용 HPC Pack 클러스터](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) 템플릿으로 이동하여 **배포**를 클릭합니다.
2. Azure 포털에서 정보를 검토한 다음 **만들기**를 클릭합니다.
   
    ![포털 만들기][portal]
3. **기본 사항** 블레이드에서 클러스터에 대한 이름을 입력합니다(이는 헤드 노드 VM의 이름도 지정하게 됨). 기존 리소스 그룹을 선택하거나 사용할 수 있는 위치에 배포용 그룹을 만들 수 있습니다. 이 위치는 특정 VM 크기 및 기타 Azure 서비스의 가용성에 영향을 줍니다([하위 지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 참조).
4. **헤드 노드 설정** 블레이드에서 첫 번째 배포의 경우 일반적으로 기본 설정을 적용할 수 있습니다. 
   
   > [!NOTE]
   > **사후 구성 스크립트 URL** 은 실행 후 헤드 노드 VM에서 실행할 공개적으로 사용 가능한 Windows PowerShell 스크립트를 지정하는 선택적 설정입니다. 
   > 
   > 
5. **Compute 노드 설정** 블레이드에서 노드용 명명 패턴, 노드의 수와 크기, 배포할 Linux 배포판을 선택합니다.
6. **인프라 설정** 블레이드에서 가상 네트워크 및 Active Directory 도메인의 이름, 도메인 및 VM 관리자 자격 증명, 저장소 계정용 명명 패턴을 입력합니다.
   
   > [!NOTE]
   > HPC Pack에서는 Active Directory 도메인을 사용하여 클러스터 사용자를 인증합니다. 
   > 
   > 
7. 유효성 검사 테스트가 실행되고 사용 약관을 검토한 후에는 **구매**를 클릭합니다.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>배포 옵션 2. IaaS 배포 스크립트 사용
HPC Pack IaaS 배포 스크립트를 사용하여 클러스터를 배포하는 경우 추가 필수 조건은 다음과 같습니다.

* **클라이언트 컴퓨터** - 클러스터 배포 스크립트를 실행할 Windows 기반 클라이언트 컴퓨터가 필요합니다.
* **Azure PowerShell** - [Azure PowerShell(버전 0.8.10 이상)을 설치 및 구성](/powershell/azure/overview) 합니다.
* **HPC 팩 IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `.\New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인할 수 있습니다. 이 문서는 4.4.1 이상 버전의 스크립트를 기반으로 합니다.

**XML 구성 파일**

HPC Pack IaaS 배포 스크립트는 XML 구성 파일을 입력으로 사용하여 HPC 클러스터에 대해 설명합니다. 다음 샘플 구성 파일은 HPC Pack 헤드 노드와 두 가지 크기의 A7 CentOS 7.0 Linux 컴퓨터 노드로 구성된 작은 클러스터를 지정합니다. 

환경 및 원하는 클러스터 구성의 필요에 따라 파일을 수정하고 HPCDemoConfig.xml 등의 이름을 지정하여 저장합니다. 예를 들어 구독 이름 및 고유 저장소 계정 이름과 클라우드 서비스 이름을 제공해야 합니다. 또한 계산 노드에 대해 지원되는 다른 Linux 이미지를 선택할 수도 있습니다. 구성 파일의 요소에 대한 자세한 내용은 스크립트 폴더의 Manual.rtf 파일과 [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**HPC Pack IaaS 배포 스크립트를 실행하려면**

1. 관리자 권한으로 클라이언트 컴퓨터에서 Windows PowerShell을 엽니다.
2. 디렉터리를 스크립트가 설치되는 폴더(이 예제에서는 E:\IaaSClusterScript)로 변경합니다.
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. 다음 명령을 실행하여 HPC Pack 클러스터를 배포합니다. 이 예제에서는 구성 파일이 E:\HPCDemoConfig.xml에 있다고 가정합니다.
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. 앞의 명령에서 **AdminPassword** 가 지정되지 않았으므로 사용자 *MyAdminName*에 대한 암호를 입력하라는 메시지가 표시됩니다.
   
    나. 스크립트에서 구성 파일의 유효성 검사를 시작합니다. 네트워크 연결에 따라 몇 분 정도 걸릴 수 있습니다.
   
    ![유효성 검사][validate]
   
    다. 유효성 검사를 통과하면 스크립트는 만들 클러스터 리소스를 나열합니다. *Y* 를 입력하여 계속합니다.
   
    ![리소스][resources]
   
    d. 스크립트에서 HPC Pack 클러스터 배포를 시작하고 추가 수동 단계 없이 구성을 완료합니다. 이 스크립트는 실행하는 데 몇 분 정도 걸릴 수 있습니다.
   
    ![배포][deploy]
   
   > [!NOTE]
   > 이 예제에서는 **-LogFile** 매개 변수가 지정되지 않았으므로 스크립트에서 자동으로 로그 파일을 생성합니다. 로그는 실시간으로 작성되지 않지만 유효성 검사 및 배포가 끝날 때 수집됩니다. 스크립트가 실행되는 동안 PowerShell 프로세스가 중지되는 경우 일부 로그가 손실됩니다.
   > 
   > 

## <a name="connect-to-the-head-node"></a>헤드 노드에 연결
Azure에서 HPC Pack 클러스터를 배포한 후에 클러스터를 배포할 때 제공한 도메인 자격 증명(예: *hpc\\clusteradmin*)을 사용하여 [원격 데스크톱을 통해 헤드 노드 VM에 연결](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)합니다. 헤드 노드에서 클러스터를 관리합니다.

헤드 노드에서 HPC 클러스터 관리자를 시작하여 HPC Pack 클러스터의 상태를 확인합니다. Windows 계산 노드에서 작업하는 것과 동일한 방식으로 Linux 계산 노드를 관리 및 모니터링할 수 있습니다. 예를 들어 **리소스 관리**에 나열된 Linux 노드가 표시됩니다(이러한 노드는 **LinuxNode** 템플릿으로 배포됨).

![노드 관리][management]

또한 **열 지도** 보기에 Linux 노드가 표시됩니다.

![열 지도][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Linux 노드가 포함된 클러스터에서 데이터를 이동하는 방법
클러스터의 Windows 헤드 노드와 Linux 노드 간에 데이터를 이동하는 여러 가지 옵션이 있습니다. 다음은 세 가지 일반적인 방법으로, 다음 섹션에서 자세히 설명됩니다.

* **Azure 파일** - Azure 저장소에 데이터 파일을 저장할 관리 SMB 파일 공유를 노출합니다. 서로 다른 가상 네트워크에 배포된 경우에도 Windows 노드와 Linux 노드는 Azure 파일 공유를 드라이브 또는 폴더로 동시에 탑재할 수 있습니다.
* **헤드 노드 SMB 공유** - Linux 노드에 헤드 노드의 표준 Windows 공유 폴더를 탑재합니다.
* **헤드 노드 NFS 서버** - Windows 및 Linux 혼합 환경에 대한 파일 공유 솔루션을 제공합니다.

### <a name="azure-file-storage"></a>Azure 파일 저장소
[Azure 파일](https://azure.microsoft.com/services/storage/files/) 서비스는 표준 SMB 2.1 프로토콜을 사용하여 파일 공유를 노출합니다. Azure VM 및 클라우드 서비스는 탑재된 공유를 통해 여러 응용 프로그램 구성 요소에서 파일 데이터를 공유할 수 있으며, 온-프레미스 응용 프로그램은 파일 저장소 API를 통해 공유의 파일 데이터에 액세스할 수 있습니다. 

Azure 파일 공유를 만들고 헤드 노드에 탑재하는 세부 단계는 [Windows에서 Azure 파일 저장소 시작](../../../storage/files/storage-how-to-use-files-windows.md)을 참조하세요. Linux 노드에 Azure 파일 공유를 탑재하려면 [Linux에서 Azure File Storage를 사용하는 방법](../../../storage/files/storage-how-to-use-files-linux.md)을 참조하세요. 영구적 연결을 설정하려면 [Microsoft Azure 파일에 대한 연결 유지](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)(영문)를 참조하세요.

다음 예제에서는 저장소 계정에 Azure 파일 공유를 만듭니다. 헤드 노드에 공유를 탑재하려면 명령 프롬프트를 열고 다음 명령을 입력합니다.

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

이 예제에서 allvhdsje는 저장소 계정 이름이고, storageaccountkey는 저장소 계정 키이고, rdma는 Azure 파일 공유 이름입니다. Azure 파일 공유는 헤드 노드에 Z:로 탑재됩니다.

Linux 노드에 Azure 파일 공유를 탑재하려면 헤드 노드에서 **clusrun** 명령을 실행합니다. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)** 은 여러 노드에서 관리 작업을 수행할 수 있는 유용한 HPC 팩 도구입니다. 이 문서의 [Linux 노드에 대한 Clusrun](#Clusrun-for-Linux-nodes) 도 참조하세요.

Windows PowerShell 창을 열고 다음 명령을 입력합니다.

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /rdma라는 폴더를 만듭니다. 두 번째 명령은 dir 및 파일 모드 비트를 777로 설정하여 Azure 파일 공유 allvhdsjw.file.core.windows.net/rdma를 /rdma 폴더에 탑재합니다. 두 번째 명령에서 allvhdsje는 저장소 계정 이름이고 storageaccountkey는 저장소 계정 키입니다.

> [!NOTE]
> 두 번째 명령의 “\`” 기호는 PowerShell의 이스케이프 기호입니다. “\`,”는 ","(쉼표)가 명령의 일부임을 의미합니다.
> 
> 

### <a name="head-node-share"></a>헤드 노드 공유
또는 Linux 노드에 헤드 노드의 공유 폴더를 탑재합니다. 공유는 파일을 공유하는 가장 간단한 방법을 제공하지만 헤드 노드와 모든 Linux 노드를 동일한 가상 네트워크에 배포해야 합니다. 단계는 다음과 같습니다.

1. 헤드 노드에서 폴더를 만들고 읽기/쓰기 권한을 가진 모든 사용자에게 공유합니다. 예를 들어 헤드 노드의 D:\OpenFOAM을 \\CentOS7RDMA-HN\OpenFOAM으로 공유합니다. 여기서 CentOS7RDMA-HN은 헤드 노드의 호스트 이름입니다.
   
    ![파일 공유 권한][fileshareperms]
   
    ![파일 공유][filesharing]
2. Windows PowerShell 창을 열고 다음 명령을 실행합니다.
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /openfoam이라는 폴더를 만듭니다. 두 번째 명령은 dir 및 파일 모드 비트를 777로 설정하여 공유 폴더 //CentOS7RDMA-HN/OpenFOAM을 폴더에 탑재합니다. 명령의 사용자 이름과 암호는 헤드 노드 클러스터 사용자의 사용자 이름과 암호여야 합니다. ( [클러스터 사용자 추가 또는 제거](https://technet.microsoft.com/library/ff919330.aspx)참조)

> [!NOTE]
> 두 번째 명령의 “\`” 기호는 PowerShell의 이스케이프 기호입니다. “\`,”는 ","(쉼표)가 명령의 일부임을 의미합니다.
> 
> 

### <a name="nfs-server"></a>NFS 서버
NFS 서비스를 사용하면 SMB 프로토콜을 사용하는 Windows Server 2012 운영 체제 실행 컴퓨터와 NFS 프로토콜을 사용하는 Linux 기반 컴퓨터 간에 파일을 공유하고 마이그레이션할 수 있습니다. NFS 서버 및 다른 모든 노드를 동일한 가상 네트워크에 배포해야 합니다. SMB 공유에 비해 Linux 노드와의 호환성이 향상됩니다. 예를 들어 파일 링크를 지원합니다.

1. NFS 서버를 설치 및 설정하려면 [네트워크 파일 시스템 첫 번째 공유 종단 간에 대한 서버](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx)(영문)의 단계를 따르세요.
   
    예를 들어 다음 속성이 설정된 nfs라는 NFS 공유를 만듭니다.
   
    ![NFS 권한 부여][nfsauth]
   
    ![NFS 공유 권한][nfsshare]
   
    ![NFS NTFS 사용 권한][nfsperm]
   
    ![NFS 관리 속성][nfsmanage]
2. Windows PowerShell 창을 열고 다음 명령을 실행합니다.
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   첫 번째 명령은 LinuxNodes 그룹의 모든 노드에 /nfsshared라는 폴더를 만듭니다. 두 번째 명령은 NFS 공유 CentOS7RDMA-HN:/nfs를 폴더에 탑재합니다. 여기서 CentOS7RDMA-HN:/nfs는 NFS 공유의 원격 경로입니다.

## <a name="how-to-submit-jobs"></a>작업을 제출하는 방법
HPC Pack 클러스터에 작업을 제출하는 방법에는 여러 가지가 있습니다.

* HPC 클러스터 관리자 또는 HPC 작업 관리자 GUI
* HPC 웹 포털
* REST API

Azure에서 HPC Pack GUI 도구 및 HPC 웹 포털을 통해 클러스터에 작업을 제출하는 방법은 Windows 컴퓨터 노드의 경우와 동일합니다. [HPC Pack 작업 관리자](https://technet.microsoft.com/library/ff919691.aspx) 및 [온-프레미스 클라이언트 컴퓨터에서 작업을 제출하는 방법](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

REST API를 통해 작업을 제출하려면 [Microsoft HPC Pack의 REST API를 사용하여 작업 만들기 및 제출](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)을 참조하세요. 또한 Linux 클라이언트에서 작업을 제출하려면 [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756)의 Python 샘플을 참조하세요.

## <a name="clusrun-for-linux-nodes"></a>Linux 노드에 대한 Clusrun
HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) 도구를 사용하여 명령 프롬프트 또는 HPC 클러스터 관리자를 통해 Linux 노드에서 명령을 실행할 수 있습니다. 다음은 몇 가지 기본적인 예입니다.

* 클러스터에 있는 모든 노드에 현재 사용자 이름을 표시합니다.
  
    ```command
    clusrun whoami
    ```
* linuxnodes 그룹의 모든 노드에서 **yum**을 사용하여 **gdb** 디버거 도구를 설치하고 10분 후에 노드를 다시 시작합니다.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* 클러스터의 각 Linux 노드에서 1초에 대해 각각 숫자 1에서 10을 표시하는 셸 스크립트를 만들고 실행한 다음 노드에서 즉시 출력을 표시합니다.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> **clusrun** 명령에 특정 이스케이프 문자를 사용해야 할 수도 있습니다. 이 예와 같이 명령 프롬프트의 ^를 사용하여 ">" 기호를 이스케이프합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* 클러스터의 노드 수를 확장하거나 클러스터에서 Linux 워크로드를 실행해 보세요. 예제는 [Azure의 Linux 계산 노드에서 Microsoft HPC 팩을 사용하여 NAMD 실행](hpcpack-cluster-namd.md)을 참조하세요.
* [RDMA 지원, 계산 집약적 VM](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)이 있는 클러스터로 MPI 작업을 실행해 보세요. 그에 대한 예는 [Azure의 Linux RDMA 클러스터에서 Microsoft HPC Pack을 사용하여 OpenFOAM 실행](hpcpack-cluster-openfoam.md)을 참조하세요.
* 온-프레미스 HPC Pack 클러스터의 Linux 노드를 사용한 작업에 관심이 있는 경우 [TechNet 지침](https://technet.microsoft.com/library/mt595803.aspx)을 참조하세요.

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
