---
title: Azure 스택 개발 키트 (ASDK)에 대 한 배포 구성을 게시 | Microsoft Docs
description: Azure 스택 개발 키트 (ASDK)를 설치한 후에 권장 되는 구성 변경 내용이 설명 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 4b58f3496b25e4fc04761b9df6e27f8313b35fe9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>ASDK 설치 후 구성 작업
후 [설치는 ASDK](asdk-install.md), 몇 가지 권장된 설치 후 구성을 변경할 수 있습니다. 

## <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell 설치 
Azure 스택 호환 Azure PowerShell 모듈은 Azure 스택이 작동 해야 합니다.

Azure 스택에 대 한 PowerShell 명령이 PowerShell 갤러리를 통해 설치 됩니다. PSGallery 리포지토리를 등록 하려면 관리자 권한 PowerShell 세션을 열고 다음 명령을 실행 합니다.

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Azure 스택 호환 AzureRM 모듈 API 버전 프로필을 통해 설치 됩니다. Azure 스택 AzureRM.Bootstrapper 모듈을 설치 하 여 액세스할 수 있는 2017-03-09-프로필 API 버전 프로필이 필요 합니다. 
 
 또는 ASDK 호스트 컴퓨터에 인터넷 연결 없이 최신 Azure 스택 PowerShell 모듈을 설치할 수 있습니다.

> [!IMPORTANT]
> 필요한 버전을 설치 하기 전에 확인 하면 [기존 Azure PowerShell 모듈을 모두 제거](.\.\azure-stack-powershell-install.md#uninstall-existing-versions-of-powershell)합니다.

- **인터넷에 연결 된** ASDK 호스트 컴퓨터에서 합니다. 개발 키트 설치에 이러한 모듈을 설치 하려면 다음 PowerShell 스크립트를 실행 합니다.

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  설치에 성공한 경우 AzureRM 및 AzureStack 모듈 출력에 표시 됩니다.

- **인터넷 연결 없이** ASDK 호스트 컴퓨터에서 합니다. 연결이 끊긴된 경우에는 다음 PowerShell 명령을 사용 하 여 인터넷 연결 되어 있는 컴퓨터에 PowerShell 모듈을 먼저 다운로드 해야 합니다.

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
    -RequiredVersion 1.2.11
  ```
  다음으로 ASDK 컴퓨터에 다운로드 한 패키지를 복사 하 고 위치를 기본 저장소로 등록 및이 리포지토리에서 AzureRM 및 AzureStack 모듈 설치:

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

## <a name="download-the-azure-stack-tools"></a>Azure 스택 도구 다운로드
[AzureStack 도구](https://github.com/Azure/AzureStack-Tools) 스택에 Azure 리소스를 배포 및 관리 하기 위한 PowerShell 모듈을 호스팅하는 GitHub 리포지토리 합니다. 이러한 도구를 얻으려면 GitHub 리포지토리 복제 하거나 AzureStack 도구 폴더에서 다음 스크립트를 실행 하 여 다운로드 합니다.

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>ASDK 설치 유효성 검사
ASDK 배포 성공적으로 수행 되었는지을 보장 하려면 다음이 단계를 수행 하 여 테스트 AzureStack cmdlet을 사용할 수 있습니다.

1. AzureStack\CloudAdmin로 ASDK 호스트 컴퓨터에 로그인 합니다.
2. (하지 PowerShell ISE) 관리자 권한으로 PowerShell을 엽니다.
3. 실행: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. 실행: `Test-AzureStack`

테스트를 완료 하는 데 몇 분 정도 걸리지만 설치에 성공한 경우는 다음과 같은 출력:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

오류가 발생 한, 도움말을 보려면 문제 해결 단계를 수행 합니다.

## <a name="activate-the-administrator-and-tenant-portals"></a>관리자 및 테 넌 트 포털을 활성화 합니다.
를 Azure AD를 사용 하는 배포 후 Azure 스택 관리자 및 테 넌 트 포털 모두 활성화 해야 합니다. 이 활성화에 Azure 스택 포털 및 Azure 리소스 관리자 정확한 권한을 제공 (동의 페이지에 나열 된) 모든 사용자에 대 한 디렉터리의에 동의 합니다.

- 관리자 포털에 대 한 이동 https://adminportal.local.azurestack.external/guest/signup에서 정보를 읽고 클릭 **Accept**합니다. 를 수락한 후 디렉터리 테 넌 트 관리자가 아닌 서비스 관리자를 추가할 수 있습니다.

- 테 넌 트 포털에 대 한 이동 https://portal.local.azurestack.external/guest/signup에서 정보를 읽고 클릭 **Accept**합니다. 를 수락한 후 디렉터리의 사용자는 테 넌 트 포털에 로그인 수 있습니다. 

> [!NOTE] 
> 포털 활성화 되지 않으면 디렉터리 관리자만 로그인 한 포털을 사용 하 여 수 있습니다. 다른 사용자가 로그인 하는 경우는 관리자가 다른 사용자에 게 사용 권한을 부여 하지 알리는 오류가 표시 됩니다. 관리자에 등록 되어 있고 Azure 스택 디렉터리에 고유 하 게 속하지 않습니다, Azure 스택 디렉터리 활성화 URL에 추가 해야 합니다. Azure 스택 fabrikam.onmicrosoft.com 및 관리자 사용자에 등록 되어 있고이 예를 들어 admin@contoso.com로 이동 https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com 포털 활성화 합니다. 

## <a name="reset-the-password-expiration-policy"></a>암호 만료 정책을 다시 설정 
개발 키트 호스트에 대 한 암호 평가 기간이 종료 되기 전에 만료 되지 않도록 확인을 하는 ASDK를 배포한 후 다음이 단계를 수행 합니다.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Powershell에서 암호 만료 정책 변경 하려면:
관리자 권한 Powershell 콘솔에서 명령을 실행 합니다.

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>변경 하려면 암호 만료 정책을 수동으로:
1. 개발 키트 호스트에서 엽니다 **그룹 정책 관리** (GPMC 합니다. MMC)로 이동 하 고 **그룹 정책 관리** – **포리스트: azurestack.local** – **도메인** – **azurestack.local**합니다.
2. 마우스 오른쪽 단추로 클릭 **기본 도메인 정책** 클릭 **편집**합니다.
3. 그룹 정책 관리 편집기에서 이동 **컴퓨터 구성** – **정책** – **Windows 설정** – **보안 설정**– **계정 정책** – **암호 정책**합니다.
4. 오른쪽 창에서 두 번 클릭 **최대 암호 사용 기간**합니다.
5. 에 **최대 암호 사용 기간 속성** 변경 대화 상자는 **암호 만료** 값을 **180**, 클릭 하 고 **확인**합니다.

![그룹 정책 관리 콘솔](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>다음 단계
[Azure와 ASDK 레지스터](asdk-register.md)
