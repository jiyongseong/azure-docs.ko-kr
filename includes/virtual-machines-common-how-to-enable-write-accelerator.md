---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: msraiye
ms.service: virtual-machines
ms.topic: include
ms.date: 04/30/2018
ms.author: raiye
ms.custom: include file
ms.openlocfilehash: 4fe1f2ad4bad9d670094bbb4eed188baf28108ea
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="write-accelerator"></a>Write Accelerator
Write Accelerator는 Azure Managed Disks를 단독으로 갖춘 Premium Storage의 M 시리즈 VM(가상 머신)용 디스크 기능입니다. 이름에서 알 수 있듯이, 이 기능은 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 향상시키기 위한 것입니다. Write Accelerator는 최신 데이터베이스를 위해 성능이 매우 뛰어난 방식으로 디스크에서 로그 파일 업데이트를 유지해야 하는 경우에 이상적입니다.

Write Accelerator는 일반적으로 공용 클라우드의 M 시리즈 VM에서 사용할 수 있습니다.

## <a name="planning-for-using-write-accelerator"></a>Write Accelerator 사용 계획
Write Accelerator는 DBMS의 트랜잭션 로그 또는 다시 실행 로그가 포함된 볼륨에 사용해야 합니다. 기능이 로그 디스크에 사용할 수 있도록 최적화되어 있으므로 DBMS의 데이터 볼륨에는 Write Accelerator를 사용하지 않는 것이 좋습니다.

Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)와만 함께 작동합니다. 


> [!IMPORTANT]
> 여러 Azure Premium Storage 디스크로 구성되고 Windows 디스크 또는 볼륨 관리자, Windows Storage Spaces, Windows SOFS(스케일 아웃 파일 서버), Linux LVM 또는 MDADM을 사용하여 스트라이프된 기존 볼륨에 Write Accelerator를 사용하거나 사용하지 않도록 설정하려면, 볼륨을 구성하는 모든 디스크는 별도의 단계에서 Write Accelerator에 사용하거나 사용하지 않도록 설정해야 합니다. **먼저 Azure VM을 종료한 후에 이러한 구성에서 Write Accelerator를 사용하거나 사용하지 않도록 설정하세요**. 


> [!IMPORTANT]
> Windows 디스크 또는 볼륨 관리자, Windows Storage Spaces, Windows SOFS(스케일 아웃 파일 서버), Linux LVM 또는 MDADM이 있는 여러 디스크로 구성된 볼륨에 속하지 않은 기존 Azure 디스크에 Write Accelerator를 사용하도록 설정하려면 Azure 디스크에 액세스하는 작업을 종료해야 합니다. 반드시 Azure 디스크를 사용하는 데이터베이스 응용 프로그램을 종료해야 합니다.

> [!IMPORTANT]
> VM의 운영 체제 디스크에 Write Accelerator를 사용하도록 설정하면 해당 VM이 다시 부팅됩니다. 

SAP 관련 VM 구성에는 OS 디스크에 Write Accelerator를 사용하도록 설정할 필요가 없습니다

### <a name="restrictions-when-using-write-accelerator"></a>Write Accelerator 사용 시 제한 사항
Azure 디스크/VHD에 Write Accelerator를 사용하는 경우 적용되는 제한 사항은 다음과 같습니다.

- 프리미엄 디스크 캐싱을 '없음' 또는 '읽기 전용'으로 설정해야 합니다. 다른 모든 캐싱 모드는 지원되지 않습니다.
- Write Accelerator를 사용하도록 설정된 디스크의 스냅숏은 아직 지원되지 않습니다. 이 제한 사항은 가상 머신의 모든 디스크에 대해 응용 프로그램 일치 스냅숏을 수행하는 Azure Backup 서비스 기능을 차단합니다.
- 더 작은 I/O 크기(32KiB 이하)에서만 가속화된 경로를 사용합니다. 데이터가 대량으로 로드되거나 여러 DBMS의 트랜잭션 로그 버퍼가 저장소에 유지되기 전에 더 많이 채워지는 워크로드 상황에서 디스크에 기록된 I/O는 가속화된 경로를 사용하지 않을 수 있습니다.

Write Accelerator에서 지원할 수 있는 VM당 Azure Premium Storage VHD 수는 제한됩니다. 이 제한은 현재 다음과 같습니다.

| VM SKU | Write Accelerator 디스크 수 | VM당 Write Accelerator 디스크 IOPS |
| --- | --- | --- |
| M128ms | 16 | 8000 |
| M128s | 16 | 8000 |
| M64ms | 8 | 4000 |
| M64s | 8 | 4000 | 

## <a name="enabling-write-accelerator-on-a-specific-disk"></a>특정 디스크에 대한 Write Accelerator 사용
다음 몇 가지 섹션에서는 Azure Premium Storage VHD에 Write Accelerator를 사용하도록 설정하는 방법에 대해 설명합니다.


