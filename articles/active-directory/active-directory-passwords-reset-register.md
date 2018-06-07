---
title: "셀프 서비스 암호 재설정 등록 - Azure Active Directory"
description: "Azure AD 셀프 서비스 암호 재설정을 위한 인증 데이터를 등록합니다."
services: active-directory
keywords: 
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: barlan
ms.custom: end-user;seohack1
ms.openlocfilehash: dbb82e125381a4307ed742294f880600612ffc52
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="register-for-self-service-password-reset"></a>셀프 서비스 암호 재설정 등록

> [!IMPORTANT]
> 로그인할 수 없어서 여기에 있나요? 그렇다면 [회사 또는 학교 암호 재설정](active-directory-passwords-update-your-own-password.md)을 참조하세요.

최종 사용자는 Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하는 경우 암호를 재설정하거나 계정 잠금을 직접 해제할 수 있습니다. 이 기능을 사용하려면 먼저 인증 방법을 등록하거나 관리자가 입력한 미리 정의된 인증 방법을 확인해야 합니다.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR을 사용하여 인증 데이터 등록 또는 확인

1. 장치에서 웹 브라우저를 열고 [암호 재설정 등록 페이지](https://aka.ms/ssprsetup)로 이동합니다.
2. 관리자가 제공한 사용자 이름과 암호를 입력합니다.
3. IT 직원이 작업을 구성한 방식에 따라 구성하고 확인하는 데 다음 옵션 중 하나 이상을 사용할 수 있습니다. 관리자가 사용자 정보를 사용할 수 있는 권한이 있는 경우 사용자의 일부 정보를 채울 수 있습니다.
    * **사무실 전화**: 관리자만 이 옵션을 설정할 수 있습니다.
    * **인증 전화**: 이 옵션을 액세스 권한이 있는 다른 전화 번호로 설정합니다. 예제는 문자 또는 호출을 받을 수 있는 휴대폰입니다.
    * **인증 전자 메일**: 재설정하려는 암호 없이 액세스할 수 있는 대체 전자 메일 주소로 이 옵션을 설정합니다.
    * **보안 질문**: 사용자가 응답하도록 관리자가 승인한 질문 목록입니다. 같은 질문을 사용하거나 두 번 이상 답변할 수 없습니다.
4. 관리자가 요구하는 정보를 제공하고 확인합니다. 둘 이상의 옵션을 사용할 수 있는 경우 여러 개의 방법을 등록하는 것이 좋습니다. 이렇게 하면 방법 중 하나를 사용할 수 없을 때 유연하게 작업할 수 있습니다. 출장 중이어서 사무실 전화에 액세스할 수 없는 경우를 예로 들 수 있습니다.

    ![인증 방법 등록 후 마침 선택][Register]

5. **마침**을 선택합니다. 나중에 필요할 때 SSPR을 사용할 수 있습니다.

**인증 전화** 또는 **인증 전자 메일**에 데이터를 입력하면 전역 디렉터리에 표시되지 않습니다. 이 데이터를 볼 수 있는 유일한 사람은 사용자와 관리자입니다. 보안 질문에 대한 대답만 볼 수 있습니다.

관리자는 적절한 방법을 등록했는지 확인하기 위해 일정 기간 후에 인증 방법을 확인해야 할 수 있습니다.

## <a name="common-problems-and-their-solutions"></a>일반적인 문제 및 해결 방법

 다음은 일반적인 오류 사례 및 해결 방법입니다.

| 오류 사례| 어떤 오류가 표시되나요?| 해결 방법 |
| --- | --- | --- |
| 내 사용자 ID를 입력한 후에 "관리자에게 문의하세요" 페이지가 보입니다. | 관리자에게 문의하세요. <br> <br> Microsoft에서 사용자 계정 암호가 관리되지 않는 것이 감지되었습니다. 결과적으로 자동으로 암호를 재설정할 수 없습니다. <br> <br> IT 직원에게 추가 지원을 요청하세요. | IT 직원이 온-프레미스 환경에서 암호를 관리하고 **계정 링크에 액세스할 수 없음**에서 암호를 재설정할 수 없기 때문에 이 메시지가 표시됩니다. <br> <br> 암호를 다시 설정하려면 도움말에 대해 IT 직원에게 직접 문의합니다. 이 기능을 활성화할 수 있도록 암호를 재설정하길 원한다는 것을 해당 IT 직원에게 알려주세요.|
| 내 사용자 ID를 입력한 후에 "계정은 암호 재설정에 사용할 수 없습니다." 오류가 발생합니다. | 암호 재설정을 위해 계정을 사용할 수 없습니다. <br> <br> 죄송하지만 IT 직원이 계정을 이 서비스와 함께 사용하도록 설정하지 않습니다. <br> <br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | IT 직원이 **계정에 액세스할 수 없음** 링크에서 조직에 대해 암호 재설정을 사용하도록 설정하지 않았거나 기능을 사용하도록 허가하지 않았기 때문에 이 메시지가 표시됩니다. <br> <br> 암호를 재설정하려면 **관리자에게 문의하세요.** 링크를 선택합니다. 회사의 IT 직원에게 전자 메일이 전송됩니다. 이 전자 메일은 이 기능을 활성화할 수 있도록 암호를 재설정하길 원한다는 것을 해당 IT 직원에게 알려줍니다. |
| 내 사용자 ID를 입력한 후에 "계정을 확인하지 못했습니다" 오류가 발생합니다. | 계정을 확인하지 못했습니다. <br> <br> 원하는 경우 사용자의 조직에서 관리자에게 연락하여 사용자용 암호를 재설정할 수 있습니다. | 이 메시지는 암호 재설정을 사용할 수 있지만 서비스를 사용하도록 등록하지 않은 경우 표시됩니다. 암호 재설정을 등록하려면 계정에 대한 액세스를 회복한 후에 [암호 재설정 등록 페이지](https://aka.ms/ssprsetup)로 이동합니다. <br> <br> 암호를 재설정하려면 **관리자에게 문의하세요.** 링크를 선택하여 회사의 IT 직원에게 메일을 보냅니다. |

## <a name="next-steps"></a>다음 단계

* [셀프 서비스 암호 재설정을 사용하여 암호 변경](active-directory-passwords-update-your-own-password.md)
* [암호 재설정 등록 페이지](https://aka.ms/ssprsetup)
* [암호 재설정 포털](https://passwordreset.microsoftonline.com/)
* [Microsoft 계정에 로그인할 수 없는 경우](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "등록된 방법을 보여 주는 암호 재설정 등록 페이지 및 마침 단추"

