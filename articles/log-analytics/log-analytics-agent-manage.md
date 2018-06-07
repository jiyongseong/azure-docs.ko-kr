---
title: Azure Log Analytics 에이전트 관리 | Microsoft Docs
description: 이 문서에서는 컴퓨터에 배포된 MMA(Microsoft Monitoring Agent)의 수명 주기 동안 일반적으로 수행하는 여러 관리 작업에 대해 설명합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: magoedte
ms.openlocfilehash: 5ff4f79a607143683b37726f1c02a6057dc6b9b0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Windows 및 Linux용 Log Analytics 에이전트 관리 및 유지 관리

Log Analytics용 Windows 또는 Linux 에이전트의 초기 배포 후 에이전트를 다시 구성하거나 수명 주기의 사용 중지 단계에 도달한 경우 컴퓨터에서 제거해야 할 수 있습니다.  이러한 일상적인 유지 관리 작업을 수동으로 또는 자동화를 통해 간단히 관리하여 조작 오류와 비용을 모두 줄일 수 있습니다.

## <a name="adding-or-removing-a-workspace"></a>작업 영역 추가 또는 제거 

### <a name="windows-agent"></a>Windows 에이전트

#### <a name="update-settings-from-control-panel"></a>제어판에서 설정 업데이트

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.
2. **제어판**을 엽니다.
3. **Microsoft Monitoring Agent**를 선택한 후 **Azure Log Analytics(OMS)** 탭을 클릭합니다.
4. 작업 영역을 제거하려면 작업 영역을 선택한 후 **제거**를 클릭합니다. 에이전트에서 보고를 중지할 다른 작업 영역에 대해 이 단계를 반복합니다.
5. 작업 영역을 추가하려면 **추가**를 클릭하고 **Log Analytics 작업 영역 추가** 대화 상자에서 작업 영역 ID 및 작업 영역 키(기본 키)를 붙여넣습니다. 컴퓨터가 Azure Government 클라우드에서 Log Analytics 작업 영역에 보고해야 하는 경우 [Azure Cloud] 드롭다운 목록에서 [Azure 미국 정부]를 선택합니다. 
6. **확인**을 클릭하여 변경 내용을 저장합니다.

#### <a name="remove-a-workspace-using-powershell"></a>PowerShell을 사용하여 작업 영역 제거 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>PowerShell을 사용하여 Azure 상용에서 작업 영역 추가 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>PowerShell을 사용하여 미국 정부를 위한 Azure에서 작업 영역 추가 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>이전에 명령줄 또는 스크립트를 사용하여 에이전트를 설치 또는 구성한 경우 `EnableAzureOperationalInsights`가 `AddCloudWorkspace` 및 `RemoveCloudWorkspace`로 바뀌었습니다.
>

### <a name="linux-agent"></a>Linux 에이전트
다음 단계는 다른 작업 영역으로 등록하거나 해당 구성에서 작업 영역을 제거하려는 경우 Linux 에이전트를 다시 구성하는 방법을 설명합니다.  

1.  작업 영역에 등록되었는지 확인하려면 다음 명령을 실행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    다음 예제와 유사한 결과가 반환됩니다. 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    상태는 에이전트가 실행 중임을 보여주거나 그렇지 않으면 에이전트를 다시 구성하는 다음 단계가 성공적으로 완료되지 않는 것을 보여줍니다.  

2. 작업 영역으로 이미 등록된 경우 다음 명령을 실행하여 등록된 작업 영역을 제거합니다.  그렇지 않으면 등록되지 않은 경우 다음 단계를 진행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. 다른 작업 영역으로 등록하려면 `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 명령을 실행합니다. 
4. 변경 내용이 적용되었는지 확인하려면 명령을 실행합니다.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    다음 예제와 유사한 결과가 반환됩니다. 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

변경 내용 적용을 위해 에이전트 서비스를 다시 시작할 필요가 없습니다.

## <a name="update-proxy-settings"></a>프록시 설정 업데이트 
배포 후 에이전트가 프록시 서버 또는 [OMS 게이트웨이](log-analytics-oms-gateway.md)를 통해 서비스와 통신하도록 구성하려면 다음 방법 중 하나를 사용하여 이 작업을 완료합니다.

### <a name="windows-agent"></a>Windows 에이전트

#### <a name="update-settings-using-control-panel"></a>제어판을 사용하여 설정 업데이트

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.
2. **제어판**을 엽니다.
3. **Microsoft Monitoring Agent**를 선택한 후 **프록시 설정** 탭을 클릭합니다.
4. **프록시 서버 사용**을 클릭하고 프록시 서버 또는 게이트웨이의 URL 및 포트 번호를 제공합니다. 프록시 서버 또는 OMS 게이트웨이에 인증이 필요한 경우 인증할 사용자 이름과 암호를 입력한 다음 **확인**을 클릭합니다. 

#### <a name="update-settings-using-powershell"></a>PowerShell을 사용하여 설정 업데이트 

다음 샘플 PowerShell 코드를 복사하고 자신의 환경 관련 정보로 업데이트한 후 PS1 파일 이름 확장명으로 저장합니다.  Log Analytics 서비스에 직접 연결하는 각 컴퓨터에서 스크립트를 실행합니다.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object.  We need to determine if we
#have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
     Write-Output 'Health Service proxy API not present, will not update settings.'
     return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```  

