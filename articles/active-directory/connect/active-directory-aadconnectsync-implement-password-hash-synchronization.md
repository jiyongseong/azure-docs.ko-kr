---
title: Azure AD Connect 동기화로 암호 해시 동기화 구현 | Microsoft Docs
description: 암호 해시 동기화 작동 방식 및 설정 방법에 대한 정보를 제공합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: billmath
ms.openlocfilehash: c223091e423d0f342f14424c58d6b7447cda50e8
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현
이 문서에서는 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure Active Directory(Azure AD) 인스턴스로 사용자 암호를 동기화하는 데 필요한 정보를 제공합니다.

## <a name="what-is-password-hash-synchronization"></a>암호 해시 동기화 정의
암호를 잊어버려서 작업을 수행하지 못할 확률은 기억해야 하는 암호의 수와 관련됩니다. 기억할 암호가 많을 수록 잊을 확률이 높습니다. 암호 다시 설정 및 기타 암호 관련 문제에 대한 질문 및 호출은 많은 helpdesk 리소스가 필요합니다.

암호 해시 동기화는 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 사용자 암호를 동기화하는 데 사용되는 기능입니다.
이 기능을 사용하여 Office 365, Microsoft Intune, CRM Online 및 Azure Active Directory Domain Services(Azure AD DS)와 같은 Azure AD 서비스에 로그인합니다. 온-프레미스 Active Directory 인스턴스에 로그인하기 위해 사용하는 암호와 동일한 암호를 사용하여 서비스에 로그인합니다.

![Azure AD Connect의 정의](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

암호의 수를 줄여서 사용자는 암호를 하나만 유지해야 합니다. 암호 해시 동기화를 사용하면 다음을 수행하는 데 도움이 됩니다.

* 사용자의 생산성 향상.
* 지원 센터 비용 절감.  

또한 [AD FS(Active Directory Federation Services)와 페더레이션](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect)을 사용하도록 선택하는 경우, 필요에 따라 AD FS 인프라에 장애가 발생할 경우 백업으로 암호 해시 동기화를 설정할 수 있습니다.

암호 해시 동기화는 Azure AD Connect 동기화를 통한 디렉터리 동기화 기능 구현의 확장판입니다. 사용자 환경에서 암호 해시 동기화를 사용하려면 다음이 필요합니다.

* Azure AD Connect 설치.  
* 온-프레미스 Active Directory 인스턴스 및 Azure Active Directory 인스턴스 간에 디렉터리 동기화 구성.
* 암호 해시 동기화 사용.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)을 참조하세요.

> [!NOTE]
> FIPS 및 암호 해시 동기화에 대해 구성된 Azure Active Directory Domain Services에 대한 자세한 내용은 문서 뒷부분에 나오는 “암호 해시 동기화 및 FIPS”를 참조하세요.
>
>

## <a name="how-password-hash-synchronization-works"></a>암호 해시 동기화 작동 방식
Active Directory 도메인 서비스는 실제 사용자 암호의 해시 값 표현 형태로 암호를 저장합니다. 해시 값은 단방향 수학 함수(*해시 알고리즘*)의 결과입니다. 암호의 일반 텍스트 버전에 대한 함수의 결과를 되돌릴 수 있는 방법이 없습니다. 암호 해시를 사용하여 온-프레미스 네트워크에 로그인할 수 없습니다.

암호를 동기화 하기 위해, Azure AD Connect 동기화는 온-프레미스 Active Directory 인스턴스에서 암호 해시를 추출합니다. Azure Active Directory 인증 서비스로 동기화 되기 전에 암호 해시에 추가적인 보안 처리가 적용됩니다. 암호는 각 사용자 기본별로 동기화되고 시간 순서대로 동기화됩니다.

암호 해시 동기화 과정의 실제 흐름은 DisplayName 또는 메일 주소와 같은 사용자 데이터 동기화와 비슷합니다. 그러나 암호는 다른 특성에 대한 표준 디렉터리 동기화 창보다 더 자주 동기화됩니다. 암호 해시 동기화 프로세스는 2분마다 실행됩니다. 이 프로세스의 빈도를 수정할 수 없습니다. 암호를 동기화할 경우 기존 클라우드 암호를 덮어씁니다.

