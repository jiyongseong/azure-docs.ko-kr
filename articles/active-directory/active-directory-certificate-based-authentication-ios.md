---
title: iOS에서 Azure Active Directory 인증서 기반 인증
description: iOS 장치에서 솔루션의 인증서 기반 인증을 구성하는 데 지원되는 시나리오 및 요구 사항에 대한 자세한 정보
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: 6b19d0556952224ba67914bfa74aac64ade2ea69
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866220"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>iOS에서 Azure Active Directory 인증서 기반 인증

iOS 장치는 연결할 때 자신의 장치에서 클라이언트 인증서를 사용하여 Azure Active Directory에서 인증하는 데 CBA(인증서 기반 인증)을 사용할 수 있습니다.

* Microsoft Outlook 및 Microsoft Word와 같은 Office 모바일 응용 프로그램
* EAS(Exchange ActiveSync) 클라이언트

이 기능을 구성하면 모바일 장치의 특정 메일 및 Microsoft Office 응용 프로그램에 사용자 이름 및 암호 조합을 입력해야 합니다.

이 항목에서는 Office 365 Enterprise, Business, Education, 미국 정부, 중국 및 독일 계획의 테넌트 사용자를 위해 iOS(Android) 장치에서 CBA를 구성하기 위한 요구 사항 및 지원되는 시나리오를 설명합니다.

이 기능은 Office 365 미국 국방부 및 연방 정부 계획에서 미리 보기 상태로 제공됩니다.

## <a name="microsoft-mobile-applications-support"></a>Microsoft 모바일 응용 프로그램 지원

| 앱 | 지원 |
| --- | --- |
| Azure Information Protection 앱 |![확인][1] |
| Intune 회사 포털 |![확인][1] |
| Microsoft 팀 |![확인][1] |
| OneNote |![확인][1] |
| OneDrive |![확인][1] |
| Outlook |![확인][1] |
| Power BI |![확인][1] |
| 비즈니스용 Skype |![확인][1] |
| Word / Excel / PowerPoint |![확인][1] |
| Yammer |![확인][1] |

## <a name="requirements"></a>요구 사항

장치 OS 버전은 iOS 9 이어야 합니다.

페더레이션 서버가 구성되어야 합니다.

Microsoft Authenticator는 iOS의 Office 응용 프로그램에 필요합니다.

Azure Active Directory에서 클라이언트 인증서를 해지하려면 ADFS 토큰에 다음 클레임이 있어야 합니다.

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`(클라이언트 인증서의 일련 번호)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`(클라이언트 인증서의 발급자에 대한 문자열)

Azure Active Directory는 이러한 클레임이 ADFS 토큰(또는 다른 SAML 토큰)에서 사용 가능한 경우 새로 고침 토큰에 이러한 클레임을 추가합니다. 새로 고침 토큰의 유효성을 검사해야 하는 경우 이 정보가 해지를 확인하는 데 사용됩니다.

조직의 ADFS 오류 페이지를 다음 정보로 업데이트하는 것이 가장 좋습니다.

* IOS에서 Microsoft Authenticator를 설치하기 위한 요구 사항
* 사용자 인증서를 얻는 방법에 대한 지침

자세한 내용은 [AD FS 로그인 페이지 사용자 지정](https://technet.microsoft.com/library/dn280950.aspx)을 참조하세요.

일부 Office 앱(최신 인증 사용)은 요청 시 Azure AD에 '*prompt=login*'을 보냅니다. 기본적으로 Azure AD는 ADFS에 대한 요청 시 ‘*prompt=login*’을 ‘*wauth=usernamepassworduri*’(ADFS에 U/P 인증을 수행하도록 요청함) 및 ‘*wfresh=0*’(ADFS에 SSO 상태를 무시하고 새 인증을 수행하도록 요청함)으로 변환합니다. 이러한 앱에 인증서 기반 인증을 사용하려면 기본 Azure AD 동작을 수정해야 합니다. 페더레이션된 도메인 설정에서 '*PromptLoginBehavior*'를 '*Disabled*'로 설정만 하면 됩니다.
다음과 같은 [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) cmdlet을 사용하면 이 작업을 수행할 수 있습니다.

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync 클라이언트 지원

iOS 9 이상에서, 네이티브 iOS 메일 클라이언트가 지원됩니다. 다른 모든 Exchange ActiveSync 응용 프로그램에 대하여, 이 기능이 지원되는지 확인하려면 응용 프로그램 개발자에게 문의하세요.

## <a name="next-steps"></a>다음 단계

사용자 환경에서 인증서 기반 인증을 구성하는 방법에 대한 자세한 내용은 [Android에서 인증서 기반 인증 시작](active-directory-certificate-based-authentication-get-started.md)을 참조하세요.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