### <a name="prerequisites"></a>필수 조건
이 시점에서 Write Accelerator 사용에 적용되는 필수 조건은 다음과 같습니다.

- Azure Write Accelerator를 적용하려는 디스크는 Premium Storage의 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)여야 합니다.
- M 시리즈 VM을 사용해야 합니다.

### <a name="enabling-through-power-shell"></a>PowerShell을 통해 사용하도록 설정
Azure PowerShell 버전 5.5.0 모듈에는 특정 Azure Premium Storage 디스크에 Write Accelerator를 사용하거나 사용하지 않도록 설정하는 관련 cmdlet에 대한 변경 내용이 포함되어 있습니다.
Write Accelerator에서 지원하는 디스크를 사용하도록 설정하거나 배포하려면, 다음 PowerShell 명령을 변경하고 Write Accelerator에 대한 매개 변수를 허용하도록 확장합니다.

새 스위치 매개 변수인 "WriteAccelerator"가 추가된 cmdlet은 다음과 같습니다. 

- Set-AzureRmVMOsDisk
- Add-AzureRmVMDataDisk
- Set-AzureRmVMDataDisk
- Add-AzureRmVmssDataDisk

매개 변수를 제공하지 않을 경우 해당 속성을 false로 설정하고 Write Accelerator에서 지원하지 않는 디스크를 배포합니다.

새 스위치 매개 변수인 "OsDiskWriteAccelerator"가 추가된 cmdlet은 다음과 같습니다. 

- Set-AzureRmVmssStorageProfile

매개 변수를 제공하지 않을 경우 해당 속성을 false로 설정하고 Write Accelerator를 활용하지 않는 디스크를 제공합니다.

새 선택적 부울(nullable이 아님) 매개 변수인 "OsDiskWriteAccelerator"가 추가된 cmdlet은 다음과 같습니다. 

- Update-AzureRmVM
- Update-AzureRmVmss

Azure Write Accelerator의 디스크 지원을 제어하려면 $true 또는 $false를 지정합니다.

명령 예제는 다음과 같습니다.

```

New-AzureRmVMConfig | Set-AzureRmVMOsDisk | Add-AzureRmVMDataDisk -Name "datadisk1" | Add-AzureRmVMDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVM

Get-AzureRmVM | Update-AzureRmVM -OsDiskWriteAccelerator $true

New-AzureRmVmssConfig | Set-AzureRmVmssStorageProfile -OsDiskWriteAccelerator | Add-AzureRmVmssDataDisk -Name "datadisk1" -WriteAccelerator:$false | Add-AzureRmVmssDataDisk -Name "logdisk1" -WriteAccelerator | New-AzureRmVmss

Get-AzureRmVmss | Update-AzureRmVmss -OsDiskWriteAccelerator:$false 

```

다음 섹션과 같이 두 가지 주요 시나리오를 스크립팅할 수 있습니다.

#### <a name="adding-a-new-disk-supported-by-write-accelerator"></a>Write Accelerator에서 지원하는 새 디스크 추가
다음 스크립트를 사용하여 VM에 새 디스크를 추가할 수 있습니다. 이 스크립트로 만든 디스크는 Write Accelerator를 사용합니다.

```

# Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "log001"
#LUN Id
$lunid=8
#size
$size=1023
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Add-AzureRmVMDataDisk -CreateOption empty -DiskSizeInGB $size -Name $vmname-$datadiskname -VM $vm -Caching None -WriteAccelerator:$true -lun $lunid
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```
특정 배포에 대해 VM, 디스크, 리소스 그룹, 디스크 크기 및 디스크의 LunID에 대한 이름을 조정해야 합니다.


#### <a name="enabling-azure-write-accelerator-on-an-existing-azure-disk"></a>기존 Azure 디스크에 Azure Write Accelerator를 사용하도록 설정
기존 디스크에 Write Accelerator를 사용하도록 설정해야 하는 경우 다음 스크립트를 사용하여 작업을 수행할 수 있습니다.

```

#Specify your VM Name
$vmName="myVM"
#Specify your Resource Group
$rgName = "myWAVMs"
#data disk name
$datadiskname = "test-log001" 
#new Write Accelerator status ($true for enabled, $false for disabled) 
$newstatus = $true
#Pulls the VM info for later
$vm=Get-AzurermVM -ResourceGroupName $rgname -Name $vmname
#add a new VM data disk
Set-AzureRmVMDataDisk -VM $vm -Name $datadiskname -Caching None -WriteAccelerator:$newstatus
#Updates the VM with the disk config - does not require a reboot
Update-AzureRmVM -ResourceGroupName $rgname -VM $vm

```

