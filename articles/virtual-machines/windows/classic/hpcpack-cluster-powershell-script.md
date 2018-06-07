---
title: Windows HPC 클러스터를 배포하기 위한 PowerShell 스크립트 | Microsoft Docs
description: PowerShell 스크립트를 실행하여 Azure 가상 머신에 Windows HPC Pack 2012 R2 클러스터 배포
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 286b2be8-2533-40df-b02a-26156b1f1133
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: e05562aeac0ea89ec1c3d80d2967c8f59c68d332
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>HPC Pack IaaS 배포 스크립트를 사용하여 Windows HPC(고성능 컴퓨팅) 클러스터 만들기
HPC Pack IaaS 배포 PowerShell 스크립트를 실행하여 Azure 가상 머신에 완전한 Windows 워크로드용 HPC Pack 2012 R2 클러스터를 배포합니다. 클러스터는 Windows Server, Microsoft HPC 팩 및 지정한 추가 Windows 계산 리소스를 실행하는 Active Directory 가입 헤드 노드로 구성됩니다. Azure에서 Linux 워크로드용 HPC Pack 클러스터를 배포하려면 [HPC Pack IaaS 배포 스크립트를 사용하여 Linux HPC 클러스터 만들기](../../linux/classic/hpcpack-cluster-powershell-script.md)를 참조하세요. 

> [!IMPORTANT] 
> 이 문서에 설명된 PowerShell 스크립트는 클래식 배포 모델을 사용하여 Azure에 Microsoft HPC Pack 2012 R2 클러스터를 만듭니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> 또한 이 문서에 설명된 스크립트는 HPC Pack 2016을 지원하지 않습니다. HPC Pack 2012 R2 및 HPC 팩 2016에 대한 Resource Manager 템플릿에 대한 정보는 [Azure에서 HPC Pack 클러스터 배포 옵션](../hpcpack-cluster-options.md)을 참조하세요.

