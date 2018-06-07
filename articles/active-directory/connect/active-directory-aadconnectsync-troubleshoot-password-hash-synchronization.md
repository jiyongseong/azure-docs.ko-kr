---
title: Azure AD Connect 동기화로 암호 해시 동기화 문제 해결 | Microsoft Docs
description: 이 문서에서는 암호 해시 동기화 문제를 해결하는 방법에 대한 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: billmath
ms.openlocfilehash: bcf266813476c682d47bfd483db77f5d8b73837a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결
이 항목에서는 암호 해시 동기화 문제를 해결하는 방법에 대한 단계를 제공합니다. 암호가 예상대로 동기화되지 않으면 사용자의 하위 집합 또는 모든 사용자의 암호일 수 있습니다.

버전 1.1.614.0 이상인 Azure AD(Azure Active Directory) Connect 배포의 경우, 암호 해시 동기화 문제를 해결하는 데 마법사에서 문제 해결 작업을 사용할 수 있습니다.

* 암호가 동기화되지 않는 문제가 있으면 [암호가 동기화되지 않음: 문제 해결 작업을 사용하여 문제 해결](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) 섹션을 참조하세요.

* 개별 개체에 문제가 있으면 [한 개체가 암호를 동기화하지 않음: 문제 해결 작업을 사용하여 문제 해결](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) 섹션을 참조하세요.

버전 1.1.524.0 이상인 배포의 경우, 이제 암호 해시 동기화 문제를 해결하는 데 사용할 수 있는 진단 cmdlet이 있습니다.

* 암호가 동기화되지 않는 문제가 있으면 [암호가 동기화되지 않음: 진단 cmdlet을 사용하여 문제 해결](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) 섹션을 참조하세요.

* 개별 개체에 문제가 있으면 [한 개체가 암호를 동기화하지 않음: 진단 cmdlet을 사용하여 문제 해결](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) 섹션을 참조하세요.

이전 버전의 Azure AD Connect 배포:

* 암호가 동기화되지 않는 문제가 있으면 [암호가 동기화되지 않음: 수동 문제 해결 단계](#no-passwords-are-synchronized-manual-troubleshooting-steps) 섹션을 참조하세요.

* 개별 개체에 문제가 있으면 [한 개체가 암호를 동기화하지 않음: 수동 문제 해결 단계](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) 섹션을 참조하세요.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>암호가 동기화되지 않음: 문제 해결 작업을 사용하여 문제 해결
문제 해결 작업을 사용하여 암호가 동기화되지 않은 이유를 파악할 수 있습니다.

> [!NOTE]
> 문제 해결 작업은 Azure AD Connect 버전 1.1.614.0 이상에서만 사용할 수 있습니다.

### <a name="run-the-troubleshooting-task"></a>문제 해결 작업 실행
암호가 동기화되지 않는 문제를 해결하려면

1. **관리자 권한으로 실행** 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.

2. `Set-ExecutionPolicy RemoteSigned` 또는 `Set-ExecutionPolicy Unrestricted`를 실행합니다.

3. Azure AD Connect 마법사를 시작합니다.

4. **추가 작업** 페이지로 이동하고 **문제 해결**을 선택하고 **다음**을 클릭합니다.

5. 문제 해결 페이지에서 **시작**을 클릭하여 PowerShell의 문제 해결 메뉴를 시작합니다.

6. 주 메뉴에서 **암호 해시 동기화 문제 해결**을 선택합니다.

7. 하위 메뉴에서 **암호 해시 동기화가 전혀 작동하지 않음**을 선택합니다.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>문제 해결 작업의 결과 이해
문제 해결 작업에서는 다음 검사를 수행합니다.

* Azure AD 테넌트에 암호 해시 동기화 기능이 사용되는지 확인합니다.

* Azure AD Connect 서버가 준비 모드에 있지 않은지 확인합니다.

* 각각의 기존 온-프레미스 Active Directory 커넥터(기존 Active Directory 포리스트에 해당)에 대해 다음을 수행합니다.

   * 암호 해시 동기화 기능이 사용되는지 확인합니다.
   
   * Windows 응용 프로그램 이벤트 로그에서 암호 해시 동기화 하트비트 이벤트를 검색합니다.

   * 온-프레미스 Active Directory 커넥터 아래의 각 Active Directory 도메인에 대해 다음을 수행합니다.

      * Azure AD Connect 서버에서 도메인에 연결할 수 있는지 확인합니다.

      * 온-프레미스 Active Directory 커넥터에서 사용되는 AD DS(Active Directory Domain Services) 계정에 올바른 사용자 이름, 암호 및 암호 해시 동기화에 필요한 권한이 있는지 확인합니다.

다음 다이어그램은 단일 도메인 온-프레미스 Active Directory 토폴로지에 대한 cmdlet의 결과를 보여 줍니다.

![암호 해시 동기화에 대한 진단 출력](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalgeneral.png)

이 섹션의 나머지 부분에서는 이 작업과, 해당 문제에서 반환되는 특정 결과에 대해 설명합니다.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>암호 해시 동기화 기능이 사용되지 않음
Azure AD Connect 마법사를 통해 암호 해시 동기화를 사용하도록 설정하지 않은 경우 다음 오류가 반환됩니다.

![암호 해시 동기화가 사용되지 않음](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Azure AD Connect 서버가 준비 모드에 있음
Azure AD Connect 서버가 준비 모드에 있으면 암호 해시 동기화가 일시적으로 사용되지 않고 다음 오류가 반환됩니다.

![Azure AD Connect 서버가 준비 모드에 있음](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>암호 해시 동기화 하트비트 이벤트 없음
각각의 온-프레미스 Active Directory 커넥터에 자체 암호 해시 동기화 채널이 있습니다. 암호 해시 동기화 채널이 설정되었으며 동기화할 암호 변경 내용이 없는 경우, Windows 응용 프로그램 이벤트 로그 아래에 하트비트 이벤트(EventId 654)가 30분마다 생성됩니다. 각각의 온-프레미스 Active Directory 커넥터에 대해 cmdlet은 지난 3시간 동안의 해당 하트비트 이벤트를 검색합니다. 하트비트 이벤트가 발견되면 다음 오류가 반환됩니다.

![암호 해시 동기화 하트비트 이벤트 없음](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>AD DS 계정에 올바른 사용 권한이 없음
온-프레미스 Active Directory 커넥터가 암호 해시 동기화에 사용하는 AD DS 계정에 적절한 사용 권한이 없으면 다음 오류가 반환됩니다.

![잘못된 자격 증명](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>잘못된 AD DS 계정 사용자 이름 또는 암호
온-프레미스 Active Directory 커넥터가 암호 해시 동기화에 사용하는 AD DS 계정에 잘못된 사용자 이름 또는 암호가 있으면 다음 오류가 반환됩니다.

![잘못된 자격 증명](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>한 개체가 암호를 동기화하지 않음: 문제 해결 작업을 사용하여 문제 해결

문제 해결 작업을 사용하여 한 개체가 암호를 동기화하지 않는 이유를 확인할 수 있습니다.

> [!NOTE]
> 문제 해결 작업은 Azure AD Connect 버전 1.1.614.0 이상에서만 사용할 수 있습니다.

### <a name="run-the-diagnostics-cmdlet"></a>진단 cmdlet 실행
특정 사용자 개체에 대한 문제 해결

1. **관리자 권한으로 실행** 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.

2. `Set-ExecutionPolicy RemoteSigned` 또는 `Set-ExecutionPolicy Unrestricted`를 실행합니다.

3. Azure AD Connect 마법사를 시작합니다.

4. **추가 작업** 페이지로 이동하고 **문제 해결**을 선택하고 **다음**을 클릭합니다.

5. 문제 해결 페이지에서 **시작**을 클릭하여 PowerShell의 문제 해결 메뉴를 시작합니다.

6. 주 메뉴에서 **암호 해시 동기화 문제 해결**을 선택합니다.

7. 하위 메뉴에서 **특정 사용자 계정에 대해 암호가 동기화되지 않음**을 선택합니다.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>문제 해결 작업의 결과 이해
문제 해결 작업에서는 다음 검사를 수행합니다.

* Active Directory 커넥터 공간, 메타버스 및 Azure AD 커넥터 공간에서 Active Directory 개체의 상태를 검사합니다.

* 암호 해시 동기화가 사용되고 Active Directory 개체에 적용되는 동기화 규칙이 있는지 확인합니다.

* 개체의 암호를 동기화하려는 마지막 시도의 결과를 검색하고 표시하려고 합니다.

다음 다이어그램은 단일 개체에 대한 암호 해시 동기화 문제를 해결하는 경우의 cmdlet 결과를 보여 줍니다.

![암호 해시 동기화에 대한 진단 출력 - 단일 개체](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectgeneral.png)

이 섹션의 나머지 부분에서는 cmdlet 및 해당 문제에서 반환되는 특정 결과에 대해 설명합니다.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>Active Directory 개체가 Azure AD로 내보내지지 않음
이 온-프레미스 Active Directory 계정에 대한 암호 해시 동기화는 Azure AD 테넌트에 해당 개체가 없으므로 실패합니다. 다음 오류가 반환됩니다.

![Azure AD 개체가 없음](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>사용자에게 임시 암호가 있음
현재, Azure AD Connect는 임시 암호를 Azure AD와 동기화하는 기능을 지원하지 않습니다. **다음 로그온할 때 암호 변경** 옵션이 온-프레미스 Active Directory 사용자에 설정되어 있으면 임시 암호로 간주됩니다. 다음 오류가 반환됩니다.

![임시 암호는 내보내지지 않음](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>마지막 암호 동기화 시도의 결과를 사용할 수 없음
기본적으로 Azure AD Connect는 7일 동안의 암호 해시 동기화 시도 결과를 저장합니다. 선택한 Active Directory 개체에 대한 결과를 사용할 수 없는 경우 다음 경고가 반환됩니다.

![단일 개체에 대한 진단 출력 - 암호 동기화 기록 없음](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>암호가 동기화되지 않음: 진단 cmdlet을 사용하여 문제 해결
`Invoke-ADSyncDiagnostics` cmdlet을 사용하여 암호가 동기화되지 않은 이유를 파악할 수 있습니다.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` cmdlet은 Azure AD Connect 버전 1.1.524.0 이상에서만 사용할 수 있습니다.

### <a name="run-the-diagnostics-cmdlet"></a>진단 cmdlet 실행
암호가 동기화되지 않는 문제를 해결하려면

1. **관리자 권한으로 실행** 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.

2. `Set-ExecutionPolicy RemoteSigned` 또는 `Set-ExecutionPolicy Unrestricted`를 실행합니다.

3. `Import-Module ADSyncDiagnostics`을 실행합니다.

4. `Invoke-ADSyncDiagnostics -PasswordSync`을 실행합니다.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>한 개체가 암호를 동기화하지 않음: 진단 cmdlet을 사용하여 문제 해결
`Invoke-ADSyncDiagnostics` cmdlet을 사용하여 한 개체가 암호를 동기화하지 않는 이유를 확인할 수 있습니다.

> [!NOTE]
> `Invoke-ADSyncDiagnostics` cmdlet은 Azure AD Connect 버전 1.1.524.0 이상에서만 사용할 수 있습니다.

### <a name="run-the-diagnostics-cmdlet"></a>진단 cmdlet 실행
사용자에 대해 암호가 동기화되지 않는 문제를 해결하려면

1. **관리자 권한으로 실행** 옵션을 사용하여 Azure AD Connect 서버에서 새 Windows PowerShell 세션을 엽니다.

2. `Set-ExecutionPolicy RemoteSigned` 또는 `Set-ExecutionPolicy Unrestricted`를 실행합니다.

3. `Import-Module ADSyncDiagnostics`을 실행합니다.

4. 다음 cmdlet을 실행합니다.
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   예: 
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>암호가 동기화되지 않음: 수동 문제 해결 단계
암호가 동기화되지 않는 이유를 확인하려면 다음 단계를 따르세요.

1. 연결 서버가 [스테이징 모드](active-directory-aadconnectsync-operations.md#staging-mode)인가요? 스테이징 모드의 서버는 암호를 동기화하지 않습니다.

2. [암호 동기화 설정 상태 가져오기](#get-the-status-of-password-sync-settings) 섹션에서 스크립트를 실행합니다. 암호 동기화 구성의 개요를 제공합니다.  

    ![암호 동기화 설정의 PowerShell 스크립트 출력](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/psverifyconfig.png)  

3. Azure AD에서 이 기능이 사용되지 않거나 동기화 채널 상태가 사용되지 않는 경우 Connect 설치 마법사를 실행합니다. **동기화 옵션 사용자 지정**을 선택하고 암호 동기화의 선택을 취소합니다. 이 변경 내용은 기능을 일시적으로 비활성화합니다. 그런 다음 마법사를 다시 실행하고 암호 동기화를 다시 사용합니다. 스크립트를 다시 실행하여 구성이 올바른지 확인합니다.

4. 이벤트 로그에서 오류를 찾습니다. 문제를 나타내는 다음 이벤트를 찾습니다.
    * 원본: "디렉터리 동기화" ID: 0, 611, 652, 655 이러한 이벤트가 표시되면 연결 문제가 있는 것입니다. 이벤트 로그 메시지에는 문제가 있는 포리스트 정보가 포함됩니다. 자세한 내용은 [연결 문제](#connectivity problem)를 참조하세요.

5. 하트비트가 표시되지 않거나 아무 작동도 진행되지 않으면 [모든 암호의 전체 동기화 트리거](#trigger-a-full-sync-of-all-passwords)를 실행합니다. 스크립트를 한 번만 실행합니다.

6. [암호를 동기화하지 않는 한 개체의 문제 해결](#one-object-is-not-synchronizing-passwords) 섹션을 참조하세요.

### <a name="connectivity-problems"></a>연결 문제

Azure AD와 연결되어 있나요?

계정에 모든 도메인에서 암호 해시를 읽는 데 필요한 권한이 있나요? 기본 설정을 사용하여 Connect를 설치한 경우 사용 권한은 이미 올바른 상태입니다. 

사용자 지정 설치를 사용한 경우 다음을 수행하여 사용 권한을 수동으로 설정합니다.
    
1. Active Directory Connector에서 사용되는 계정을 찾으려면 **Synchronization Service Manager**를 시작합니다. 
 
2. **커넥터**로 이동한 다음 문제를 해결하려는 온-프레미스 Active Directory 포리스트를 검색합니다. 
 
3. 커넥터를 선택하고 **속성**을 클릭합니다. 
 
4. **Active Directory 포리스트에 연결**로 이동합니다.  
    
    ![Active Directory 커넥터에서 사용되는 계정](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/connectoraccount.png)  
    계정이 있는 사용자 이름 및 도메인을 적어둡니다.
    
5. **Active Directory 사용자 및 컴퓨터**를 시작한 다음 앞에서 찾은 계정에 대해 포리스트에 있는 모든 도메인의 루트에서 다음 사용 권한이 설정되어 있는지 확인합니다.
    * 디렉터리 변경 내용 복제
    * 모든 디렉터리 변경 내용 복제

6. Azure AD Connect에서 도메인 컨트롤러에 연결할 수 있나요? Connect 서버에서 일부 도메인 컨트롤러에 연결할 수 없는 경우 **기본 설정 도메인 컨트롤러만 사용**을 구성합니다.  
    
    ![Active Directory 커넥터에서 사용되는 도메인 컨트롤러](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/preferreddc.png)  
    
7. **Synchronization Service Manager** 및 **디렉터리 파티션 구성**으로 돌아갑니다. 
 
8. **디렉터리 파티션 선택**에서 도메인을 선택하고 **기본 설정 도메인 컨트롤러만 사용** 확인란을 선택한 다음 **구성**을 클릭합니다. 

9. 목록에서 Connect가 암호 동기화에 사용해야 하는 도메인 컨트롤러를 입력합니다. 동일한 목록이 가져오기 및 내보내기에도 사용됩니다. 모든 도메인에 대해 이 단계를 수행합니다.

10. 스크립트에서 하트비트가 없음을 보여 주는 경우 [모든 암호의 전체 동기화 트리거](#trigger-a-full-sync-of-all-passwords)에서 스크립트를 실행합니다.

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>한 개체가 암호를 동기화하지 않음: 수동 문제 해결 단계
개체의 상태를 검토하여 암호 해시 동기화 문제를 쉽게 해결할 수 있습니다.

1. **Active Directory 사용자 및 컴퓨터**에서 해당 사용자를 검색하고 **다음 로그온할 때 반드시 암호 변경** 확인란이 선택 취소되어 있는지 확인합니다.  

    ![Active Directory 생산성 높은 암호](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/adprodpassword.png)  

    확인란이 선택된 경우 사용자에게 로그인하여 암호를 변경하도록 요청합니다. 임시 암호는 Azure AD와 동기화되지 않습니다.

2. Active Directory에서 암호가 올바른 것 같으면 동기화 엔진에서 사용자를 따릅니다. 온-프레미스 Active Directory에서 Azure AD까지 사용자를 따라 개체에 설명이 포함된 오류가 있는지 확인할 수 있습니다.

    a. [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)를 시작합니다.

    나. **커넥터**를 클릭합니다.

    다. 사용자가 있는 **Active Directory Connector**를 선택합니다.

    d. **커넥터 공간 검색**을 선택합니다.

    e. **범위** 상자에서 **DN 또는 앵커**를 선택한 다음 문제를 해결하려는 사용자의 전체 DN을 입력합니다.

    ![DN을 사용하여 커넥터 공간에서 사용자 검색](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/searchcs.png)  

    f. 원하는 사용자를 찾은 후 **속성**을 클릭하여 모든 특성을 확인합니다. 해당 사용자가 검색 결과에 없으면 [필터링 규칙](active-directory-aadconnectsync-configure-filtering.md)을 확인하고 [변경 내용 적용 및 확인](active-directory-aadconnectsync-configure-filtering.md#apply-and-verify-changes)을 실행하여 사용자가 Connect에 표시되도록 합니다.

    g. 지난주에 대한 개체의 암호 동기화 정보를 보려면 **로그**를 클릭합니다.  

    ![개체 로그 정보](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/csobjectlog.png)  

    개체 로그가 비어 있으면 Azure AD Connect가 Active Directory에서 암호 해시를 읽지 못한 것입니다. [연결 오류](#connectivity-errors) 문제를 계속 해결합니다. **success** 이외의 값이 표시되면 [암호 동기화 로그](#password-sync-log)의 테이블을 참조하세요.

    h. **계보** 탭을 선택한 다음 **PasswordSync** 열에서 하나 이상의 동기화 규칙이 **True**인지 확인합니다. 기본 구성에서 동기화 규칙의 이름은 **AD에서 들어오기 - User AccountEnabled**입니다.  

    ![사용자에 대한 계보 정보](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync.png)  

    i. **메타버스 개체 속성**을 클릭하여 사용자 특성 목록을 표시합니다.  

    ![메타버스 정보](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvpasswordsync.png)  

    **cloudFiltered** 특성이 없는지 확인합니다. 도메인 특성(domainFQDN 및 domainNetBios)이 예상 값을 갖는지 확인합니다.

    j. **커넥터** 탭을 클릭합니다. 온-프레미스 Active Directory 및 Azure AD 둘 다에 대한 커넥터가 표시되는지 확인합니다.

    ![메타버스 정보](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/mvconnectors.png)  

    k. Azure AD를 나타내는 행을 선택하고 **속성**을 클릭한 다음 **계보** 탭을 클릭합니다. 커넥터 공간 개체에 대한 **PasswordSync** 열의 아웃바운드 규칙이 **True**로 설정되어 있어야 합니다. 기본 구성에서 동기화 규칙의 이름은 **AAD로 나가기 - 사용자 조인**입니다.  

    ![커넥터 공간 개체 속성 대화 상자](./media/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>암호 동기화 로그
상태 열에는 다음과 같은 값을 포함할 수 있습니다.

| 상태 | 설명 |
| --- | --- |
| 성공 |암호가 성공적으로 동기화되었습니다. |
| FilteredByTarget |**다음 로그인할 때 반드시 암호 변경**으로 암호가 설정됩니다. 암호가 동기화되지 않았습니다. |
| NoTargetConnection |메타버스에 또는 Azure AD 커넥터 공간에 개체가 없습니다. |
| SourceConnectorNotPresent |개체를 온-프레미스 Active Directory Connector 공간에서 찾을 수 없습니다. |
| TargetNotExportedToDirectory |Azure AD 커넥터 공간에 있는 개체가 아직 내보내지지 않았습니다. |
| MigratedCheckDetailsForMoreInfo |로그 항목 1.0.9125.0 빌드 전에 만들어졌으며 레거시 상태로 표시됩니다. |
| 오류 |서비스에 알 수 없는 오류가 반환되었습니다. |
| 알 수 없음 |암호 해시의 배치를 처리하는 동안 오류가 발생했습니다.  |
| MissingAttribute |Azure AD Domain Services에 필요한 특정 특성(예: Kerberos 해시)을 사용할 수 없습니다. |
| RetryRequestedByTarget |Azure AD Domain Services에 필요한 특정 특성(예: Kerberos 해시)을 이전에 사용할 수 없었습니다. 사용자의 암호 해시를 다시 동기화하려고 합니다. |

## <a name="scripts-to-help-troubleshooting"></a>문제 해결에 도움이 되는 스크립트

### <a name="get-the-status-of-password-sync-settings"></a>암호 동기화 설정의 상태 가져오기
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>모든 암호의 전체 동기화 트리거
> [!NOTE]
> 이 스크립트는 한 번만 실행합니다. 이 스크립트를 두 번 이상 실행해야 한다면 문제가 있는 것입니다. 문제를 해결하려면 Microsoft 지원에 문의하세요.

다음 스크립트를 사용하여 모든 암호의 전체 동기화를 트리거할 수 있습니다.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect 동기화로 암호 해시 동기화 구현](active-directory-aadconnectsync-implement-password-hash-synchronization.md)
* [Azure AD Connect 동기화: 동기화 옵션 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
