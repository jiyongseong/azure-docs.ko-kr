---
title: Azure PowerShell을 사용하여 Windows VM에서 진단을 사용하도록 설정 | Microsoft Docs
services: virtual-machines-windows
documentationcenter: ''
description: PowerShell을 사용하여 Windows를 실행하는 가상 컴퓨터에서 Azure 진단을 사용하도록 설정하는 방법에 대해 알아봅니다.
author: sbtron
manager: jeconnoc
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 17f4e26b732b27e4c6969ea1182676f8d58bda68
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>PowerShell을 사용하여 Windows를 실행하는 가상 머신에서 Azure 진단을 사용하도록 설정

Azure 진단은 배포된 응용 프로그램에서 진단 데이터를 수집할 수 있도록 하는 Azure 내 기능입니다. 진단 확장을 사용하여 Windows를 실행 중인 Azure 가상 머신(VM)에서 응용 프로그램 로그 또는 성능 카운터 등과 같은 진단 데이터를 수집할 수 있습니다. 이 문서는 Windows PowerShell을 사용하여 VM에 대해 진단 확장을 사용하도록 설정하는 방법을 설명합니다. 이 문서에 필요한 필수 조건은 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview) 을 참조하세요.

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>리소스 관리자 배포 모델을 사용하는 경우 진단 확장을 사용하도록 설정
Azure 리소스 관리자 배포 모델을 통해 Windows VM을 만드는 동안 리소스 관리자 템플릿에 확장 구성을 추가하여 진단 확장을 사용하도록 설정할 수 있습니다. [Azure Resource Manager를 사용하여 Windows 가상 머신과 모니터링 및 진단 기능 만들기](diagnostics-template.md)를 참조하세요.

리소스 관리자 배포 모델을 통해 만든 기존 VM에서 진단 확장을 사용하도록 설정하려면 아래 표시된 [Set-AzureRMVMDiagnosticsExtension](/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension) PowerShell cmdlet을 사용합니다.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path*는 아래 [샘플](#sample-diagnostics-configuration)에 설명된 XML의 진단 구성이 포함된 파일의 경로입니다.  

진단 구성 파일이 저장소 계정 이름으로 **StorageAccount** 요소를 지정할 경우 *Set-AzureRMVMDiagnosticsExtension* 스크립트에서 해당 저장소 계정으로 진단 데이터를 보내도록 진단 확장을 자동으로 설정합니다. 이렇게 작동하려면, 저장소 계정이 VM과 동일한 구독 내에 있어야 합니다.

진단 구성에 **StorageAccount** 가 지정되지 않은 경우 cmdlet에 *StorageAccountName* 매개 변수를 전달해야 합니다. *StorageAccountName* 매개 변수가 지정된 경우 cmdlet은 항상 진단 구성 파일에 지정된 저장소 계정이 아닌 매개 변수에 지정된 저장소 계정을 사용합니다.

진단 저장소 계정이 VM과 다른 구독에 있는 경우 *StorageAccountName* 및 *StorageAccountKey* 매개 변수를 cmdlet에 명시적으로 전달해야 합니다. 진단 저장소 계정이 동일한 구독에 있는 경우 진단 확장을 사용하도록 설정하면 cmdlet이 키 값을 자동으로 쿼리하고 설정할 수 있으므로 *StorageAccountKey* 매개 변수가 필요하지 않습니다. 하지만 진단 저장소 계정이 다른 구독에 있는 경우에는 cmdlet이 자동으로 키를 얻지 못할 수 있으며, 사용자가 *StorageAccountKey* 매개 변수를 통해 키를 명시적으로 지정해야 합니다.  

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

VM에서 진단 확장을 사용하도록 설정하면 [Get-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/get-azurermvmdiagnosticsextension) cmdlet을 사용하여 현재 설정을 가져올 수 있습니다.

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

cmdlet은 *PublicSettings*를 반환하며, 여기에는 진단 구성이 포함됩니다. WadCfg 및 xmlCfg의 두 종류의 구성이 지원됩니다. WadCfg는 JSON 구성이며 xmlCfg는 Base64 인코딩 형식의 XML 구성입니다. XML을 읽으려면 디코딩해야 합니다.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

[Remove-AzureRMVmDiagnosticsExtension](/powershell/module/azurerm.compute/remove-azurermvmdiagnosticsextension) cmdlet을 사용하면 VM에서 진단 확장을 제거할 수 있습니다.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>클래식 배포 모델을 사용하는 경우 진단 확장을 사용하도록 설정
[Set-AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) cmdlet을 사용하면 클래식 배포 모델을 통해 만든 VM에서 진단 확장을 사용하도록 설정할 수 있습니다. 다음 예제에서는 클래식 배포 모델을 통해 새 VM을 만들고 진단 확장을 사용하도록 설정하는 방법을 보여줍니다.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

클래식 배포 모델을 통해 만든 기존 VM에서 진단 확장을 사용하도록 설정하려면, 우선 [Get-AzureVM](/powershell/module/azure/get-azurevm) cmdlet을 사용하여 VM 구성을 가져옵니다. 그런 다음 [Set-AzureVMDiagnosticsExtension](/powershell/module/azure/set-azurevmdiagnosticsextension) cmdlet을 사용하여 진단 확장을 포함하도록 VM 구성을 업데이트합니다. 마지막으로 [Update-AzureVM](/powershell/module/azure/update-azurevm)을 사용하여 VM에 업데이트된 구성을 적용합니다.

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>샘플 진단 구성
위의 스크립트를 통해 진단 공용 구성에 다음과 같은 XML을 사용할 수 있습니다. 이 샘플 구성은 Windows 이벤트 로그의 응용 프로그램, 보안 및 시스템 채널 오류 및 진단 인프라 로그의 오류와 함께 다양한 성능 카운터를 저장소 계정으로 전송합니다.

다음을 포함하도록 구성을 업데이트해야 합니다.

* *Metrics* 요소의 **resourceID** 특성을 VM의 리소스 ID로 업데이트해야 합니다.
  
  * 리소스 ID는 "/subscriptions/{*VM과 관련된 구독의 구독 ID*}/resourceGroups/{*VM의 리소스 그룹 이름*}/providers/Microsoft.Compute/virtualMachines/{*VM 이름*}"과 같은 패턴을 사용하여 생성할 수 있습니다.
  * 예를 들어 VM이 실행 중인 구독의 구독 ID가 **11111111-1111-1111-1111-111111111111**이고, 리소스 그룹의 리소스 그룹 이름이 **MyResourceGroup**이고, VM 이름이 **MyWindowsVM**일 경우 *resourceID* 값은 다음과 같습니다.
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * 성능 카운터와 메트릭 구성을 기반으로 메트릭을 생성하는 방법에 대한 자세한 내용은 [Azure Diagnostics metrics table in storage(저장소의 Azure 진단 메트릭 테이블)](diagnostics-template.md#wadmetrics-tables-in-storage)을 참조하세요.
* **StorageAccount** 요소를 진단 저장소 계정의 이름으로 업데이트해야 합니다.
  
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## <a name="next-steps"></a>다음 단계
* 문제 해결을 위한 Azure 진단 기능 및 기타 기법 사용에 대한 추가 지침은 [Azure Cloud Services 및 Virtual Machines에서 진단 사용](../../cloud-services/cloud-services-dotnet-diagnostics.md)을 참조하세요.
* [진단 구성 스키마](https://msdn.microsoft.com/library/azure/mt634524.aspx) 는 진단 확장에 대한 다양한 XML 구성 옵션을 설명합니다.

