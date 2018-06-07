---
title: Azure 스택 데이터 센터 통합-Identity
description: 데이터 센터 AD FS와 Azure 스택 AD FS를 통합 하는 방법에 알아봅니다
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: cc15c92037e18800a6f919d0ca18acb20ed5e893
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
---
# <a name="azure-stack-datacenter-integration---identity"></a>Azure 스택 데이터 센터 통합-Identity
Id 공급자와 Azure Active Directory (Azure AD) 또는 Active Directory Federation Services (AD FS)을 사용 하 여 Azure 스택을 배포할 수 있습니다. Azure 스택을 배포 하기 전에 선택을 해야 합니다. AD FS를 사용 하 여 배포를 Azure 스택 연결이 끊어진된 모드에서 배포는 라고도 합니다.

다음 표에서 두 가지 identity 옵션 간의 차이점을 보여 줍니다.

||인터넷에서 분리|인터넷에 연결|
|---------|---------|---------|
|결제|용량 이어야 합니다.<br> 기업 계약 (EA)만|용량 또는 사용량 기준 과금으로-있습니다-사용<br>EA 또는 클라우드 솔루션 공급자 (CSP)|
|ID|AD FS 여야 합니다.|Azure AD 또는 AD FS|
|마켓플레이스에서 배포|지원됨<br>BYOL 라이선스|지원됨<br>BYOL 라이선스|
|등록|권장, 이동식 미디어를 사용 하려면 필요<br> 와 별도 연결 된 장치입니다.|자동화 된|
|패치 및 업데이트|필수, 이동식 미디어를 필요 합니다.<br> 와 별도 연결 된 장치입니다.|업데이트 패키지를 직접 다운로드할 수 있습니다.<br> 인터넷을 통해 Azure 스택 합니다.|

> [!IMPORTANT]
> 전체 Azure 스택 솔루션 다시 배포 하지 않고 id 공급자를 전환할 수 없습니다.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services 및 그래프

기존 Active Directory 포리스트를 스택에서 Azure 리소스를 사용 하 여 인증에서 id를 AD FS와 함께 배포 있습니다. 이 기존 Active Directory 포리스트의 AD FS 페더레이션 트러스트를 만들 수 있도록 AD FS 배포를 해야 합니다.

인증은 id의 한 부분입니다. 액세스 제어 RBAC (역할 기반) Azure 스택의 관리 하려면 그래프 구성 요소를 구성 합니다. 리소스에 대 한 액세스 위임 되는 그래프 구성 요소 LDAP 프로토콜을 사용 하 여 기존 Active Directory 포리스트의 사용자 계정을 조회 합니다.

