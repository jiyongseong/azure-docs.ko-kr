---
title: 셀프 서비스 암호 재설정 빠른 시작 - Azure Active Directory
description: 신속하게 Azure AD 셀프 서비스 암호 재설정 배포
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 856ae994e49f809ca2ef906ba6cdc7f8624dd201
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>신속한 Azure AD 셀프 서비스 암호 재설정 배포

> [!IMPORTANT]
> **로그인하는 데 문제가 있나요?** [Azure AD 암호를 잊어버렸어요. 도와주세요!](../active-directory-passwords-update-your-own-password.md)를 참조하세요.

IT 관리자는 SSPR(셀프 서비스 암호 재설정)을 사용하여 사용자에게 자신의 암호 또는 계정을 재설정하거나 잠금 해제할 수 있는 권한을 간단하게 부여할 수 있습니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

이 가이드에서는 이미 여러분이 작업 평가판 또는 사용이 허가된 Azure AD(Azure Active Directory) 테넌트를 갖고 있다고 가정합니다. Azure AD 설정과 관련하여 도움이 필요한 경우 [Azure AD 시작하기](../get-started-azure-ad.md)를 참조하세요.

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Azure AD 테넌트에 대한 SSPR 사용 설정

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. 기존 Azure AD 테넌트의 **Azure Portal**에 있는 **Azure Active Directory** 아래에서 **암호 재설정**을 선택합니다.

2. **속성** 페이지의 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 옵션 아래에서 다음 중 하나를 선택합니다.
   * **없음**: 아무도 SSPR 기능을 사용할 수 없습니다.
   * **선택한 사용자**: 선택한 특정 Azure AD 그룹 구성원만 SSPR 기능을 사용할 수 있습니다. 개념 증명을 위해 이 기능을 배포할 때 사용자 그룹을 정의하고 이 설정을 사용하는 것이 좋습니다. 여기서는 보안 그룹 중첩이 지원됩니다.
   * **모두**: Azure AD 테넌트에 계정이 있는 모든 사용자가 SSPR 기능을 사용할 수 있습니다. 개념 증명을 완료하고 이 기능을 테넌트 전체에 배포할 준비가 완료된 경우 이 설정을 사용하는 것이 좋습니다.

   > [!IMPORTANT]
   > Azure 관리자 계정은 이 옵션의 설정에 관계 없이 항상 암호를 재설정할 수 있습니다. 

3. **인증 방법을** 페이지에서 다음을 선택합니다.
   * **재설정에 필요한 방법 수**: 하나(최소) 또는 둘(최대)을 지원합니다.
   * **사용자가 사용할 수 있는 방법**: 적어도 하나 이상이 필요합니다.
      * **전자 메일**: 사용자가 구성한 인증 이메일 주소로 코드가 포함된 전자 메일을 보냅니다.
      * **휴대폰**: 사용자가 구성한 휴대폰 번호로 코드가 포함된 전화 또는 문자 메시지 중에 무엇을 받을 것인지 선택할 수 있습니다.
      * **사무실 전화**: 사용자가 구성한 사무실 전화 번호로 코드가 포함된 전화를 겁니다.
      * **보안 질문**: 다음을 선택해야 합니다.
         * **등록에 필요한 질문 수**: 등록에 필요한 최소 질문 수입니다. 사용자는 더 많은 질문에 답하도록 선택하여 질문을 가져올 질문 풀을 만들 수 있습니다. 이 옵션은 3-5개 질문으로 설정할 수 있으며 암호 재설정에 필요한 질문 수보다 많거나 같아야 합니다. 사용자는 보안 질문을 선택할 때 **사용자 지정** 단추를 눌러 사용자 지정 질문을 추가할 수 있습니다.
         * **재설정에 필요한 질문 수**: 3-5개 질문에 정확하게 대답해야만 사용자 암호를 재설정하거나 잠금 해제할 수 있도록 설정할 수 있습니다.
            
    ![인증][Authentication]

4. 권장 사항: **사용자 지정** 아래에서 **관리자에게 문의** 링크가 사용자가 정의한 페이지 또는 이메일 주소를 가리키도록 변경할 수 있습니다. 이 링크는 사용자가 이미 지원 질문에 사용하고 있는 이메일 주소 또는 웹 사이트로 설정하는 것이 좋습니다.

5. 선택 사항: **등록** 페이지는 관리자에게 다음 옵션을 제공합니다.
   * 사용자가 로그인할 때 등록하도록 요구.
   * 사용자가 인증 정보를 다시 확인해야 하기 전의 일 수 설정.

