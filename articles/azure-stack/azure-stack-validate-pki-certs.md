---
title: Azure 스택 통합 시스템 배포에 대 한 Azure 스택 공개 키 인프라 인증서 유효성 검사 | Microsoft Docs
description: Azure 스택 통합 시스템에 대 한 Azure 스택 PKI 인증서를 확인 하는 방법을 설명 합니다. Azure 스택 인증서 검사기 도구를 사용 하 여 설명 합니다.
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
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: e381d2ed3c6a972d776dd31f311fcebe2e35823a
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605613"
---
# <a name="validate-azure-stack-pki-certificates"></a>Azure 스택 PKI 인증서의 유효성을 검사합니다

이 문서에서 설명 하는 Azure 스택 준비 검사기 도구를 사용할 수 [PowerShell 갤러리에서](https://aka.ms/AzsReadinessChecker)합니다. 유효성을 검사 하는 도구를 사용할 수는 [PKI 인증서를 생성](azure-stack-get-pki-certs.md) 전 배포에 적합 합니다. 짧아 충분 한 시간을 테스트 하 고 필요한 경우 인증서를 다시 발급 하 여 인증서를 확인 해야 합니다.

준비 검사 도구는 다음과 같은 인증서 유효성 검사를 수행합니다.

- **PFX 읽기**  
    올바른 PFX 파일을 올바른 암호를 확인 하 고 공용 정보는 암호로 보호 되지 않는 경우 경고를 표시 합니다. 
- **서명 알고리즘**  
    서명 알고리즘 SHA1 아닌지 확인 합니다.
- **개인 키**  
    개인 키가 로컬 컴퓨터 특성을 사용 하 여 내보낸 있는지 확인 합니다. 
- **인증서 체인**  
    확인 인증서 체인은 자체 서명 된 인증서에 대 한 확인을 포함 하 여 그대로 유지 합니다.
- **DNS 이름**  
    각 끝점에 대 한 관련 DNS 이름을 포함 하는 SAN 또는 지 원하는 와일드 카드 표시 되는지 확인 합니다.
- **키 사용**  
    디지털 서명 및 키 암호화 키 사용 포함 되 고 서버 인증과 클라이언트 인증을 포함 하는 향상 된 키 사용을 확인 합니다.
- **키 크기**  
    키 크기는 2048 이상 인지 확인 합니다.
- **체인 순서**  
    순서가 올바른지 유효성을 검사 하는 인증서의 순서를 확인 합니다.
- **기타 인증서**  
    다른 인증서가 없습니다. 관련 리프 인증서와 체인 이외의 PFX에 패키지 된 확인 합니다.
- **프로필이 없습니다.**  
    새 사용자 데이터를 로드할 수 PFX 사용자 프로필 로드 없이 인증서를 서비스 하는 동안 gMSA 계정의 동작을 모방을 확인 합니다.

> [!IMPORTANT]  
> PKI 인증서는 PFX 파일 및 중요 한 정보로 암호 처리 되어야 합니다.

## <a name="prerequisites"></a>필수 조건

시스템은 Azure 스택 배포를 위한 PKI 인증서의 유효성 검사 하기 전에 다음 선행 조건을 충족 해야 합니다.

- Microsoft Azure 스택 준비 상태 검사
- SSL 인증서 내보낸 다음는 [준비 지침은](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 또는 Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>핵심 서비스 인증서 유효성 검사를 수행 합니다.

다음이 단계를 사용 하 여 준비 하 고 배포 및 비밀 회전에 대 한 Azure 스택 PKI 인증서를 확인:

1. 설치 **AzsReadinessChecker** PowerShell 프롬프트에서 (5.1 이상), 다음 cmdlet을 실행 하 여:

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ````

2. 인증서 디렉터리 구조를 만듭니다. 아래 예제에서는 변경할 수 있습니다 `<c:\certificates>` 를 새 디렉터리 경로로 선택 합니다.

    ````PowerShell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob','ACSQueue','ACSTable','ADFS','Admin Portal','ARM Admin','ARM Public','Graph','KeyVault','KeyVaultInternal','Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ````
    
    > [!Note]  
    > Id 시스템으로 AD FS를 사용 하는 경우 AD FS와 그래프가 필요 합니다.
    
     - 이전 단계에서 만든 적절 한 디렉터리에 사용 하 여 인증서를 배치 합니다. 예:   
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Certs\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\Certs\ARM Admin\CustomerCertificate.pfx`

3. PowerShell 창에서 값을 변경할 **RegionName** 및 **FQDN** Azure 스택 환경에 적합 한 및 다음 실행:

    ````PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD 

    ````

4. 모든 테스트를 통과 하는 출력 및 모든 인증서를 확인 합니다. 예: 

    ````PowerShell
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Validation

    Starting Azure Stack Certificate Validation 1.1803.405.3
    Testing: ARM Public\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ACSBlob\ssl.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location: 
    C:\AzsReadinessChecker\AzsReadinessReport.json
    AzsReadinessChecker Completed
    ````

### <a name="known-issues"></a>알려진 문제

**증상**: 테스트를 건너

**원인**: AzsReadinessChecker 종속성이 충족 되지 않으면 경우 특정 테스트를 건너뜁니다.

 - 인증서 체인에 실패 하는 경우 다른 인증서는 건너뜁니다.

    ````PowerShell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Finished Certificate Validation

    AzsReadinessChecker Log location: C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Report location (for OEM): C:\AzsReadinessChecker\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

**해결 방법**: 도구의 세부 정보 구역에서 각 인증서에 대 한 테스트의 각 집합에 대 한 지침을 따릅니다.

## <a name="perform-platform-as-a-service-certificate-validation"></a>플랫폼으로 서비스 인증서 유효성 검사 수행

SQL/MySQL 또는 응용 프로그램 서비스 배포를 계획 하는 경우 다음이 단계를 사용 하 여 준비 하 고 플랫폼에 대 한 Azure 스택 PKI 인증서는 서비스 (PaaS) 인증서로 유효성을 검사 합니다.

1.  설치 **AzsReadinessChecker** PowerShell 프롬프트에서 (5.1 이상), 다음 cmdlet을 실행 하 여:

    ````PowerShell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ````

2.  경로 및 암호 유효성 검사 필요 각 PaaS 인증서를 포함 하는 중첩 된 해시 테이블을 만듭니다. 실행할 PowerShell 창:

    ```PowerShell
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  값을 변경 **RegionName** 및 **FQDN** 유효성 검사를 시작 하려면 Azure 스택 환경과 일치 하도록 합니다. 그런 후 다음을 실행합니다.

    ```PowerShell
    Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  출력 하 고 있는 모든 인증서 모든 테스트를 통과 있는지 확인 하십시오.

    ```PowerShell
    AzsReadinessChecker v1.1805.425.2 started
    Starting PaaS Certificate Validation
    
    Starting Azure Stack Certificate Validation 1.0 
    Testing: PaaSCerts\wildcard.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\api.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\wildcard.dbadapter.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: PaaSCerts\sso.appservice.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="using-validated-certificates"></a>유효성이 검사 된 인증서를 사용 하 여

AzsReadinessChecker 하 여 유효성 검사 된 인증서를 Azure 스택 배포에서 또는 Azure 스택 비밀 회전에 대 한 사용할 준비가 됩니다. 

 - 배포에 적합 한 안전 하 게 전송 인증서를 배포 엔지니어링에 지정 된 배포 호스트에 복사할 수 있습니다는 [Azure 스택 PKI 요구 사항 문서](azure-stack-pki-certs.md)합니다.
 - 비밀 회전에 대 한 수행 하 여 Azure 스택 환경의 공용 인프라 끝점에 대 한 오래 된 인증서를 업데이트 하는 인증서를 사용할 수는 [Azure 스택 비밀 회전 설명서](azure-stack-rotate-secrets.md)합니다.
 - PaaS 서비스의 경우에 따라 Azure 스택의 SQL, MySQL 및 응용 프로그램 서비스 리소스 공급자를 설치 하는 인증서를 사용할 수는 [Azure 스택 설명서에는 서비스 제공의 개요](azure-stack-offer-services-overview.md)합니다.

## <a name="next-steps"></a>다음 단계

[데이터 센터 id 통합](azure-stack-integrate-identity.md)