### <a name="linux-agent"></a>Linux 에이전트
Linux 컴퓨터가 프록시 서버 또는 OMS 게이트웨이를 통해 Log Analytics와 통신해야 하는 경우 다음 단계를 수행합니다.  프록시 구성 값은 다음 구문 `[protocol://][user:password@]proxyhost[:port]`를 갖습니다.  *proxyhost* 속성은 프록시 서버의 정규화된 도메인 이름 또는 IP 주소를 허용합니다.

1. 다음 명령을 실행하여 `/etc/opt/microsoft/omsagent/proxy.conf` 파일을 편집하고 특정 설정으로 값을 변경합니다.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. 다음 명령을 실행하여 에이전트를 다시 시작합니다. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>에이전트 제거
다음 절차 중 하나에 따라 명령줄이나 설치 마법사를 사용하여 Windows 또는 Linux 에이전트를 제거합니다.

### <a name="windows-agent"></a>Windows 에이전트

#### <a name="uninstall-from-control-panel"></a>제어판에서 제거
1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.  
2. **제어판**에서 **프로그램 및 기능**을 클릭합니다.
3. **프로그램 및 기능**에서 **Microsoft Monitoring Agent**를 클릭하고 **제거**를 클릭한 다음 **예**를 클릭합니다.

>[!NOTE]
>Azure Portal의 작업 영역에서 다운로드할 수 있는 **MMASetup-<platform>.exe**를 두 번 클릭하여 에이전트 설치 마법사를 실행할 수도 있습니다.

#### <a name="uninstall-from-the-command-line"></a>명령줄에서 제거
다운로드한 에이전트용 파일은 IExpress로 만든 자체 포함 설치 패키지입니다.  에이전트용 설치 프로그램과 지원 파일이 패키지에 포함되어 있으며 다음 예제에 나온 대로 명령줄을 사용하여 올바로 제거하려면 이러한 프로그램과 파일을 추출해야 합니다. 

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.  
2. 에이전트 설치 파일을 추출하려면 관리자 권한 명령 프롬프트에서 `extract MMASetup-<platform>.exe`를 실행합니다. 그러면 파일을 추출할 경로를 묻은 메시지가 표시됩니다.  `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` 인수를 전달하여 경로를 지정할 수도 있습니다.  IExpress에서 지원하는 명령줄 스위치에 대한 자세한 내용은 [IExpress의 명령줄 스위치](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages)를 참조하고 필요에 맞게 예제를 업데이트하세요.
3. 프롬프트에 `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`를 입력합니다.  

### <a name="linux-agent"></a>Linux 에이전트
에이전트를 제거하려면 Linux 컴퓨터에서 다음 명령을 실행합니다.  *--purge* 인수는 에이전트와 해당 구성을 완전히 제거합니다.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>에이전트에서 Operations Manager 관리 그룹에 보고하도록 구성

### <a name="windows-agent"></a>Windows 에이전트
Windows용 OMS 에이전트에서 System Center Operations Manager 관리 그룹에 보고하도록 구성하려면 다음 단계를 수행합니다. 

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.
2. **제어판**을 엽니다. 
3. **Microsoft Monitoring Agent**를 클릭한 다음 **Operations Manager** 탭을 클릭합니다.
4. Operations Manager 서버가 Active Directory와 통합된 경우 **AD DS에서 관리 그룹 할당 자동 업데이트**를 클릭합니다.
5. **추가**를 클릭하여 **관리 그룹 추가** 대화 상자를 엽니다.
6. **관리 그룹 이름** 필드에 관리 그룹의 이름을 입력합니다.
7. **기본 관리 서버** 필드에 기본 관리 서버의 컴퓨터 이름을 입력합니다.
8. **관리 서버 포트** 필드에 TCP 포트 번호를 입력합니다.
9. **에이전트 작업 계정**에서 로컬 시스템 계정 또는 로컬 도메인 계정을 선택합니다.
10. **확인**을 클릭하여 **관리 그룹 추가** 대화 상자를 닫은 다음 **확인**을 클릭하여 **Microsoft Monitoring Agent 속성** 대화 상자를 닫습니다.

### <a name="linux-agent"></a>Linux 에이전트
OMS Agent for Linux에서 System Center Operations Manager 관리 그룹에 보고하도록 구성하려면 다음 단계를 수행합니다. 

1. 파일 `/etc/opt/omi/conf/omiserver.conf`
2. `httpsport=`로 시작되는 줄이 1270 포트를 지정하도록 합니다. 예: `httpsport=1270`
3. OMI 서버를 다시 시작합니다. `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>다음 단계

에이전트를 설치하거나 관리하는 중에 문제가 발생하면 [Troubleshooting the Linux agent](log-analytics-agent-linux-support.md)(Linux 에이전트 문제 해결)를 참조하세요.  