## <a name="overview"></a>개요
[Azure Marketplace](https://azure.microsoft.com/marketplace/)에서 이미지를 배포하여 리소스 그룹에 새 VM(가상 머신)을 만드는 경우 기본 OS 드라이브는 종종 127GB입니다(일부 이미지의 경우 OS 디스크 크기가 기본적으로 더 작음). VM에 데이터 디스크를 추가할 수 있고(선택한 SKU에 따라 추가할 수 있는 양이 달라짐) 응용 프로그램 및 CPU 사용량이 많은 워크로드는 이러한 추가 디스크에 설치하는 것이 좋지만, 고객이 다음과 같은 특정 시나리오를 지원하기 위해 OS 드라이브를 확장해야 하는 경우가 자주 있습니다.

1. OS 드라이브에 구성 요소를 설치하는 기존 응용 프로그램을 지원하려 합니다.
2. OS 드라이브가 더 큰 온-프레미스로 실제 PC 또는 가상 머신을 마이그레이션하려 합니다.

> [!IMPORTANT]
> Azure는 리소스를 만들고 작업하기 위한 두 가지 배포 모델로 리소스 관리자와 클래식을 제공합니다. 이 문서에서는 리소스 관리자 모델에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.
> 
> 
> [!WARNING]
> Azure Virtual Machine의 OS 디스크 크기를 조정하면 다시 시작됩니다.
>

## <a name="resize-the-os-drive"></a>OS 드라이브 크기 조정
이 문서에서는 [Azure Powershell](/powershell/azureps-cmdlets-docs)의 리소스 관리자 모듈을 사용하여 OS 드라이브의 크기를 조정하겠습니다. 디스크의 크기를 조정하는 방법이 디스크 형식 간에 다르므로 관리되지 않는 디스크와 Managed Disks에 대한 OS 드라이브의 크기를 조정하는 방법을 보여줍니다.

### <a name="for-resizing-unmanaged-disks"></a>관리되지 않는 디스크의 크기를 조정하는 경우:

관리 모드에서 Powershell ISE 또는 Powershell 창을 열고 다음 단계를 수행합니다.

1. 리소스 관리 모드에서 Microsoft Azure 계정에 로그인하고 다음과 같이 구독을 선택합니다.
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. 다음과 같이 리소스 그룹 이름 및 VM 이름을 설정합니다.
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 다음과 같이 VM에 대한 참조를 얻습니다.
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 다음과 같이 VM을 중지한 후 디스크의 크기를 조정합니다.
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 드디어 기다리던 순간이 왔습니다! 다음과 같이 관리되지 않는 OS 디스크의 크기를 원하는 값으로 설정하고 VM을 업데이트합니다.
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > 새 크기가 기존 디스크 크기보다 커야 합니다. OS 디스크에 허용되는 최대 크기는 2,048GB입니다. (VHD Blob은 이 크기 이상으로 확장할 수 있지만, OS가 처음 2,048GB의 공간에서만 작동할 수 있습니다.)
   > 
   > 
6. VM이 업데이트될 때까지 몇 초 정도 걸릴 수 있습니다. 명령 실행이 완료되면 다음과 같이 VM을 다시 시작합니다.
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

### <a name="for-resizing-managed-disks"></a>Managed Disks의 크기를 조정하는 경우:

관리 모드에서 Powershell ISE 또는 Powershell 창을 열고 다음 단계를 수행합니다.

1. 리소스 관리 모드에서 Microsoft Azure 계정에 로그인하고 다음과 같이 구독을 선택합니다.
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. 다음과 같이 리소스 그룹 이름 및 VM 이름을 설정합니다.
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. 다음과 같이 VM에 대한 참조를 얻습니다.
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. 다음과 같이 VM을 중지한 후 디스크의 크기를 조정합니다.
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. 관리되는 OS 디스크에 대한 참조를 가져옵니다. 다음과 같이 관리 OS 디스크의 크기를 원하는 값으로 설정하고 디스크를 업데이트합니다.
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > 새 크기가 기존 디스크 크기보다 커야 합니다. OS 디스크에 허용되는 최대 크기는 2,048GB입니다. (VHD Blob은 이 크기 이상으로 확장할 수 있지만, OS가 처음 2,048GB의 공간에서만 작동할 수 있습니다.)
   > 
   > 
6. VM이 업데이트될 때까지 몇 초 정도 걸릴 수 있습니다. 명령 실행이 완료되면 다음과 같이 VM을 다시 시작합니다.
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

이것으로 끝입니다! 이제 VM에 RDP를 실행하고, 컴퓨터 관리(또는 디스크 관리)를 열고, 새로 할당된 공간을 사용하여 드라이브를 확장합니다.

## <a name="summary"></a>요약
이 문서에서는 Powershell의 Azure Resource Manager 모듈을 사용하여 IaaS 가상 컴퓨터의 OS 드라이브를 확장했습니다. 관리되지 않는 디스크 및 Managed Disks 모두에서 참조하실 수 있도록 아래에 전체 스크립트를 다시 작성해 두었습니다.

관리되지 않는 디스크:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
관리 디스크:

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="next-steps"></a>다음 단계
이 문서에서는 VM의 관리되지 않는/관리 OS 디스크를 확장하는 방법에 초점을 두었지만, 작성한 스크립트에서는 VM에 연결된 데이터 디스크를 확장하는 데 사용할 수 있습니다. 예를 들어 VM에 연결된 첫 번째 데이터 디스크를 확장하려면 아래와 같이 ```StorageProfile```의 ```OSDisk``` 개체를 ```DataDisks``` 배열로 바꾸고 숫자 인덱스를 사용하여 첫 번째 연결된 데이터 디스크의 참조를 가져옵니다.

관리되지 않는 디스크:
```Powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```
Managed Disk:
```Powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

마찬가지로, 위와 같이 인덱스를 사용하여 또는 아래와 같이 디스크의 ```Name``` 속성을 사용하여 VM에 연결된 다른 데이터 디스크를 참조할 수 있습니다.

관리되지 않는 디스크:
```Powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```
Managed Disk:
```Powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

Azure Resource Manager VM에 디스크를 연결하는 방법은 이 [문서](../articles/virtual-machines/windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

