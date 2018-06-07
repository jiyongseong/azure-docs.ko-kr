---
title: Azure 스택의 암호를 회전 | Microsoft Docs
description: Azure 스택에서 암호를 회전 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: a3dfce6ce1b136e39047cfd47b336b2fb2a35af9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure 스택의 암호를 회전

*이러한 지침은 Azure 스택 통합 시스템 버전 1803 및 나중에만 적용 됩니다. Pre 1802 Azure 스택 버전에 대 한 비밀 회전을 하지 마십시오*

Azure 스택 Azure 스택 인프라 리소스와 서비스 간의 안전한 통신을 유지 하기 위해 다양 한 암호를 사용 합니다.

- **내부 암호**  
모든 인증서, 암호, 보안 문자열 및 Azure 스택 연산자의 개입 없이 Azure 스택 인프라에서 사용 되는 키입니다. 

- **외부 암호**  
Azure 스택 연산자에 의해 제공 되는 외부 연결 서비스에 대 한 서비스 인증서를 인프라입니다. 다음 서비스에 대 한 인증서가 포함 됩니다. 
    - 관리자 포털 
    - 공용 포털 
    - 관리자가 Azure 리소스 관리자 
    - 전역 Azure 리소스 관리자 
    - 관리자 Keyvault 
    - Keyvault 
    - ACS (blob, 테이블 및 큐 저장소 포함) 
    - ADFS<sup>*</sup>
    - 그래프<sup>*</sup>

    > <sup>*</sup> 환경의 id 공급자가 Active Directory Federated Services (AD FS) 하는 경우에 적용 가능 합니다.

> [!NOTE]
> 다른 모든 키 및 문자열, BMC를 포함 하 여 보안 암호 전환, 사용자 및 관리자 계정 암호는 관리자가 수동으로 업데이트 합니다. 

Azure 스택 인프라의 무결성을 유지 하기 위해 연산자에는 조직의 보안 요구 사항과 일치 하는 것에 해당 인프라의 암호를 정기적으로 회전 하는 기능이 필요 합니다.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>새 인증 기관에서 외부 인증서와 암호를 회전

Azure 스택 다음 경우에 새 인증 기관 (CA)에서 외부 인증서를 사용 하 여 비밀 회전을 지원합니다.

|설치 된 인증서 CA|CA를 회전 하려면|지원됨|지원 되는 버전 azure 스택|
|-----|-----|-----|-----|-----|
|자체 서명 된|엔터프라이즈에|지원되지 않음||
|자체 서명 된|자체 서명 하려면|지원되지 않음||
|자체 서명 된|Public에<sup>*</sup>|지원됨|1803 & 이상|
|엔터프라이즈에서|엔터프라이즈에|고객 동일한 엔터프라이즈 배포에 사용 되는 CA를 사용 하 여으로 지원 합니다.|1803 & 이상|
|엔터프라이즈에서|자체 서명 하려면|지원되지 않음||
|엔터프라이즈에서|Public에<sup>*</sup>|지원됨|1803 & 이상|
|Public<sup>*</sup>|엔터프라이즈에|지원되지 않음|1803 & 이상|
|Public<sup>*</sup>|자체 서명 하려면|지원되지 않음||
|Public<sup>*</sup>|Public에<sup>*</sup>|지원됨|1803 & 이상|

<sup>*</sup> 다음 공용 인증 기관은 Windows 신뢰할 수 있는 루트 프로그램의 일부인 것입니다. 전체 목록을 찾을 수 있습니다 [Microsoft 신뢰할 수 있는 루트 인증서 프로그램: 2017 년 6 월 27) (현재 참가자](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca)합니다.

## <a name="alert-remediation"></a>경고 관리

암호 만료의 30 일 이내에, 관리자 포털에 다음과 같은 경고가 생성 됩니다. 

- 보류 중인 서비스 계정 암호 만료 
- 보류 중인 내부 인증서 만료 
- 보류 중인 외부 인증서 만료 

아래 지침을 사용 하 여 비밀 회전 실행 이러한 경고를 수정 합니다.

## <a name="pre-steps-for-secret-rotation"></a>비밀 회전에 대 한 사전 단계

1.  모든 유지 관리 작업의 사용자를 게 알립니다. 일반 유지 관리 기간을 최대한 많이, 업무 외 시간을 예약 합니다. 유지 관리 작업 사용자 워크 로드와 포털 작업에 영향을 줄 수 있습니다.

    > [!note]  
    > 다음 단계는 Azure 스택 외부 암호를 회전 하는 경우에 적용 됩니다.