6. 선택 사항: **알림** 페이지는 관리자에게 다음 옵션을 제공합니다.
   * 사용자에게 암호 재설정에 대해 알림.
   * 다른 관리자가 암호를 재설정하면 모든 관리자에게 알림.

현재는 Azure AD 테넌트에 SSPR을 구성했습니다. 이제는 관리자가 개입하지 않고 사용자가 [셀프 서비스 암호 재설정 등록](../active-directory-passwords-reset-register.md) 및 [암호 재설정 또는 변경](../active-directory-passwords-update-your-own-password.md) 문서의 지침을 사용하여 자신의 암호를 업데이트할 수 있습니다. 클라우드 전용인 경우 여기서 멈출 수 있습니다. 또는 다음 섹션을 계속 진행하여 온-프레미스 Active Directory 도메인과 암호 동기화를 구성할 수 있습니다.

> [!TIP]
> 관리자가 아닌 사용자로 SSPR을 테스트합니다. Microsoft가 Azure 관리자 계정에 강력한 인증 요구 사항을 적용하기 때문입니다. 관리자 암호 정책에 대한 자세한 내용은 [암호 정책](concept-sspr-policy.md#administrator-password-policy-differences) 문서를 참조하세요.

## <a name="configure-synchronization-to-an-existing-identity-source"></a>기존 ID 소스와 동기화 구성

Azure AD에 온-프레미스 ID 동기화를 사용하려면 조직의 서버에 [Azure AD Connect](./../connect/active-directory-aadconnect.md)를 설치하고 구성해야 합니다. 이 응용 프로그램은 기존 ID 소스의 사용자 및 그룹을 Azure AD 테넌트와 동기화합니다. 자세한 내용은 다음을 참조하세요.

* [DirSync 또는 Azure AD Sync에서 Azure AD Connect로 업그레이드](./../connect/active-directory-aadconnect-dirsync-deprecated.md)
* [기본 설정을 사용하여 Azure AD Connect 시작](./../connect/active-directory-aadconnect-get-started-express.md)
* [비밀번호 쓰기 저장을 구성](howto-sspr-writeback.md#configure-password-writeback)하여 Azure AD에서 온-프레미스 디렉터리로 비밀번호를 다시 기록합니다

### <a name="on-premises-policy-change"></a>온-프레미스 정책 변경

온-프레미스 Active Directory 도메인의 사용자를 동기화하고 사용자가 자신의 암호를 즉시 다시 설정할 수 있게 허용하려면 온-프레미스 암호 정책을 다음과 같이 변경합니다.

1. **컴퓨터 구성** > **정책** > **Windows 설정** > **보안 설정**  >  **계정 정책** > **암호 정책**으로 이동합니다.

2. **최소 암호 사용 기간**을 **0일**로 설정합니다.

이 보안 설정은 사용자가 암호를 변경하기 전에 해당 암호를 사용해야 하는 기간(일)을 결정합니다. 최소 사용 기간을 **0일**로 설정하면 지원 팀에서 사용자의 암호를 변경하는 경우에 사용자가 SSPR을 사용할 수 있습니다.

![정책][Policy]

## <a name="disable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용 안 함

셀프 서비스 암호 재설정을 사용하지 않게 설정하는 방법은 간단합니다. Azure AD 테넌트를 열고 **암호 재설정** > **속성**으로 이동한 다음 **셀프 서비스 암호 재설정이 사용하도록 설정됨** 아래에서 **없음**을 선택합니다.

### <a name="learn-more"></a>자세한 정보
다음 문서에서는 Azure AD를 통한 암호 재설정에 대한 추가 정보를 제공합니다.

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](howto-sspr-deployment.md)
* [암호 재설정 또는 변경](../active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](concept-sspr-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](howto-sspr-authenticationdata.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](concept-sspr-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](howto-sspr-writeback.md)
* [SSPR 작업은 어떻게 보고 합니까?](howto-sspr-reporting.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](concept-sspr-howitworks.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)

## <a name="next-steps"></a>다음 단계

이 퀵스타트에서는 사용자를 위한 셀프 서비스 암호 재설정 구성 방법을 알게 되었습니다. 이 단계를 완료하려면 Azure Portal에서 계속 진행합니다.

> [!div class="nextstepaction"]
> [셀프 서비스 암호 재설정 사용](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "사용 가능한 Azure AD 인증 방법 및 필요한 수량"
[Policy]: ./media/quickstart-sspr/password-policy.png "0일로 설정된 온-프레미스 암호 그룹 정책"

