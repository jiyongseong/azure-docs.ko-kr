---
title: Azure Network Watcher 문제 해결로 VPN Gateway 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Automation 및 Network Watcher로 온-프레미스 연결을 진단하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: a102916bb0626f5b110fb134a8a25c902cfaefe7
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Azure Network Watcher 문제 해결로 VPN Gateway 모니터링

고객에게 안정적인 서비스를 제공하기 위해서는 네트워크 성능에 대해 깊은 통찰력을 얻는 것이 중요합니다. 따라서 네트워크 중단 상태를 신속하게 검색하고 수정 작업을 수행하여 중단 조건을 완화하는 것이 중요합니다. Azure Automation을 사용하면 Runbook을 통해 프로그래밍 방식으로 작업을 구현 및 실행할 수 있습니다. Azure Automation을 사용하면 지속적인 자동 관리 네트워크 모니터링 및 경고를 수행하는 완벽한 작성법이 만들어집니다.

## <a name="scenario"></a>시나리오

다음 이미지의 시나리오는 VPN Gateway 및 터널을 사용하여 온-프레미스 연결이 설정된 여러 계층의 응용 프로그램입니다. 응용 프로그램 성능을 위해서는 VPN Gateway가 작동되어 실행 중인지 확인하는 것이 중요합니다.

Runbook은 VPN 터널의 연결 상태를 확인하는 스크립트를 사용하여 생성되며 리소스 문제 해결 API를 사용하여 연결 터널 상태를 확인합니다. 상태가 정상적이지 않은 경우 전자 메일 트리거가 관리자에게 전송됩니다.

![예제 시나리오][scenario]

이 시나리오에서는 다음을 수행합니다.

- `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet을 호출하여 Runbook을 만들고 연결 상태 문제 해결
- Runbook에 일정 연결

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오를 시작하기 전에 다음과 같은 필수 구성 요소가 있어야 합니다.

- Azure에서 Azure Automation 계정. Automation 계정에 최신 모듈 및 AzureRM.Network 모듈이 있는지 확인합니다. AzureRM.Network 모듈을 Automation 계정에 추가해야 하는 경우 모듈 갤러리에서 해당 모듈을 사용할 수 있습니다.
- Azure Automation에 구성된 자격 증명 집합이 있어야 합니다. [Azure Automation 보안](../automation/automation-security-overview.md)에서 자세히 알아보세요.
- Azure Automation에 정의된 유효한 SMTP 서버(Office 365, 온-프레미스 전자 메일 또는 기타) 및 자격 증명
- Azure에 구성된 Virtual Network 게이트웨이입니다.
- 로그를 저장할 기존 컨테이너가 포함된 기존 저장소 계정

> [!NOTE]
> 이전 이미지에 나와 있는 인프라는 설명을 위한 것이며 이 문서에 포함된 단계로는 만들어지지 않습니다.

### <a name="create-the-runbook"></a>Runbook 만들기

예제를 구성하는 첫 번째 단계는 Runbook을 만드는 것입니다. 이 예제에서는 실행 계정을 사용합니다. 실행 계정에 대해 자세히 알아보려면 [Azure 실행 계정으로 Runbook 인증](../automation/automation-create-runas-account.md)을 참조하세요.

### <a name="step-1"></a>1단계

[Azure Portal](https://portal.azure.com)에서 Azure Automation으로 이동하고 **Runbook**을 클릭합니다.

![자동화 계정 개요][1]

### <a name="step-2"></a>2단계

**Runbook 추가**를 클릭하여 Runbook 만들기 과정을 시작합니다.

![Runbook 블레이드][2]

### <a name="step-3"></a>3단계

**빨리 만들기**에서 **새 Runbook 만들기**를 클릭하여 Runbook을 만듭니다.

![Runbook 추가 블레이드][3]

### <a name="step-4"></a>4단계:

이 단계에서는 Runbook에 이름을 부여하는데, 예제에서 이를 **Get-VPNGatewayStatus**라고 합니다. Runbook에 설명이 포함된 이름을 부여하는 것이 중요하며 다음 표준 PowerShell 이름 지정 기준에 따라 이름을 지정하는 것이 좋습니다. 이 예제에 대한 Runbook 형식은 **PowerShell**이고, 다른 옵션은 그래픽, PowerShell 워크플로 및 그래픽 PowerShell 워크플로입니다.

![Runbook 블레이드][4]

### <a name="step-5"></a>5단계

이 단계에서는 Runbook이 생성되고 다음 코드 예제는 예제에 필요한 모든 코드를 제공합니다. \<value\>를 포함하는 코드의 항목은 구독의 값으로 대체해야 합니다.

다음 코드를 사용하고 **저장**을 클릭합니다.

```PowerShell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>6단계

Runbook을 저장했으면 일정을 연결하여 Runbook의 실행을 자동화해야 합니다. 프로세스를 시작하려면 **일정**을 클릭합니다.

![6단계][6]

## <a name="link-a-schedule-to-the-runbook"></a>Runbook에 일정 연결

새 일정이 생성되어야 합니다. **Runbook에 일정 연결**을 클릭합니다.

![7단계][7]

### <a name="step-1"></a>1단계

**일정** 블레이드에서 **새 일정 만들기**를 클릭합니다.

![8단계][8]

### <a name="step-2"></a>2단계

**새 일정** 블레이드에서 일정 정보를 입력합니다. 설정할 수 있는 값은 다음 목록에 있습니다.

- **이름** - 일정의 이름입니다.
- **설명** - 일정에 대한 설명입니다.
- **시작** - 이 값은 일정이 트리거된 시간을 구성하는 날짜, 시간 및 표준 시간대의 조합입니다.
- **되풀이** - 이 값에 따라 일정 되풀이가 결정됩니다.  유효한 값은 **한 번** 또는 **되풀이**입니다.
- **되풀이 간격** - 일정의 되풀이 간격을 시간, 일, 주 또는 달로 나타냅니다.
- **만료 설정** - 이 값은 일정이 만료되는지 여부를 결정합니다. **예** 또는 **아니요**로 설정할 수 있습니다. 예를 선택한 경우 유효한 날짜 및 시간이 제공됩니다.

> [!NOTE]
> 매시간보다 더 자주 Runbook을 실행하도록 해야 하는 경우 서로 다른 간격으로 여러 일정을 만들어야 합니다(즉, 한 시간 후 15, 30, 45분).

![9단계][9]

### <a name="step-3"></a>3단계

저장을 클릭하여 일정을 Runbook에 저장합니다.

![10단계][10]

## <a name="next-steps"></a>다음 단계

이제 Network Watcher 문제 해결을 Azure Automation과 통합하는 방법을 이해하고 [Azure Network Watcher에서 경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 방문하여 VM 경고에서 패킷 캡처를 트리거하는 방법을 알아볼 수 있습니다.

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
