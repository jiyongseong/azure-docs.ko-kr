---
title: Azure AD 계층 암호 보안 | Microsoft Docs
description: Azure AD가 강력한 암호를 적용하고 사이버 범죄자로부터 사용자 암호를 보호하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 04e437eb5a040f418df22e0857cb96d7ef99bbbb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Azure AD 암호 보호에 대한 다중 계층 접근 방법

이 문서에서는 Azure AD(Azure Active Directory) 또는 Microsoft 계정을 보호하기 위해 사용자 또는 관리자로서 수행할 수 있는 몇 가지 모범 사례를 설명합니다.

 > [!NOTE]
 > **로그인하는 데 문제가 있나요?** 그렇다면 [여기를 클릭하여 암호를 변경하고 재설정하는 방법을 알아보세요](active-directory-passwords-update-your-own-password.md).
 >
 > Azure AD 관리자는 [Azure Active Directory에서 사용자 암호 재설정](active-directory-users-reset-password-azure-portal.md) 문서의 지침을 사용하여 사용자 암호를 다시 설정할 수 있습니다.
 >

## <a name="password-requirements"></a>암호 요구 사항

Azure AD는 암호를 보호하기 위해 다음과 같은 일반적인 접근 방식을 포함합니다.

* 암호 길이 요구 사항
* 암호 복잡성 요구 사항
* 일반 및 정기적인 암호 만료

Azure Active Directory의 암호 재설정에 대한 자세한 내용은 [IT 전문가를 위한 Azure AD 셀프 서비스 암호 재설정](active-directory-passwords-update-your-own-password.md) 항목을 참조하세요.

## <a name="azure-ad-password-protections"></a>Azure AD 암호 보호

Azure AD 및 Microsoft 계정 시스템은 사용자 및 관리자 암호를 안전하게 보호하기 위해 다음을 비롯하여 업계에서 증명된 접근 방법을 사용합니다.

* 동적으로 금지된 암호
* 스마트 암호 잠금

현재 연구 결과에 따른 암호 관리에 대한 내용은 [암호 지침](https://aka.ms/passwordguidance) 백서를 참조하세요.

### <a name="dynamically-banned-passwords"></a>동적으로 금지된 암호

Azure AD 및 Microsoft 계정은 일반적으로 사용되는 암호를 동적으로 금지함으로써 암호 보호를 보호합니다. Azure AD ID 보호 팀은 금지된 암호 목록을 정기적으로 분석하여 사용자가 자주 사용되는 암호를 선택하지 않도록 방지합니다. Azure AD 및 Microsoft 계정 서비스 고객은 이 서비스를 사용할 수 있습니다.

암호를 만들 때 관리자는 사용자가 고유한 글자, 숫자, 문자 또는 단어 조합을 포함하는 암호 구문을 선택했는지 확인해야 합니다. 이 방법은 사용자 암호가 거의 노출되지 않게 하면서 사용자가 보다 쉽게 기억할 수 있도록 합니다.

#### <a name="password-breaches"></a>암호 위반

Microsoft는 항상 한 발 앞서 사이버 범죄에 대처하려고 합니다.

Azure AD ID 보호 팀은 일반적으로 사용되는 암호를 지속적으로 분석합니다. 사이버 범죄자들도 암호 해시를 공격하는 [레인보우 테이블](https://en.wikipedia.org/wiki/Rainbow_table)을 빌드하는 등 공격을 알리는 데 유사한 전략을 사용합니다.

Microsoft는 지속적으로 [데이터 위반](https://www.privacyrights.org/data-breaches)을 분석하여 동적으로 업데이트된 차단 암호 목록을 관리합니다. 그러면 취약한 암호가 Azure AD 고객에게 실제 위협이 되기 전에 차단할 수 있습니다. 현재 보안 노력에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/sir/default.aspx)를 참조하세요.

### <a name="smart-password-lockout"></a>스마트 암호 잠금

Azure AD에서 사용자 암호를 해킹하려는 잠재적인 사이버 범죄를 감지하면 스마트 암호 잠금을 사용하여 사용자 계정을 잠그게 됩니다. Azure AD는 특정 로그인 세션과 연결된 위험성을 확인하도록 설계되었습니다. 그런 후 최신 보안 데이터를 사용하여 잠금 의미 체계를 적용함으로써 사이버 위협을 막을 수 있습니다.

사용자가 Azure AD에서 차단된 경우 해당 화면은 다음과 비슷하게 표시됩니다.

  ![Azure AD에서 차단](./media/active-directory-secure-passwords/locked-out-azuread.png)

다른 Microsoft 계정의 경우 해당 화면은 다음과 비슷하게 표시됩니다.

  ![Microsoft 계정에서 차단](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Azure Active Directory의 암호 재설정에 대한 자세한 내용은 [IT 전문가를 위한 Azure AD 셀프 서비스 암호 재설정](active-directory-passwords-update-your-own-password.md) 항목을 참조하세요.

  >[!NOTE]
  >Azure AD 관리자인 경우 [Windows Hello](https://www.microsoft.com/windows/windows-hello)를 사용하여 사용자가 기존 암호를 모두 만들지 않도록 할 수 있습니다.
  >

## <a name="next-steps"></a>다음 단계

* [고유한 암호를 업데이트하는 방법](active-directory-passwords-update-your-own-password.md)
* [Azure ID 관리의 기본 항목](fundamentals-identity.md)
* [암호 재설정 활동에 대한 보고서](authentication/howto-sspr-reporting.md)