2.  새로운 교체 집합이 외부 인증서를 준비 합니다. 새 설정 된 인증서 사양에 설명 된 일치는 [Azure 스택 PKI 인증서 요구 사항](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs)합니다.
3.  보안 백업 위치에 회전에 대 한 사용 되는 인증서는 백업을 저장 합니다. 회전에 실행 되 고 실패 하면 다음을 하는 경우는 회전을 다시 실행 하기 전에 백업 복사본과 함께 파일 공유에 있는 인증서를 대체 합니다. 보안 백업 위치에 백업 복사본을 보관 하십시오.
3.  ERCS Vm에서 액세스할 수 파일 공유를 만듭니다. 파일 공유는 읽을 수 있는 고에 대 한 쓰기 가능 해야는 **CloudAdmin** identity입니다.
4.  파일 공유에 액세스할 수 있는 컴퓨터에서 PowerShell ISE 콘솔을 엽니다. 프로그램 파일 공유로 이동 합니다. 
5.  실행 **[CertDirectoryMaker.ps1](http://www.aka.ms/azssecretrotationhelper)** 외부 인증서에 대 한 필수 디렉터리를 만들려고 합니다.

## <a name="rotating-external-and-internal-secrets"></a>외부 및 내부 암호를 회전

두 외부 내부 비공식 회전 하려면:

1. 새로 만든 내 **/인증서** 사전 단계에서 만든 디렉터리 필수 인증서 섹션에 설명 된 형식에 따라 디렉터리 구조에 새 인증서 집합을 대체 외부 배치 [Azure 스택 PKI 인증서 요구 사항](https://docs.microsoft.com/azure/azure-stack/azure-stack-pki-certs#mandatory-certificates)합니다.
2. PowerShell 세션을 만들기는 [권한 있는 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint) 를 사용 하는 **CloudAdmin** 계정 및 세션 변수로 저장 합니다. 이 변수는 다음 단계에서 매개 변수로 사용 합니다.

    > [!IMPORTANT]  
    > 세션을 시작, 세션 변수로 저장 하지 마십시오.
    
3. 실행  **[invoke-command 기반](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-5.1)** 합니다. 으로 권한 있는 끝점 PowerShell 세션 변수를 전달 된 **세션** 매개 변수입니다. 
4. 실행 **시작 SecretRotation** 는 다음 매개 변수:
    - **PfxFilesPath**  
    이전에 만든 인증서 디렉터리에 네트워크 경로 지정 합니다.  
    - **PathAccessCredential**  
    공유에 대 한 자격 증명을 사용 하는 PSCredential 개체입니다. 
    - **CertificatePassword**  
    만든 pfx 인증서 파일을 모두 사용 되는 암호의 보안 문자열입니다.
4. 암호를 회전 하는 동안 기다립니다.  
콘솔이 나타납니다 비밀 회전 성공적으로 완료 되 면 **전체 작업 상태: 성공**합니다. 
5. 비밀 회전을 성공적으로 완료 한 후 이전 단계에서 만든 공유에서 인증서를 제거 하 고 보안의 백업 위치에 저장 합니다. 

## <a name="walkthrough-of-secret-rotation"></a>비밀 회전 연습

다음 PowerShell 예에서는 cmdlet 및 매개 변수를 암호를 회전 하기 위해 실행 하는 방법을 보여 줍니다.

```powershell
#Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IPofERCSMachine>"}'
$PEPCreds = Get-Credential 
$PEPsession = New-PSSession -computername <IPofERCSMachine> -Credential $PEPCreds -ConfigurationName PrivilegedEndpoint 

#Run Secret Rotation
$CertPassword = ConvertTo-SecureString "Certpasswordhere" -AsPlainText -Force
$CertShareCred = Get-Credential 
$CertSharePath = "<NetworkPathofCertShare>"
Invoke-Command -session $PEPsession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword }
Remove-PSSession -Session $PEPSession
```
## <a name="rotating-only-internal-secrets"></a>내부 비밀만 회전

Azure 스택 내부 비밀만 회전 하려면:

1. PowerShell 세션을 만들기는 [권한 있는 끝점](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint)합니다.
2. 권한 있는 끝점 세션에서 실행 **시작 SecretRotation** 인수 없이 합니다.

## <a name="start-secretrotation-reference"></a>시작 SecretRotation 참조

Azure 스택 시스템의 암호를 회전합니다. Azure 스택 권한 있는 끝점에 대해에 실행 합니다.

### <a name="syntax"></a>구문

경로 (기본값)

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential] <PSCredential> [-CertificatePassword <SecureString>]  
```

### <a name="description"></a>설명

Azure 스택 시스템의 인프라 암호를 회전 하는 시작 SecretRotation cmdlet. 내부 인프라 네트워크에 노출 하는 모든 암호를 회전 기본적으로 사용자 입력이 포함 된 회전 모든 외부 네트워크 인프라 끝점의 인증서입니다. 외부 네트워크 인프라 끝점을 회전 하는 경우 시작 SecretRotation Invoke-command 스크립트 블록을 통해 실행 하 여 Azure 스택 환경 privileged 끝점 세션 세션 매개 변수로 전달 되어야 합니다.
 
### <a name="parameters"></a>매개 변수

| 매개 변수 | type | 필수 | Position | 기본값 | 설명 |
| -- | -- | -- | -- | -- | -- |
| PfxFilesPath | 문자열  | False  | named  | 없음  | 에 대 한 파일 공유 경로 **\Certificates** 모든 외부 포함 된 디렉터리 네트워크 끝점 인증서입니다. 내부 및 외부 암호를 회전 하는 경우에 필요 합니다. 최종 디렉터리 있어야 **\Certificates**합니다. |
| CertificatePassword | SecureString | False  | named  | 없음  | -PfXFilesPath에서 제공 하는 모든 인증서에 대 한 암호입니다. 내부 및 외부 암호를 회전 하는 경우에 PfxFilesPath가 제공 하는 경우 필수 값입니다. |
|

### <a name="examples"></a>예
 
**내부 인프라 비밀만 회전**

```powershell  
PS C:\> Start-SecretRotation  
```

이 명령은 모든 Azure 스택 내부 네트워크에 노출 하는 인프라 암호를 회전 합니다. 모든 스택 생성 된 암호를 회전 하는 시작 SecretRotation 하지만 있기 때문에 제공 된 인증서가 없습니다. 외부 끝점 인증서 회전 되지 않습니다.  

**내부 및 외부 인프라 암호를 회전**
  
```powershell
PS C:\> Invoke-Command -session $PEPSession -ScriptBlock { 
Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCred -CertificatePassword $using:CertPassword } 
Remove-PSSession -Session $PEPSession
```

이 명령은 모든 Azure 스택 외부 네트워크 인프라 끝점에 사용 되는 TLS 인증서와 함께 Azure 스택 내부 네트워크에 노출 하는 인프라 암호를 회전 합니다. 시작 SecretRotation 모든 스택 생성 된 암호를 회전 하 고 있는 인증서를 제공 하기 때문에 외부 끝점 인증서 회전도 됩니다.  


## <a name="update-the-baseboard-management-controller-bmc-password"></a>베이스 보드 관리 컨트롤러 (BMC) 암호 업데이트

베이스 보드 관리 컨트롤러 BMC ()는 실제 서버의 상태를 모니터링합니다. 사양 및 BMC의 암호를 업데이트 하는 방법에 대 한 지침 (oem) 하드웨어 공급 업체에 따라 다릅니다. Azure 스택 일반 흐름에 있는 구성 요소에 대 한 암호를 업데이트 해야 합니다.

1. Azure 스택 물리적 서버에서 BMC OEM 지침에 따라 업데이트 합니다. 사용자 환경에서 각 BMC에 대 한 암호는 같아야 합니다.
2. Azure 스택 세션에서 권한 있는 끝점을 엽니다. 명령에 대 한 참조 [권한 있는 끝점을 사용 하 여 Azure 스택의](azure-stack-privileged-endpoint.md)합니다.
3. 프롬프트를 변경 된 후에 PowerShell **[IP 주소 또는 ERCS VM name]: PS >** 또는 **[azs ercs01]: PS >** 실행 하는 환경에 따라 `Set-BmcPassword` 를 실행 하 여 `invoke-command`합니다. 권한 있는 끝점 세션 변수를 매개 변수로 전달 합니다. 예: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```
    
    또한 암호와 함께 코드 줄으로 정적 PowerShell 버전을 사용할 수 있습니다.
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="next-steps"></a>다음 단계

[Azure 스택 보안에 대 한 자세한 정보](azure-stack-security-foundations.md)
