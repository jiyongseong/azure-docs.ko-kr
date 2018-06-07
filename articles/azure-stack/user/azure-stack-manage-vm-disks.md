---
title: Azure 스택에서 VM 디스크 관리 | Microsoft Docs
description: Azure 스택의 가상 컴퓨터에 대 한 디스크를 프로 비전 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 8e91b4d83aa90a7e744fb8e73cda788dbf8c58ec
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Azure 스택에서 가상 컴퓨터 디스크 저장소를 프로 비전

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

이 문서에서는 Azure 스택 포털을 사용 하 여 또는 PowerShell을 사용 하 여 가상 컴퓨터 디스크 저장소를 프로 비전 하는 방법을 설명 합니다.

## <a name="overview"></a>개요

Azure 스택 사용을 지원 [디스크를 관리 되지 않는](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) 운영 체제 (OS) 및 데이터 디스크 둘 다로 가상 컴퓨터에 있습니다.

만들 관리 되지 않는 디스크를 사용 하려면 한 [저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) 디스크를 저장 하려면. 만들 디스크는 VM 디스크 라고 하 고 저장소 계정에서 컨테이너에 저장 됩니다.

### <a name="best-practice-guidelines"></a>모범 사례 지침

성능을 개선 하 고 전반적인 비용 절감, 별도 컨테이너에서 각 VM 디스크를 배치 하는 것이 좋습니다. 컨테이너 중 운영 체제 디스크 또는 데이터 디스크를 하나만 동시에 포함 해야 합니다. 그러나 (없는 두 가지 유형의 디스크를 모두 동일한 컨테이너에 넣는 않게 됩니다.)

VM에 하나 이상의 데이터 디스크를 추가 하는 경우 이러한 디스크를 저장 하려면 추가 컨테이너 위치로 사용 합니다. 추가 Vm에 대 한 OS 디스크 자신의 컨테이너에도 있어야 합니다.

여러 Vm을 만들 때 각 새 가상 컴퓨터에 대 한 동일한 저장소 계정을 다시 사용할 수 있습니다. 만들 컨테이너에만 고유 해야 합니다.

### <a name="adding-new-disks"></a>새 디스크를 추가합니다.

다음 표에서 포털을 사용 하 고 PowerShell을 사용 하 여 디스크를 추가 하는 방법을 요약 합니다.