[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>예제 구성 파일
다음 예제에서 구독 이름과 계정 및 서비스 이름을 고유한 값으로 대체합니다.

### <a name="example-1"></a>예 1
다음 구성 파일은 로컬 데이터베이스를 사용하는 1개 헤드 노드와 Windows Server 2012 R2 운영 체제를 실행하는 5개 계산 노드가 있는 HPC 팩 클러스터를 배포합니다. 모든 클라우드 서비스는 미국 서부 위치에서 직접 생성됩니다. 헤드 노드는 도메인 포리스트의 도메인 컨트롤러의 역할을 합니다.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>예 2
다음 구성 파일은 기존 도메인 포리스트에 HPC 팩 클러스터를 배포합니다. 클러스터에는 로컬 데이터베이스가 포함된 1개 헤드와 BGInfo VM 확장이 적용된 12개 계산 노드가 있습니다.
도메인 포리스트의 모든 VM에 대해 Windows 업데이트 자동 설치를 사용하지 않습니다. 모든 클라우드 서비스는 동아시아 위치에서 직접 생성됩니다. 계산 노드는 3개 클라우드 서비스와 3개 저장소 계정에 생성됩니다(*MyHPCCNService01* 및 *mycnstorage01*에서 *MyHPCCN-0001* - *MyHPCCN-0005*, *MyHPCCNService02* 및 *mycnstorage02*에서 *MyHPCCN-0006* - *MyHPCCN0010*, *MyHPCCNService03* 및 *mycnstorage03*에서 *MyHPCCN-0011* - *MyHPCCN-0012*). 계산 노드는 계산 노드에서 캡처된 기존 개인 이미지에서 만들어집니다. 자동 증가 및 축소 서비스를 사용하며 기본 증가 및 축소 간격이 적용됩니다.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>예 3
다음 구성 파일은 기존 도메인 포리스트에 HPC 팩 클러스터를 배포합니다. 클러스터에는 1개 헤드 노드, 500GB의 데이터 디스크가 포함된 1개 데이터베이스 서버, Windows Server 2012 R2 운영 체제를 실행하는 2개 broker 노드, Windows Server 2012 R2 운영 체제를 실행하는 5개 계산 노드가 포함되어 있습니다. 클라우드 서비스인 MyHPCCNService는 선호도 그룹 *MyIBAffinityGroup*에 만들어지며 기타 클라우드 서비스는 선호도 그룹 *MyAffinityGroup*에 만들어집니다. 헤드 노드에서 HPC 작업 Scheduler REST API 및 HPC 웹 포털을 사용합니다.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>예제 4
다음 구성 파일은 기존 도메인 포리스트에 HPC 팩 클러스터를 배포합니다. 클러스터에는 로컬 데이터베이스를 사용하는 2개 헤드 노드가 포함되어 있으며, 2개 Azure 노드 템플릿이 만들어지고, Azure 노드 템플릿 *AzureTemplate1*에 대해 3개 중간 크기 Azure 노드가 만들어집니다. 헤드 노드가 구성된 다음 헤드 노드에 스크립트 파일이 실행됩니다.

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>문제 해결
* **"VNet이 없음" 오류** - 스크립트를 실행하여 하나의 구독으로 Azure에 여러 클러스터를 배포할 경우 하나 이상의 배포가 실패하고 "VNet *VNet\_Name* 없음" 오류가 표시됩니다.
  이 오류가 발생할 경우 실패한 배포에 대해 스크립트를 다시 실행합니다.
* **Azure 가상 네트워크에서 인터넷에 액세스할 수 없는 문제** - 배포 스크립트를 사용하여 새 도메인 컨트롤러로 클러스터를 만들거나 헤드 노드 VM을 도메인 컨트롤러로 수동으로 승격할 경우 VM에서 인터넷으로 연결할 때 문제가 발생할 수 있습니다. 이러한 문제는 전달자 DNS 서버가 도메인 컨트롤러에 자동으로 구성되어 있고 이 전달자 DNS 서버가 올바르게 확인되지 않을 경우 발생할 수 있습니다.
  
    이 문제를 해결하려면 도메인 컨트롤러에 로그인하고 전달자 구성 설정을 제거하거나 유효한 전달자 DNS 서버를 구성합니다. 이 설정을 구성하려면 서버 관리자에서 **도구** >
    **DNS**를 클릭하여 DNS 관리자를 연 다음 **전달자**를 두 번 클릭합니다.
* **계산 집약적 VM에서 RDMA 네트워크에 액세스하지 못하는 문제** - RDMA 지원 크기(예: A8 또는 A9)를 사용하여 Windows Server 계산 노드 또는 broker 노드 VM를 추가할 경우 이러한 VM에서 RDMA 응용 프로그램 네트워크에 연결하지 못하는 문제가 발생할 수 있습니다. 이러한 문제가 발생하는 원인 중 하나는 클러스터에 VM을 추가할 때 HpcVmDrivers 확장이 올바르게 설치되지 않았기 때문일 수 있습니다. 예를 들어 확장이 설치 상태에서 정지할 수 있습니다.
  
    이 문제를 해결하려면 가장 먼저 VM에서 확장 상태를 확인합니다. 확장이 올바르게 설치되지 않은 경우 HPC 클러스터에서 노드를 제거한 다음 노드를 다시 추가해 보십시오. 예를 들어 헤드 노드에서 Add-HpcIaaSNode.ps1 스크립트를 실행하여 계산 노드 VM을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 클러스터에서 테스트 워크로드를 실행합니다. 예를 보려면 HPC 팩 [시작하기 가이드](https://technet.microsoft.com/library/jj884144)를 참조하세요.
* 클러스터 배포를 스크립트로 작성하고 HPC 워크로드를 실행하는 자습서를 보려면 [Azure에서 Excel 및 SOA 워크로드를 실행할 HPC Pack 클러스터 시작하기](../../virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.
* 사용자가 만든 클러스터에서 계산 노드를 시작, 중지, 추가, 제거하는 HPC 팩의 도구. [Azure에서 HPC Pack 클러스터의 계산 노드 관리](hpcpack-cluster-node-manage.md)를 참조하세요.
* 로컬 컴퓨터에서 클러스터에 작업 제출을 시작하려면 [온-프레미스 컴퓨터에서 Azure의 HPC 팩 클러스터로 HPC 작업 제출](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조합니다.

