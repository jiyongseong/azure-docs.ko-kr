---
title: Azure 스택 용 PowerShell 설치 | Microsoft Docs
description: Azure 스택에 대 한 PowerShell을 설치 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 5/18/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: b3c09582f5135655640768bcbcbef91750827bfa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="install-powershell-for-azure-stack"></a>Azure 스택에 대 한 PowerShell을 설치 합니다.

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 호환 Azure PowerShell 모듈은 Azure 스택이 작동 해야 합니다. 이 가이드에서는 과정을 단계별로 Azure 스택에 대 한 PowerShell을 설치 하는 데 필요한 단계.

이 문서에 Azure 스택에 대 한 PowerShell을 설치 하는 지침을 설명 합니다.

> [!Note]
> 다음 단계에는 PowerShell 5.0 있어야합니다. 버전을 확인 하려면 $PSVersionTable.PSVersion을 실행 하 고 비교는 **주요** 버전입니다.

Azure 스택에 대 한 PowerShell 명령이 PowerShell 갤러리를 통해 설치 됩니다. PSGallery 저장소로 등록 되어 있는지 검사 하기, 관리자 권한 PowerShell 세션을 열고, 다음 명령을 실행 하는 다음 절차를 사용할 수 있습니다.

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

저장소에 등록 되어 있지 않으면 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 합니다.

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> 이 단계는 인터넷 액세스가 필요 합니다. 

## <a name="uninstall-existing-versions-of-powershell"></a>기존 버전의 PowerShell 제거 합니다.

필요한 버전을 설치 하기 전에 모든 이전에 설치 된 Azure 스택 PowerShell 모듈을 제거 해야 합니다. 다음 두 가지 방법 중 하나를 사용 하 여 해당 데이터를 제거할 수 있습니다.

 - 기존 PowerShell 모듈을 제거 하려면 모든 현재 PowerShell 세션을 닫은 다음 명령을 실행 합니다.

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - "Azure"로 시작 하는 모든 폴더에서 삭제 된 `C:\Program Files\WindowsPowerShell\Modules` 및 `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` 폴더입니다. 이러한 폴더를 삭제 합니다. 기존 PowerShell 모듈을 모두 제거 됩니다.

다음 섹션에서는 Azure 스택에 대 한 PowerShell을 설치 하는 데 필요한 단계를 설명 합니다. 연결 된, 부분적으로 연결에서 운영 하는 Azure 스택 또는 연결이 끊긴된 시나리오에서 PowerShell은 설치할 수 있습니다.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>인터넷 연결) (에 연결 된 시나리오에서 PowerShell 설치

Azure 스택 호환 AzureRM 모듈 API 버전 프로필을 통해 설치 됩니다. Azure 스택 필요는 **2017-03-09-프로필** AzureRM.Bootstrapper 모듈을 설치 하 여 액세스할 수 있는 API 버전 프로필입니다. API 버전 프로필 및 이들 프로그램에서 제공 하는 cmdlet에 대 한 자세한 내용은 참조는 [API 버전 프로필을 관리](user/azure-stack-version-profiles.md)합니다. AzureRM 모듈 외에 Azure 스택 관련 PowerShell 모듈을 설치 해야 합니다. 개발 워크스테이션에 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

설치를 확인 하려면 다음 명령을 실행 합니다.

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

설치에 성공한 경우 AzureRM 및 AzureStack 모듈 출력에 표시 됩니다.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>제한 된 인터넷 연결) (에 연결 되어 있지 않거나 부분적으로 연결 된 시나리오에서 PowerShell을 설치

연결이 끊긴 시나리오에서는 먼저 인터넷 연결 되어 있는 컴퓨터에 PowerShell 모듈을 다운로드 하 고 설치를 위한 Azure 스택 개발 키트를 전송 해야 합니다.

> [!IMPORTANT]  
> Azure 스택 1.3.0 PowerShell 모듈의 릴리스는 주요 변경 내용 목록이 포함 되어 있습니다. 1.2.11에서 업그레이드 하 버전에서는 참조는 [마이그레이션 가이드](https://aka.ms/azspowershellmigration)합니다.

1. 인터넷에 연결 하 고 AzureRM, 다운로드 및 AzureStack 패키지를 위한 다음 스크립트를 사용 하 여 로컬 컴퓨터에 있는 컴퓨터에 로그인 합니다.

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > 1804 이상 Azure 스택 업데이트와 함께 실행 되지 않는 경우 변경 된 **requiredversion** 매개 변수 값을 `1.2.11`합니다. 

2. USB 장치를 통해 다운로드 한 패키지를 복사 합니다.

3. 워크스테이션에 로그인 하 고 워크스테이션에서 위치에 USB 장치에서 패키지를 복사 합니다.

4. 이제이 위치를 기본 저장소로 등록 하 고이 저장소에서 AzureRM 및 AzureStack 모듈을 설치 해야 합니다.

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>프록시 서버를 사용 하도록 PowerShell을 구성

프록시 서버를 인터넷에 액세스 해야 하는 시나리오에서는 먼저 기존 프록시 서버를 사용 하는 PowerShell을 구성 해야 합니다.

1. 관리자 권한 PowerShell 프롬프트를 엽니다.
2. 다음 명령을 실행합니다.

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>다음 단계

 - [GitHub에서 Azure 스택 도구 다운로드](azure-stack-powershell-download.md)
 - [Azure 스택 사용자의 PowerShell 환경 구성](user/azure-stack-powershell-configure-user.md)  
 - [Azure 스택 운영자의 PowerShell 환경 구성](azure-stack-powershell-configure-admin.md) 
 - [Azure 스택에서 API 버전 프로필을 관리](user/azure-stack-version-profiles.md)  
