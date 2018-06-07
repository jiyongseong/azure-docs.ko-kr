---
title: "Azure Log Analytics에서 이벤트에 대해 IIS와 Table Storage에 Blob Storage 사용 | Microsoft Docs"
description: "Log Analytics는 Table Storage에 진단을 쓰는 Azure 서비스 또는 Blob Storage에 기록된 IIS 로그에 대해 로그를 읽을 수 있습니다."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459ef90ca1d76bada6565bfefd7b4bd1086197d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Log Analytics에서 이벤트에 대해 IIS와 Azure Table Storage에 Azure Blob Storage 사용

Log Analytics는 Table Storage에 진단을 쓰는 다음 서비스에 대한 로그나 Blob Storage에 기록된 IIS 로그를 읽을 수 있습니다.

* Service Fabric 클러스터(미리 보기)
* 가상 컴퓨터
* 웹/작업자 역할

Log Analytics에서 이러한 리소스에 대한 데이터를 수집하려면 Azure 진단을 사용하도록 설정되어 있어야 합니다.

진단이 사용하도록 설정되어 있으면 Azure Portal 또는 PowerShell을 사용하여 로그를 수집하도록 Log Analytics를 구성할 수 있습니다.

Azure 진단은 Azure에서 실행 중인 작업자 역할, 웹 역할 또는 가상 컴퓨터에서 진단 데이터를 수집하는 데 사용할 수 있는 Azure 확장입니다. 데이터는 Azure Storage 계정에 저장되며 이후 Log Analytics를 통해 수집될 수 있습니다.

Log Analytics가 이러한 Azure Diagnostics 로그를 수집하려면 로그가 다음 위치에 있어야 합니다.

| 로그 형식 | 리소스 종류 | 위치 |
| --- | --- | --- |
| IIS 로그 |가상 컴퓨터 <br> 웹 역할 <br> 작업자 역할 |wad-iis-logfiles(Blob Storage) |
| syslog |가상 컴퓨터 |LinuxsyslogVer2v0(Table Storage) |
| Service Fabric 작업 이벤트 |Service Fabric 노드 |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor 이벤트 |Service Fabric 노드 |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service 이벤트 |Service Fabric 노드 |WADServiceFabricReliableServiceEventTable |
| Windows 이벤트 로그 |Service Fabric 노드 <br> 가상 컴퓨터 <br> 웹 역할 <br> 작업자 역할 |WADWindowsEventLogsTable(Table Storage) |
| Windows ETW 로그 |Service Fabric 노드 <br> 가상 컴퓨터 <br> 웹 역할 <br> 작업자 역할 |WADETWEventTable(Table Storage) |

> [!NOTE]
> Azure 웹사이트에서 IIS 로그는 현재 지원되지 않습니다.
>
>

가상 컴퓨터의 경우 [Log Analytics 에이전트](log-analytics-azure-vm-extension.md)를 가상 컴퓨터에 설치하여 추가 정보를 얻을 수 있습니다. IIS 로그 및 이벤트 로그를 분석할 수 있을 뿐만 아니라 구성 변경 추적, SQL 평가, 업데이트 평가 등 추가 분석을 수행할 수 있습니다.

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>이벤트 로그 및 IIS 로그 컬렉션에 대한 Azure 진단을 가상 컴퓨터에서 사용
다음 절차를 사용하여 Microsoft Azure Portal에서 이벤트 로그 및 IIS 로그를 수집할 수 있도록 가상 컴퓨터에서 Azure 진단을 사용하도록 설정합니다.

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Azure Portal을 통해 가상 컴퓨터에서 Azure 진단을 사용하도록 설정하려면
1. 가상 컴퓨터를 만들 때 VM 에이전트를 설치합니다. 가상 컴퓨터가 이미 있는 경우 VM 에이전트가 이미 설치되어 있는지 확인합니다.

   * Azure Portal에서 가상 컴퓨터로 이동하여 **옵션 구성**, **진단**을 차례로 선택한 다음 **상태**를 **설정**으로 설정합니다.

     완료되면 VM에서 Azure 진단 확장이 설치 및 실행됩니다. 이 확장은 진단 데이터를 수집합니다.
2. 모니터링을 설정하고 기존 VM에 대한 이벤트 로깅을 구성합니다. VM 수준에서 진단을 설정할 수 있습니다. 진단을 사용하도록 설정한 다음 이벤트 로깅을 구성하려면 다음 단계를 수행합니다.

   1. VM을 선택합니다.
   2. **모니터링**을 클릭합니다.
   3. **진단**을 클릭합니다.
   4. **상태**를 **켬**으로 설정합니다.
   5. 수집하려는 각 진단 로그를 선택합니다.
   6. **확인**을 클릭합니다.

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>IIS 로그 및 이벤트 컬렉션에 대한 웹 역할에서 Azure 진단 사용
Azure 진단을 사용하도록 설정하는 일반적인 단계는 [클라우드 서비스에서 진단을 사용하도록 설정하는 방법](../cloud-services/cloud-services-dotnet-diagnostics.md)을 참조하세요. 아래 지침에서는 Log Analytics에서 사용하기 위해 이 정보를 사용자 지정합니다.

