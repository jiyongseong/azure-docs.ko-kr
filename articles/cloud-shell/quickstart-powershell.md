---
title: Azure Cloud Shell의 PowerShell(미리 보기) 빠른 시작 | Microsoft Docs
description: Azure Cloud Shell의 PowerShell에 대한 빠른 시작
services: Azure
documentationcenter: ''
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: damaerte
ms.openlocfilehash: e3e59395b7066169b8a7863f45a446051b830a71
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159275"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell-preview"></a>Azure Cloud Shell의 PowerShell에 대한 빠른 시작(미리 보기)

이 문서는 [Azure Portal](https://aka.ms/PSCloudPreview)에서 Azure Cloud Shell의 PowerShell을 사용하는 방법을 자세히 설명합니다.

> [!NOTE]
> [Azure Cloud Shell의 Bash](quickstart.md) 빠른 시작도 사용할 수 있습니다.

## <a name="start-cloud-shell"></a>Cloud Shell 시작

1. Azure Portal의 위쪽 탐색 모음에서 **Azure Cloud Shell** 버튼을 클릭합니다.

  ![](media/quickstart-powershell/shell-icon.png)

2. 드롭다운에서 PowerShell 환경을 선택하면 Azure 드라이브 `(Azure:)`에 들어가게 됩니다.

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell 명령 실행

Azure Cloud Shell에서 다음과 같은 일반 PowerShell 명령을 실행합니다.

```PowerShell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Azure 리소스 이동

 1. 구독 나열

    ``` PowerShell
    PS Azure:\> dir
    ```

 2. 기본 구독에 `cd`

    ``` PowerShell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. 현재 구독에서 모든 Azure 리소스 보기
 
    Azure 리소스 뷰를 여러 개 나열하려면 `dir`을 입력합니다.
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>AllResources 보기 
Azure 리소스를 보려면 `AllResources` 디렉터리 아래에 `dir`을 입력 합니다.
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>리소스 그룹 탐색

 ph x="1" /> 디렉터리로 가서 특정 리소스 그룹 안에서 가상 컴퓨터를 찾을 수 있습니다.

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> `dir`을 두 번째 입력할 때, 클라우드 셸이 훨씬 더 빠르게 항목을 표시할 수 있음을 알 수 있습니다.
> 이는 자식 항목이 향상된 사용자 환경을 위해 메모리에 캐시되기 때문입니다.
그렇지만, 새로운 데이터를 가져오기 위해 언제나 `dir -Force`을 사용할 수 있습니다.

### <a name="navigate-storage-resources"></a>저장소 리소스 이동
    
`StorageAccounts` 디렉터리에 들어가면 저장소 리소스를 쉽게 탐색할 수 있습니다.
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

연결 문자열과 함께 다음 명령을 사용하여 Azure Files 공유를 탑재할 수 있습니다.
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

자세한 내용은 [Azure Files 공유를 탑재하고 Windows에서 공유에 액세스][azmount]를 참조하세요.

또한 Azure Files 공유에서 디렉터리를 다음과 같이 탐색할 수 있습니다.

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>가상 머신과 상호 작용

ph x="1" /> 디렉터리를 통해 현재 구독에서 모든 가상 머신을 찾을 수 있습니다.
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell 스크립트를 원격 VM에 걸쳐 호출

 > [!WARNING]
 > [Azure VM의 원격 관리 문제 해결](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)을 참조하세요.

  MyVM1이란 VM이 있다고 가정하고 `Invoke-AzureRmVMCommand`를 사용하여 원격 컴퓨터에 PowerShell 스크립트 블록을 호출합니다.

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  또한 먼저 VirtualMachines 디렉터리로 이동하고 다음과 같이 `Invoke-AzureRmVMCommand`를 실행할 수 있습니다.

  ``` PowerShell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  그러면 다음과 같은 출력이 표시됩니다.

  ``` PowerShell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>원격 VM에 대화형으로 로그온

`Enter-AzureRmVM`을 사용하여 Azure에서 실행하는 VM에 대화형으로 로그인할 수 있습니다.

  ``` PowerShell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

또는 먼저 `VirtualMachines` 디렉터리로 이동하고 다음과 같이 `Enter-AzureRmVM`를 실행할 수 있습니다.

  ``` PowerShell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>WebApps 검색

`WebApps` 디렉터리에 들어가면 웹앱 리소스를 쉽게 탐색할 수 있습니다.

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="ssh"></a>SSH

[Win32-OpenSSH](https://github.com/PowerShell/Win32-OpenSSH)는 PowerShell Cloud Shell에서 사용할 수 있습니다.
SSH를 사용하여 서버 또는 VM을 인증하려면 Cloud Shell에서 공개-개인 키 쌍을 생성하고 공개 키를 원격 컴퓨터의 `authorized_keys`(예: `/home/user/.ssh/authorized_keys`)에 게시합니다.

> [!NOTE]
> Cloud Shell에서 `ssh-keygen`을 사용하여 SSH 개인-공개 키를 만들고 이 키를 `$env:USERPROFILE\.ssh`에 게시할 수 있습니다.

### <a name="using-a-custom-profile-to-persist-git-and-ssh-settings"></a>사용자 지정 프로필을 사용하여 GIT 및 SSH 설정 유지

로그아웃 시 세션이 유지되지 않으므로 `$env:USERPROFILE\.ssh` 디렉터리를 `clouddrive`에 저장하거나 Cloud Shell을 시작할 때 symlink를 만듭니다.
다음 코드 조각을 profile.ps1에 추가하여 `clouddrive`에 symlink를 만듭니다.

``` PowerShell
# Check if the .ssh directory exists
if( -not (Test-Path $home\clouddrive\.ssh)){
    mkdir $home\clouddrive\.ssh
}

# .ssh path relative to this script
$script:sshFolderPath = Join-Path $PSScriptRoot .ssh

# Create a symlink to .ssh in user's $home
if(Test-Path $script:sshFolderPath){
   if(-not (Test-Path (Join-Path $HOME .ssh ))){
        New-Item -ItemType SymbolicLink -Path $HOME -Name .ssh -Value $script:sshFolderPath
   }
}

```

### <a name="using-ssh"></a>SSH 사용

[여기](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell)의 지침에 따라 AzureRM cmdlet을 사용하여 새 VM 구성을 만듭니다.
`New-AzureRmVM`을 호출하여 배포를 시작하기 전에 SSH 공개 키를 VM 구성에 추가합니다.
새로 만든 VM에는 공개 키가 `~\.ssh\authorized_keys` 위치에 포함되므로 자격 증명이 없는 SSH 세션을 VM에 사용할 수 있습니다.

``` PowerShell

# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
Add-AzureRmVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzureRmVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com

```


## <a name="list-available-commands"></a>사용할 수 있는 명령 목록

`Azure` 드라이브에서 상황별 Azure 명령을 가져오려면 `Get-AzureRmCommand`를 입력합니다.

또는 사용 가능한 Azure 명령을 알아보려면 언제나 `Get-Command *azurerm* -Module AzureRM.*`을 사용할 수 있습니다.

## <a name="install-custom-modules"></a>사용자 지정 모듈 설치

`Install-Module`을 실행하면 [PowerShell 갤러리][gallery]에서 모듈을 설치할 수 있습니다.

## <a name="get-help"></a>Get-Help

Azure Cloud Shell에서 PowerShell에 대한 정보를 가져오려면 `Get-Help`을 입력합니다.

``` PowerShell
PS Azure:\> Get-Help
```

특정 명령에 대해 여전히 `Get-Help`를 실행한 후 뒤 이어 cmdlet을 실행할 수 있습니다.

``` PowerShell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure Files를 사용하여 데이터 저장

예를 들어 `helloworld.ps1`이란 스크립트를 만들어 `clouddrive`에 저장하고 셸 세션 전반에 걸쳐 사용할 수 있습니다.

``` PowerShell
cd C:\users\ContainerAdministrator\clouddrive
PS C:\users\ContainerAdministrator\clouddrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\clouddrive> .\helloworld.ps1
Hello World!
```

다음에 Azure Cloud Shell의 PowerShell을 사용할 때 `helloworld.ps1` 파일은 Azure Files 공유를 탑재한 `clouddrive` 디렉터리에 존재할 것입니다.

## <a name="use-custom-profile"></a>사용자 지정 프로필 사용

PowerShell 프로필 `profile.ps1` 또는 `Microsoft.PowerShell_profile.ps1`를 만들어 PowerShell 환경을 사용자 지정할 수 있습니다. 그것을 `clouddrive`에 저장하여 Cloud Shell을 시작할 때 모든 PowerShell 세션에 로드될 수 있게 합니다.

프로필을 만드는 방법에 대해서는 [프로필 소개][profile]를 참조합니다.

## <a name="use-git"></a>Git 사용

Cloud Shell에서 Git 리포지토리를 복제하려면 [개인용 액세스 토큰][githubtoken]을 만들고 그것을 사용자 이름으로 사용합니다. 토큰이 있다면 다음과 같이 리포지토리를 복제합니다.

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
로그아웃하거나 세션 시간이 초과될 때 Cloud Shell에서 세션이 유지되지 않기 때문에, Git config 파일은 다음 로그온 시 존재하지 않습니다. Git config를 유지하려면 gitconfig를 `clouddrive`에 저장하고 Cloud Shell이 시작될 때 symlink를 복사하거나 만들어야 합니다. 다음 코드 조각을 profile.ps1에 사용하여 `clouddrive`에 symlink를 만듭니다.

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>셸 종료

세션을 종료하려면 `exit`을 입력합니다.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
