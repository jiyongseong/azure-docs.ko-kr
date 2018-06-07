---
title: 셀프 서비스 암호 재설정 정책 - Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정 정책 옵션
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5952d0b1568e2554f53797515cde5cb23ad95a9b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Azure Active Directory에서 암호 정책 및 제한

이 문서에서는 Azure AD(Azure Active Directory) 테넌트에 저장된 사용자 계정과 관련된 암호 정책 및 복잡성 요구 사항에 대해 설명합니다.

## <a name="administrator-password-policy-differences"></a>관리자 암호 정책 차이점

Microsoft에서는 Azure 관리자 역할에 강력한 기본 *두 게이트* 암호 재설정 정책을 적용합니다. 

두 게이트 정책을 사용할 경우 관리자는 보안 질문을 사용할 수 없습니다.

 두 게이트 정책은 전자 메일 주소 *및* 전화 번호 등, 두 가지 인증 데이터를 요구합니다. 다음과 같은 경우에 두 게이트 정책이 적용됩니다.

* 다음과 같은 모든 Azure 관리자 역할이 영향을 받습니다.
  * 기술 지원팀 관리자
  * 서비스 지원 관리자
  * 대금 청구 관리자
  * 파트너 계층1 지원
  * 파트너 계층2 지원
  * Exchange 서비스 관리자
  * Lync 서비스 관리자
  * 사용자 계정 관리자
  * 디렉터리 작성자
  * 전역 관리자 또는 회사 관리자
  * SharePoint 서비스 관리자
  * 규정 준수 관리자
  * 응용 프로그램 관리자
  * 보안 관리자
  * 권한 있는 역할 관리자
  * Microsoft Intune 서비스 관리자
  * 응용 프로그램 프록시 서비스 관리자
  * CRM 서비스 관리자
  * Power BI 서비스 관리자
  
* 평가판 구독에서 30일이 경과한 경우

  또는

* 베니티 도메인이 있는 경우(예: contoso.com)

  또는

* Azure AD Connect가 온-프레미스 디렉터리에서 ID를 동기화하는 경우

### <a name="exceptions"></a>예외
한 게이트 정책은 전자 메일 주소 *또는* 전화 번호 등, 한 가지 인증 데이터를 요구합니다. 다음과 같은 경우에 한 게이트 정책이 적용됩니다.

* 평가판 구독의 처음 30일 이내인 경우

  또는

* 베니티 도메인이 없는 경우(*.onmicrosoft.com) 

  and 

  Azure AD Connect가 ID를 동기화하지 않는 경우


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>모든 사용자 계정에 적용되는 UserPrincipalName 정책

Azure AD에 로그인해야 하는 모든 사용자 계정에는 해당 계정에 연결된 고유한 UPN(사용자 계정 이름) 특성 값이 있어야 합니다. 다음 표에서는 클라우드와 동기화되는 온-프레미스 Active Directory 사용자 계정 및 클라우드 전용 사용자 계정에 모두 적용되는 정책을 간략하게 요약하고 있습니다.

| 자산 | UserPrincipalName 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul> <li>A-Z</li> <li>a-z</li><li>0-9</li> <li> 에서도 확인할 수 있습니다. - \_ ! \# ^ \~</li></ul> |
| 허용되지 않는 문자 |<ul> <li>도메인에서 사용자 이름을 구분하지 않는 모든 "\@\"" 문자입니다.</li> <li>"\@\"" 기호 바로 앞에는 "."(마침표) 문자를 사용할 수 없습니다.</li></ul> |
| 길이 제약 조건 |<ul> <li>총 길이는 113자를 초과할 수 없습니다.</li><li>"\@\"" 기호 앞에는 최대 64자를 사용할 수 있습니다.</li><li>"\@\"" 기호 뒤에는 최대 48자를 사용할 수 있습니다.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>클라우드 사용자 계정에만 적용되는 암호 정책

다음 표에서는 Azure AD에서 생성 및 관리되는 사용자 계정에 적용할 수 있는 사용 가능 암호 정책 설정에 대해 설명합니다.

