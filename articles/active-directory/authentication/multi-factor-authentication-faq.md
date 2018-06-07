---
title: Azure Multi-Factor Authentication FAQ| Microsoft Docs
description: Azure Multi-Factor Authentication과 관련된 질문과 대답입니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 226400c6da5db589db6ce2f1887c3e96cf9e1f14
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대한 질문과 대답

이 FAQ에서는 Azure Multi-factor Authentication 및 Multi-Factor Authentication 서비스 사용에 대한 일반적인 질문에 대한 답변을 제공합니다. 서비스 전반, 청구 모델, 사용자 경험 및 문제 해결에 대한 질문으로 세분화됩니다.

## <a name="general"></a>일반
**Q: Azure Multi-Factor Authentication 서버는 사용자 데이터를 어떻게 처리하나요?**

Multi-Factor Authentication 서버를 사용하면 사용자의 데이터가 온-프레미스 서버에만 저장됩니다. 영구 사용자 데이터는 클라우드에 저장되지 않습니다. 사용자가 2단계 인증을 수행하는 경우 Multi-factor Authentication 서버는 인증을 위해 Azure Multi-factor Authentication 클라우드 서비스에 데이터를 보냅니다. Multi-factor Authentication 서버와 Multi-factor Authentication 클라우드 서비스 간의 통신에는 포트 443 아웃바운드를 통해 SSL(Secure Sockets Layer) 또는 TLS(전송 계층 보안)가 사용됩니다.

클라우드 서비스에 인증 요청을 보내는 경우 인증 및 사용 보고서를 위한 데이터를 수집합니다. 2단계 인증 로그에 포함된 데이터 필드는 다음과 같습니다.

* **고유 ID** (사용자 이름 또는 온-프레미스 Multi-Factor Authentication 서버 ID)
* **이름과 성** (선택 사항)
* **전자 메일 주소** (선택 사항)
* **전화 번호** (음성 통화 또는 SMS 인증을 수행할 때)
* **장치 토큰** (모바일 앱 인증을 수행할 때)
* **인증 모드**
* **인증 결과**
* **Multi-Factor Authentication 서버 이름**
* **Multi-Factor Authentication 서버 IP**
* **클라이언트 IP** (사용 가능한 경우)

Multi-Factor Authentication 서버에 선택적 필드를 구성할 수 있습니다.

인증 결과(성공 또는 거부) 및 거부 사유는 인증 데이터와 함께 저장됩니다. 이 데이터는 인증 및 사용 보고서에서 사용할 수 있습니다.

**Q: 사용자에게 SMS 메시지를 보낼 때 사용되는 SMS 짧은 코드는 무엇인가요?**

미국에서 Microsoft는 다음과 같은 SMS 짧은 코드를 사용합니다.

   * 97671
   * 69829
   * 51789
   * 99399

캐나다에서 Microsoft는 다음과 같은 SMS 짧은 코드를 사용합니다.

   * 759731 
   * 673801

Microsoft는 동일한 번호를 통한 일관적인 SMS 또는 음성 기반 Multi-Factor Authentication 즉시 이행을 보장하지 않습니다. 사용자를 위해, SMS 이행성을 향상하기 위한 조정 작업을 수시로 진행하고 있는 Microsoft는 언제든지 짧은 코드를 추가하거나 제거할 수 있습니다. Microsoft는 미국 및 캐나다 이외의 국가에는 짧은 코드를 지원하지 않습니다.

