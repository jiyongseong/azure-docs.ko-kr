---
title: Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드
description: Azure Automation DSC를 통한 관리를 위한 컴퓨터 설정 방법
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4493f9da0de12fbdfffdf0f4da0dd581ac3b589f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195559"
---
# <a name="onboarding-machines-for-management-by-azure-automation-dsc"></a>Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드

## <a name="why-manage-machines-with-azure-automation-dsc"></a>Azure Automation DSC로 컴퓨터를 관리하는 이유

[PowerShell 필요 상태 구성](https://technet.microsoft.com/library/dn249912.aspx)과 마찬가지로 Azure Automation 필요 상태 구성은 클라우드나 온-프레미스 데이터 센터의 DSC 노드(물리적 및 가상 머신)를 위한 간단하지만 강력한 구성 관리 서비스입니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. Azure Automation 관리 계층이 PowerShell 스크립팅에 대한 것이라면 Azure Automation DSC 관리 계층은 DSC에 대한 것입니다. 즉, Azure Automation에서 PowerShell 스크립트 관리를 지원하는 동일한 방법으로 DSC 구성 관리를 지원합니다. Azure Automation DSC를 사용할 경우의 이점에 대한 자세한 내용은 [Azure Automation DSC 개요](automation-dsc-overview.md)를 참조하세요.

Azure Automation DSC를 다양한 컴퓨터의 관리에 사용할 수 있습니다.

* Azure 가상 머신(기본)
* Azure 가상 머신
* AWS(Amazon Web Services) 가상 머신
* 온-프레미스나 Azure/AWS 이외의 클라우드에 있는 실제/가상 Windows 컴퓨터
* 온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

또한 클라우드에서 컴퓨터 구성을 관리할 수 없는 경우 Azure Automation DSC는 보고서 전용 끝점으로 사용될 수 있습니다. 이 옵션을 사용하면 DSC 온-프레미스를 통해 원하는 구성을 설정(푸시)하고 Azure Automation에서 원하는 상태로 노드 준수에서 다양하게 보고하는 세부 정보를 볼 수 있습니다.

> [!NOTE]
> DSC를 사용한 Azure VM 관리는 설치된 가상 머신 DSC 확장이 2.70보다 큰 경우 추가 비용 없이 포함됩니다. 자세한 내용은 [**Automation 가격 페이지**](https://azure.microsoft.com/pricing/details/automation/)를 참조하세요.


다음 섹션에서는 Azure Automation DSC에 대해 각 컴퓨터 형식을 온보드하는 방법을 간략히 설명합니다.

## <a name="azure-virtual-machines-classic"></a>Azure 가상 머신(기본)

Azure Automation DSC를 사용하면 Azure Portal이나 PowerShell을 사용하는 구성 관리를 위해 Azure 가상 머신(기본)를 간편하게 온보드할 수 있습니다. 내부적으로, VM에 대한 관리자의 원격 작업 없이 Azure VM 필요 상태 구성 확장이 VM을 Azure Automation DSC에 등록합니다. Azure VM DSC(Desired State Configuration) 확장은 비동기적으로 실행되므로 진행 상황을 추적하거나 문제를 해결하는 단계는 뒤에 나오는 [**Azure 가상 머신 온보드 문제 해결**](#troubleshooting-azure-virtual-machine-onboarding) 섹션에서 제공됩니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](http://portal.azure.com/)에서 **찾아보기** -> **가상 머신(클래식)** 를 클릭합니다. 온보드할 Windows VM을 선택합니다. 가상 머신의 대시보드 블레이드에서 **모든 설정** -> **확장** -> **추가** -> **Azure Automation DSC** -> **만들기**를 클릭합니다. 사용 사례에 필요한 [PowerShell DSC 로컬 구성 관리자 값](https://msdn.microsoft.com/powershell/dsc/metaconfig4), Automation 계정의 등록 키 및 등록 URL과, 선택적으로 VM에 할당할 노드 구성을 입력합니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)

머신을 등록할 Automation 계정에 대한 등록 URL과 키를 찾으려면 뒤에 나오는 [**등록 보호**](#secure-registration) 섹션을 참조하세요.

### <a name="powershell"></a>PowerShell

```powershell
# log in to both Azure Service Management and Azure Resource Manager
Add-AzureAccount
Connect-AzureRmAccount

# fill in correct values for your VM/Automation account here
$VMName = ""
$ServiceName = ""
$AutomationAccountName = ""
$AutomationAccountResourceGroup = ""

# fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
# NOTE: DSC Node Configuration names are case sensitive in the portal.
$NodeConfigName = ""

# get Azure Automation DSC registration info
$Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
$RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

# use the DSC extension to onboard the VM for management with Azure Automation DSC
$VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName

$PublicConfiguration = ConvertTo-Json -Depth 8 @{
    SasToken = ""
    ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
    ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

# update these PowerShell DSC Local Configuration Manager defaults if they do not match your use case.
# See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
    RegistrationKey = @{
        UserName = 'notused'
        Password = 'PrivateSettingsRef:RegistrationKey'
    }
    RegistrationUrl = $RegistrationInfo.Endpoint
    NodeConfigurationName = $NodeConfigName
    ConfigurationMode = "ApplyAndMonitor"
    ConfigurationModeFrequencyMins = 15
    RefreshFrequencyMins = 30
    RebootNodeIfNeeded = $False
    ActionAfterReboot = "ContinueConfiguration"
    AllowModuleOverwrite = $False
    }
}

$PrivateConfiguration = ConvertTo-Json -Depth 8 @{
    Items = @{
        RegistrationKey = $RegistrationInfo.PrimaryKey
    }
}

$VM = Set-AzureVMExtension `
    -VM $vm `
    -Publisher Microsoft.Powershell `
    -ExtensionName DSC `
    -Version 2.19 `
    -PublicConfiguration $PublicConfiguration `
    -PrivateConfiguration $PrivateConfiguration `
    -ForceUpdate

$VM | Update-AzureVM
```

> [!NOTE]
> DSC 노드 구성 이름은 포털에서 대/소문자를 구분합니다. 대/소문자가 일치하지 않는 경우 노드는 DSC 노드에서 표시되지 않습니다.

## <a name="azure-virtual-machines"></a>Azure 가상 머신

Azure Automation DSC를 사용하면 Azure Portal, Azure Resource Manager 템플릿 또는 PowerShell을 사용하는 구성 관리를 위해 Azure 가상 머신을 간편하게 온보드할 수 있습니다. 내부적으로, VM에 대한 관리자의 원격 작업 없이 Azure VM 필요 상태 구성 확장이 VM을 Azure Automation DSC에 등록합니다. Azure VM DSC(Desired State Configuration) 확장은 비동기적으로 실행되므로 진행 상황을 추적하거나 문제를 해결하는 단계는 뒤에 나오는 [**Azure 가상 머신 온보드 문제 해결**](#troubleshooting-azure-virtual-machine-onboarding) 섹션에서 제공됩니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com/)에서 가상 머신을 온보드할 Azure Automation 계정으로 이동합니다. Automation 계정 대시보드에서 **DSC 노드** -> **+ Azure VM 추가**를 클릭합니다.

등록하려면 Azure 가상 머신을 선택합니다.

컴퓨터에 필요한 상태 확장이 설치된 PowerShell이 없고 전원 상태가 실행 중인 경우 **연결**을 클릭합니다.

**등록**에서 사용 사례에 필요한 [PowerShell DSC 로컬 Configuration Manager 값](https://msdn.microsoft.com/powershell/dsc/metaconfig4)과 선택적으로 VM에 할당할 노드 구성을 입력합니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure 가상 머신은 Azure Resource Manager 템플릿을 통해 Azure Automation DSC에 배포 및 온보드할 수 있습니다. 기존 VM을 Azure Automation DSC에 온보드하는 템플릿 예는 [DSC 확장 및 Azure Automation DSC를 통한 VM 구성](https://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/) 을 참조하세요. 이 템플릿에서 입력으로 가져온 등록 키와 등록 URL을 찾으려면 뒤에 나오는 [**등록 보호**](#secure-registration) 섹션을 참조하세요.

### <a name="powershell"></a>PowerShell

PowerShell을 통해 Azure 포털의 가상 머신을 온보드하는 데 [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) cmdlet을 사용할 수 있습니다.

## <a name="amazon-web-services-aws-virtual-machines"></a>AWS(Amazon Web Services) 가상 머신

AWS DSC 도구 키트를 사용하여 Azure Automation DSC에 의한 구성 관리를 위해 Amazon Web Services 가상 머신을 쉽게 등록할 수 있습니다. 이러한 도구 키트에 대한 자세한 내용은 [여기](https://blogs.msdn.microsoft.com/powershell/2016/04/20/aws-dsc-toolkit/)에서 확인할 수 있습니다.

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws"></a>온-프레미스나 Azure/AWS 이외의 클라우드에 있는 실제/가상 Windows 컴퓨터

온-프레미스 Windows 컴퓨터와 비 Azure 클라우드(예: Amazon Web Services)의 Windows 컴퓨터도 인터넷에 대한 아웃바운드 액세스 권한이 있다면 Azure Automation DSC에 간단한 절차를 통해 온보드할 수 있습니다.

1. 최신 버전의 [WMF 5](http://aka.ms/wmf5latest) 가 Azure Automation DSC에 온보드하려는 컴퓨터에 설치되었는지 확인합니다.
2. 뒤에 나오는 [**DSC 메타 구성 생성**](#generating-dsc-metaconfigurations) 섹션의 지침에 따라 필요한 DSC 메타 구성이 포함된 폴더를 생성합니다.
3. 등록할 컴퓨터에 PowerShell DSC 메타 구성을 원격으로 적용합니다. **이 명령이 실행되는 컴퓨터에는 최신 버전의 [WMF 5](http://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.**

    ```powershell
    Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
    ```

4. PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 2단계의 메타 구성 폴더를 등록할 각 컴퓨터에 복사합니다. 그런 다음 등록할 각 컴퓨터에서 **Set-DscLocalConfigurationManager** 를 로컬로 호출합니다.
5. Azure Portal 또는 cmdlet를 사용하여 Azure Automation 계정에서 등록된 DSC 노드로 온보드할 컴퓨터가 표시되는지 확인합니다.

## <a name="physicalvirtual-linux-machines-on-premises-in-azure-or-in-a-cloud-other-than-azure"></a>온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

온-프레미스 Linux 컴퓨터, Azure의 Linux 컴퓨터 및 비 Azure 클라우드의 Linux 컴퓨터 또한 몇 가지 간단한 절차를 통해 인터넷으로의 아웃 바운드 액세스 권한만큼 Azure Automation DSC에 온보드할 수 있습니다.

1. 최신 버전의 [Linux용 PowerShell DSC(필요한 상태 구성)](https://github.com/Microsoft/PowerShell-DSC-for-Linux)가 Azure Automation DSC에 온보딩하려는 컴퓨터에 설치되어 있는지 확인합니다.
2. [PowerShell DSC 로컬 구성 관리자 기본값](https://msdn.microsoft.com/powershell/dsc/metaconfig4) 이 해당 사용 사례와 일치하는 경우 Azure Automation DSC에서 끌어오고 보고하는 **모든** 컴퓨터를 등록하려 합니다.

   + Azure Automation DSC에 온보드할 각 Linux 컴퓨터에서 PowerShell DSC 로컬 구성 관리자 기본값으로 Register.py를 사용하여 온보드합니다.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   + Automation 계정에 대한 등록 키와 등록 URL을 찾으려면 뒤에 나오는 [**등록 보호**](#secure-registration) 섹션을 참조하세요.

     PowerShell DSC 로컬 구성 관리자 기본값이 해당 사용 사례와 일치**하지****않거나** Azure Automation DSC에 보고하지만 구성 또는 PowerShell 모듈을 끌어오지 않는 컴퓨터를 등록하려면 3-6단계를 수행합니다. 그렇지 않으면 6단계로 직접 이동합니다.

3. 뒤에 나오는 [**DSC 메타 구성 생성**](#generating-dsc-metaconfigurations) 섹션의 지침에 따라 필요한 DSC 메타 구성이 포함된 폴더를 생성합니다.
4. 온보드할 컴퓨터에 PowerShell DSC 메타 구성을 원격으로 적용합니다.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String "<root password>" -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard

    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

이 명령이 실행되는 컴퓨터에는 최신 버전의 [WMF 5](http://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.

1. PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 온보드할 각 Linux 컴퓨터에 대해 5단계의 폴더에서 해당 컴퓨터에 대한 메타 구성을 Linux 컴퓨터에 복사합니다. 그런 다음 Azure Automation DSC에 온보드할 각 Linux 컴퓨터에서 로컬로 `SetDscLocalConfigurationManager.py` 를 호출합니다.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Azure Portal 또는 cmdlet를 사용하여 Azure Automation 계정에서 등록된 DSC 노드로 온보드할 컴퓨터가 표시되는지 확인합니다.

## <a name="generating-dsc-metaconfigurations"></a>DSC 메타 구성 생성

일반적으로 컴퓨터를 Azure Automation DSC에 등록하려면 [DSC 메타 구성](https://msdn.microsoft.com/powershell/dsc/metaconfig)은 적용될 때 생성될 수 있으며 컴퓨터의 DSC 에이전트가 Azure Automation DSC에서 끌어오거나 보고하도록 지시합니다. Azure Automation DSC에 대한 DSC 메타 구성은 PowerShell DSC 구성 또는 Azure Automation PowerShell cmdlet을 사용하여 생성될 수 있습니다.

> [!NOTE]
> DSC 메타 구성은 관리를 위해 Automation 계정에 컴퓨터를 등록하는 데 필요한 암호를 포함합니다. 사용한 후에 만들거나 삭제한 DSC 메타 구성을 제대로 보호해야 합니다.

### <a name="using-a-dsc-configuration"></a>DSC 구성 사용

1. 로컬 환경의 컴퓨터에서 관리자 권한으로 PowerShell ISE를 엽니다. 이 컴퓨터에는 최신 버전의 [WMF 5](http://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.
2. 다음 스크립트를 로컬로 복사합니다. 이 스크립트는 메타 구성을 만들기 위한 PowerShell DSC 구성 및 메타 구성 생성을 시작하는 명령을 포함합니다.

> [!NOTE]
> DSC 노드 구성 이름은 포털에서 대/소문자를 구분합니다. 대/소문자가 일치하지 않는 경우 노드는 DSC 노드에서 표시되지 않습니다.

    ```powershell
    # The DSC configuration that will generate metaconfigurations
    [DscLocalConfigurationManager()]
    Configuration DscMetaConfigs
    {

        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = "ApplyAndMonitor",

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = "ContinueConfiguration",

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq "")
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
        $RefreshMode = "PUSH"
        }
        else
        {
        $RefreshMode = "PULL"
        }

        Node $ComputerName
        {

            Settings
            {
                RefreshFrequencyMins = $RefreshFrequencyMins
                RefreshMode = $RefreshMode
                ConfigurationMode = $ConfigurationMode
                AllowModuleOverwrite = $AllowModuleOverwrite
                RebootNodeIfNeeded = $RebootNodeIfNeeded
                ActionAfterReboot = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationDSC
                {
                    ServerUrl = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationDSC
                {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationDSC
            {
                ServerUrl = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
    }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
    $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
    }

    # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    DscMetaConfigs @Params
    ```

3. 등록할 컴퓨터의 이름 뿐만 아니라 Automation 계정에 대한 등록 키 및 URL을 입력합니다. 모든 다른 매개 변수는 선택 사항입니다. Automation 계정에 대한 등록 키와 등록 URL을 찾으려면 뒤에 나오는 [**등록 보호**](#secure-registration) 섹션을 참조하세요.
4. 컴퓨터가 Azure Automation DSC에 DSC 상태 정보를 보고하지만 구성 또는 PowerShell 모듈을 끌어오지 않도록 하려면 **ReportOnly** 매개 변수를 true로 설정합니다.
5. 스크립트를 실행합니다. 이제 작업 디렉터리에 **DscMetaConfigs**라는 폴더가 있어야 하며 이는 등록할 컴퓨터에 대한 PowerShell DSC 메타 구성을 포함합니다(관리자로).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Azure Automation cmdlet 사용

PowerShell DSC 로컬 구성 관리자 기본값이 해당 사용 사례와 일치하는 경우 Azure Automation DSC에서 끌어오고 보고하는 모든 컴퓨터를 등록하려 합니다. Azure Automation cmdlet은 필요한 DSC 메타 구성을 생성하는 단순화된 방법을 제공합니다.

1. 로컬 환경의 컴퓨터에서 관리자 권한으로 PowerShell 콘솔이나 PowerShell ISE를 엽니다.
2. **Connect-AzureRmAccount**를 사용하여 Azure Resource Manager에 연결
3. 노드를 등록하려는 Automation 계정에서 등록하려 컴퓨터에 대한 PowerShell DSC 메타 구성을 다운로드합니다.

    ```powershell
    # Define the parameters for Get-AzureRmAutomationDscOnboardingMetaconfig using PowerShell Splatting
    $Params = @{

        ResourceGroupName = 'ContosoResources'; # The name of the ARM Resource Group that contains your Azure Automation Account
        AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
        ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
        OutputFolder = "$env:UserProfile\Desktop\";
    }
    # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
    # For more info about splatting, run: Get-Help -Name about_Splatting
    Get-AzureRmAutomationDscOnboardingMetaconfig @Params
    ```
    
4. 이제 ***DscMetaConfigs***라는 폴더가 있어야 하며 이는 등록할 컴퓨터에 대한 PowerShell DSC 메타 구성을 포함합니다(관리자로).
    
    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>등록 보호

DSC 노드에서 PowerShell DSC 끌어오기 또는 보고 서버(Azure 자동화 DSC 포함)를 인증할 수 있도록 하는 WMF 5 DSC 등록 프로토콜을 통해 Azure Automation 계정에 머신을 안전하게 등록할 수 있습니다. 노드는 **등록 URL**에 있는 서버에 등록되며 **등록 키**를 통해 인증됩니다. 등록하는 동안 DSC 노드와 DSC 풀/보고 서버가 이 노드에 대해, 서버 게시-등록에 대한 인증에 사용할 고유 인증서를 협상합니다. 이 프로세스는 노드가 손상되어 악의적 동작을 수행하는 등, 온보드된 노드가 다른 노드를 가장하는 것을 방지합니다.  등록 후 해당 등록 키는 다시 인증에 사용되지 않으며 노드에서 삭제됩니다.

Azure Portal의 **계정 설정** 아래에 있는 **키**에서 DSC 등록 프로토콜에 필요한 정보를 얻을 수 있습니다. Automation 계정의 **Essentials** 패널에서 키 아이콘을 클릭하여 이 블레이드를 엽니다.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

* 등록 URL은 키 관리 블레이드의 URL 필드입니다.
* 등록 키는 키 관리 블레이드에서 주기본 액세스 키 또는 보조 액세스 키입니다. 둘 중 하나를 사용할 수 있습니다.

이전 키를 사용한 추가적인 노드 등록 차단을 위해 Automation 계정의 기본 액세스 키와 보조 액세스 키는 언제든 다시 생성하여 보안을 강화할 수 있습니다( **키 관리** 블레이드에서).

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure 가상 머신 온보드 문제 해결

Azure Automation DSC를 사용하면 구성 관리를 위해 간편하게 Azure Windows VM을 온보드할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation DSC에 등록하는 데 사용됩니다. Azure VM 필요 상태 구성 확장은 비동기적으로 실행되므로 그 진행 상황 추적과 실행 문제 해결이 중요할 수 있습니다.

> [!NOTE]
> Azure VM 필요 상태 구성 확장을 사용하는 Azure Automation DSC에 Azure Windows VM을 온보드하는 모든 방법에서 노드가 Azure Automation에 등록된 것으로 나타나는 데는 최대 1시간이 소요됩니다. 이것은 Azure Automation DSC의 VM을 온보드하기 위해 필요한 Azure VM DSC 확장에 의한 VM의 Windows 관리 프레임워크 5.0의 설치 때문에 꼭 필요한 일입니다.

Azure VM DSC(Desired State Configuration) 확장의 상태를 확인하거나 문제를 해결하려면, Azure Portal에서 등록될 VM으로 이동한 다음, **설정** 아래에서 **확장**을 클릭합니다. 그런 다음, 운영 체제에 따라 **DSC** 또는 **DSCForLinux**를 클릭합니다. 자세한 내용을 보려면 **자세한 상태 보기**를 클릭합니다.

## <a name="certificate-expiration-and-reregistration"></a>인증서 만료 및 다시 등록

컴퓨터를 Azure Automation DSC의 DSC 노드로 등록한 후에 해당 노드를 나중에 등록해야 하는 많은 이유가 있습니다.

* 등록 후에는 1년 후 만료되는 인증에 대해 각 노드가 자동으로 고유의 인증서를 협상합니다. 현재 PowerShell DSC 등록 프로토콜이 만료가 임박한 인증서를 자동으로 갱신할 수 없으므로 1년 기한 후 노드를 다시 등록해야 합니다. 다시 등록하기 전에 각 노드가 Windows Management Framework 5.0 RTM을 실행 중인지 확인합니다. 노드의 인증 인증서가 만료되고 노드가 다시 등록되지 않으면, 노드가 Azure Automation과 통신할 수 없으며 '응답 없음'으로 표시됩니다. 노드 만료 시점으로부터 90일 안이나, 인증서 만료 이후에 재등록을 수행하면 새 인증서를 생성하여 사용하게 됩니다.
* ConfigurationMode와 같은 노드의 초기 등록 중에 설정된 [PowerShell DSC 로컬 구성 관리자 값](https://msdn.microsoft.com/powershell/dsc/metaconfig4)을 변경하려면 현재 DSC 에이전트 값은 다시 등록을 통해 변경될 수 있습니다. 한 가지 예외는 노드에 할당된 노드 구성입니다. 이는 직접 Azure Automation DSC에서 변경될 수 있습니다.

다시 등록은 이 문서에서 설명하는 등록 방법 중 하나를 사용하여 처음에 노드를 등록한 동일한 방법으로 수행될 수 있습니다. 다시 등록하기 전에 Azure Automation DSC에서 노드를 등록 취소할 필요가 없습니다.

## <a name="related-articles"></a>관련 문서

* [Azure Automation DSC 개요](automation-dsc-overview.md)
* [Azure Automation DSC cmdlets](/powershell/module/azurerm.automation/#automation)
* [Azure Automation DSC 가격 책정](https://azure.microsoft.com/pricing/details/automation/)