| 방법 | 옵션
|-|-|
|[사용자 포털](#use-the-portal-to-add-additional-disks-to-a-vm)|-기존 VM을 새 데이터 디스크를 추가 합니다. 새 디스크는 Azure 스택에 의해 생성 됩니다. </br> </br>-이전에 프로 비전 된 VM에 기존 디스크 (.vhd) 파일을 추가 합니다. 이 위해.vhd를 준비 하 고 Azure 스택에 파일을 업로드 해야 합니다. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -OS 디스크에서 새 VM을 만들고 동시에 해당 VM에 하나 이상의 데이터 디스크를 추가 합니다. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>포털을 사용 하 여 VM에 디스크를 추가 하려면

기본적으로 대부분의 마켓플레이스 항목에 대 한 VM을 만듭니다 포털을 사용할 때만 운영 체제 디스크가 만들어집니다.

VM을 만든 후 포털을 사용할 수 있습니다.
* 새 데이터 디스크를 VM에 연결 합니다.
* 기존 데이터 디스크를 업로드 하 고 VM에 연결 합니다.

각 관리 되지 않는 디스크를 추가 하면 별도 컨테이너에 저장 되어야 합니다.

>[!NOTE]
>Azure에서 만들고 관리 하는 디스크 라고 [관리 디스크](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/managed-disks-overview)합니다.

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>만들고 새 데이터 디스크를 연결 합니다. 포털을 사용 합니다.

1.  포털에서 선택 **가상 컴퓨터**합니다.    
    ![예: VM 대시보드](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  이전에 프로 비전 된 가상 컴퓨터를 선택 합니다.   
    ![예: 대시보드에 VM 선택](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  가상 컴퓨터에 대 한 선택 **디스크** > **새 연결**합니다.       
    ![예: 새 디스크를 vm에 연결](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  에 **새 디스크 연결** 창 선택 **위치**합니다. 기본적으로 위치는 운영 체제 디스크를 보유 하는 동일한 컨테이너에 설정 됩니다.      
    ![예: 디스크 위치를 설정 합니다.](media/azure-stack-manage-vm-disks/disk-location.png)

5.  선택 된 **저장소 계정** 사용 하도록 합니다. 다음으로, 선택는 **컨테이너** 데이터 디스크를 배치 하려는 합니다. **컨테이너** 페이지에서 원하는 경우 새 컨테이너를 만들 수 있습니다. 그런 다음 자체 컨테이너에 새 디스크에 대 한 위치를 변경할 수 있습니다. 별도 컨테이너를 사용 하 여 각 디스크에 대 한 성능을 향상 시킬 수 있는 데이터 디스크의 위치를 배포할 수 있습니다. 선택 **선택** 는 선택 내용을 저장 합니다.     
    ![예: 컨테이너를 선택 합니다.](media/azure-stack-manage-vm-disks/select-container.png)

6.  에 **새 디스크 연결** 페이지를 업데이트는 **이름**, **형식**, **크기**, 및 **호스트 캐싱** 설정 디스크입니다. 다음 선택 **확인** VM에 대 한 새 디스크 구성을 저장할 수 있습니다.  
    ![예: 전체 디스크 연결](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Azure 스택 디스크를 만들고이 가상 컴퓨터에 연결을 새 디스크에서 가상 컴퓨터의 디스크 설정에 나열 됩니다 **데이터 디스크**합니다.   
    ![예: 디스크 보기](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>VM에 기존 데이터 디스크 추가

1.  [.Vhd 파일을 준비](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) VM에 대 한 데이터 디스크로 사용 하기 위해. .Vhd 파일을 첨부 하려면 VM과 함께 사용 하는 저장소 계정에 해당.vhd 파일을 업로드 합니다.

  다른 컨테이너를 사용 하 여 컨테이너 OS 디스크를 포함 하는 보다.vhd 파일을 저장 하도록 계획 합니다.   
  ![예: VHD 파일 업로드](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  .Vhd 파일을 업로드 한 후 VM에 VHD를 연결 준비가 되었습니다. 왼쪽 메뉴에서 선택 **가상 컴퓨터**합니다.  
 ![예: 대시보드에 VM 선택](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  목록에서 가상 컴퓨터를 선택 합니다.    
  ![예: 대시보드에 VM 선택](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  가상 컴퓨터에 대 한 페이지에서 선택 **디스크** > **연결 기존**합니다.   
  ![예: 기존 디스크를 연결 합니다.](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  에 **기존 디스크 연결** 페이지에서 **VHD 파일**합니다. **저장소 계정은** 페이지가 열립니다.    
  ![예: VHD 파일을 선택](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  아래 **저장소 계정은**를 사용 하려면 계정을 선택 하 고 이전에 업로드 한.vhd 파일을 보관 하는 컨테이너를 선택 합니다. .Vhd 파일을 선택한 다음 선택 **선택** 는 선택 내용을 저장 합니다.    
  ![예: 컨테이너를 선택 합니다.](media/azure-stack-manage-vm-disks/select-container2.png)

7.  아래 **기존 디스크 연결**, 선택한 파일 아래에 있는지 **VHD 파일**합니다. 업데이트는 **호스트 캐싱** 는 디스크의 설정을 선택한 후 **확인** VM에 대 한 새 디스크 구성을 저장할 수 있습니다.    
  ![예: VHD 파일을 첨부](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Azure 스택 디스크를 만들고이 가상 컴퓨터에 연결을 새 디스크에서 가상 컴퓨터의 디스크 설정에 나열 됩니다 **데이터 디스크**합니다.   
  ![예: 완료 디스크 연결](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>PowerShell을 사용 하 여 VM에 여러 관리 되지 않는 디스크를 추가 하려면
PowerShell을 사용 하 여 VM을 프로 비전 하 고 새 데이터 디스크를 추가 하거나 기존 연결 **.vhd** 파일을 데이터 디스크로 합니다.

**추가 AzureRmVMDataDisk** cmdlet은 가상 컴퓨터에 데이터 디스크를 추가 합니다. 가상 컴퓨터를 만들거나 기존 가상 컴퓨터에 데이터 디스크를 추가할 수 있습니다 때 데이터 디스크를 추가할 수 있습니다. 지정 된 **VhdUri** 매개 변수를 다른 컨테이너에 디스크를 배포 합니다.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>새 가상 컴퓨터에 데이터 디스크를 추가 합니다.
다음 예에서는 3 개의 데이터 디스크를 사용 하 여 VM을 만들려면 PowerShell 명령을 사용, 각각 서로 다른 컨테이너에 배치 합니다.

첫 번째 명령은 가상 컴퓨터 개체를 만들고 만든 다음이 고 *$VirtualMachine* 변수입니다. 이 명령은 가상 컴퓨터에 이름 및 크기를 할당합니다.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

다음의 세 명령은 위해 세 개의 데이터 디스크의 경로 할당는 *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, 및 *$DataDiskVhdUri03* 변수입니다. 디스크를 다른 컨테이너를 배포 하는 URL에 다른 경로 이름을 정의 합니다.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

에 저장 된 가상 컴퓨터에 데이터 디스크를 추가 하는 최종의 세 명령은 *$VirtualMachine*합니다. 각 명령 이름, 위치 및 디스크의 추가 속성을 지정합니다. 각 디스크의 URI에 저장 된 *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, 및 *$DataDiskVhdUri03*합니다.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

운영 체제 디스크 및 네트워크 구성에 VM을 추가 하려면 다음 PowerShell 명령을 사용 하 고 새 VM을 시작 합니다.
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>기존 가상 컴퓨터에 데이터 디스크를 추가 합니다.
다음 예에서는 기존 VM에 세 개의 데이터 디스크를 추가 하려면 PowerShell 명령을 사용 합니다.
첫 번째 명령은 가상 컴퓨터를 사용 하 여 가상 컴퓨터 v를 가져와 **Get AzureRmVM** cmdlet. 이 명령은 가상 머신을 *$VirtualMachine* 변수에 저장합니다.
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
다음의 세 명령은 $DataDiskVhdUri01, $DataDiskVhdUri02, 및 $DataDiskVhdUri03 변수를 3 개의 데이터 디스크의 경로 할당합니다.  vhduri에 서로 다른 경로 이름을 디스크 배치에 대 한 서로 다른 컨테이너를 나타냅니다.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  에 저장 된 가상 컴퓨터에 데이터 디스크를 추가 하는 다음의 세 명령은 *$VirtualMachine* 변수입니다. 각 명령 이름, 위치 및 디스크의 추가 속성을 지정합니다. 각 디스크의 URI에 저장 된 *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, 및 *$DataDiskVhdUri03*합니다.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  에 저장 된 가상 컴퓨터의 상태를 업데이트 하는 마지막 명령은 *$VirtualMachine* 에-*ResourceGroupName*합니다.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>다음 단계
Azure 스택 가상 컴퓨터에 대 한 자세한 참조 [스택 Azure의에서 가상 컴퓨터에 대 한 고려 사항](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)합니다.