## <a name="billing"></a>결제
[Multi-Factor Authentication 가격 책정 페이지](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 또는 [Azure Multi-Factor Authentication 획득 방법](concept-mfa-licensing.md)에 대한 설명서를 참조하여 대부분의 청구 관련 질문에 대답할 수 있습니다.

**Q: 인증하는 데 사용되는 전화 통화 및 문자 메시지에 대한 요금이 조직에 부과되나요?**

아니요, Azure Multi-Factor Authentication을 통해 사용자에게 전달된 개별 전화 통화 또는 문자 메시지에 대한 요금이 부과되지 않습니다. 인증 단위 MFA 공급자를 사용하는 경우 사용된 방법이 아닌 인증마다 비용이 청구됩니다.

사용자는 자신의 개인 전화 서비스에 따라 수신한 전화 통화 또는 문자 메시지 수신에 대한 요금이 부과될 수 있습니다.

**Q: 사용자별 청구 모델에서는 활성화된 모든 사용자에 대해 요금이 청구되나요? 2단계 인증을 수행한 사용자에 대해서만 청구되나요?**

대금 청구는 해당 월에 2단계 인증을 수행했는지 여부에 관계없이 Multi-Factor Authentication을 사용하도록 구성된 사용자 수를 기준으로 합니다.

**Q:Multi-Factor Authentication은 어떤 방식으로 청구됩니까?**

사용자 단위 또는 인증 단위 MFA 공급자를 만들 때 해당 조직의 Azure 구독이 사용량을 기준으로 매월 청구됩니다. 이 청구 모델은 가상 머신 및 웹 사이트의 사용량에 대해 Azure에서 청구하는 방식과 유사합니다.

Azure Multi-Factor Authentication에 대한 구독을 구매하는 경우 조직은 각 사용자에 대해 연간 라이선스 사용료만 지불합니다. MFA 라이선스 및 Office 365, Azure AD Premium 또는 Enterprise Mobility + Security 번들은 이 방법으로 청구됩니다. 

[Azure Multi-factor Authentication 획득 방법](concept-mfa-licensing.md)에서 옵션에 대해 자세히 알아보세요.

**Q: Azure Multi-Factor Authentication의 평가판 버전이 있나요?**

일부 경우에 그렇습니다.

Azure 관리자를 위한 Multi-Factor Authentication에서는 비용 없이 Azure 및 Office 365 관리자 포털 등의 Microsoft 온라인 서비스에 액세스할 수 있는 Azure MFA 기능의 하위 집합을 제공합니다. 이 제품은 MFA 라이선스, 번들 또는 독립 실행형 사용량 기반 공급자를 통해 Azure MFA의 정식 버전을 가지고 있지 않은 Azure Active Directory 인스턴스의 전역 관리자에게만 적용됩니다. 관리자가 무료 버전을 사용하는 경우 사용자가 Azure MFA의 정식 버전을 구입하면 모든 전역 관리자가 유료 버전으로 자동으로 승격됩니다.

Office 365 사용자용 Multi-Factor Authentication에서는 비용 없이 Exchange Online, SharePoint Online 등의 Office 365 서비스에 액세스할 수 있는 Azure MFA 기능의 하위 집합을 제공합니다. 이 제품은 Azure Active Directory의 해당 인스턴스가 MFA 라이선스, 번들 또는 독립 실행형 사용량 기반 공급자를 통해 Azure MFA의 정식 버전을 보유하지 않은 경우 Office 365 라이선스가 할당된 사용자에게 적용됩니다.

**Q: 조직에서 사용자당 청구 모델과 인증당 청구 모델 간을 전환할 수 있습니까?**

조직에서 사용량 기반 청구를 포함하는 독립 실행형 서비스로 MFA를 구입한 경우 MFA 공급자를 만들 때 청구 모델을 선택합니다. MFA 공급자가 생성된 후에는 청구 모델을 변경할 수 없습니다. 하지만 MFA 공급자를 삭제하고 다른 청구 모델로 새 공급자를 만들 수 있습니다.

MFA 공급자가 생성되면 Azure Active Directory(즉, “Azure AD 테넌트”)에 연결할 수 있습니다. 현재 MFA 공급자가 Azure AD 테넌트에 연결된 경우 MFA 공급자를 안전하게 삭제하고 동일한 Azure AD 테넌트에 연결된 새 MFA 공급자를 만들 수 있습니다. 또는 MFA로 설정된 모든 사용자를 처리할 만큼 충분한 MFA, Azure AD Premium 또는 Enterprise Mobility + Security(EMS) 라이선스를 구입한 경우 MFA 공급자를 모두 삭제할 수 있습니다.

하지만 MFA 공급자가 Azure AD 테넌트에 연결되어 있지 *않거나* 새 MFA 공급자를 다른 Azure AD 테넌트에 연결한 경우 사용자 설정 및 구성 옵션이 전송되지 않습니다. 또한 새 MFA 공급자를 통해 생성된 활성화 자격 증명을 사용하여 기존 Azure MFA 서버를 다시 활성화해야 합니다. MFA 서버를 새 MFA 공급자에 연결하기 위해 다시 활성화해도 전화 및 문자 메시지 인증에는 영향을 미치지 않지만 모바일 앱을 다시 활성화할 때까지 모바일 앱 알림이 모든 사용자에 대해 작동 중지됩니다.

[Azure Multi-Factor Auth 공급자 시작](concept-mfa-authprovider.md)에서 MFA 공급자에 대해 자세히 알아보세요.

**Q: 조직에서 언제든지 사용량 기반 요금 청구 및 구독(라이선스 기반 모델) 간을 전환할 수 있나요?**

일부 경우에 그렇습니다.

디렉터리에 *사용자당* Azure Multi-Factor Authentication 공급자가 있는 경우 MFA 라이선스를 추가할 수 있습니다. 라이선스가 있는 사용자는 사용자별 사용량 기반 청구 요금에 계산되지 않습니다. 라이선스가 없는 사용자는 계속해서 MFA 공급자를 통해 MFA 사용을 설정할 수 있습니다. Multi-Factor Authentication을 사용하도록 구성된 모든 사용자를 위한 라이선스를 구입 및 할당하면 Azure Multi-Factor Authentication 공급자를 삭제할 수 있습니다. 향후 라이선스보다 더 많은 사용자가 생기면 항상 다른 사용자 단위 MFA 공급자를 만들 수 있습니다.

디렉터리에 *인증 단위* Azure Multi-Factor Authentication 공급자가 있는 경우 MFA 공급자가 구독에 연결되기만 한다면 인증마다 요금이 항상 청구됩니다. 사용자에게 MFA 라이선스를 할당할 수 있지만 사용자의 MFA 라이선스 할당 여부에 관계없이 2단계 인증 요청마다 요금이 계속 청구됩니다.

**Q: 내 조직은 ID를 사용하고 동기화하여 Azure Multi-Factor Authentication을 사용해야 합니까?**

조직에서 사용량 기반 청구 모델을 사용하는 경우 Azure Active Directory는 선택 사항이며 필수가 아닙니다. MFA 공급자가 Azure AD 테넌트에 연결되어 있지 않은 경우 Azure Multi-Factor Authentication 서버 온-프레미스만 배포할 수 있습니다.

라이선스를 구입하고 디렉터리의 사용자에게 할당할 때 라이선스가 Azure AD 테넌트에 추가되기 때문에 라이선스 모델에 대해 Azure Active Directory가 필요합니다.

## <a name="manage-and-support-user-accounts"></a>사용자 계정 관리 및 지원

**Q: 사용자가 전화로 응답을 받지 못하거나 사용자가 전화를 가지고 있지 않은 경우 사용자에게 어떻게 하라고 말해 주어야 하나요?**

모든 사용자가 두 가지 이상의 인증 방법을 구성했기를 바랍니다. 다시 로그인을 시도하도록 지시하지만 로그인 페이지에서 다른 인증 방법을 선택하도록 알려 줍니다.

사용자에게 [최종 사용자 문제 해결 가이드](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md)를 안내해줄 수 있습니다.


**Q: 내 사용자 중 하나가 계정에 로그인할 수 없는 경우 어떻게 해야 하나요?**

등록 프로세스를 다시 진행하도록 하여 사용자 계정을 재설정할 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 장치 설정 관리](howto-mfa-userdevicesettings.md)에 대해 자세히 알아보세요.

