---
title: Azure AD SSPR 데이터 요구 사항 | Microsoft Docs
description: Azure AD 셀프 서비스 암호 재설정의 데이터 요구 사항 및 충족 방법
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5409bf198d0e3f6537619ef4698d9f2e31bd27c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257591"
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>최종 사용자를 등록할 필요 없이 암호 재설정 배포

Azure Active Directory(Azure AD) 셀프 서비스 암호 재설정(SSPR)을 배포하려면 인증 데이터가 있어야 합니다. 일부 조직에서는 사용자들이 직접 자신의 인증 데이터를 입력하도록 하지만 대부분의 조직에서는 Active Directory에 있는 기존 데이터와 동기화하는 것을 선호합니다. 동기화된 데이터는 다음과 같은 조건이 충족되는 경우 사용자 개입 없이 Azure AD 및 SSPR에 제공됩니다.
   * 온-프레미스 디렉터리에 데이터가 올바른 형식으로 저장되어 있습니다.
   * [기본 설정을 사용하여 Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md)가 구성되어 있습니다.

올바르게 작동하려면 전화 번호가 *+국가코드 전화번호* 형식으로 저장되어야 합니다(예: +1 4255551234).

> [!NOTE]
> 국가 번호와 전화 번호 사이에 공백이 필요합니다.
>
> 암호 재설정은 전화 번호 확장을 지원하지 않습니다. +1 4255551234X12345 형식에서도 전화를 걸지 전에 확장이 제거됩니다.

## <a name="fields-populated"></a>채워진 필드

Azure AD Connect에서 기본 설정을 사용한 경우 다음과 같은 매핑이 수행됩니다.

| 온-프레미스 Active Directory | Azure AD |
| --- | --- |
| telephoneNumber | 사무실 전화 |
| mobile | 휴대폰 |

사용자가 자신의 휴대폰 번호를 확인하면 Azure AD에서 인증 연락처 정보 아래의 전화 필드도 해당 번호로 채워집니다.

## <a name="authentication-contact-info"></a>인증 연락처 정보

전역 관리자는 다음 스크린샷에 표시된 대로 사용자에 대한 인증 연락처 정보를 수동으로 설정할 수 있습니다.

![연락처][Contact]

전화 필드가 채워지고 휴대폰이 SSPR 정책에서 활성화되는 경우 사용자는 암호 재설정 등록 페이지와 암호 재설정 워크플로 중 해당 번호를 볼 수 있습니다. 

대체 전화 필드는 암호 재설정에 사용되지 않습니다.

이메일 필드가 채워지고 이메일이 SSPR 정책에서 활성화되는 경우 사용자는 암호 재설정 등록 페이지와 암호 재설정 워크플로 중 해당 이메일을 볼 수 있습니다.

대체 이메일 필드가 채워지고 이메일이 SSPR 정책에서 활성화되는 경우 사용자는 암호 재설정 등록 페이지에서 해당 이메일을 볼 수 **없지만** 암호 재설정 워크플로 중 볼 수 있습니다. 


## <a name="security-questions-and-answers"></a>보안 질문 및 답변

본인 확인 질문과 답변은 Azure AD 테넌트에 안전하게 저장되며 사용자가 [SSPR 등록 포털](https://aka.ms/ssprsetup)을 통해서만 액세스할 수 있습니다. 관리자는 다른 사용자의 질문과 답변 내용을 보거나 수정할 수 없습니다.

### <a name="what-happens-when-a-user-registers"></a>사용자가 등록하면 어떻게 되나요?

사용자가 등록하면 등록 페이지에서 다음 필드를 설정합니다.

* **인증 전화**
* **인증 메일**
* **본인 확인 질문 및 답변**

관리자가 **휴대폰** 또는 **대체 메일**에 값을 입력하면 서비스에 등록하지 않은 사용자도 해당 값을 사용하여 자신의 암호를 즉시 재설정할 수 있습니다. 또한 사용자는 처음으로 등록할 때 해당 값을 볼 수 있고, 원하는 경우 값을 변경할 수도 있습니다. 사용자가 등록을 완료한 후에는 해당 값이 각각 **인증 전화** 필드와 **인증 메일** 필드에 그대로 유지됩니다.

## <a name="set-and-read-the-authentication-data-through-powershell"></a>PowerShell을 사용하여 인증 데이터 설정 및 읽기

PowerShell을 사용하여 다음 필드를 설정할 수 있습니다.

* **대체 메일**
* **휴대폰**
* **사무실 전화**: 온-프레미스 디렉터리와 동기화하지 않는 경우에만 설정할 수 있습니다.

### <a name="use-powershell-version-1"></a>PowerShell 버전 1 사용

시작하려면 [Azure AD PowerShell 모듈을 다운로드하고 설치](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)해야 합니다. 설치를 완료한 후에는 다음 단계에 따라서 각 필드를 구성합니다.

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>PowerShell 버전 1을 사용하여 인증 데이터 설정

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>PowerShell 버전 1을 사용하여 인증 데이터 읽기

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>인증 전화 및 인증 메일 옵션 읽기

PowerShell 버전 1에서 아래 명령을 사용하여 **인증 전화**와 **인증 메일**을 읽습니다.

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>PowerShell 버전 2 사용

시작하려면 [Azure AD 버전 2 PowerShell 모듈을 다운로드하여 설치](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0)합니다. 설치를 완료한 후에는 다음 단계에 따라서 각 필드를 구성합니다.

아래 명령을 사용하면 Install-Module을 지원하는 최신 버전의 PowerShell에서 빠르게 설치할 수 있습니다. (첫 줄에서는 모듈이 이미 설치되어 있는지 확인합니다.)

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>PowerShell 버전 2를 사용하여 인증 데이터 설정

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>PowerShell 버전 2를 사용하여 인증 데이터 읽기

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](howto-sspr-deployment.md)
* [암호 재설정 또는 변경](../active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](concept-sspr-licensing.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](concept-sspr-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](howto-sspr-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](howto-sspr-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](concept-sspr-howitworks.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)

[Contact]: ./media/howto-sspr-authenticationdata/user-authentication-contact-info.png "전역 관리자는 사용자의 인증 연락처 정보를 수정할 수 있습니다."
