---
title: "Azure 스택에 연결 | Microsoft Docs"
description: "Azure 스택에 연결 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: d9d7beae9ea81f2568377ee3593362871aae98fe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Azure Stack에 연결

*적용 대상: Azure 스택 개발 키트*

리소스를 관리 하려면 먼저 Azure 스택 개발 키트에 연결 해야 합니다. 이 문서에서는 개발 키트에 연결할 수행 하는 단계에 설명 합니다. 다음 연결 옵션 중 하나를 사용할 수 있습니다.

* [원격 데스크톱 연결](#connect-with-remote-desktop)합니다. 원격 데스크톱 연결을 사용 하 여 연결 하면 사용자 한 명이 개발 키트에서 신속 하 게 연결할 수 있습니다.
* [가상 사설망 (VPN)](#connect-with-vpn)합니다. VPN을 사용 하 여 연결 하면 여러 사용자가 클라이언트 (설치 해야 함)는 Azure 스택 인프라 외부에서 동시에 연결할 수 있습니다.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Azure 스택 원격 데스크톱 연결을 사용 하 여 연결
사용자 한 명이 동시 연산자 포털 또는 원격 데스크톱 연결을 통해 사용자 포털에서 리소스를 관리할 수 있습니다.

1. 원격 데스크톱 연결을 열고 개발 키트에 연결 합니다. 사용자 이름에 대 한 입력 **AzureStack\AzureStackAdmin**합니다. Azure 스택을 설정할 때 지정한 연산자 암호를 사용 합니다.  

2. 개발 키트 컴퓨터에서 서버 관리자를 엽니다. 선택 **로컬 서버**선택을 취소는 **Internet Explorer 보안 강화** 확인란을 선택한 후 서버 관리자를 닫습니다.

3. 열려는 [사용자 포털](azure-stack-key-features.md#portal)https://portal.local.azurestack.external/로 이동 하세요. 사용자 자격 증명을 사용 하 여 로그인 합니다. Azure 스택 열려는 [연산자 포털](azure-stack-key-features.md#portal)https://adminportal.local.azurestack.external/로 이동 하세요. 설치 중에 지정 된 Azure Active Directory (Azure AD) 자격 증명을 사용 하 여 로그인 합니다.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Azure 스택 VPN을 사용 하 여 연결

분할 터널 Azure 스택 개발 키트를 VPN 연결을 설정할 수 있습니다. Azure 스택 연산자 포털, 사용자 포털 및 Visual Studio와 PowerShell 스택에서 Azure 리소스 관리와 같은 로컬에 설치 된 도구에 액세스 하는 VPN 연결을 사용할 수 있습니다. Azure AD와 Active Directory Federation Services (AD FS) 배포에서 VPN 연결을 사용할 수 있습니다. VPN 연결을 사용 하면 여러 클라이언트가 동시에 Azure 스택에 연결할 수 있도록 합니다. 

> [!NOTE] 
> VPN 연결 Azure 스택 인프라 Vm에 대 한 연결을 제공 하지 않습니다. 

### <a name="prerequisites"></a>필수 조건

1. 설치 [Azure 스택 호환 Azure PowerShell](azure-stack-powershell-install.md) 로컬 컴퓨터에 있습니다.  
2. 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다. 

### <a name="set-up-vpn-connectivity"></a>VPN 연결을 설정 합니다.

개발 키트를 VPN 연결을 만들려면 로컬 Windows 기반 컴퓨터의 관리자 권한으로 Windows PowerShell을 엽니다. 다음 스크립트 (사용자 환경에 대 한 값이 IP 주소 및 암호 업데이트)를 실행 합니다.

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

설치에 성공 하면 **azurestack** VPN 연결 목록에 나타납니다.

![네트워크 연결](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 방법 중 하나를 사용 하 여 Azure 스택 인스턴스에 연결 합니다.  

* 사용 하 여 `Connect-AzsVpn ` 명령: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Azure 스택 호스트 신뢰 및에서 인증서를 설치 대화 상자가 나타나면 **AzureStackCertificateAuthority** 로컬 컴퓨터의 인증서 저장소에 있습니다. (프롬프트 PowerShell 창에 의해 숨겨져 될 수도 있습니다.) 

* 로컬 컴퓨터의 선택 **네트워크 설정** > **VPN** > **azurestack** > **연결**. 로그인 프롬프트에서 사용자 이름을 입력 합니다 (**AzureStack\AzureStackAdmin**) 및 암호.

### <a name="test-vpn-connectivity"></a>VPN 연결 테스트

포털 연결을 테스트 하려면 웹 브라우저를 열고 하 고 사용자 포털 (https://portal.local.azurestack.external/) 또는 연산자 포털 (https://adminportal.local.azurestack.external/)로 이동 하십시오. 에 로그인 하 고 리소스를 만듭니다.  

## <a name="next-steps"></a>다음 단계

[Azure 스택 사용자에 게 가상 컴퓨터를 사용할 수 있도록](azure-stack-tutorial-tenant-vm.md)