**Q: 내 사용자 중 하나가 앱 암호를 사용하는 전화기를 분실한 경우 어떻게 해야 하나요?**

무단 액세스를 방지하려면 모든 사용자 앱 암호를 삭제합니다. 사용자에게 교체용 장치가 있는 경우 암호를 다시 만들 수 있습니다. [클라우드에서 Azure Multi-Factor Authentication을 사용하여 사용자 및 장치 설정 관리](howto-mfa-userdevicesettings.md)에 대해 자세히 알아보세요.

**Q: 사용자가 비브라우저 앱에 로그인할 수 없으면 어떻게 하나요?**

조직에서 레거시 클라이언트를 계속 사용하고 [앱 암호 사용을 허용](howto-mfa-mfasettings.md#app-passwords)한 경우 사용자는 자신의 사용자 이름 및 암호로 이러한 레거시 클라이언트에 로그인할 수 없습니다. 대신, [앱 암호를 설정](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-app-passwords.md)해야 합니다. 사용자는 로그인 정보를 지우고(삭제)하고 앱을 다시 시작한 후 일반 암호 대신 *앱 암호*와 사용자 이름을 사용하여 로그인해야 합니다.

조직에 레거시 클라이언트가 없는 경우 사용자가 앱 암호를 만들도록 허용하지 않아야 합니다.

> [!NOTE]
> Office 2013 클라이언트에 대한 최신 인증
>
> 앱 암호는 최신 인증을 지원하는 않는 앱에만 필요합니다. Office 2013 클라이언트는 최신 인증 프로토콜을 지원하지만 구성되어야 합니다. 최신 Office 클라이언트는 최신 인증 프로토콜을 자동으로 지원합니다. 자세한 내용은 [발표된 Office 2013 최신 인증 공개 미리 보기](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)를 참조하세요.

**Q: 내 사용자가 간혹 문자 메시지를 받지 못하거나 양방향 텍스트 메시지에 응답했지만 인증 시간이 초과된 것으로 표시된다고 합니다.**

서비스의 안정성에 영향을 줄 수 있는 제어할 수 없는 요소가 있기 때문에 양방향 SMS에서 텍스트 메시지 전달 및 응답 수신이 보장되지 않습니다. 이러한 요소에는 대상 국가, 휴대폰 통신사 및 신호 강도가 포함됩니다.

사용자에게 문자 메시지를 안정적으로 수신하는 데 문제가 있는 경우 대신 모바일 앱 또는 휴대폰 전화 방법을 사용하도록 지시합니다. 모바일 앱은 셀룰러 및 Wi-Fi 연결 모두를 통해 알림을 받을 수 있습니다. 또한 장치에 신호가 전혀 없는 경우에도 모바일 앱은 인증 코드를 생성할 수 있습니다. [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) 및 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)의 경우 Microsoft Authenticator 앱을 사용할 수 있습니다.

