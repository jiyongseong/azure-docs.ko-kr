---
title: 'Azure AD Connect: 연결 문제 해결 | Microsoft Docs'
description: Azure AD Connect의 연결 문제를 해결하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 1c8bbbde653ed8e927ab1550c32ae86a4dc2ffac
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Azure AD Connect 연결 문제 해결
이 문서는 Azure AD Connect와 Azure AD 간 연결의 작동 방식 및 연결 문제 해결 방법을 설명합니다. 이러한 문제는 프록시 서버 환경에서 발생할 가능성이 가장 높습니다.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>설치 마법사에서 연결 문제 해결
Azure AD Connect는 인증에 최신 인증을 사용합니다(ADAL 라이브러리 사용). 설치 마법사 및 동기화 엔진은 .NET 응용 프로그램이므로 machine.config를 제대로 구성해야 합니다.

이 문서에서는 Fabrikam이 해당 프록시를 통해 Azure AD에 연결되는 방법을 보여 줍니다. 프록시 서버의 이름은 fabrikamproxy이고 포트 8080을 사용하고 있습니다.

첫째, [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) 가 올바르게 구성되었는지 확인해야 합니다.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> Microsoft가 아닌 타사 일부 블로그에 miiserver.exe.config를 대신 변경하는 것에 대한 내용이 나와 있습니다. 그러나 업그레이드할 때마다 이 파일을 덮어쓰기 때문에 초기 설치 중에는 작동한다 해도 첫 번째 업그레이드에서 시스템이 작동을 멈춥니다. 이런 이유로 인해 machine.config를 업데이트하는 것이 좋습니다.
>
>

또한 프록시 서버에 필요한 URL이 열려 있어야 합니다. 공식 목록은 [Office 365 URL 및 IP 주소 범위 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)에 나와 있습니다.

이러한 URL 중 다음 표는 Azure AD에 연결하기 위한 절대적인 최소 기본 사항입니다. 이 목록은 비밀번호 쓰기 저장 또는 Azure AD Connect Health와 같은 선택적 기능은 포함하지 않습니다. 여기에 초기 구성 시 발생하는 문제 해결에 도움이 되는 내용이 나와 있습니다.

| URL | 포트 | 설명 |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |CRL 목록을 다운로드하는 데 사용됩니다. |
| \*.verisign.com |HTTP/80 |CRL 목록을 다운로드하는 데 사용됩니다. |
| \*.entrust.com |HTTP/80 |MFA에 대한 CRL 목록을 다운로드하는 데 사용됩니다. |
| \*.windows.net |HTTPS/443 |Azure AD에 로그인하는 데 사용됩니다. |
| secure.aadcdn.microsoftonline-p.com |HTTPS/443 |MFA에 사용됩니다. |
| \*.microsoftonline.com |HTTPS/443 |Azure AD 디렉터리를 구성하고 데이터 가져오거나 내보내는 데 사용됩니다. |

## <a name="errors-in-the-wizard"></a>마법사 오류
설치 마법사는 두 개의 서로 다른 보안 컨텍스트를 사용합니다. **Azure AD에 연결** 페이지에서 현재 로그인된 사용자를 사용합니다. **구성** 페이지에서 [동기화 엔진에 대한 서비스를 실행하는 계정](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account)으로 변경합니다. 프록시 구성은 전역으로 적용되므로 문제가 있는 경우 해당 내용이 마법사의 **Azure AD에 연결** 페이지에 나올 것입니다.

