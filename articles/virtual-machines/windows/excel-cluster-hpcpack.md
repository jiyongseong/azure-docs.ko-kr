---
title: Excel 및 SOA를 위한 HPC 팩 클러스터 | Microsoft Docs
description: Azure의 HPC Pack 클러스터에서 대규모 Excel 및 SOA 워크로드 실행 시작
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: cb6a9abe-caf3-44da-b911-849a50f6cfb3
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/01/2017
ms.author: danlep
ms.openlocfilehash: aaf26e04fdb38fd76f4ab8211f9fdda8ebafd668
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>Azure의 HPC Pack 클러스터에서 Excel 및 SOA 작업 실행 시작
이 문서에서는 Azure 빠른 시작 템플릿 또는 Azure PowerShell 배포 스크립트(선택 사항)를 사용하여 Azure 가상 머신에 Microsoft HPC Pack 2012 R2 클러스터를 배포하는 방법을 설명합니다. 이 클러스터는 HPC Pack을 사용하여 Microsoft Excel 또는 SOA(서비스 지향 아키텍처) 작업을 실행하도록 설계된 Azure Marketplace VM 이미지를 사용합니다. 클러스터를 사용하여 온-프레미스 클라이언트 컴퓨터에서 Excel HPC 및 SOA 서비스를 실행할 수 있습니다. Excel HPC 서비스에는 Excel 통합 문서 오프로딩 및 Excel 사용자 정의 함수, 즉 UDF가 포함됩니다.

> [!IMPORTANT] 
> 이 문서는 HPC Pack 2012 R2의 기능, 템플릿 및 스크립트를 기준으로 합니다. 이 시나리오는 현재 HPC Pack 2016에서 지원되지 않습니다.
>

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

다음 다이어그램은 만들려는 HPC Pack 클러스터를 개략적으로 보여 줍니다.

![Excel 작업을 실행하는 노드가 있는 HPC 클러스터][scenario]

