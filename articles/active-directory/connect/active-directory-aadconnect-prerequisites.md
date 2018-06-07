---
title: 'Azure AD Connect: 필수 조건 및 하드웨어 | Microsoft Docs'
description: 이 항목에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: billmath
ms.openlocfilehash: d6d6eadf0ae8996b019a0564715f843913101944
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2018
---
# <a name="prerequisites-for-azure-ad-connect"></a>Azure AD Connect에 대한 필수 조건
이 항목에서는 Azure AD Connect에 대한 필수 조건 및 하드웨어 요구 사항을 설명합니다.

## <a name="before-you-install-azure-ad-connect"></a>Azure AD Connect를 설치하기 전에
Azure AD Connect를 설치하기 전에 필요한 몇 가지 사항이 있습니다.

### <a name="azure-ad"></a>Azure AD
* Azure 구독 또는 [Azure 평가판 구독](https://azure.microsoft.com/pricing/free-trial/)입니다. 이 구독은 Azure AD Connect를 사용하지 않고 Azure Portal에 액세스하기 위해서만 필요합니다. PowerShell 또는 Office 365를 사용하는 경우, Azure AD Connect를 사용하기 위해 Azure 구독을 설치할 필요가 없습니다. 또한 Office 365 라이선스가 있는 경우 Office 365 포털을 사용할 수 있습니다. 또한 유료 Office 365 라이선스를 가지고 Office 365 포털에서 Azure Portal로 가져올 수 있습니다.
  * [Azure Portal](https://portal.azure.com)을 사용할 수도 있습니다. 이 포털에는 Azure AD 라이선스가 필요하지 않습니다.
* [도메인을 추가하고 확인합니다](../active-directory-domains-add-azure-portal.md) . 예를 들어, 사용자가 contoso.com을 사용하려는 경우 해당 도메인을 확인하고 contoso.onmicrosoft.com 기본 도메인을 사용하지 않도록 합니다.
* Azure AD 테넌트는 기본적으로 5만 개의 개체를 허용합니다. 도메인을 확인하는 경우, 제한은 30만 개의 개체로 늘어납니다. Azure AD에서 더 많은 개체가 필요한 경우 제한을 더 증가시키려면 지원 사례를 열어야 합니다. 개체가 50만 개 이상 필요한 경우 Office 365, Azure AD Basic, Azure AD Premium 또는 Enterprise Mobility 및 Security와 같은 라이선스가 필요합니다.
* ADSyncPrep은 Azure AD Connect에 대한 Active Directory 환경을 준비하는 데 사용되는 기능을 제공하는 PowerShell 스크립트 모듈입니다.  ADSyncPrep에는 [Azure AD Microsoft Online v1.1 PowerShell 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)이 필요합니다.  버전 2는 작동하지 않습니다.  `Install-Module` cmdlet을 사용하여 모듈을 설치할 수 있습니다.  자세한 내용은 제공된 링크를 참조하세요.

### <a name="prepare-your-on-premises-data"></a>온-프레미스 데이터 준비
* Azure AD 및 Office 365로 동기화하기 전에 [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac)를 사용하여 디렉터리의 중복 및 서식 문제 등의 오류가 있는지 식별합니다.
* [Azure AD에서 사용하도록 설정할 수 있는 선택적 동기화 기능](active-directory-aadconnectsyncservice-features.md) 을 검토하여 사용하도록 설정해야 할 기능을 평가합니다.

### <a name="on-premises-active-directory"></a>온-프레미스 Active Directory
* AD 스키마의 버전 및 포리스트 기능 수준은 Windows Server 2003 이상이어야 합니다. 도메인 컨트롤러는 스키마와 포레스트의 수준 요구 사항이 맞으면 어떤 버전도 실행할 수 있습니다.
* **비밀번호 쓰기 저장** 기능을 사용하려는 경우 도메인 컨트롤러가 Windows Server 2008(최신 SP 포함) 이상에 있어야 합니다. 또한 DC가 Windows Server 2008(R2 이전 버전)에 있는 경우 [핫픽스 KB2386717](http://support.microsoft.com/kb/2386717)을 적용해야 합니다.
* Azure AD에서 사용되는 도메인 컨트롤러는 쓰기 가능해야 합니다. RODC(읽기 전용 도메인 컨트롤러)를 사용하는 것은 **지원되지 않으며** Azure AD Connect는 쓰기 리디렉션을 따르지 않습니다.
* SLD(단일 레이블 도메인)를 사용하는 온-프레미스 포리스트/도메인은 **지원되지 않습니다**.
* "점으로 구분된"(이름에 마침표 "." 포함) NetBios 이름을 사용하는 온-프레미스 포리스트/도메인은 **지원되지 않습니다**.
* [Active Directory 휴지통을 사용하도록 설정](active-directory-aadconnectsync-recycle-bin.md)하는 것이 좋습니다.

### <a name="azure-ad-connect-server"></a>Azure AD Connect 서버
* Azure AD Connect는 Small Business Server 또는 Windows Server Essentials에 설치할 수 없습니다. 서버는 Windows Server Standard 이상을 사용해야 합니다.
* Azure AD Connect 서버에는 전체 GUI가 설치되어 있어야 합니다. Server Core에 설치하는 것은 **지원되지 않습니다**.
* Azure AD Connect는 반드시 Windows Server 2008 이상의 버전에 설치되어야 합니다. Express 설정을 사용하는 경우 이 서버는 도메인 컨트롤러 또는 멤버 서버일 수 있습니다. 사용자 지정 설정을 사용하는 경우 서버는 독립 실행형일 수 있고 도메인에 가입할 필요는 없습니다.
* Windows Server 2008 또는 Windows Server 2008 R2에 Azure AD Connect를 설치하는 경우 Windows 업데이트에서 최신 핫픽스를 적용해야 합니다. 패치가 적용되지 않은 서버에서는 설치를 시작할 수 없습니다.
* **암호 동기화**기능을 사용하려는 경우 Azure AD Connect 서버가 Windows Server 2008 R2 SP1 이상에 있어야 합니다.
* **그룹 관리 서비스 계정**을 사용하려는 경우 Azure AD Connect 서버가 Windows Server 2012 이상이어야 합니다.
* Azure AD Connect 서버에는 [.NET Framework 4.5.1](#component-prerequisites) 이상 및 [Microsoft PowerShell 3.0](#component-prerequisites) 이상이 설치되어 있어야 합니다.
* Azure AD Connect 서버에는 PowerShell 기록 그룹 정책을 사용하도록 설정되면 안됩니다.
* Active Directory Federation Services를 배포하는 경우 AD FS 또는 웹 응용 프로그램 프록시가 설치될 서버는 Windows Server 2012 R2 이상이어야 합니다. [Windows 원격 관리](#windows-remote-management) 를 사용할 수 있어야 합니다.
* Active Directory Federation Services를 배포하고 있는 경우 [SSL 인증서](#ssl-certificate-requirements)가 필요합니다.
* Active Directory Federation Services를 배포하고 있는 경우 [이름 확인](#name-resolution-for-federation-servers)을 구성해야 합니다.
* 전역 관리자가 MFA를 사용하도록 설정한 경우 URL **https://secure.aadcdn.microsoftonline-p.com**이 신뢰할 수 있는 사이트 목록에 있어야 합니다. MFA 챌린지를 묻는 메시지가 표시되기 전에 이 사이트를 추가하지 않은 경우 신뢰할 수 있는 사이트 목록에 추가하라는 메시지가 표시됩니다. Internet Explorer를 사용하여 신뢰할 수 있는 사이트에 추가할 수 있습니다.

### <a name="sql-server-used-by-azure-ad-connect"></a>Azure AD Connect에서 사용하는 SQL Server
* Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. 기본적으로 SQL Server 2012 Express LocalDB(SQL Server Express의 라이트 버전)가 설치됩니다. SQL Server Express는 약 100,000개의 개체를 관리할 수 있는 10GB의 용량을 제공합니다. 더 큰 볼륨의 디렉터리 개체 관리가 필요한 경우 설치 마법사가 SQL Server의 다른 설치를 가리키도록 해야 합니다.
* 별도의 SQL Server를 사용하는 경우 다음 요구 사항이 적용됩니다.
  * Azure AD Connect는 SQL Server 2008(최신 서비스 팩)에서 SQL Server 2016 SP1까지 Microsoft SQL Server의 모든 버전을 지원합니다. Microsoft Azure SQL Database는 데이터베이스로 **지원되지 않습니다** .
  * 대/소문자를 구분하지 않는 SQL 데이터 정렬을 사용해야 합니다. 이러한 데이터 정렬은 이름에 \_CI_를 사용하여 식별됩니다. 이름에 \_CS_를 사용하여 식별되는 대/소문자 구분 데이터 정렬을 사용하는 것은 **지원되지 않습니다**.
  * SQL 인스턴스당 동기화 엔진을 한 개만 사용할 수 있습니다. SQL 인스턴스를 FIM/MIM 동기화, DirSync 또는 Azure AD Sync와 공유하는 것은 **지원되지 않습니다**.

### <a name="accounts"></a>계정
* 통합하려는 Azure AD 테넌트에 대한 Azure AD 전역 관리자 계정. 이 계정은 **학교 또는 조직 계정**이어야 하며 **Microsoft 계정**이 될 수 없습니다.
* Express 설정을 사용하거나 DirSync에서 업그레이드하는 경우 로컬 Active Directory에 대한 엔터프라이즈 관리자 계정이 있어야 합니다.
* 사용자 지정 설정 설치 경로를 사용하는 경우 [Active Directory의 계정입니다](active-directory-aadconnect-accounts-permissions.md).

### <a name="connectivity"></a>연결
* Azure AD Connect 서버는 인트라넷 및 인터넷에 대해 DNS 확인을 해야 합니다. DNS 서버는 온-프레미스 Active Directory와 Azure AD 끝점 모두에 대해 이름을 확인할 수 있어야 합니다.
* 인트라넷에 방화벽이 있고 Azure AD Connect 서버와 도메인 컨트롤러 사이에서 포트를 열어야 하는 경우 자세한 내용은 [Azure AD Connect 포트](active-directory-aadconnect-ports.md) 를 참조하세요.
* 프록시 또는 방화벽에 액세스할 수 있는 URL을 제한하는 경우 [Office 365 URL 및 IP 주소 범위 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 에서 설명한 URL이 열려야 합니다.
  * 독일의 Microsoft Cloud 또는 Microsoft Azure Government 클라우드를 사용하는 경우 URL은 [Azure AD Connect 동기화 서비스 인스턴스가 고려 사항](active-directory-aadconnect-instances.md) 을 참조하세요.
* Azure AD Connect(1.1.614.0 버전 이상)는 기본적으로 TLS 1.2를 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. 기본 운영 체제에서 TLS 1.2를 사용할 수 없는 경우 Azure AD Connect에 이전 프로토콜(TLS 1.1 및 TLS 1.0)이 점차적으로 다시 적용됩니다. 예를 들어 Windows Server 2008에서 TLS 1.1 또는 TLS 1.2를 지원하지 않기 때문에 Windows Server 2008에서 실행되는 Azure AD Connect는 TLS 1.0을 사용합니다.
* 1.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진과 Azure AD 간의 통신을 암호화합니다. TLS 1.2로 변경하려면 [Azure AD Connect에 TLS 1.2 사용](#enable-tls-12-for-azure-ad-connect)의 단계를 수행합니다.
* 인터넷에 연결하는 데 아웃바운드 프록시를 사용하는 경우 설치 마법사 및 Azure AD Connect 동기화에서 인터넷 및 Azure AD에 연결하려면 **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** 파일에 다음 설정을 추가해야 합니다. 이 텍스트는 파일의 맨 아래에 입력해야 합니다. 이 코드에서 &lt;PROXYADRESS&gt;는 실제 프록시 IP 주소 또는 호스트 이름을 나타냅니다.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* 프록시 서버에 인증이 필요한 경우 [서비스 계정](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account)이 도메인에 있어야 하고 사용자 지정 설정 설치 경로를 사용하여 [사용자 지정 서비스 계정](active-directory-aadconnect-get-started-custom.md#install-required-components)을 지정해야 합니다. 또한 Machine.config와 서로 다른 변경을 해야 합니다. machine.config에서 이 변경 내용을 적용하면 설치 마법사와 동기화 엔진이 프록시 서버의 인증 요청에 응답합니다. **구성** 페이지를 제외하고 모든 설치 마법사 페이지에서 로그인한 사용자의 자격 증명이 사용됩니다. 설치 마법사의 끝에 나오는 **구성** 페이지에서 컨텍스트가 이전에 만든 [서비스 계정](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account)으로 전환됩니다. machine.config 섹션은 다음과 같이 표시됩니다.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Azure AD Connect에서 디렉터리 동기화의 일부로 웹 요청을 Azure AD로 보내면 Azure AD에서 응답하는 데 최대 5분이 걸릴 수 있습니다. 프록시 서버에서 연결 유휴 시간 제한 구성을 사용하는 것이 일반적입니다. 구성이 6분 이상으로 설정되었는지 확인해 주세요.

자세한 내용은 [기본 프록시 요소](https://msdn.microsoft.com/library/kd3cf2ex.aspx)에 대한 MSDN을 참조하세요.  
연결에 문제가 있는 경우 [연결 문제 해결](active-directory-aadconnect-troubleshoot-connectivity.md)을 참조하세요.

### <a name="other"></a>기타
* 선택 사항: 동기화를 확인할 테스트 사용자 계정

## <a name="component-prerequisites"></a>구성 요소 필수 조건
### <a name="powershell-and-net-framework"></a>PowerShell 및 .Net Framework
Azure AD Connect는 Microsoft PowerShell 및 .NET Framework 4.5.1에 따라 다릅니다. 서버에 이 버전 이상을 설치해야 합니다. Windows Server 버전에 따라 다음을 수행합니다.

* Windows Server 2012R2
  * Microsoft PowerShell은 기본적으로 설치되므로 별도의 작업이 필요하지 않습니다.
  * .NET Framework 4.5.1 이후 릴리스는 Windows 업데이트를 통해 제공됩니다. 제어판에서 Windows Server에 최신 업데이트를 설치했는지 확인합니다.
* Windows Server 2008R2 및 Windows Server 2012
  * 최신 버전의 Microsoft PowerShell은 **Windows Management Framework 4.0**에서 사용할 수 있으며 이는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
  * .NET Framework 4.5.1과 이후 릴리스는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
* Windows Server 2008
  * 지원되는 최신 버전의 PowerShell은 **Windows Management Framework 3.0**에서 사용할 수 있으며 이는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.
  * .NET Framework 4.5.1과 이후 릴리스는 [Microsoft 다운로드 센터](http://www.microsoft.com/downloads)에서 찾을 수 있습니다.

### <a name="enable-tls-12-for-azure-ad-connect"></a>Azure AD Connect에 TLS 1.2 사용
1.1.614.0 버전 이전의 Azure AD Connect는 기본적으로 TLS 1.0을 사용하여 동기화 엔진 서버와 Azure AD 간의 통신을 암호화합니다. 서버에서 기본적으로 TLS 1.2를 사용하도록 .Net 응용 프로그램을 구성하여 이를 변경할 수 있습니다. TLS 1.2에 대한 자세한 내용은 [Microsoft 보안 권고 2960358](https://technet.microsoft.com/security/advisory/2960358)에서 찾을 수 있습니다.

1. Windows Server 2008에서는 TLS 1.2를 사용할 수 없습니다. Windows Server 2008R2 이상이 필요합니다. 운영 체제에 대해 .Net 4.5.1 핫픽스를 설치했는지 확인하고 [Microsoft 보안 권고 2960358](https://technet.microsoft.com/security/advisory/2960358)을 참조하세요. 이 핫픽스 또는 이후 릴리스를 서버에 이미 설치했을 수 있습니다.
2. Windows Server 2008R2를 사용하는 경우 TLS 1.2가 사용되도록 설정되어 있는지 확인합니다. Windows Server 2012 서버 및 이후 버전에서는 TLS 1.2가 이미 사용되도록 설정되어 있습니다.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. 모든 운영 체제에 대해 이 레지스트리 키를 설정하고 서버를 다시 시작합니다.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. 동기화 엔진 서버와 원격 SQL Server 간에 TLS 1.2를 사용하도록 설정하려는 경우 [Microsoft SQL Server에 대한 TLS 1.2 지원](https://support.microsoft.com/kb/3135244)을 위해 필요한 버전이 설치되어 있는지 확인합니다.

## <a name="prerequisites-for-federation-installation-and-configuration"></a>페더레이션 설치 및 구성을 위한 필수 조건
### <a name="windows-remote-management"></a>Windows 원격 관리
Azure AD Connect를 사용하여 웹 응용 프로그램 프록시 또는 Active Directory Federation Services를 배포하는 경우 아래 요구 사항을 확인합니다.

* 대상 서버가 도메인에 가입된 경우 Windows 원격 관리가 사용되는지 확인합니다.
  * 관리자 권한 PSH 명령 창에서 `Enable-PSRemoting –force`
* 대상 서버가 도메인에 연결된 WAP 컴퓨터가 아닌 경우, 몇 가지 추가 요구 사항이 있습니다.
  * 대상 컴퓨터(WAP 컴퓨터):
    * Winrm(Windows Remote Management / WS-Management) 서비스가 서비스 스냅인을 통해 실행되는지 확인합니다.
    * 관리자 권한 PSH 명령 창에서 `Enable-PSRemoting –force`
  * 마법사를 실행 중인 컴퓨터(대상 컴퓨터가 도메인에 가입되지 않거나 신뢰할 수 없는 도메인인 경우):
    * 관리자 권한 PSH 명령 창에서 `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * 서버 관리자:
      * DMZ WAP 호스트를 컴퓨터 풀에 추가(서버 관리자 -> 관리 -> 서버 추가...DNS 탭 사용)
      * 서버 관리자 모든 서버 탭: WAP 서버를 마우스 오른쪽 단추로 클릭하고 다음으로 관리...를 선택하고, WAP 컴퓨터에 대한 로컬(도메인이 아닌) 자격 증명을 입력합니다.
      * 서버 관리자 모든 서버 탭에서 원격 PSH 연결 유효성을 검사하려면, WAP 서버를 마우스 오른쪽 단추로 클릭하고 Windows PowerShell을 선택합니다. 원격 PowerShell 세션을 설정할 수 있도록 원격 PSH 세션을 열어야 합니다.

### <a name="ssl-certificate-requirements"></a>SSL 인증서 요구 사항
* AD FS 팜의 모든 노드 및 모든 웹 응용 프로그램 프록시 서버에서 동일한 SSL 인증서를 사용하는 것이 가장 좋습니다.
* 인증서는 X509 인증서여야 합니다.
* 테스트 랩 환경의 페더레이션 서버에서 자체 서명된 인증서를 사용할 수 있습니다. 그러나 프로덕션 환경에서는 공용 CA에서 인증서를 가져오는 것이 좋습니다.
  * 공개적으로 신뢰할 수 없는 인증서를 사용하면 각 웹 응용 프로그램 프록시 서버에 설치된 인증서가 모든 페더레이션 서버 및 로컬 서버에서 신뢰할 수 있는 지 확인합니다.
* 인증서의 ID는 페더레이션 서비스 이름(예: sts.contoso.com)과 일치해야 합니다.
  * ID는 dNSName 유형의 주체 대체 이름(SAN)의 확장 유형이거나, SAN 항목이 없는 경우 공통 이름으로 지정된 주제 이름입니다.  
  * 그 중 하나가 페더레이션 서비스 이름과 일치하는 경우 여러 SAN 항목이 인증서에 있을 수 있습니다.
  * 작업 공간 연결을 사용하도록 하려는 경우 **enterpriseregistration** 값과 함께 추가 SAN이 필요합니다. 이어서 조직의 사용자 이름(UPN) 접미사를(예:  **enterpriseregistration.contoso.com**) 붙입니다.
* CryptoAPI 다음 세대(CNG) 및 키 저장소 공급자를 기반으로 하는 인증서는 지원되지 않습니다. 즉, KSP(키 저장소 공급자)가 아닌 CSP(암호화 서비스 공급자)를 기반으로 인증서를 사용해야 합니다.
* 와일드카드 인증서가 지원됩니다.

### <a name="name-resolution-for-federation-servers"></a>페더레이션 서버에 대한 이름 확인
* 인트라넷(내부 DNS 서버) 및 엑스트라넷(도메인 등록 기관을 통한 공용 DNS) 모두의 AD FS 페더레이션 서비스 이름(예: sts.contoso.com)에 대한 DNS 레코드를 설정합니다. 인트라넷 DNS 레코드의 경우, CNAME 레코드가 아닌 A 레코드를 사용해야 합니다. 도메인 연결된 컴퓨터에서 제대로 작동 하려면 windows 인증이 필요합니다.
* 둘 이상의 AD FS 서버 또는 웹 응용 프로그램 프록시 서버를 배포하는 경우 사용자 부하 분산 장치를 구성하고 AD FS 페더레이션 서비스 이름(예: sts.contoso.com)에 대한 DNS 레코드가 부하 분산 장치를 가리키는지 확인합니다.
* Windows 통합된 인증이 인트라넷에서 Internet Explorer를 사용하여 브라우저 응용 프로그램을 작동하려면 AD FS 페더레이션 서비스 이름(예: sts.contoso.com)이 IE에서 인트라넷 영역에 추가되었는지를 확인합니다. 그룹 정책을 통해 제어되고 모든 사용자 도메인에 가입된 컴퓨터에 배포될 수 있습니다.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect 지원 구성 요소
다음은 Azure AD Connect를 설치한 서버에서 Azure AD Connect가 설치되는 데 필요한 구성 요소의 목록입니다. 이 목록은 기본 Express 설치에 해당합니다. 동기화 서비스 설치 페이지에서 다른 SQL Server를 사용하도록 선택하는 경우 SQL Express LocalDB는 로컬에 설치되지 않습니다.

* Azure AD Connect Health
* IT 전문가를 위한 Microsoft Online Services 로그인 도우미(설치되지만 종속되지 않음)
* Microsoft SQL Server 2012 명령줄 유틸리티
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 재배포 패키지

## <a name="hardware-requirements-for-azure-ad-connect"></a>Azure AD Connect의 하드웨어 요구 사항
아래 테이블은 Azure AD Connect 동기화 컴퓨터의 최소 요구 사항을 보여 줍니다.

| Active Directory의 개체 수 | CPU | 메모리 | 하드 드라이브 크기 |
| --- | --- | --- | --- |
| 10,000개 미만 |1.6GHz |4GB |70GB |
| 10,000–50,000개 |1.6GHz |4GB |70GB |
| 50,000–100,000개 |1.6GHz |16GB |100GB |
| 처음 사용자용 SQL Server가 필요한 100,000개 이상의 개체 | | | |
| 100,000–300,000개 |1.6GHz |32GB |300GB |
| 300,000–600,000개 |1.6GHz |32GB |450GB |
| 600,000개 초과 |1.6GHz |32GB |500GB |

AD FS 또는 웹 응용 프로그램 서버를 실행하는 컴퓨터에 대한 최소 요구 사항은 다음과 같습니다.

* CPU: 듀얼 코어 1.6GHz 이상
* 메모리: 2GB 이상
* Azure VM: A2 구성 이상

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