다음 문제는 설치 마법사에서 발생하는 가장 일반적인 오류입니다.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>설치 마법사가 올바르게 구성되지 않음
마법사 자체가 프록시에 연결할 수 없는 경우 이 오류가 표시됩니다.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* 이 오류가 표시되면 [machine.config](active-directory-aadconnect-prerequisites.md#connectivity)가 올바르게 구성되었는지 확인합니다.
* 올바르게 구성된 경우 문제가 마법사 외부에 있는지 확인하기 위해 [프록시 연결 확인](#verify-proxy-connectivity) 의 단계를 수행합니다.

### <a name="a-microsoft-account-is-used"></a>Microsoft 계정이 사용됨
**학교 또는 조직 계정**이 아닌 **Microsoft 계정**을 사용하면 일반 오류가 발생합니다.  
![Microsoft 계정이 사용됨](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>MFA 끝점에 연결할 수 없음
엔드포인트 **https://secure.aadcdn.microsoftonline-p.com**에 연결할 수 없고 전역 관리자가 MFA를 사용하도록 설정한 경우 이 오류가 표시됩니다.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* 이 오류가 표시되는 경우 끝점 **secure.aadcdn.microsoftonline-p.com**이 프록시에 추가되어 있는지 확인합니다.

### <a name="the-password-cannot-be-verified"></a>암호를 확인할 수 없음
설치 마법사가 Azure AD 연결에 성공했지만 암호 자체를 확인할 수 없는 경우 이 오류가 표시됩니다.  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* 암호가 임시 암호라서 변경해야 하나요? 실제로 올바른 암호인가요? Azure AD Connect 서버 이외의 컴퓨터에서 https://login.microsoftonline.com에 로그인하여 계정이 사용 가능한지 확인합니다.

### <a name="verify-proxy-connectivity"></a>프록시 연결 확인
Azure AD Connect 서버가 프록시 및 인터넷에 실제로 연결되었는지 확인하려면 프록시가 웹 요청을 허용하는지 확인하기 위해 PowerShell을 사용합니다. PowerShell 프롬프트에서 `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`를 실행합니다. (기술적으로 https://login.microsoftonline.com을 첫 번째로 호출하고 작동도 되지만 다른 URI가 더 빠르게 응답합니다.)

PowerShell은 프록시에 연결하기 위해 machine.config의 구성을 사용합니다. winhttp/netsh 설정이 이러한 cmdlet에 영향을 주지 않아야 합니다.

프록시가 올바르게 구성되었으면 ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)과 같은 성공 상태가 표시됩니다.

**원격 서버에 연결할 수 없습니다.**라는 메시지가 표시되면 PowerShell이 프록시를 사용하지 않고 직접 호출을 수행하려고 하거나 DNS가 올바르게 구성되지 않습니다. **machine.config** 파일이 올바르게 구성되었는지 확인합니다.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

프록시가 올바르게 구성되지 않으면 ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)과 같은 오류가 표시됩니다.

| 오류 | 오류 텍스트 | 주석 |
| --- | --- | --- |
| 403 |사용할 수 없음 |요청된 URL에 대해 프록시가 열려 있지 않습니다. 프록시 구성을 다시 확인하고 [URL](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 이 열려 있는지 확인합니다. |
| 407 |프록시 인증 필요 |프록시 서버에 로그인이 필요한데 아무 것도 제공되지 않았습니다. 프록시 서버에 인증이 필요한 경우 machine.config에서 이 설정이 구성되었는지 확인합니다. 또한 마법사를 실행하는 사용자 및 서비스 계정에 대해 도메인 계정을 사용하고 있는지 확인합니다. |

### <a name="proxy-idle-timeout-setting"></a>프록시 유휴 시간 제한 설정
Azure AD Connect가 Azure AD로 내보내기 요청을 전송하면 Azure AD는 응답을 생성하기 전에 요청을 처리하는 데 최대 5분이 소요될 수 있습니다. 대규모 그룹 구성원이 동일한 내보내기 요청에 포함되어 있는 그룹 개체가 많이 있을 때 특히 이러한 처리 시간이 오래 걸릴 수 있습니다. 프록시 유휴 시간 제한을 5분보다 크게 구성해야 합니다. 그렇지 않은 경우 Azure AD Connect 서버에서 Azure AD와의 간헐적 연결 문제가 확인될 수 있습니다.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>Azure AD Connect와 Azure AD 간의 통신 패턴
위의 모든 단계를 수행했는데도 여전히 연결할 수 없다면 이제 네트워크 로그를 살펴봅니다. 이 섹션에는 일반적이고 성공적인 연결 패턴이 나와 있습니다. 네트워크 로그를 읽고 있는 중이라면 무시해도 되는 지엽적인 내용도 나옵니다.

* https://dc.services.visualstudio.com에 대한 호출이 있습니다. 설치가 성공하는 데 프록시에 이 URL이 열려 있을 필요는 없으므로 이러한 호출은 무시해도 됩니다.
* DNS 확인에서 microsoftonline.com이 아닌 DNS 이름 공간 nsatc.net 및 기타 네임스페이스에 있어야 하는 실제 호스트가 나열됩니다. 그러나 실제 서버 이름에 대한 웹 서비스를 요청하지 않으므로 이러한 URL을 프록시에 추가할 필요가 없습니다.
* 끝점 adminwebservice 및 provisioningapi는 검색 끝점이며 사용할 실제 끝점을 찾는 데 사용됩니다. 이러한 끝점은 사용자의 하위 지역에 따라 다릅니다.

### <a name="reference-proxy-logs"></a>참조 프록시 로그
다음은 실제 프록시 로그의 덤프 및 수행된 설치 마법사 페이지입니다(동일한 끝점에 대한 중복 항목은 제거됨). 이 섹션은 고유한 프록시 및 네트워크 로그에 대한 참조로 사용할 수 있습니다. 실제 끝점은 사용자 환경에서 다를 수 있습니다(특히 *기울임꼴*의 URL).

**Azure AD에 연결**

| Time | URL |
| --- | --- |
| 1/11/2016 8:31 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:32 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**구성**

| Time | URL |
| --- | --- |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:43 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:44 |connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**초기 동기화**

| Time | URL |
| --- | --- |
| 1/11/2016 8:48 |connect://login.windows.net:443 |
| 1/11/2016 8:49 |connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 1/11/2016 8:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>인증 오류
이 섹션에서는 ADAL(Azure AD Connect에 의해 사용된 인증 라이브러리) 및 PowerShell에서 반환될 수 있는 오류를 다룹니다. 설명한 오류는 다음 단계를 이해하는 데 도움이 됩니다.

### <a name="invalid-grant"></a>잘못된 권한 부여
잘못된 사용자 이름 또는 암호 자세한 내용은 [암호를 확인할 수 없음](#the-password-cannot-be-verified)을 참조하세요.

### <a name="unknown-user-type"></a>알 수 없는 사용자 유형
Azure AD 디렉터리를 찾거나 해결할 수 없습니다. 확인되지 않은 도메인의 사용자 이름으로 로그인을 시도하시겠습니까?

### <a name="user-realm-discovery-failed"></a>사용자 영역 검색에 실패했습니다
네트워크 또는 프록시 구성 문제 데이터베이스에 연결할 수 없습니다. [설치 마법사에서 연결 문제 해결](#troubleshoot-connectivity-issues-in-the-installation-wizard)을 참조하세요.

### <a name="user-password-expired"></a>사용자 암호가 만료되었습니다
자격 증명이 만료되었습니다. 암호를 변경합니다.

### <a name="authorizationfailure"></a>AuthorizationFailure
알 수 없는 문제

### <a name="authentication-cancelled"></a>인증이 취소되었습니다
MFA(Multi-Factor Authentication) 시도를 취소했습니다.

### <a name="connecttomsonline"></a>ConnectToMSOnline
인증에 성공했지만 Azure AD PowerShell에 인증 문제가 있습니다.

### <a name="azurerolemissing"></a>AzureRoleMissing
인증이 성공했습니다. 전역 관리자가 아닙니다.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
인증이 성공했습니다. Privileged Identity Management를 사용하며 현재 전역 관리자가 아닙니다. 자세한 내용은 [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md)를 참조하세요.

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
인증이 성공했습니다. Azure AD에서 회사 정보를 검색할 수 없습니다.

### <a name="retrievedomains"></a>RetrieveDomains
인증이 성공했습니다. Azure AD에서 도메인 정보를 검색할 수 없습니다.

### <a name="unexpected-exception"></a>예기치 않은 예외
설치 마법사에서 예기치 않은 오류로 표시됩니다. **학교 또는 조직 계정**이 아닌 **Microsoft 계정**을 사용하려고 하면 발생할 수 있습니다.

## <a name="troubleshooting-steps-for-previous-releases"></a>이전 릴리스에 대한 문제 해결 단계입니다.
빌드 번호 1.1.105.0(2016년 2월에 발표됨)으로 시작하는 릴리스에서 로그인 도우미 사용이 중지되었습니다. 이 섹션 및 구성은 더 이상 필요하지 않지만 참조로 유지됩니다.

단일 로그인 도우미를 작동하려면 winhttp가 구성되어야 합니다. 이 구성은 [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity)를 사용하여 수행할 수 있습니다.  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>로그인 도우미가 올바르게 구성되지 않음
이 오류는 로그인 도우미가 프록시를 연결할 수 없거나 프록시가 요청을 허용하지 않을 때 표시됩니다.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* 이 오류가 표시되면 [netsh](active-directory-aadconnect-prerequisites.md#connectivity)에서 프록시 구성을 살펴보고 구성이 올바른지 확인합니다.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* 올바르게 구성된 경우 문제가 마법사 외부에 있는지 확인하기 위해 [프록시 연결 확인](#verify-proxy-connectivity) 의 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.
