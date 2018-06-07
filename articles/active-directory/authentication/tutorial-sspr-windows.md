---
title: Windows 10 로그인 화면의 Azure AD SSPR | Microsoft Docs
description: Windows 10 로그인 화면 Azure AD 암호 재설정 구성 및 PIN 잊음
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 04/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 701ab041eb603a1a06025d5bb43964880353ada9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>로그인 화면에서 Azure AD 암호 재설정

Azure AD SSPR(셀프 서비스 암호 재설정)을 이미 배포했지만 사용자는 암호를 잊어버린 경우 여전히 기술 지원팀을 호출할 수 있습니다. 사용자는 웹 브라우저를 사용하여 SSPR에 액세스할 수 없기 때문에 기술 지원팀을 호출합니다.

새로운 Windows 10 2018년 4월 업데이트에서는 **Azure AD 조인 장치** 또는 **하이브리드 Azure AD 조인** 장치를 사용하는 사용자의 로그인 화면에 사용 가능한 "암호 재설정" 링크가 표시됩니다. 이 링크를 클릭하면 익숙한 SSPR(셀프 서비스 암호 재설정) 환경으로 이동됩니다.

사용자가 Windows 10 로그인 화면에서 Azure AD 암호를 재설정할 수 있게 하려면 다음 요구 사항을 충족해야 합니다.

* Windows 10 2018년 4월 업데이트 또는 [Azure AD에 조인된](../device-management-azure-portal.md) 또는 [하이브리드 Azure AD에 조인된](../device-management-hybrid-azuread-joined-devices-setup.md) 최신 클라이언트.
* Azure AD 셀프 서비스 암호 재설정을 사용하도록 설정.
* 다음 방법 중 하나를 통해 암호 재설정 링크를 사용하도록 설정을 구성하고 배포.
   * [Intune 장치 구성 프로필](tutorial-sspr-windows.md#configure-reset-password-link-using-intune)
   * [레지스트리 키](tutorial-sspr-windows.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>Intune을 사용하여 암호 재설정 링크 구성

### <a name="create-a-device-configuration-policy-in-intune"></a>Intune에서 장치 구성 정책 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 클릭
2. **장치 구성** > **프로필** > **프로필 만들기**로 이동하여 새 장치 구성 프로필 만들기
   * 프로필의 의미 있는 이름 입력
   * 필요에 따라 프로필에 대한 의미 있는 설명 입력
   * 플랫폼 **Windows 10 이상**
   * 프로필 형식 **사용자 지정**

   ![프로필 만들기][CreateProfile]

3. **설정** 구성
   * 다음 OMA URI 설정을 **추가**하여 암호 재설정 링크를 사용하도록 설정
      * 설정이 하는 일을 설명하는 의미 있는 이름 입력
      * 필요에 따라 설정에 대한 의미 있는 설명 입력
      * **OMA URI**을 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`으로 설정
      * **데이터 형식**을 **정수**로 설정
      * **값**을 **1**로 설정
      * **확인**
   * **확인**
4. **만들기**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Intune에서 장치 구성 정책 할당

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>장치 구성 정책을 적용할 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인하여 **Azure Active Directory** 클릭
2. **사용자 및 그룹** > **모든 그룹** > **새 그룹**으로 이동
3. 그룹 이름을 입력하고 **구성원 자격 유형** 아래에서 **할당됨** 선택
   * **구성원** 아래에서 정책을 적용할 Azure AD 가입 Windows 10 장치 선택
   * **선택** 클릭
4. **만들기**

그룹을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../active-directory-manage-groups.md) 문서에서 찾을 수 있습니다.

#### <a name="assign-device-configuration-policy-to-device-group"></a>장치 그룹에 장치 구성 정책 할당

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Intune** 클릭
2. **장치 구성** > **프로필**로 이동한 후 앞에서 만든 프로필을 클릭하여 앞에서 만든 장치 구성 프로필 찾기
3. 해당 프로필을 장치 그룹에 할당 
   * **포함** > **포함할 그룹 선택** 아래에서 **할당** 클릭
   * 앞에서 만든 그룹을 선택하고 **선택** 클릭
   * 설정 메뉴에서 **저장**

   ![할당][Assignment]

Intune을 사용하여 로그온 화면에 암호 재설정 링크를 사용하도록 설정하기 위한 장치 구성 정책을 만들어서 할당했습니다.

## <a name="configure-reset-password-link-using-the-registry"></a>레지스트리를 사용하여 암호 재설정 링크 구성

설정 변경을 테스트할 때에는 이 메서드를 사용하는 것이 좋습니다.

1. 관리 자격 증명을 사용하여 Azure AD 가입 장치에 로그인
2. 관리자 권한으로 **regedit** 실행
3. 다음 레지스트리 키를 설정
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

정책을 구성하고 할당했으니, 사용자에 어떤 변화가 있을까요? 로그온 화면에서 암호를 재설정할 수 있다는 사실을 사용자가 어떻게 알 수 있을까요?

![로그인 화면][LoginScreen]

이제 사용자가 로그인을 시도하면 셀프 서비스 암호 재설정 환경을 제공하는 암호 재설정 링크가 로그인 화면에 표시됩니다. 사용자는 이 기능을 사용하면 다른 장치를 사용하여 웹 브라우저에 액세스할 필요 없이 암호를 재설정할 수 있습니다.

이 기능을 사용하는 방법에 대한 지침은 [회사 또는 학교 암호 재설정](../active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in)에서 찾을 수 있습니다.

## <a name="common-issues"></a>일반적인 문제

Hyper-V를 사용하여 이 기능을 테스트할 때에는 "암호 재설정" 링크가 표시되지 않습니다.

* 테스트에 사용하는 VM으로 이동하여 **보기**를 클릭한 다음 **고급 세션**을 선택 취소합니다.

원격 데스크톱을 사용하여 이 기능을 테스트할 때에는 "암호 재설정" 링크가 표시되지 않습니다.

* 현재 원격 데스크톱에서는 암호 재설정이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

다음 링크는 Azure AD를 사용한 암호 재설정에 대한 추가 정보를 제공합니다.

* [SSPR을 배포하려면 어떻게 하나요?](howto-sspr-deployment.md)
* [로그인 화면에서 PIN 재설정을 사용하려면 어떻게 하나요?](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [MDM 인증 정책에 대한 자세한 정보](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/tutorial-sspr-windows/create-profile.png "Windows 10 로그온 화면에서 암호 재설정 링크를 사용하도록 설정하기 위한 Intune 장치 구성 프로필 만들기"
[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Windows 10 장치 그룹에 Intune 장치 구성 정책 할당"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Windows 10 로그온 화면의 암호 재설정 링크"