암호 해시 동기화 기능을 사용하도록 처음으로 설정하면 범위 내 모든 사용자 암호에 대한 초기 동기화를 수행합니다. 동기화할 사용자 암호의 하위 집합을 명시적으로 정의할 수 없습니다.

온-프레미스 암호를 변경하면, 업데이트된 암호는 대개 몇 분 내에 동기화됩니다.
암호 해시 동기화 기능은 실패한 사용자 암호 동기화를 자동으로 다시 시도합니다. 암호를 동기화하는 동안 오류가 발생하면 이벤트 뷰어에 오류가 기록됩니다.

암호 동기화는 현재 로그인된 사용자에게 아무런 영향도 미치지 않습니다.
클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 세션이 즉시 영향을 받지는 않습니다. 하지만, 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

사용자는 회사 네트워크에 로그인되어 있는지 여부에 관계없이 Azure AD에 인증하려면 회사 자격 증명을 다시 입력해야 합니다. 하지만 사용자가 로그인 시 로그인 유지(KMSI) 확인란을 선택하면 이러한 패턴을 최소화할 수 있습니다. 이 확인란을 선택하면 짧은 기간 동안 인증을 우회하는 세션 쿠키가 설정됩니다. KMSI 동작은 Azure AD 관리자가 설정 또는 해제할 수 있습니다.

> [!NOTE]
> 암호 동기화는 Active Directory의 개체 형식 사용자에만 지원됩니다. iNetOrgPerson 개체 형식에 대해 지원되지 않습니다.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>암호 해시 동기화의 작동 방식에 대한 자세한 설명
다음은 Active Directory와 Azure AD 간에 암호 해시 동기화가 작동하는 방식에 대한 자세한 설명입니다.