Azure 진단을 사용하는 경우:

* IIS 로그는 기본적으로 scheduledTransferPeriod 전송 간격에 전송 하는 로그 데이터와 함께 저장됩니다.
* Windows 이벤트 로그는 기본적으로 전송되지 않습니다.

### <a name="to-enable-diagnostics"></a>진단을 사용하도록 설정하려면
Windows 이벤트 로그를 사용하도록 설정하거나 scheduledTransferPeriod를 변경하려면 [4단계: 진단 구성 파일 만들기 및 확장 설치](../cloud-services/cloud-services-dotnet-diagnostics.md)에서처럼 XML 구성 파일(diagnostics.wadcfg)을 사용하여 Azure Diagnostics를 구성합니다.

다음 예제 구성 파일은 응용 프로그램 및 시스템 로그에서 모든 이벤트 및 IIS 로그를 수집합니다.

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

다음 예에서처럼 ConfigurationSettings이 저장소 계정을 지정하는지 확인합니다.

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** 및 **AccountKey** 값은 Azure Portal 저장소 계정 대시보드의 액세스 키 관리 아래에 있습니다. 연결 문자열에 대한 프로토콜은 **https**여야 합니다.

업데이트된 진단 구성이 클라우드 서비스에 적용되고 Azure Storage에 진단을 기록한 후에는 Log Analytics를 구성할 준비가 완료됩니다.

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Azure Portal을 사용하여 Azure Storage에서 로그 수집
Azure Portal을 사용하여 다음 Azure 서비스에 대한 로그를 수집하도록 Log Analytics를 구성할 수 있습니다.

* 서비스 패브릭 클러스터
* 가상 컴퓨터
* 웹/작업자 역할

Azure Portal에서 Log Analytics 작업 공간으로 이동하여 다음 작업을 수행합니다.

1. *저장소 계정 로그*를 클릭합니다.
2. 작업 *추가*를 클릭합니다.
3. 진단 로그를 포함하는 저장소 계정을 선택합니다.
   * 이 계정은 클래식 저장소 계정 또는 Azure Resource Manager 저장소 계정일 수 있습니다.
4. 로그를 수집할 대상 데이터 형식을 선택합니다.
   * 선택 항목은 IIS 로그, 이벤트, Syslog(Linux), ETW 로그 및 Service Fabric 이벤트입니다.
5. 원본 값은 데이터 형식에 따라 자동으로 채워지며 변경할 수 없습니다.
6. 확인을 클릭하여 구성을 저장합니다.

Log Analytics가 수집할 다른 저장소 계정과 데이터 형식에 대해 2-6단계를 반복합니다.

약 30분 후에 Log Analytics의 저장소 계정에서 데이터를 볼 수 있습니다. 구성이 적용된 후에 저장소에 기록된 데이터만 볼 수 있습니다. Log Analytics는 저장소 계정의 기존 데이터를 읽지 않습니다.

> [!NOTE]
> 포털은 원본이 저장소 계정에 있는지 또는 새 데이터를 쓰는 중인지 확인하지 않습니다.
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>PowerShell을 사용하여 이벤트 로그 및 IIS 로그 컬렉션에 대한 Azure 진단을 가상 컴퓨터에서 사용하도록 설정
[Azure 진단을 인덱싱하도록 Log Analytics 구성](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics) 단계를 사용하여 PowerShell을 통해 Table Storage에 기록된 Azure 진단을 읽을 수 있습니다.

Azure PowerShell을 사용하여 Azure 저장소에 기록된 이벤트를 보다 정확하게 지정할 수 있습니다.
자세한 내용은 [Azure 가상 컴퓨터에서 진단 사용](../virtual-machines-dotnet-diagnostics.md)을 참조하세요.

다음 PowerShell 스크립트를 사용하여 Azure 진단을 사용하도록 설정하고 업데이트할 수 있습니다.
또한 사용자 지정 로깅 구성을 사용하여 이 스크립트를 사용할 수 있습니다.
스크립트를 수정하여 저장소 계정, 서비스 이름 및 가상 컴퓨터 이름을 설정합니다.
스크립트는 클래식 가상 컴퓨터에 대한 cmdlet을 사용합니다.

다음 스크립트 샘플을 검토하고 복사하며 필요에 따라 수정하고, 샘플을 PowerShell 스크립트 파일로 저장한 다음 스크립트를 실행합니다.

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>다음 단계
* 지원되는 Azure 서비스에 대해 [Azure 서비스에 대한 로그 및 메트릭 수집](log-analytics-azure-storage.md)
* [솔루션을 사용하도록 설정](log-analytics-add-solutions.md) 하여 데이터에 대한 정보를 제공합니다.
* [검색 쿼리를 사용](log-analytics-log-searches.md) 하여 데이터를 분석합니다.
