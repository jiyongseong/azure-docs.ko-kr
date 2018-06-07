---
title: "Azure AD Domain Services: 암호 동기화 사용 | Microsoft Docs"
description: "Azure Active Directory Domain Services 시작"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2017
ms.author: maheshu
ms.openlocfilehash: 0f6204e8f0f779809cd9c657acbcbcf39d57d481
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 암호 동기화 사용
이전 작업에서 Azure AD(Azure Active Directory) 테넌트에 대해 Azure Active Directory Domain Services를 사용하도록 설정했습니다. 다음 작업은 NTLM(NT LAN Manager) 및 Kerberos 인증에 필요한 자격 증명 해시를 Azure AD Domain Services로 동기화하도록 설정하는 것입니다. 자격 증명 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다.

관련된 단계는 Azure AD Connect를 사용하여 온-프레미스 디렉터리에서 동기화된 사용자 계정과 클라우드 전용 사용자 계정에 대해 서로 다릅니다.

<br>
| **사용자 계정의 유형** | **수행할 단계** |
| --- | --- |
| **사용자 계정은 온-프레미스 디렉터리에서 동기화됩니다.** |**&#x2713;** [이 문서의 지침에 따르세요.](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) | 
| **Azure AD에서 만든 클라우드 사용자 계정** |**&#x2713;** [관리되는 도메인으로 클라우드 전용 사용자 계정의 암호 동기화](active-directory-ds-getting-started-password-sync.md) |
<br>

> [!TIP]
> **두 단계를 모두 완료해야 할 수도 있습니다.**
> Azure AD 테넌트에 클라우드 전용의 사용자와 온-프레미스 AD의 사용자가 있는 경우 두 단계를 모두 수행해야 합니다.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>작업 5: 온-프레미스 AD와 동기화된 사용자 계정에 대한 관리되는 도메인으로 암호 동기화 사용
동기화된 Azure AD 테넌트는 Azure AD Connect를 사용하여 조직의 온-프레미스 디렉터리와 동기화하도록 설정됩니다. 기본적으로 Azure AD Connect는 NTLM 및 Kerberos 자격 증명 해시를 Azure AD로 동기화하지 않습니다. Azure AD 도메인 서비스를 사용하려면 Azure AD Connect를 구성하여 NTLM 및 Kerberos 인증에 필요한 자격 증명 해시를 동기화해야 합니다. 다음 단계를 수행하면 필요한 자격 증명 해시를 온-프레미스 디렉터리에서 Azure AD 테넌트로 동기화할 수 있습니다.

> [!NOTE]
> **조직에 온-프레미스 환경의 디렉터리에서 동기화된 사용자 계정이 있는 경우 관리되는 도메인을 사용하려면 NTLM 및 Kerberos 해시 동기화를 사용하도록 설정해야 합니다.** 동기화된 사용자 계정은 온-프레미스 디렉터리에서 만든 계정이며, Azure AD Connect를 사용하여 Azure AD 테넌트로 동기화됩니다.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Azure AD Connect 설치 또는 업데이트
도메인에 가입한 컴퓨터에 Azure AD Connect의 최신 권장 릴리스를 설치합니다. Azure AD Connect 설치의 기존 인스턴스가 있는 경우 최신 버전의 Azure AD Connect를 사용하도록 업데이트해야 합니다. 이미 해결되었을 수도 있는 알려진 문제/버그를 방지하려면 최신 버전의 Azure AD Connect를 사용합니다.

**[Azure AD Connect 다운로드](http://www.microsoft.com/download/details.aspx?id=47594)**

권장 버전: **1.1.614.0** - 2017년 9월 5일에 게시되었습니다.

> [!WARNING]
> Azure AD 테넌트에 동기화할 레거시 암호 자격 증명(NTLM 및 Kerberos 인증에 필요)을 사용하도록 설정하려면 Azure AD Connect의 최신 권장 릴리스를 설치해야 합니다. 이 기능은 Azure AD Connect의 이전 릴리스 또는 레거시 DirSync 도구에서 사용할 수 없습니다.
>
>

Azure AD Connect에 대한 설치 지침은 [Azure AD Connect 시작](../active-directory/active-directory-aadconnect.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>NTLM 및 Kerberos 자격 증명 해시를 Azure AD에 동기화하도록 설정합니다.
각 AD 포리스트에서 다음 PowerShell 스크립트를 실행합니다. 스크립트에서 모든 온-프레미스 사용자의 NTLM 및 Kerberos 암호 해시를 Azure AD 테넌트에 동기화할 수 있습니다. 스크립트는 또한 Azure AD Connect에서 전체 동기화를 시작합니다.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

디렉터리의 크기(사용자, 그룹 등의 수)에 따라 자격 증명 해시를 Azure AD에 동기화하는 데는 시간이 걸립니다. 자격 증명 해시를 Azure AD에 동기화한 직후 Azure AD 도메인 서비스 관리되는 도메인에서 암호를 사용할 수 있습니다.

<br>

## <a name="related-content"></a>관련 콘텐츠
* [클라우드 전용 Azure AD 디렉터리에 대해 AAD 도메인 서비스에 대한 암호 동기화 활성화](active-directory-ds-getting-started-password-sync.md)
* [Azure AD 도메인 서비스 관리되는 도메인 관리](active-directory-ds-admin-guide-administer-domain.md)
* [Windows 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Red Hat Enterprise Linux 가상 컴퓨터를 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