## <a name="prerequisites"></a>필수 조건
* **클라이언트 컴퓨터** -클러스터에 샘플 Excel 및 SOA 작업을 제출하기 위한 Windows 기반 클라이언트 컴퓨터가 필요합니다. 또한 Azure PowerShell 클러스터 배포 방법을 선택하는 경우 해당 배포 스크립트를 실행하기 위한 Windows 컴퓨터도 필요합니다.
* **Azure 구독** - Azure 구독이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/) 을 만들 수 있습니다.
* **코어 할당량** - 멀티 코어 VM 크기를 사용하여 여러 클러스터 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. Azure 빠른 시작 템플릿을 사용하는 경우 Azure 지역별로 Resource Manager의 코어 할당량이 설정됩니다. 이 경우 특정 지역의 할당량을 늘려야 할 수 있습니다. [Azure 구독 제한, 할당량 및 제약 조건](../../azure-subscription-service-limits.md)을 참조하세요. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 합니다.
* **Microsoft Office 라이선스** - Microsoft Excel과 함께 Marketplace HPC Pack 2012 R2 VM 이미지를 사용하여 컴퓨터 노드를 배포하는 경우 30일 평가 버전의 Microsoft Excel Professional Plus 2013이 설치됩니다. 평가 기간이 종료된 후에 작업을 계속 실행하기 위해 Excel을 정품 인증하려면 유효한 Microsoft Office 라이선스를 제공해야 합니다. 이 문서의 뒷부분에 나오는 [Excel 활성화](#excel-activation) 를 참조하세요. 

## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>1단계. Azure에서 HPC Pack 클러스터 설정
여기서는 HPC Pack 2012 R2 클러스터를 설정하는 두 가지 방법을 설명합니다. 첫 번째 방법은 Azure 빠른 시작 템플릿과 Azure 포털을 사용하는 것이고, 두 번째 방법은 Azure PowerShell 배포 스크립트를 사용하는 것입니다.

### <a name="option-1-use-a-quickstart-template"></a>옵션 1. 빠른 시작 템플릿 사용
Azure 빠른 시작 템플릿을 사용하여 Azure 포털에서 HPC Pack 클러스터를 빠르게 배포합니다. 포털에서 템플릿을 열면 클러스터에 대한 설정을 입력할 수 있는 간단한 UI가 표시됩니다. 단계는 다음과 같습니다. 

> [!TIP]
> 원하는 경우 Excel 작업 전용으로 유사한 클러스터를 만드는 [Azure Marketplace 템플릿](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn) 을 사용합니다. 단계는 다음과 약간 다릅니다.
> 
> 

1. [GitHub의 HPC 클러스터 만들기 템플릿 페이지](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)를 방문합니다. 원하는 경우 템플릿 및 소스 코드에 대한 정보를 검토합니다.
2. Azure에 배포를 클릭하면 Azure 포털의 템플릿을 사용하여 **배포** 를 시작합니다.
   
   ![Azure에 템플릿 배포][github]
3. 포털에서 다음 단계에 따라 HPC 클러스터 템플릿에 대한 매개 변수를 입력합니다.
   
   a. **매개 변수** 페이지에서 템플릿 매개 변수의 값을 입력하거나 수정합니다. 도움말 정보를 보려면 각 설정 옆에 있는 아이콘을 클릭합니다. 다음 화면에는 샘플 값이 표시되어 있습니다. 이 예제에서는 *hpc.local* 도메인에 헤드 노드 1개와 컴퓨터 노드 2개로 구성된 *hpc01*이라는 클러스터를 만듭니다. 컴퓨터 노드는 Microsoft Excel을 포함하는 HPC Pack VM 이미지에서 생성됩니다.
   
   ![매개 변수 입력][parameters-new-portal]
   
   > [!NOTE]
   > 헤드 노드 VM은 Windows Server 2012 R2에서 HPC Pack 2012 R2의 [최신 Marketplace 이미지](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) 로부터 자동으로 생성됩니다. 현재 이미지는 HPC Pack 2012 R2 업데이트 3을 기반으로 합니다.
   > 
   > Compute 노드 VM은 선택한 컴퓨터 노드 제품군의 최신 이미지로부터 생성됩니다. Microsoft Excel Professional Plus 2013 평가 버전을 포함하는 최신 HPC 팩 계산 노드 이미지에 대해 **ComputeNodeWithExcel** 옵션을 선택합니다. 일반 SOA 세션 또는 Excel UDF 오프로딩용 클러스터를 배포하려면 Excel을 설치하지 않고 **ComputeNode** 옵션을 선택합니다.
   > 
   > 
   
   나. 구독을 선택합니다.
   
   다. *hpc01RG*와 같은 클러스터용 리소스 그룹을 만듭니다.
   
   d. 리소스 그룹의 위치(예: 미국 중부)를 선택합니다.
   
   e. **약관** 페이지에서 약관 내용을 검토합니다. 해당 내용에 동의하는 경우 **구매**를 클릭합니다. 그런 다음 템플릿에 대한 값 설정을 마쳤으면 **만들기**를 클릭합니다.
4. 배포가 완료되면(일반적으로 약 30분 소요) 클러스터 헤드 노드에서 클러스터 인증서 파일을 내보냅니다. 이후 단계에서 보안 HTTP 바인딩을 위한 서버 쪽 인증을 제공하기 위해 클라이언트 컴퓨터에서 이 공용 인증서를 가져옵니다.
   
   a. Azure Portal에서 대시보드로 이동하여 헤드 노드를 선택하고, 원격 데스크톱을 사용하여 연결하려면 페이지 위쪽에 있는 **연결**을 클릭합니다.
   
    <!-- ![Connect to the head node][connect] -->
   
   나. 인증서 관리자의 표준 절차에 따라 개인 키 없이 Cert:\LocalMachine\My 아래에 있는 헤드 노드 인증서를 내보냅니다. 이 예제에서는 *CN = hpc01.eastus.cloudapp.azure.com*을 내보냅니다.
   
   ![인증서 내보내기][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>옵션 2. HPC Pack IaaS 배포 스크립트 사용
HPC Pack IaaS 배포 스크립트는 HPC Pack 클러스터를 배포하는 다른 유용한 방법을 제공합니다. 템플릿이 Azure Resource Manager 배포 모델을 사용하는 반면 클래식 배포 모델에서 클러스터를 만듭니다. 또한 스크립트는 Azure Global 또는 Azure China 서비스의 구독과 호환됩니다.

**추가 필수 조건**

* **Azure PowerShell** - [Azure PowerShell(버전 0.8.10 이상)을 설치 및 구성](/powershell/azure/overview) 합니다.
* **HPC Pack IaaS 배포 스크립트** - [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=44949)에서 최신 버전의 스크립트를 다운로드하고 압축을 풉니다. `New-HPCIaaSCluster.ps1 –Version`을 실행하여 스크립트 버전을 확인합니다. 이 문서는 4.5.0 이상 버전의 스크립트를 기반으로 합니다.

**구성 파일 만들기**

 HPC Pack IaaS 배포 스크립트는 HPC 클러스터의 인프라를 설명하는 XML 구성 파일을 입력으로 사용합니다. Microsoft Excel을 포함하는 컴퓨터 노드 이미지에서 만든 컴퓨터 노드 18개와 헤드 노드 1개로 구성된 클러스터를 배포하려면 다음 샘플 구성 파일에 해당 환경의 값을 대체합니다. 구성 파일에 대한 자세한 내용은 스크립트 폴더의 Manual.rtf 파일과 [HPC 팩 IaaS 배포 스크립트를 사용하여 HPC 클러스터 만들기](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 참조하세요.

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**구성 파일에 대한 참고 사항**

* 헤드 노드의 **VMName**은 **ServiceName**과 **같아야** 합니다. 그렇지 않으면 SOA 작업이 실행되지 않습니다.
* 헤드 노드 인증서를 생성하고 내보내도록 **EnableWebPortal** 을 지정하세요.
* 이 파일은 헤드 노드에서 실행되는 구성 후 PowerShell 스크립트 PostConfig.ps1을 지정합니다. 다음 샘플 스크립트는 Azure Storage 연결 문자열을 구성하고, 헤드 노드에서 컴퓨터 노드 역할을 제거하고, 배포된 모든 노드를 온라인으로 설정합니다. 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**스크립트 실행**

1. 관리자 권한으로 클라이언트 컴퓨터에서 PowerShell 콘솔을 엽니다.
2. 디렉터리를 스크립트 폴더(이 예제에서는 E:\IaaSClusterScript)로 변경합니다.
   
   ```
   cd E:\IaaSClusterScript
   ```
3. HPC Pack 클러스터를 배포하려면 다음 명령을 실행합니다. 이 예제에서는 구성 파일이 E:\HPCDemoConfig.xml에 있다고 가정합니다.
   
   ```
   .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
   ```

HPC Pack 배포 스크립트가 일정 시간 동안 실행됩니다. 스크립트에서 수행하는 작업 중 하나는 클러스터 인증서를 다운로드하여 클라이언트 컴퓨터에 있는 현재 사용자의 Documents 폴더에 저장하는 것입니다. 이 스크립트는 다음과 유사한 메시지를 생성합니다. 다음 단계에서는 해당 인증서 저장소의 인증서를 가져옵니다.    

    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>2단계. 온-프레미스 클라이언트에서 Excel 통합 문서 오프로드 및 UDF 실행
### <a name="excel-activation"></a>Excel 활성화
프로덕션 작업에 대해 ComputeNodeWithExcel VM 이미지를 사용하는 경우 컴퓨터 노드에서 Excel을 정품 인증하려면 유효한 Microsoft Office 라이선스 키를 제공해야 합니다. 이렇게 하지 않으면 평가 버전의 Excel은 30일 후에 만료되며 Excel 통합 문서 실행이 실패하고 COMException(0x800AC472)이 발생합니다. 

Excel의 평가 기간은 30일 연장할 수 있습니다. 이렇게 하려면 헤드 노드에 로그온한 다음 HPC 클러스터 관리자를 통해 모든 Excel 컴퓨터 노드에서 `%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe`를 실행합니다. 평가 기간은 2회까지 연장할 수 있습니다. 그 후에는 유효한 Office 라이선스 키를 제공해야 합니다.

VM 이미지에 설치되어 있는 Office Professional Plus 2013은 GVLK(일반 볼륨 라이선스 키)를 사용하는 볼륨 버전입니다. KMS(키 관리 서비스)/AD-BA(Active Directory 기반 정품 인증) 또는 MAK(복수 정품 인증 키)를 통해 이 버전을 정품 인증할 수 있습니다. 

    * KMS/AD-BA를 사용하려면 기존 KMS 서버를 사용하거나 Microsoft Office 2013 볼륨 라이선스 팩을 사용하여 새 서버를 설정합니다. (원하는 경우 헤드 노드에서 서버를 설정합니다.) 그런 다음 인터넷이나 전화를 통해 KMS 호스트 키를 활성화합니다. 그런 다음 clusrun `ospp.vbs`은 KMS 서버 및 포트를 설정하고 Excel 계산 노드의 Office를 활성화합니다. 

    * MAK를 사용하여 첫 번째 clusrun `ospp.vbs`은 키를 입력한 다음 인터넷이나 전화를 통해 모든 Excel 계산 노드를 활성화합니다. 

> [!NOTE]
> Office Professsional Plus 2013용 정품 키를 이 VM 이미지에 사용할 수는 없습니다. 이 Office Professional Plus 2013 볼륨 버전 이외의 Office 또는 Excel 버전용 설치 미디어와 유효한 키가 있는 경우에는 사용하면 됩니다. 이렇게 하려면 먼저 이 볼륨 버전을 제거한 후에 소유 중인 버전을 설치합니다. 다시 설치된 Excel 계산 노드를 대규모로 배포에서 사용할 사용자 지정 VM 이미지로 캡처할 수 있습니다.
> 
> 

### <a name="offload-excel-workbooks"></a>Excel 통합 문서 오프로드
Azure의 HPC Pack 클러스터에서 실행되도록 다음 단계에 따라 Excel 통합 문서를 오프로드합니다. 이렇게 하려면 Excel 2010 또는 2013이 클라이언트 컴퓨터에 이미 설치되어 있어야 합니다.

1. 1단계의 옵션 중 하나를 사용하여 Excel 컴퓨터 노드 이미지로 HPC Pack 클러스터를 배포합니다. 클러스터 인증서 파일(.cer) 및 클러스터 사용자 이름과 암호를 가져옵니다.
2. 클라이언트 컴퓨터에서 Cert:\CurrentUser\Root 아래의 클러스터 인증서를 가져옵니다.
3. Excel이 설치되어 있는지 확인합니다. 클라이언트 컴퓨터에서 Excel.exe와 동일한 폴더에 다음과 같은 내용으로 Excel.exe.config 파일을 만듭니다. 이 단계를 수행하면 HPC Pack 2012 R2 Excel COM 추가 기능이 로드됩니다.
   
    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
4. HPC Pack 클러스터에 작업을 제출하도록 클라이언트를 설정합니다. 이렇게 설정하는 옵션 중 하나는 전체 [HPC Pack 2012 R2 업데이트 3 설치](http://www.microsoft.com/download/details.aspx?id=49922) 파일을 다운로드한 다음 HPC Pack 클라이언트를 설치하는 것입니다. [HPC 팩 2012 R2 업데이트 3 클라이언트 유틸리티](https://www.microsoft.com/download/details.aspx?id=49923) 및 사용 중인 컴퓨터에 적합한 Visual C++ 2010 재배포 가능 패키지([x64](http://www.microsoft.com/download/details.aspx?id=14632), [x86](https://www.microsoft.com/download/details.aspx?id=5555))를 다운로드하여 설치할 수도 있습니다.
5. 이 예제에서는 ConvertiblePricing_Complete.xlsb라는 샘플 Excel 통합 문서를 사용합니다. [여기](https://www.microsoft.com/en-us/download/details.aspx?id=2939)에서 다운로드할 수 있습니다.
6. D:\Excel\Run과 같은 작업 폴더에 Excel 통합 문서를 복사합니다.
7. Excel 통합 문서를 엽니다. **개발** 리본에서 **COM 추가 기능**을 클릭하고 HPC 팩 Excel COM 추가 기능이 로드되었는지 확인합니다.
   
   ![HPC Pack용 Excel 추가 기능][addin]
8. 주석으로 처리된 줄을 다음 스크립트와 같이 변경하여 Excel에서 VBA 매크로 HPCControlMacros를 편집합니다. 사용자 환경에 적절한 값으로 대체합니다.
   
   ![HPC Pack용 Excel 매크로][macro]
   
   ```
   'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
   Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"
   
   'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
   Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.Initialize ActiveWorkbook
   HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles
   
   'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
   HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"
   
   'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
   HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
   ```
9. Excel 통합 문서를 D:\Excel\Upload와 같은 업로드 디렉터리에 복사합니다. 이 디렉터리는 VBA 매크로의 HPC_DependsFiles 상수에 지정되어 있습니다.
10. Azure의 클러스터에서 통합 문서를 실행하려면 워크시트의 **클러스터** 단추를 클릭합니다.

### <a name="run-excel-udfs"></a>Excel UDF 실행
Excel UDF를 실행하려면 앞의 1-3단계에 따라 클라이언트 컴퓨터를 설정합니다. Excel UDF의 경우 컴퓨터 노드에 Excel 응용 프로그램을 설치할 필요가 없습니다. 따라서 클러스터 컴퓨터 노드를 만들 때 Excel이 포함된 컴퓨터 노드 이미지가 아닌 일반 컴퓨터 노드 이미지를 선택합니다.

> [!NOTE]
> Excel 2010 및 2013 클러스터 커넥터 대화 상자에는 34자 제한이 있습니다. 이 대화 상자를 사용하여 UDF를 실행하는 클러스터를 지정합니다. 전체 클러스터 이름이 hpcexcelhn01.southeastasia.cloudapp.azure.com과 같이 제한보다 더 긴 경우에는 대화 상자에서 입력할 수 없습니다. 이러한 문제를 해결하려면 긴 클러스터 이름의 값을 사용해 *CCP_IAASHN*과 같은 컴퓨터 전체 변수를 설정합니다. 그런 다음 대화 상자에 *%CCP_IAASHN%*를 클러스터 헤드 노드 이름으로 입력합니다. 
> 
> 

클러스터가 배포된 후 다음 단계를 계속 진행하여 샘플 기본 제공 Excel UDF를 실행합니다. 사용자 지정 Excel UDF의 경우 다음 [리소스](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) 를 참조하여 XLL을 빌드하고 IaaS 클러스터에 배포하세요.

1. 새 Excel 통합 문서를 엽니다. **개발** 리본 메뉴에서 **추가 기능**을 클릭합니다. 그런 다음 대화 상자에서 **찾아보기**를 클릭하고 %CCP_HOME%Bin\XLL32 폴더로 이동한 다음 샘플 ClusterUDF32.xll을 선택합니다. 클라이언트 컴퓨터에 ClusterUDF32가 없는 경우 헤드 노드의 %CCP_HOME%Bin\XLL32폴더에서 복사합니다.
   
   ![UDF 선택][udf]
2. **파일** > **옵션** > **고급**을 클릭합니다. **수식** 아래에서 **사용자 정의 XLL 함수가 계산 클러스터를 실행할 수 있도록 허용**을 선택합니다. 그런 다음 **옵션**을 클릭하고 **클러스터 헤드 노드 이름**에 전체 클러스터 이름을 입력합니다. 앞에서 언급한 대로 이 입력 상자는 34자로 제한되므로 긴 클러스터 이름은 맞지 않을 수 있습니다. 여기서 긴 클러스터 이름에 대해 컴퓨터 전체 변수를 사용할 수 있습니다.
   
   ![UDF 구성][options]
3. 클러스터에서 UDF 계산을 실행하려면 값이 =XllGetComputerNameC()인 셀을 클릭하고 Enter 키를 누릅니다. 이 함수는 UDF가 실행되는 컴퓨터 노드의 이름만 검색합니다. 처음 실행하는 경우 자격 증명 대화 상자에 IaaS 클러스터에 연결할 사용자 이름 및 암호를 묻는 메시지가 표시됩니다.
   
   ![UDF 실행][run]
   
   계산할 셀이 많은 경우 Alt+Shift+Ctrl+F9를 눌러 모든 셀에서 계산을 실행합니다.

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>3단계. 온-프레미스 클라이언트에서 SOA 작업 실행
HPC Pack IaaS 클러스터에서 일반 SOA 응용 프로그램을 실행하려면 먼저 1단계에서 설명한 방법 중 하나를 사용하여 클러스터를 배포합니다. 컴퓨터 노드에는 Excel이 필요하지 않으므로 이 경우에는 일반 컴퓨터 노드 이미지를 지정합니다. 이어서 다음 단계를 수행합니다.

1. 클러스터 인증서를 검색한 후 클라이언트 컴퓨터의 Cert:\CurrentUser\Root 아래로 가져옵니다.
2. [HPC 팩 2012 R2 업데이트 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921) 및 [HPC 팩 2012 R2 업데이트 3 클라이언트 유틸리티](https://www.microsoft.com/download/details.aspx?id=49923)를 설치합니다. 이러한 도구를 사용하면 SOA 클라이언트 응용 프로그램을 개발하고 실행할 수 있습니다.
3. HelloWorldR2 [샘플 코드](https://www.microsoft.com/download/details.aspx?id=41633)를 다운로드합니다. Visual Studio 2010 또는 2012에서 HelloWorldR2.sln을 엽니다. (이 샘플은 보다 최신 버전의 Visual Studio와는 현재 호환되지 않습니다.)
4. 먼저 EchoService 프로젝트를 빌드합니다. 그런 다음 온-프레미스 클러스터에 배포하는 것과 같은 방식으로 서비스를 IaaS 클러스터에 배포합니다. 자세한 단계는 HelloWordR2의 Readme.doc를 참조하세요. 다음 섹션의 설명에 따라 HelloWorldR2 및 기타 프로젝트를 수정하고 빌드하여 Azure IaaS 클러스터에서 실행되는 SOA 클라이언트 응용 프로그램을 생성합니다.

### <a name="use-http-binding-with-azure-storage-queue"></a>Azure 저장소 큐와 함께 HTTP 바인딩 사용
Azure 저장소 큐와 함께 HTTP 바인딩을 사용하려면 샘플 코드를 일부 변경합니다.

* 클러스터 이름을 업데이트합니다.
  
    ```
  // Before
  const string headnode = "[headnode]";
  // After e.g.
  const string headnode = "hpc01.eastus.cloudapp.azure.com";
  or
  const string headnode = "hpc01.cloudapp.net";
  ```
* 필요에 따라 SessionStartInfo에 기본 TransportScheme을 사용하거나 명시적으로 HTTP로 설정합니다.

```
    info.TransportScheme = TransportScheme.Http;
```

* BrokerClient에 대해 기본 바인딩을 사용합니다.
  
    ```
  // Before
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
  // After
  using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
  ```
  
    또는 basicHttpBinding을 사용하여 명시적으로 설정합니다.
  
    ```
  BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
  binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
  ```
* 필요에 따라 SessionStartInfo에서 UseAzureQueue 플래그를 true로 설정합니다. 설정되지 않은 경우 클러스터 이름에 Azure 도메인 접미사가 있고 TransportScheme이 HTTP인 경우 기본적으로 true로 설정됩니다.
  
    ```
    info.UseAzureQueue = true;
  ```

### <a name="use-http-binding-without-azure-storage-queue"></a>Azure 저장소 큐 없이 HTTP 바인딩 사용
Azure Storage 큐 없이 HTTP 바인딩을 사용하려면 SessionStartInfo에서 명시적으로 UseAzureQueue 플래그를 false로 설정합니다.

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>NetTcp 바인딩 사용
NetTcp 바인딩을 사용하려면 구성이 온-프레미스 클러스터에 연결하는 것과 유사합니다. 헤드 노드 VM에서 몇 개의 끝점을 열어야 합니다. 예를 들어 HPC Pack IaaS 배포 스크립트를 사용하여 클러스터를 만든 경우 다음과 같이 Azure Portal에서 끝점을 설정합니다.

1. VM을 중지합니다.
2. 세션, 브로커, 브로커 작업자 및 데이터 서비스에 대해 각각 TCP 포트 9090, 9087, 9091, 9094를 추가합니다.
   
    ![끝점 구성][endpoint-new-portal]
3. VM을 시작합니다.

헤드 이름을 IaaS 클러스터 전체 이름으로 변경하는 것을 제외하고 SOA 클라이언트 응용 프로그램에 필요한 변경 내용은 없습니다.

## <a name="next-steps"></a>다음 단계
* HPC 팩을 사용하여 Excel 작업을 실행하는 방법에 대한 자세한 내용은 [이러한 리소스](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx) 를 참조하세요.
* HPC 팩을 사용하여 SOA 서비스를 배포 및 관리하는 방법에 대한 자세한 내용은 [Microsoft HPC 팩에서 SOA 서비스 관리](https://technet.microsoft.com/library/ff919412.aspx) 를 참조하세요.

<!--Image references-->
[scenario]: ./media/excel-cluster-hpcpack/scenario.png
[github]: ./media/excel-cluster-hpcpack/github.png
[template]: ./media/excel-cluster-hpcpack/template.png
[parameters]: ./media/excel-cluster-hpcpack/parameters.png
[parameters-new-portal]: ./media/excel-cluster-hpcpack/parameters-new-portal.png
[create]: ./media/excel-cluster-hpcpack/create.png
[connect]: ./media/excel-cluster-hpcpack/connect.png
[cert]: ./media/excel-cluster-hpcpack/cert.png
[addin]: ./media/excel-cluster-hpcpack/addin.png
[macro]: ./media/excel-cluster-hpcpack/macro.png
[options]: ./media/excel-cluster-hpcpack/options.png
[run]: ./media/excel-cluster-hpcpack/run.png
[endpoint]: ./media/excel-cluster-hpcpack/endpoint.png
[endpoint-new-portal]: ./media/excel-cluster-hpcpack/endpoint-new-portal.png
[udf]: ./media/excel-cluster-hpcpack/udf.png
