---
title: 'Azure Active Directory Domain Services: Kerberos 제한 위임 활성화 | Microsoft Docs'
description: Azure Active Directory Domain Services 관리되는 도메인에서 Kerberos 제한 위임 활성화
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: maheshu
ms.openlocfilehash: 6ed797ca25161919ccf5e69be0073a67bfcef6d6
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>관리되는 도메인에서 KCD(Kerberos 제한 위임) 구성
대부분의 응용 프로그램은 사용자의 컨텍스트에서 리소스에 액세스해야 합니다. Active Directory는 이 사용 사례가 가능한 Kerberos 위임이라고 하는 메커니즘을 지원합니다. 또한 사용자의 컨텍스트에서 특정 리소스에만 액세스할 수 있도록 위임을 제한할 수 있습니다. Azure AD Domain Services 관리되는 도메인은 더 안전하게 잠겨 있으므로 기존의 Active Directory 도메인과는 다릅니다.

이 문서는 Azure AD Domain Services 관리되는 도메인에서 Kerberos 제한 위임을 구성하는 방법을 보여 줍니다.

## <a name="kerberos-constrained-delegation-kcd"></a>Kerberos 제한 위임(KCD)
Kerberos 위임을 사용하면 리소스에 액세스하도록 계정을 다른 보안 주체(예: 사용자)로 가장할 수 있습니다. 웹 응용 프로그램을 사용자의 컨텍스트에서 백 엔드 웹 API에 액세스하는 것으로 간주합니다. 이 예제에서는 웹 응용 프로그램(서비스 계정 또는 컴퓨터/컴퓨터 계정의 컨텍스트에서 실행)이 리소스(백 엔드 웹 API)에 액세스할 때 사용자를 가장합니다. Kerberos 위임은 사용자의 컨텍스트에서 가장한 계정이 액세스할 수 있는 리소스를 제한하지 않으므로 안전하지 않습니다.

Kerberos 제한 위임(KCD)은 특정 서버가 사용자를 대신하여 작동할 수 있는 서비스/리소스를 제한합니다. 기존의 KCD는 서비스에 대한 도메인 계정을 구성하기 위해 도메인 관리자 권한이 필요하고 계정을 단일 도메인으로 제한합니다.

또한 기존의 KCD에는 이것과 관련된 몇 가지 문제가 있습니다. 이전 운영 체제에서 도메인 관리자가 서비스에 대한 계정 기반 KCD를 구성한 경우, 서비스 관리자가 소유한 리소스 서비스에 위임된 프런트 엔드 서비스를 알 수 있는 유용한 방법이 없었습니다. 또한 리소스 서비스로 위임할 수 있었던 모든 프런트 엔드 서비스는 잠재적 공격 시점을 나타냈습니다. 프런트 엔드 서비스를 호스팅한 서버가 손상되었고 리소스 서비스로 위임하도록 구성되어 있는 경우 리소스 서비스도 손상되었습니다.

> [!NOTE]
> Azure AD Domain Services 관리되는 도메인에서 도메인 관리자 권한이 없습니다. 따라서 **관리되는 도메인에 기존 계정 기반 KCD를 구성할 수 없습니다**. 이 문서에 설명된 대로 리소스 기반 KCD를 사용합니다. 또한 이 메커니즘은 더 안전합니다.
>
>

## <a name="resource-based-kcd"></a>리소스 기반 KCD
Windows Server 2012 이후부터 서비스 관리자는 제한된 위임을 자신의 서비스에 맞게 구성할 수 있습니다. 이 모델에서 백 엔드 서비스 관리자는 KCD를 사용하여 특정 프런트 엔드 서비스를 허용하거나 거부할 수 있습니다. 이 모델은 **리소스 기반 KCD**라고도 합니다.

리소스 기반 KCD는 PowerShell을 사용하여 구성됩니다. 가장하는 계정이 컴퓨터 계정인지 아니면 사용자 계정/서비스 계정인지에 따라 `Set-ADComputer` 또는 `Set-ADUser` cmdlet을 사용합니다.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>관리되는 도메인에서 컴퓨터 계정에 대한 리소스 기반 KCD 구성
컴퓨터 'contoso100-webapp.contoso100.com'에서 실행되는 웹앱이 있다고 가정합니다.  도메인 사용자의 컨텍스트에서 리소스('contoso100-api.contoso100.com'에서 실행되는 웹 API)에 대한 액세스가 필요합니다. 이 시나리오에서 리소스 기반 KCD를 설정하는 방법은 다음과 같습니다.

1. [사용자 지정 OU를 만듭니다](active-directory-ds-admin-guide-create-ou.md). 이 사용자 지정 OU를 관리할 수 있는 권한을 관리되는 도메인 내의 사용자에게 위임할 수 있습니다.
2. 두 가상 머신(하나는 웹앱 실행, 다른 하나는 웹 API 실행)을 관리되는 도메인에 가입합니다. 사용자 지정 OU에 이러한 컴퓨터 계정을 만듭니다.
3. 이제, 다음 PowerShell 명령을 사용하여 리소스 기반 KCD를 구성합니다.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> 웹앱과 웹 API에 대한 컴퓨터 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 'AAD DC 컴퓨터' 컨테이너의 컴퓨터 계정에 대해서는 리소스 기반 KCD를 구성할 수 없습니다.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>관리되는 도메인에서 사용자 계정에 대한 리소스 기반 KCD 구성
서비스 계정 'appsvc'로 실행되는 웹앱이 있고 도메인 사용자의 컨텍스트에서 리소스(서비스 계정 'backendsvc'로 실행되는 웹 API)에 대한 액세스가 필요하다고 가정합니다. 이 시나리오에서 리소스 기반 KCD를 설정하는 방법은 다음과 같습니다.

1. [사용자 지정 OU를 만듭니다](active-directory-ds-admin-guide-create-ou.md). 이 사용자 지정 OU를 관리할 수 있는 권한을 관리되는 도메인 내의 사용자에게 위임할 수 있습니다.
2. 백 엔드 웹 API/리소스를 실행 중인 가상 머신을 관리되는 도메인에 가입합니다. 사용자 지정 OU에 해당 컴퓨터 계정을 만듭니다.
3. 사용자 지정 OU 내에서 웹앱을 실행하는 데 사용되는 서비스 계정(예: 'appsvc')을 만듭니다.
4. 이제, 다음 PowerShell 명령을 사용하여 리소스 기반 KCD를 구성합니다.

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> 백 엔드 웹 API에 대한 컴퓨터 계정과 서비스 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 'AAD DC 컴퓨터' 컨테이너의 컴퓨터 계정 또는 기본 제공 'AAD DC 사용자' 컨테이너의 사용자 계정에 대해서는 리소스 기반 KCD를 구성할 수 없습니다. 따라서, 리소스 기반 KCD를 설정하는 데는 Azure AD에서 동기화된 사용자 계정을 사용할 수 없습니다.
>

## <a name="related-content"></a>관련 콘텐츠
* [Azure AD Domain Services - 시작 가이드](active-directory-ds-getting-started.md)
* [Kerberos 제한 위임 개요](https://technet.microsoft.com/library/jj553400.aspx)