![Azure AD FS 스택 아키텍처](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

기존 AD FS에는 계정 보안 토큰 서비스 (STS) (리소스 STS)의 스택 Azure AD FS에 대 한 클레임을 전송 하는 합니다. Azure 스택 자동화 기존 AD FS에 대 한 메타 데이터 끝점과 클레임 공급자 트러스트를 만듭니다.

기존 AD FS에서 신뢰 당사자 트러스트를 구성 합니다. 이 단계는 자동화에서 수행 되지 하 고 연산자에서 구성 해야 합니다. Azure 스택 메타 데이터 끝점은 설명 되어 권한 있는 끝점을 통해 또는 AzureStackStampDeploymentInfo.JSON 파일에서 명령을 실행 하 여 `Get-AzureStackInfo`합니다.

신뢰 당사자 트러스트 구성을 필요로 하는 Microsoft에서 제공 하는 클레임 변환 규칙을 구성할 수 있습니다.

그래프 구성에 대 한 서비스 계정에 읽기 기존 Active Directory에 권한이 있는 이어야 합니다. 이 계정은 RBAC 시나리오를 사용 하는 자동화에 대 한 입력으로 합니다.

마지막 단계에 대 한 새 소유자는 기본 공급자 구독에 대해 구성 됩니다. 이 계정에는 Azure 스택 관리자 포털에 로그인 하는 경우 모든 리소스에 모든 권한이 있습니다.

Requirements:


|구성 요소|요구 사항|
|---------|---------|
|그래프|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>그래프와 통합을 설정

그래프는 단일 Active Directory 포리스트에와 통합을 지원합니다. 여러 포리스트에 있는 경우 사용자 및 그룹을 인출 하는 구성에 지정 된 포리스트만 사용 됩니다.

다음 정보는 자동화 매개 변수에 대 한 입력으로 필요 합니다.


|매개 변수|설명|예|
|---------|---------|---------|
|CustomADGlobalCatalog|Active Directory 포리스트 대상의 FQDN<br>와 통합|contoso.com|
|CustomADAdminCredentials|LDAP 읽기 권한이 있는 사용자|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>기존 Active directory에서 (선택 사항) 사용자 계정 만들기

필요에 따라 기존 Active directory에서 그래프 서비스에 대 한 계정을 만들 수 있습니다. 사용 하려는 계정이 아직 없는 경우이 단계를 수행 합니다.

1. 기존 Active Directory에서 사용자 계정 (권장)를 만듭니다.
   - **사용자 이름**: graphservice
   - **암호**: 강력한 암호를 사용<br>암호가 만료 되지 않도록 구성 합니다.

   특별 사용 권한 또는 멤버 자격이 없습니다가 필요 합니다.

#### <a name="trigger-automation-to-configure-graph"></a>트리거 자동화 그래프를 구성 하려면

이 절차에서는 Azure 스택의 권한 있는 끝점과 통신할 수 있는 데이터 센터 네트워크의 컴퓨터를 사용 합니다.

2. 관리자 권한 Windows PowerShell 세션 (관리자 권한으로 실행)를 열고 권한 있는 끝점의 IP 주소에 연결 합니다. 에 대 한 자격 증명을 사용 하 여 **CloudAdmin** 를 인증 합니다.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. 권한 있는 끝점에 연결 된 했으므로 다음 명령을 실행 합니다. 

   ```powershell
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   메시지가 표시 되 면 (예: graphservice) 그래프 서비스에 대 한 사용 하려는 사용자 계정에 대 한 자격 증명을 지정 합니다. 레지스터 DirectoryService cmdlet에 대 한 입력 포리스트 이름/도메인 포리스트의 아닌 포리스트의 다른 도메인 루트 있어야 합니다.

   > [!IMPORTANT]
   > 팝업 자격 증명에 대 한 대기 (Get-credential은 권한 있는 끝점에는 지원 되지 않음) 하 고 그래프 서비스 계정 자격 증명을 입력 합니다.

#### <a name="graph-protocols-and-ports"></a>그래프 프로토콜 및 포트

Azure 스택에서 그래프 서비스 키 배포 센터 (KDC) Active Directory 포리스트 대상에서 로그인 요청을 처리할 수 있는 쓰기 가능한 서버 GC (글로벌 카탈로그)와 통신 하는 다음 프로토콜 및 포트를 사용 합니다.

Azure 스택에서 그래프 서비스는 대상 Active Directory와 통신 하는 다음 프로토콜 및 포트를 사용 합니다.

|type|포트|프로토콜|
|---------|---------|---------|
|LDAP|389|TCP 및 UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>페더레이션 메타 데이터를 다운로드 하 여 AD FS와 통합을 설정 합니다.

다음 정보가 필요 합니다 자동화 매개 변수에 대 한 입력으로:

|매개 변수|설명|예|
|---------|---------|---------|
|CustomAdfsName|클레임 공급자의 이름입니다. <cr>이런 방식으로 AD FS 방문 페이지를 표시 합니다.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|페더레이션 메타 데이터 링크|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Azure 스택에서 클레임 공급자 트러스트를 구성 하는 트리거 자동화

이 절차에서는 Azure 스택의 권한 있는 끝점과 통신할 수 있는 컴퓨터를 사용 합니다. 인증서 계정에 의해 사용 되는 것으로 예상 **AD STS FS** Azure 스택에서 신뢰할 수 있습니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 권한 있는 끝점에 연결 합니다.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 권한 있는 끝점에 연결 된 했으므로 사용자 환경에 적합 한 매개 변수를 사용 하 여 다음 명령을 실행 합니다.

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. 사용자 환경에 적합 한 매개 변수를 사용 하 여 기본 공급자 구독 소유자를 업데이트 하려면 다음 명령을 실행 합니다.

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>페더레이션 메타 데이터 파일을 제공 하 여 AD FS와 통합을 설정 합니다.

다음 조건 중 하나가 true 이면이 메서드를 사용 합니다.

- 인증서 체인은 AD FS를 Azure 스택의 다른 모든 끝점에 비해 다릅니다.
- Azure 스택 AD FS 인스턴스에서 기존 AD FS 서버에 없는 네트워크로 연결 되어 있습니다.

다음 정보가 필요 합니다 자동화 매개 변수에 대 한 입력으로:


|매개 변수|설명|예|
|---------|---------|---------|
|CustomAdfsName|클레임 공급자의 이름입니다. 이런 방식으로 AD FS 방문 페이지에 나타납니다.|Contoso|
|CustomADFSFederationMetadataFile|페더레이션 메타 데이터 파일|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml|

### <a name="create-federation-metadata-file"></a>페더레이션 메타 데이터 파일 만들기

다음 절차에 대 한 STS 계정이 기존 AD FS 배포에 네트워크 연결 되어 있는 컴퓨터를 사용 해야 합니다. 또한 필요한 인증서를 설치 해야 합니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 적합 한 사용자 환경에 대 한 매개 변수를 사용 하 여 다음 명령을 실행 합니다.

   ```powershell
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. 권한 있는 끝점에서 액세스할 수 있는 공유에 메타 데이터 파일을 복사 합니다.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Azure 스택에서 클레임 공급자 트러스트를 구성 하는 트리거 자동화

이 절차에서는 Azure 스택의 권한 있는 끝점과 통신할 수 있는 컴퓨터를 사용 합니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 권한 있는 끝점에 연결 합니다.

   ```powershell
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 권한 있는 끝점에 연결 된 했으므로 사용자 환경에 적합 한 매개 변수를 사용 하 여 다음 명령을 실행 합니다.

   ```powershell
   Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml
   ```

3. 사용자 환경에 적합 한 매개 변수를 사용 하 여 기본 공급자 구독 소유자를 업데이트 하려면 다음 명령을 실행 합니다.

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>기존 AD FS 배포 (STS 계정)에서 신뢰 당사자를 구성 합니다.

Microsoft는 클레임 변환 규칙을 포함 하 여 신뢰 당사자 트러스트를 구성 하는 스크립트를 제공 합니다. 스크립트를 사용 하는 선택 사항 수동으로 명령을 실행할 수 있습니다.

도우미 스크립트를 다운로드할 수 있습니다 [Azure 스택 도구](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) Github에서 합니다.

수동으로 명령을 실행 하려는 경우 다음이 단계를 따르십시오.

1. 데이터 센터의 AD FS 인스턴스 또는 팜의 구성원에 (예: c:\ClaimRules.txt으로 저장).txt 파일에 다음 콘텐츠를 복사 합니다.

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Windows Forms 기반 인증을 사용 하려면 상승 된 권한으로 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   ```powershell
   Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")
   ```

3. 신뢰 당사자 트러스트를 추가 하려면 AD FS 인스턴스 또는 팜의 구성원에 다음 Windows PowerShell 명령을 실행 합니다. AD FS 끝점을 업데이트할 수 있는지 확인 하 고 1 단계에서에서 만든 파일을 가리킵니다.

   **AD FS 2016에 대 한**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"
   ```

   **AD FS 2012/2012 r 2에 대 한**

   ```powershell
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true
   ```

   > [!IMPORTANT]
   > Windows Server 2012 또는 2012 R2 AD FS를 사용 하는 경우 발급 권한 부여 규칙을 구성 하려면 AD FS MMC 스냅인을 사용 해야 합니다.

4. Internet Explorer 또는 Microsoft Edge 브라우저를 사용 하 여 Azure 스택 액세스할 때 토큰 바인딩을 무시 해야 합니다. 그렇지 않으면 로그인 시도 실패 합니다. AD FS 인스턴스 또는 팜 구성원에서 다음 명령을 실행 합니다.

   > [!note]  
   > Windows Server 2012 또는 2012 R2 AD FS를 사용 하는 경우이 단계는 적용 되지 않습니다. 이 명령은 건너뛰고 계속 해 서의 통합에 안전 합니다.

   ```powershell
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

5. 새로 고침 토큰을 사용 하려면 관리자 권한 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   ```powershell
   Set-ADFSRelyingPartyTrust -TargetName AzureStack -TokenLifeTime 1440
   ```

## <a name="spn-creation"></a>SPN 만들기

서비스 사용자 이름 (SPN) 인증을 위해 사용 해야 하는 많은 시나리오가 있습니다. 다음은 몇 가지 예입니다.

- Azure 스택의 AD FS 배포와 CLI 사용
- System Center 관리 팩에 대 한 AD FS와 함께 배포 될 때 Azure 스택
- 리소스 공급자에서 AD FS와 함께 배포 될 때 Azure 스택
- 다양 한 응용 프로그램
- 비 대화형 로그온 필요

> [!Important]  
> AD FS는 대화형 로그온 세션을 지원합니다. 자동화 된 시나리오에 대 한 비 대화형 로그온을 필요한 경우 SPN을 사용 해야 합니다.

SPN 만들기에 대 한 자세한 내용은 참조 [AD FS에 대 한 서비스 보안 주체를 만들](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs)합니다.


## <a name="troubleshooting"></a>문제 해결

### <a name="configuration-rollback"></a>구성 롤백

오류가 발생 환경에 인증할 수 없습니다 더 이상 상태에서를 해제 하는 rollback 옵션을 사용할 수 없습니다.

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   ```powershell
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 다음 cmdlet을 실행 하십시오.

   ```powershell
   Reset-DatacenterIntegationConfiguration
   ```

   롤백 작업을 실행 한 후 모든 구성 변경 내용이 롤백됩니다. 기본 제공 인증만 **CloudAdmin** 사용자가 가능 합니다.

   > [!IMPORTANT]
   > 기본 공급자 구독의 원래 소유자를 구성 해야 합니다.

   ```powershell
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>추가 로그를 수집합니다.

Cmdlet 중 하나라도 실패할 경우를 사용 하 여 추가 로그를 수집할 수 있습니다는 `Get-Azurestacklogs` cmdlet.

1. 관리자 권한 Windows PowerShell 세션을 열고 다음 명령을 실행 합니다.

   ```powershell
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. 다음 cmdlet을 실행 합니다.

   ```powershell
   Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE
   ```


## <a name="next-steps"></a>다음 단계

[외부 모니터링 솔루션을 통합](azure-stack-integrate-monitor.md)