텍스트 메시지를 사용해야 하는 경우 가능하면 양방향 SMS보다는 단방향 SMS를 사용하는 것이 좋습니다. 단방향 SMS는 보다 안정적이며 다른 국가에서 전송된 문자 메시지에 응답할 때 발생하는 전역 SMS 요금이 부과되지 않도록 합니다.

**Q: 시스템 시간이 초과되기 전에 사용자가 문자 메시지를 통해 인증 코드를 입력해야 하는 시간을 변경할 수 있나요?**

일부 경우에 가능합니다. 

Azure MFA 서버 v7.0 이상을 사용하는 단방향 SMS의 경우 레지스트리 키를 설정하여 시간 제한 설정을 구성할 수 있습니다. MFA 클라우드 서비스가 텍스트 메시지를 보내면 확인 코드(또는 일회용 암호)가 MFA 서버에 반환됩니다. MFA 서버는 코드를 기본적으로 300초 동안 메모리에 저장합니다. 300초가 경과하기 전에 사용자가 코드를 입력하지 않으면 인증이 거부됩니다. 기본 시간 제한 설정을 변경하려면 다음 단계를 사용합니다.

1. HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor로 이동합니다.
2. **pfsvc_pendingSmsTimeoutSeconds**라는 DWORD 레지스트리 키를 만들고 Azure MFA 서버에서 일회용 암호를 저장할 시간(초)을 설정합니다.