| 자산 | 요구 사항 |
| --- | --- |
| 허용되는 문자 |<ul><li>A-Z</li><li>a-z</li><li>0-9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 허용되지 않는 문자 |<ul><li>유니코드 문자</li><li>공백</li><li> 강력한 암호만 가능</li></ul> |
| 암호 제한 |<ul><li>최소 8자, 최대 16자</li><li>강력한 암호만: 다음 4개 중 3개가 필요합니다.<ul><li>소문자</li><li>대문자</li><li>숫자(0-9)</li><li>기호(이전 암호 제한 참조)</li></ul></li></ul> |
| 암호 만료 기간 |<ul><li>기본값: **90**일</li><li>값은 Windows PowerShell용 Azure Active Directory 모듈에서 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 알림 |<ul><li>기본값: **14**일(암호 만료 이전)</li><li>값은 `Set-MsolPasswordPolicy` cmdlet을 사용하여 구성할 수 있습니다.</li></ul> |
| 암호 만료 |<ul><li>기본값: **false**일(사용 가능한 암호 만료임을 나타냄)</li><li>`Set-MsolUser` cmdlet을 사용하여 개별 사용자 계정에 대한 값을 구성할 수 있습니다.</li></ul> |
| 암호 변경 기록 |사용자 암호를 변경할 때 마지막 암호를 다시 사용할 수 *없습니다*. |
| 암호 재설정 기록 | 사용자가 잊어버린 암호를 재설정할 때 마지막 암호를 다시 사용할 수 *있습니다*. |
| 계정 잠금 |로그인 시도를 10번 실패하면(잘못된 암호) 1분 동안 사용자가 잠기게 됩니다. 잘못된 로그인을 더 시도하면 사용자가 잠기는 시간이 더 늘어납니다. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Azure AD에서 암호 만료 정책 설정

Microsoft 클라우드 서비스의 전역 관리자는 Windows PowerShell용 Microsoft Azure AD 모듈을 사용하여 사용자의 암호가 만료되지 않도록 설정할 수 있습니다. 또한 Windows PowerShell cmdlet을 사용하여 만료되지 않는 구성을 제거하거나 어떤 사용자 암호가 만료되지 않도록 설정되어 있는지 확인할 수 있습니다. 

이 지침은 ID 및 디렉터리 서비스로 Azure AD를 사용하는 Intune, Office 365와 같은 다른 공급자에 제공됩니다. 암호 만료는 정책에서 변경할 수 있는 유일한 부분입니다.

> [!NOTE]
> 디렉터리 동기화를 통해 동기화되지 않는 사용자 계정의 암호만 만료되지 않도록 구성할 수 있습니다. 디렉터리 동기화에 대한 자세한 내용은 [Azure AD와 AD 연결](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)을 참조하세요.
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>PowerShell을 사용하여 암호 정책 설정 또는 확인

시작하려면 [Azure AD PowerShell 모듈을 다운로드하고 설치](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)해야 합니다. 설치를 완료한 후에는 다음 단계를 사용하여 각 필드를 구성합니다.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>암호에 대한 만료 정책 확인 방법
1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
2. 다음 중 하나의 명령을 실행합니다.

   * 사용자의 암호가 만료되지 않도록 설정되어 있는지 확인하려면 해당 사용자의 UPN(예: *aprilr@contoso.onmicrosoft.com*) 또는 사용자 ID를 사용하여 `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires` cmdlet을 실행합니다.
   * 모든 사용자에 대한 **암호 사용 기간 제한 없음** 설정을 보려면 `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires` cmdlet을 실행합니다.

### <a name="set-a-password-to-expire"></a>암호가 만료되도록 설정

1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
2. 다음 중 하나의 명령을 실행합니다.

   * 특정 사용자의 암호가 만료되도록 설정하려면 해당 사용자의 UPN 또는 사용자 ID를 사용하여 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false` cmdlet을 실행합니다.
   * 조직의 모든 사용자 암호가 만료되도록 설정하려면 다음 cmdlet을 사용합니다. `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>암호가 만료되지 않도록 설정

1. 회사 관리자 자격 증명을 사용하여 Windows PowerShell에 연결합니다.
2. 다음 중 하나의 명령을 실행합니다.

   * 특정 사용자의 암호가 만료되지 않도록 설정하려면 해당 사용자의 UPN 또는 사용자 ID를 사용하여 `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true` cmdlet을 실행합니다.
   * 조직의 모든 사용자 암호가 만료되지 않도록 설정하려면 `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > `-PasswordNeverExpires $true`로 설정된 암호는 계속해서 `pwdLastSet` 특성에 따라 사용 기간이 계산됩니다. 사용자 암호가 만료되지 않도록 설정하고 90일이 넘어가면 암호가 만료됩니다. `pwdLastSet` 특성에 따라, 만료를 `-PasswordNeverExpires $false`로 변경한 경우 `pwdLastSet`이 90일보다 오래된 모든 암호는 다음 번에 로그인할 때 변경해야 합니다. 이 변경으로 많은 사용자가 영향을 받을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

다음 문서에서는 Azure AD를 통한 암호 재설정에 대한 추가 정보를 제공합니다.

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](howto-sspr-deployment.md)
* [암호 재설정 또는 변경](../active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](concept-sspr-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](howto-sspr-authenticationdata.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](concept-sspr-howitworks.md#authentication-methods)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](howto-sspr-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](howto-sspr-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](concept-sspr-howitworks.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)