![자세한 암호 흐름](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. 2분마다 AD Connect 서버의 암호 해시 동기화 에이전트는 DC 간 데이터 동기화에 사용되는 표준 [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) 복제 프로토콜을 통해 DC에서 저장된 암호 해시(unicodePwd 특성)를 요청합니다. 암호 해시를 얻으려면 서비스 계정에 디렉터리 변경 내용 복제 및 모든 디렉터리 변경 내용 복제 AD 권한(설치 시 기본적으로 부여)이 있어야 합니다.
2. 전송하기 전에 DC는 RPC 세션 키의 [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) 해시인 키와 솔트를 사용하여 MD4 암호 해시를 암호화합니다. 그런 다음, RPC를 통해 암호 해시 동기화 에이전트에 결과를 전송합니다. 또한 DC는 DC 복제 프로토콜을 사용하여 동기화 에이전트에 솔트를 전달하고, 따라서 에이전트는 봉투(Envelope)를 해독할 수 있게 됩니다.
3.  암호 해시 동기화 에이전트는 봉투(Envelope)를 암호화한 후 [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx)와 솔트를 사용하여 수신한 데이터를 원래 MD4 형식으로 해독하는 키를 생성합니다. 암호 해시 동기화 에이전트는 어떤 경우에도 일반 텍스트 암호에 액세스할 수 없습니다. 암호 해시 동기화 에이전트의 MD5 사용은 DC와의 복제 프로토콜 호환성으로 그 용도가 엄격하게 제한되며 DC와 암호 해시 동기화 에이전트 간의 온-프레미스에서만 사용됩니다.
4.  암호 해시 동기화 에이전트는 해시를 32바이트 16진수 문자열로 변환한 후 UTF-16 인코딩을 사용하여 이 문자열을 다시 이진으로 변환하는 방법으로 16바이트 이진 암호 해시를 64바이트로 확장합니다.
5.  암호 해시 동기화 에이전트는 10바이트 길이 솔트로 구성된 솔트를 64비트 이진 파일에 추가하여 원래 해시의 보안을 강화합니다.
6.  그런 다음, 암호 해시 동기화 에이전트는 MD4 해시와 솔트를 결합하고 [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) 함수에 입력합니다. [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) 키 지정 해시 알고리즘이 1000번 반복하여 사용됩니다. 
7.  암호 해시 동기화 에이전트는 결과 32바이트 해시를 가져오고, 솔트와 SHA256 반복 횟수를 해시에 연결하고(Azure AD가 사용할 수 있도록), SSL을 통해 Azure AD Connect에서 Azure AD로 문자열을 전송합니다.</br> 
8.  사용자가 Azure AD에 로그인을 시도하고 암호를 입력하면 암호가 동일한 MD4+salt+PBKDF2+HMAC-SHA256 프로세스를 통해 실행됩니다. 결과 해시가 Azure AD에 저장된 해시와 일치하고 사용자가 올바른 암호를 입력하면 해당 사용자가 인증됩니다. 

>[!Note] 
>원래 MD4 해시는 Azure AD로 전송되지 않습니다. 대신 원래 MD4 해시의 SHA256 해시가 전송됩니다. 결과적으로 Azure AD에 저장된 해시를 습득하더라도 온-프레미스 pass-the-hash 공격에 사용할 수 없습니다.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 암호 해시 동기화 작동 방식
암호 해시 동기화 기능을 사용하여 온-프레미스 암호를 [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md)에 동기화할 수도 있습니다. 이 시나리오에서는 Azure Active Directory Domain Services 인스턴스가 온-프레미스 Active Directory 인스턴스에서 사용할 수 있는 모든 방법을 사용하여 클라우드에서 사용자를 인증합니다. 이 시나리오의 환경은 온-프레미스 환경에서 ADMT(Active Directory 마이그레이션 도구)를 사용하는 것과 비슷합니다.

### <a name="security-considerations"></a>보안 고려 사항
암호를 동기화할 때, 사용자 암호의 일반 텍스트 버전은 암호 해시 동기화 기능, Azure AD 혹은 다른 어떤 관련 서비스에 노출되지 않습니다.

사용자 인증은 조직의 고유한 Active Directory 인스턴스가 아닌 Azure AD에 대해 이루어집니다. 암호 데이터가 어떤 형태로든 프레미스를 벗어난다는 점을 조직에서 염려하는 경우 Azure AD에 저장된 SHA256 암호 데이터, 즉 원래 MD4 해시의 해시가 Active Directory에 저장된 것보다 훨씬 안전하다는 사실을 고려하십시오. 뿐만 아니라 이 SHA256 해시는 해독이 불가능하기 때문에 조직의 Active Directory 환경으로 가져와서 pass-the-hash 공격에서 유효한 사용자 암호로 표시할 수 없습니다.





### <a name="password-policy-considerations"></a>암호 정책 고려 사항
암호 해시 동기화를 사용할 경우, 영향을 받는 두 가지 정책이 있습니다.

* 암호 복잡성 정책
* 암호 만료 정책

#### <a name="password-complexity-policy"></a>암호 복잡성 정책  
암호 해시 동기화를 사용하는 경우 온-프레미스 Active Directory 인스턴스의 암호 복잡성 정책이 동기화된 사용자에 대한 클라우드의 복잡성 정책을 재정의합니다. 온-프레미스 Active Directory 인스턴스의 유효한 모든 암호를 사용하여 Azure AD 서비스에 액세스할 수 있습니다.

> [!NOTE]
> 클라우드에서 직접 만든 사용자의 암호는 클라우드 내에서 정의된 암호 정책을 계속 따릅니다.

#### <a name="password-expiration-policy"></a>암호 만료 정책  
사용자가 암호 해시 동기화 범위 내에 있을 경우 클라우드 계정 암호는 *사용 기간 제한 없음*으로 설정됩니다.

온-프레미스 환경에서 만료된 동기화된 암호를 사용하여 클라우드 서비스에 계속 로그인할 수 있습니다. 클라우드 암호는 온-프레미스 환경에서 다음에 암호를 변경할 때 업데이트됩니다.

#### <a name="account-expiration"></a>계정 만료
조직에서 사용자 계정 관리의 일환으로 accountExpires 특성을 사용하는 경우 이 특성이 Azure AD와 동기화되지 않는다는 점에 주의해야 합니다. 결과적으로 암호 해시 동기화를 위해 구성된 환경의 만료된 Active Directory 계정은 Azure AD에서 계속 활성 상태입니다. 계정이 만료되면 워크플로 작업에서 사용자의 Azure AD 계정을 비활성화하는 PowerShell 스크립트를 트리거하는 것이 좋습니다. 반대로 계정이 켜지면 Azure AD 인스턴스도 켜져야 합니다.

### <a name="overwrite-synchronized-passwords"></a>동기화된 암호 덮어쓰기
관리자는 Windows PowerShell을 사용하여 사용자 암호를 수동으로 재설정할 수 있습니다.

이런 경우, 새 암호는 사용자의 동기화된 암호를 재정의하고 클라우드 내에 정의된 모든 암호 정책이 새 암호에 적용됩니다.

사용자가 온-프레미스 암호를 다시 변경하면, 새 암호는 클라우드에 동기화되며, 수동으로 업데이트한 암호를 재정의합니다.

암호 동기화는 로그인된 Azure 사용자에게 아무런 영향도 미치지 않습니다. 클라우드 서비스에 로그온해 있는 동안, 동기화된 암호가 변경되더라도 현재 클라우드 세션이 즉시 영향을 받지는 않습니다. KMSI는 이런 차이가 나는 기간을 연장합니다. 클라우드 서비스에서 다시 인증을 요구하면 새 암호를 제공해야 합니다.

### <a name="additional-advantages"></a>추가적인 이점

- 일반적으로 암호 해시 동기화는 페더레이션 서비스보다 구현에 더 가깝습니다. 추가 서버가 필요 없으며, 사용자를 인증하기 위해 고가용성의 페더레이션 서비스에 의존하지 않아도 됩니다. 
- 페더레이션 외에 암호 해시 동기화를 사용하도록 설정할 수도 있습니다. 페더레이션 서비스에 중단이 발생하는 경우 대체 서비스로 사용될 수 있습니다.












## <a name="enable-password-hash-synchronization"></a>암호 해시 동기화 사용
**기본 설정** 옵션을 사용하여 Azure AD Connect를 설치할 경우, 암호 해시 동기화를 사용하도록 자동으로 설정됩니다. 자세한 내용은 [기본 설정을 사용하여 Azure AD Connect 시작](active-directory-aadconnect-get-started-express.md)을 참조하세요.

Azure AD Connect를 설치할 때 사용자 지정 설정을 사용하는 경우 사용자 로그인 페이지에서 암호 해시 동기화를 사용할 수 있습니다. 자세한 내용은 [Azure AD Connect의 사용자 지정 설치](active-directory-aadconnect-get-started-custom.md)를 참조하세요.

![암호 해시 동기화 사용](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>암호 해시 동기화 및 FIPS
서버가 FIPS(Federal Information Processing Standard)에 따라 잠긴 다음 MD5가 비활성화됩니다.

**암호 해시 동기화에 MD5를 사용하려면 다음 단계를 수행합니다.**

1. %programfiles%\Azure AD Sync\Bin으로 이동합니다.
2. miiserver.exe.config를 엽니다.
3. 파일의 끝에서 구성/런타임 노드로 이동합니다.
4. 다음 노드를 추가 합니다. `<enforceFIPSPolicy enabled="false"/>`
5. 변경 내용을 저장합니다.

참조용으로 다음 조각처럼 보여야 합니다.

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

보안 및 FIPS에 대한 자세한 내용은 [AAD 암호 동기화, 암호화 및 FIPS 준수](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)를 참조하세요.

## <a name="troubleshoot-password-hash-synchronization"></a>암호 해시 동기화 문제 해결
암호 해시 동기화에 문제가 있으면 [암호 해시 동기화 문제 해결](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure AD Connect 동기화: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