>[!TIP] 
>여러 MFA 서버를 설정한 경우 원래 인증 요청을 처리하는 서버만 사용자에게 전송된 확인 코드를 알고 있습니다. 사용자가 코드를 입력하면 유효성을 검사할 인증 요청이 동일한 서버로 전송되어야 합니다. 코드 유효성 검사가 다른 서버로 전송되면 인증이 거부됩니다. 

Azure MFA 서버를 사용하는 양방향 SMS의 경우 MFA 관리 포털에서 시간 제한 설정을 구성할 수 있습니다. 사용자가 정의된 시간 제한 기간 내 SMS에 응답하지 않는 경우 해당 인증이 거부됩니다. 

클라우드에서 Azure MFA를 사용하는 단방향 SMS의 경우(AD FS 어댑터 또는 네트워크 정책 서버 확장 프로그램 포함) 시간 제한 설정을 구성할 수 없습니다. Azure AD는 180초 동안 확인 코드를 저장합니다. 

**Q: Azure Multi-factor Authentication 서버에서 하드웨어 토큰을 사용할 수 있나요?**

Azure Multi-factor Authentication 서버를 사용하는 경우 타사 OATH(공개 인증), TOTP(시간 기반, 일회용 암호) 토큰을 가져온 후 2단계 인증에 사용할 수 있습니다.

비밀 키를 CSV 파일에 추가하고 Azure Multi-factor Authentication 서버에 가져올 수 있는 경우 OATH TOTP 토큰에 해당하는 ActiveIdentity 토큰을 사용할 수 있습니다. OATH 토큰은 클라이언트 시스템에서 사용자 입력을 수락할 수 있는 한 ADFS(Active Directory Federation Services), IIS(Internet Information Server) 폼 기반 인증 및 RADIUS(Remote Authentication Dial-In User Service)에서 사용할 수 있습니다.

다음 형식으로 타사 OATH TOTP 토큰을 가져올 수 있습니다.  

- 휴대용 대칭 키 컨테이너(PSKC)  
- 파일에 일련 번호, Base 32 형식인 암호 키 및 시간 간격이 있는 경우 CSV  

**Q: Azure Multi-factor Authentication 서버를 사용하여 터미널 서비스 보안을 유지할 수 있나요?**

예, 그렇지만 Windows Server 2012 R2 이상을 사용하는 경우 RD 게이트웨이(원격 데스크톱 게이트웨이)를 사용해서만 터미널 서비스의 보안을 유지할 수 있습니다.

Windows Server 2012 R2의 보안 변경 때문에 Azure Multi-Factor Authentication 서버가 Windows Server 2012 및 이전 버전에서 LSA(로컬 보안 기관) 보안 패키지에 연결하는 방식이 변경되었습니다. Windows 2012 이전의 터미널 서비스 버전의 경우 [Windows 인증으로 응용 프로그램 보호](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)를 수행할 수 있습니다. Windows Server 2012 R2를 사용하는 경우 RD 게이트웨이가 필요합니다.

**Q: MFA 서버에서 발신자 ID를 구성했지만 익명 발신자로부터 Multi-Factor Authentication을 받는 사용자가 여전히 있습니다.**

경우에 따라 Multi-Factor Authentication 호출이 공용 전화망을 통해 이루어진 경우 발신자 번호를 지원하지 않는 통신 회사를 통해 라우팅됩니다. 이 때문에 항상 Multi-Factor Authentication에서 호출자 ID를 보내더라도 이 ID가 보장되지 않습니다.

**Q: 내 사용자에게 보안 정보를 등록하라는 메시지가 표시되는 이유는 무엇인가요?**
사용자에게 보안 정보를 등록하라는 메시지가 표시되는 데는 여러 가지 이유가 있습니다.

