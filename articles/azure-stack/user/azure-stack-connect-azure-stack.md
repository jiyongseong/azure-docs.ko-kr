---
title: "Azure 스택에 연결 | Microsoft Docs"
description: "Azure 스택을 연결 하는 방법에 알아봅니다"
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
ms.date: 08/22/2017
ms.author: mabrigg
ms.openlocfilehash: 7479202a8afabf5a84560691a2bccf849206c077
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="connect-to-azure-stack"></a>Azure Stack에 연결

리소스를 관리 하려면 Azure 스택 개발 키트에 연결 해야 합니다. 이 문서에 자세히 설명 하는 단계에서 개발 키트에 연결 되어야 합니다. 다음 연결 옵션 중 하나를 사용할 수 있습니다.

* [원격 데스크톱](#connect-with-remote-desktop): 신속 하 게 개발 키트의 연결 단일 동시 사용자 수 있습니다.
* [가상 개인 네트워크 (VPN)](#connect-with-vpn): 클라이언트 (구성 필요) Azure 스택 인프라 외부에서 여러 동시 사용자 연결 수 있습니다.

## <a name="connect-to-azure-stack-with-remote-desktop"></a>Azure 스택 원격 데스크톱에 연결
원격 데스크톱 연결을 한 명의 동시 사용자 리소스를 관리 포털으로 작업할 수 있습니다.

1. 원격 데스크톱 연결을 열고 개발 키트에 연결 합니다. 입력 **AzureStack\AzureStackAdmin** Azure 스택 설정 중에 제공한 관리자 암호와 사용자 이름, 합니다.  

2. 서버 관리자를 열고 개발 키트 컴퓨터에서 클릭 **로컬 서버**Internet Explorer 보안 강화를 해제 하 고 서버 관리자를 닫습니다.

3. 포털을 열려면 (https://portal.local.azurestack.external/)로 이동한 다음 사용자 자격 증명을 사용 하 여 로그인 합니다.


## <a name="connect-to-azure-stack-with-vpn"></a>Azure VPN 스택을에 연결

분할 터널 Azure 스택 개발 키트를 가상 개인 네트워크 (VPN) 연결을 설정할 수 있습니다. VPN 연결을 통해 사용자 포털 관리자 포털에 액세스할 수 하 고 로컬로 스택에서 Azure 리소스를 관리 하는 Visual Studio, PowerShell 등의 도구를 설치 합니다. Active Directory 페더레이션 Services(AD FS) 기반의 배포 및 VPN 연결을 모두 Azure 활성 Directory(AAD)에서 지원 됩니다. VPN 연결을 여러 클라이언트가 동시에 Azure 스택에 연결할 사용 합니다. 

> [!NOTE] 
> 이 VPN 연결 Azure 스택 인프라 Vm에 대 한 연결을 제공 하지 않습니다. 

### <a name="prerequisites"></a>필수 조건

* 설치 [Azure 스택 호환 Azure PowerShell](azure-stack-powershell-install.md) 로컬 컴퓨터에 있습니다.  
* 다운로드는 [Azure 스택을 사용 하는 데 필요한 도구](azure-stack-powershell-download.md)합니다. 

### <a name="configure-vpn-connectivity"></a>VPN 연결 구성

개발 키트를 VPN 연결을 만들려면 로컬 Windows 기반 컴퓨터에서 관리자 권한 PowerShell 세션을 열고 (사용자 환경에 대 한 IP 주소와 암호 값을 업데이트할 수 있는지 확인) 다음 스크립트를 실행 합니다.

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

설치 과정에 성공 하면이 표시 **azurestack** VPN 연결 목록에 있습니다.

![네트워크 연결](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack에 연결

다음 두 가지 방법 중 하나를 사용 하 여 Azure 스택 인스턴스 연결:  

* 사용 하 여는 `Connect-AzsVpn ` 명령: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  Azure 스택 호스트 신뢰 및에서 인증서를 설치 대화 상자가 나타나면 **AzureStackCertificateAuthority** 로컬 컴퓨터의 인증서 저장소에 있습니다. (프롬프트 PowerShell 세션 창 뒤에 나타날 수 있습니다). 

* 로컬 컴퓨터의 열고 **네트워크 설정** > **VPN** > 클릭 **azurestack** > **연결**합니다. 로그인 프롬프트 (AzureStack\AzureStackAdmin) 사용자 이름 및 암호를 입력 합니다.

### <a name="test-the-vpn-connectivity"></a>VPN 연결 테스트

포털 연결을 테스트 하려면 인터넷 브라우저를 열고 및 (https://portal.local.azurestack.external/)는 사용자 포털로 이동, 로그인 및 리소스를 만듭니다.  

## <a name="next-steps"></a>다음 단계