VM, 디스크 및 리소스 그룹에 대한 이름을 조정해야 합니다. 위의 스크립트에서는 $newstatus 값이 '$true'로 설정된 기존 디스크에 Write Accelerator를 추가합니다. '$false' 값을 사용하면 지정된 디스크에서 Write Accelerator를 사용하지 않도록 설정됩니다.

> [!Note]
> 위의 스크립트가 실행되면 지정된 디스크를 분리하고, 디스크에 대해 Write Accelerator를 사용하도록 설정한 다음, 해당 디스크를 다시 연결합니다

### <a name="enabling-through-azure-portal"></a>Azure Portal을 통해 사용하도록 설정

디스크 캐싱 설정을 지정하는 포털을 통해 Write Accelerator를 사용하도록 설정할 수 있습니다. 

![Azure Portal의 Write Accelerator](./media/virtual-machines-common-how-to-enable-write-accelerator/wa_scrnsht.png)


### <a name="enabling-through-rest-apis"></a>Rest API를 통해 사용하도록 설정
Azure Rest API를 통해 배포하려면 Azure armclient를 설치해야 합니다.

#### <a name="install-armclient"></a>armclient 설치

armclient를 실행하려면 Chocolatey를 통해 설치해야 합니다. cmd.exe 또는 powershell을 통해 설치할 수 있습니다. 이러한 명령에는 대해 관리자 권한을 사용합니다("관리자 권한으로 실행").

cmd.exe를 사용하여 다음 명령을 실행합니다.

```
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```

PowerShell을 사용하는 경우 다음을 사용해야 합니다.

```
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

이제 cmd.exe 또는 PowerShell에서 다음 명령으로 armclient를 설치할 수 있습니다.

```
choco install armclient
```

#### <a name="getting-your-current-vm-configuration"></a>현재 VM 구성 가져오기
디스크 구성의 특성을 변경하려면 먼저 JSON 파일에서 현재 구성을 가져와야 합니다. 다음 명령을 실행하여 현재 구성을 가져올 수 있습니다.

```
armclient GET /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 > <<filename.json>>
```
'<<   >>' 내의 용어를 사용자의 데이터(JSON 파일에 있어야 하는 파일 이름 포함)로 바꿉니다.

출력은 다음과 같습니다.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

다음 단계는 JSON 파일을 업데이트하고 'log1'이라는 디스크에서 Write Accelerator를 사용하도록 설정하는 것입니다. 이 단계는 디스크의 캐시 항목 뒤에 있는 JSON 파일에 이 특성을 추가하여 수행할 수 있습니다. 

```
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
```

그런 다음, 다음 명령으로 기존 배포를 업데이트합니다.

```
armclient PUT /subscriptions/<<subscription-ID<</resourceGroups/<<ResourceGroup>>/providers/Microsoft.Compute/virtualMachines/<<virtualmachinename>>?api-version=2017-12-01 @<<filename.json>>

```

출력은 다음과 같습니다. 한 디스크에서 Write Accelerator를 사용하도록 설정되어 있음을 확인할 수 있습니다.

```
{
  "properties": {
    "vmId": "2444c93e-f8bb-4a20-af2d-1658d9dbbbcb",
    "hardwareProfile": {
      "vmSize": "Standard_M64s"
    },
    "storageProfile": {
      "imageReference": {
        "publisher": "SUSE",
        "offer": "SLES-SAP",
        "sku": "12-SP3",
        "version": "latest"
      },
      "osDisk": {
        "osType": "Linux",
        "name": "mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a",
        "createOption": "FromImage",
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS",
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/mylittlesap_OsDisk_1_754a1b8bb390468e9b4c429b81cc5f5a"
        },
        "diskSizeGB": 30
      },
      "dataDisks": [
        {
          "lun": 0,
          "name": "data1",
          "createOption": "Attach",
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data1"
          },
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "name": "log1",
          "createOption": "Attach",
          "caching": "None",
          **"writeAcceleratorEnabled": true,**
          "managedDisk": {
            "storageAccountType": "Premium_LRS",
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/disks/data2"
          },
          "diskSizeGB": 1023
        }
      ]
    },
    "osProfile": {
      "computerName": "mylittlesapVM",
      "adminUsername": "pl",
      "linuxConfiguration": {
        "disablePasswordAuthentication": false
      },
      "secrets": []
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Network/networkInterfaces/mylittlesap518"
        }
      ]
    },
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": true,
        "storageUri": "https://mylittlesapdiag895.blob.core.windows.net/"
      }
    },
    "provisioningState": "Succeeded"
  },
  "type": "Microsoft.Compute/virtualMachines",
  "location": "westeurope",
  "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/mylittlesap/providers/Microsoft.Compute/virtualMachines/mylittlesapVM",
  "name": "mylittlesapVM"

```

변경 시점부터 해당 드라이브가 Write Accelerator에서 지원됩니다.

 