- Azure AD의 관리자가 MFA를 사용할 수 있도록 사용자를 설정했지만 사용자의 계정에 대한 보안 정보가 아직 등록되어 있지 않습니다.
- 사용자가 Azure AD에서 셀프 서비스 암호 재설정을 사용하도록 설정되었습니다. 보안 정보를 통해 향후 암호를 재설정(분실한 경우)할 수 있습니다.
- 사용자가 MFA를 요구하는 조건부 액세스 정책이 있는 응용 프로그램에 액세스했고 MFA에 이전에 등록되지 않았습니다.
- 사용자는 Azure AD(Azure AD 조인 포함)로 장치를 등록하고 있으며 조직에는 장치 등록을 위해 MFA가 필요하지만 사용자는 이전에 MFA에 등록하지 않았습니다.
- 사용자는 Windows 10에서 비즈니스용 Windows Hello를 생성하고 있으며(MFA 필요) MFA에 이전에 등록되지 않았습니다.
- 조직에서 사용자에게 적용된 MFA 등록 정책을 만들고 사용하도록 설정했습니다.
- 사용자는 MFA에 대해 이전에 등록되었지만 관리자가 사용하지 않도록 설정한 인증 방법을 선택했습니다. 따라서 사용자는 MFA 등록 과정을 다시 거쳐서 새로운 기본 인증 방법을 선택해야 합니다.


## <a name="errors"></a>오류
**Q: 사용자가 모바일 앱 알림을 사용할 때 "활성화된 계정에 대한 인증 요청이 아닙니다." 오류 메시지가 표시되면 사용자는 어떻게 해야 하나요?**

모바일 앱에서 해당 계정을 제거하려면 이 절차에 따라 알린 다음 다시 추가합니다.

1. [Azure 포털 프로필](https://account.activedirectory.windowsazure.com/profile/)로 이동한 후 조직 계정으로 로그인합니다.
2. **추가 보안 인증**을 선택합니다.
3. 모바일 앱에서 기존 계정을 제거합니다.
4. **구성**을 클릭하고 지침에 따라 모바일 앱을 다시 구성합니다.

**Q: 비 브라우저 응용 프로그램에 로그인할 때 0x800434D4L 오류 메시지가 표시되면 사용자는 어떻게 해야 하나요?**

2단계 인증이 필요한 계정에서 작동하지 않는 로컬 컴퓨터에 설치된 비브라우저 응용 프로그램에 로그인을 시도할 때 0x800434D4L 오류가 발생합니다.

이 오류를 해결하려면 관리 관련 작업용 사용자 계정과 비관리 관련 작업용 사용자 계정을 따로 두어야 합니다. 나중에 관리 계정과 비관리 계정 간의 사서함을 연결하면 비관리 계정을 사용하여 Outlook에 로그인할 수 있습니다. 이 해결책에 대한 자세한 내용은 [관리자에게 사용자의 사서함 내용을 열고 보는 기능을 제공하는](http://help.outlook.com/141/gg709759.aspx?sl=1) 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계
질문이 여기에서 답변되지 않은 경우 페이지의 맨 아래에 의견을 남겨 주세요. 또는 도움말을 얻는 몇 가지 추가 옵션은 다음과 같습니다.

* [Microsoft 지원 기술 자료](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)에서 일반적인 기술 문제에 대한 솔루션을 검색합니다.
* 커뮤니티에서 기술 질문 및 대답을 검색하고 찾아보거나 [Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)에서 직접 원하는 질문을 할 수 있습니다.
* 기존 PhoneFactor 고객이며 암호를 재설정에 대해 질문이 있거나 도움이 필요한 경우 [암호 재설정](mailto:phonefactorsupport@microsoft.com) 링크를 사용하여 지원 사례를 개설하세요.
* [Azure Multi-Factor Authentication 서버(PhoneFactor) 지원](https://support.microsoft.com/oas/default.aspx?prid=14947)을 통해 지원 전문가에게 문의하세요. 문의하는 경우 가능한 문제에 대한 많은 정보를 제공해주시면 도움이 됩니다. 오류를 발견한 페이지, 특정 오류 코드, 특정 세션 ID 및 오류를 발견한 사용자의 ID를 포함하는 정보를 제공해 주시면 됩니다.
