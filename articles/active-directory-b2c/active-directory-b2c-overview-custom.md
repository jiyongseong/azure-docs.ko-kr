---
title: 'Azure Active Directory B2C: 사용자 지정 정책 | Microsoft Docs'
description: Azure Active Directory B2C 사용자 지정 정책에 대한 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/04/2017
ms.author: davidmu
ms.openlocfilehash: 22d34ac4128da1d1a9f20619aec2aaccc2425a21
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32138920"
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>사용자 지정 정책이란?

사용자 지정 정책은 Azure AD B2C 테넌트의 동작을 정의하는 구성 파일입니다. Azure AD B2C 포털에서 가장 일반적인 ID 작업에 대해 **기본 제공 정책**이 미리 정의되는 반면 거의 무제한의 많은 작업을 완료하기 위해 ID 개발자는 사용자 지정 정책을 완벽하게 편집할 수 있습니다. 사용자 지정 정책이 사용자 및 ID 시나리오에 알맞은지 확인하려면 계속 읽습니다.

## <a name="comparing-built-in-policies-and-custom-policies"></a>기본 제공 정책과 사용자 지정 정책 비교

| | 기본 제공 정책 | 사용자 지정 정책 |
|-|-------------------|-----------------|
|대상 사용자 | ID 전문 지식이 있거나 없는 모든 앱 개발자 | ID 전문가: 시스템 통합 서비스, 컨설턴트 및 사내 ID 팀입니다. OpenIDConnect 흐름에 만족하고 ID 공급자와 클레임 기반 인증을 이해합니다. |
|구성 방법 | 사용자에게 친숙한 UI를 사용하는 Azure Portal | XML 파일 직접 편집 및 Azure Portal에 업로드 |
|UI 사용자 지정 | HTML, CSS 및 JavaScript 지원을 비롯한 전체 UI 사용자 지정(사용자 지정 도메인 필요)<br><br>사용자 지정 문자열을 사용하여 다국어 지원 | 동일 |
| 특성 사용자 지정 | 표준 및 사용자 지정 특성 | 동일 |
|토큰 및 세션 관리 | 사용자 지정 토큰 및 다중 세션 옵션 | 동일 |
|ID 공급자| **현재**: 미리 정의된 로컬, 소셜 공급자<br><br>**이후**: 표준 기반 OIDC, SAML, OAuth | **현재**: 표준 기반 OIDC, OAUTH, SAML<br><br>**이후**: WsFed |
|ID 작업(예제) | 로컬 및 많은 소셜 계정을 사용하여 등록 또는 로그인<br><br>셀프 서비스 암호 재설정<br><br>프로필 편집<br><br>Multi-Factor Auth 시나리오<br><br>토큰 및 세션 사용자 지정<br><br>액세스 토큰 흐름 | 사용자 지정 ID 공급자를 사용하는 기본 정책과 동일한 작업 완료 또는 사용자 지정 범위 사용<br><br>등록 시 다른 시스템에서 사용자 프로비전<br><br>고유한 전자 메일 서비스 공급자를 사용하여 환영 전자 메일 보내기<br><br>B2C 외부 사용자 저장소 사용<br><br>API를 통해 신뢰할 수 있는 시스템을 사용하여 사용자 제공 정보의 유효성 검사 |

## <a name="policy-files"></a>정책 파일

사용자 지정 정책은 계층 구조 체인에서 서로를 참조하는 하나 또는 여러 XML 형식 파일로 표시됩니다. XML 요소는 다른 요소 간에 클레임 스키마, 클레임 변환, 콘텐츠 정의, 클레임 공급자/기술 프로필 및 Userjourney 오케스트레이션 단계를 정의합니다.

세 가지 종류의 정책 파일을 사용하는 것이 좋습니다.

- **기본 파일**은 Azure에서 전체 샘플을 제공하므로 대부분의 정의를 포함합니다.  문제 해결 및 정책을 장기적인 유지 관리에 도움이 되도록 이 파일을 최소한으로 변경하는 것이 좋습니다.
- **확장 파일**은 테넌트에 대한 고유한 구성 변경 내용을 보유합니다.
- **RP(신뢰 당사자) 파일**은 응용 프로그램 또는 서비스(즉, 신뢰 당사자)에 의해 직접 호출되는 단일 작업 중심 파일입니다.  자세한 정보는 정책 파일 정의에 대한 문서를 읽어보세요.  각 고유 작업에는 고유한 RP이 필요하고 브랜딩 요구 사항에 따라 "전체 응용 프로그램 x 전체 사용 사례 수"일 수 있습니다.


Azure AD B2C의 기본 제공 정책은 위에 표시된 3개의 파일 패턴을 따르지만 개발자는 포털이 확장 파일에 대한 변경 내용을 백그라운드에서 수행하는 동안 개발자는 RP(신뢰 당사자) 파일만을 확인할 수 있습니다.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>사용자 지정 정책을 사용하는 경우 알아야 할 핵심 개념

### <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C

Azure의 CIAM(고객 ID 및 액세스 관리) 서비스입니다. 서비스에는 다음이 포함됩니다.

1. 특수한 용도의 Azure Active Directory 형태인 사용자 디렉터리는 Microsoft Graph를 통해 액세스할 수 있고 로컬 계정 및 페더레이션된 계정 모두에 대한 사용자 데이터를 보유합니다. 
2. **ID 경험 프레임워크**에 액세스하여 사용자와 엔터티 간의 신뢰를 조정하고, ID/액세스 관리 작업을 완료하기 위해 이들 간에 클레임을 전달합니다. 
3. STS(보안 토큰 서비스)는 ID 토큰, 새로 고침 토큰 및 액세스 토큰(및 해당하는 SAML 어설션)을 발급하고 유효성을 검사하여 리소스를 보호합니다.

Azure AD B2C는 시퀀스에서 ID 공급자, 사용자, 다른 시스템 및 로컬 사용자 디렉터리와 상호 작용하여 ID 작업(예: 사용자 로그인, 새 사용자 등록, 암호 재설정)을 수행합니다. 다자간 신뢰를 설정하고 이러한 단계를 실행하는 기본 플랫폼을 ID 경험 프레임워크라고 하며, 정책(사용자 경험 또는 보안 프레임워크 정책이라고도 함)은 수행할 행위자, 작업, 프로토콜 및 일련의 단계를 명시적으로 정의합니다.

### <a name="identity-experience-framework"></a>ID 경험 프레임워크

OpenIDConnect, OAuth, SAML, WSFed 및 일부 비표준 형식(예: REST API 기반 시스템 간 클레임 교환)과 같은 표준 프로토콜 형식인 엔터티(광범위하게 클레임 공급자) 간의 신뢰를 조정하는 완전히 구성 가능한 정책 기반이며 클라우드 기반인 Azure 플랫폼입니다. I2E는 HTML, CSS 및 JavaScript를 지원하는 사용자에게 친숙한 white labeled 환경을 만듭니다.  현재 ID 경험 프레임워크는 Azure AD B2C 서비스의 컨텍스트에서만 사용할 수 있으며 CIAM과 관련된 작업에 우선적으로 적용됩니다.

### <a name="built-in-policies"></a>기본 제공 정책

가장 일반적으로 사용되는 ID 작업(예: 사용자 등록, 로그인, 암호 재설정)을 수행하고 Azure AD B2C에서 관계가 미리 정의된 신뢰할 수 있는 대상(예: Facebook ID 공급자, LinkedIn, Microsoft 계정, Google 계정)과 상호 작용하도록 Azure AD B2C의 동작을 지시하는 미리 정의된 구성 파일입니다.  나중에 일반적으로 Azure Active Directory Premium, Active Directory/ADFS, Salesforce ID Provider와 같은 엔터프라이즈 영역에 있는 ID 공급자의 사용자 지정을 위해 기본 제공 정책을 제공할 수도 있습니다.


### <a name="custom-policies"></a>사용자 지정 정책

Azure AD B2C 테넌트에서 ID 경험 프레임워크의 동작을 정의하는 구성 파일입니다. 사용자 지정 정책은 신뢰 당사자(예: 응용 프로그램)에서 호출할 때 ID 경험 프레임워크에서 실행하는 하나 이상의 XML 파일(정책 파일 정의 참조)로 액세스할 수 있습니다. 사용자 지정 정책은 ID 개발자가 직접 편집하여 거의 무제한의 작업을 완료할 수 있습니다. 사용자 지정 정책을 구성하는 개발자는 신뢰할 수 있는 관계를 세부적으로 신중하게 정의하여 메타데이터 끝점, 정확한 클레임 정의 교환을 포함하고 ID 공급자 각각의 필요에 따라 암호, 키 및 인증서를 구성해야 합니다.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Trustframeworks ID 경험 프레임워크에 대한 정책 파일 정의

### <a name="policy-files"></a>정책 파일

사용자 지정 정책은 계층 구조 체인에서 서로를 참조하는 하나 또는 여러 XML 형식 파일로 표시됩니다. XML 요소는 다른 요소 간에 클레임 스키마, 클레임 변환, 콘텐츠 정의, 클레임 공급자/기술 프로필 및 사용자 경험 오케스트레이션 단계를 정의합니다.  세 가지 종류의 정책 파일을 사용하는 것이 좋습니다.

- **기본 파일**은 Azure에서 전체 샘플을 제공하므로 대부분의 정의를 포함합니다.  문제 해결 및 정책을 장기적인 유지 관리에 도움이 되도록 이 파일을 최소한으로 변경하는 것이 좋습니다.
- **확장 파일**은 테넌트에 대한 고유한 구성 변경 내용을 보유합니다.
- **RP(신뢰 당사자) 파일**은 응용 프로그램 또는 서비스(즉, 신뢰 당사자)에 의해 직접 호출되는 단일 작업 중심 파일입니다.  자세한 정보는 정책 파일 정의에 대한 문서를 읽어보세요.  각 고유 작업에는 고유한 RP이 필요하고 브랜딩 요구 사항에 따라 "전체 응용 프로그램 x 전체 사용 사례 수"일 수 있습니다.

![정책 파일 형식](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| 정책 파일의 종류 | 예제 파일 이름 | 권장되는 사용 | 상속 원본 |
|---------------------|--------------------|-----------------|---------------|
| 기본 |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Microsoft에서 구성된 핵심 클레임 스키마, 클레임 변환, 클레임 공급자 및 사용자 경험을 포함합니다.<br><br>이 파일에 대한 최소한의 변경 | 없음 |
| 확장(EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | 여기에서 기본 파일에 대한 변경 내용 통합<br><br>수정된 클레임 공급자<br><br>수정된 사용자 경험<br><br>사용자 고유의 사용자 지정 스키마 정의 | 기본 파일 |
| 신뢰 당사자(RP) | B2C_1A_sign_up_sign_in.xml| 여기에서 토큰 셰이프 및 세션 설정 변경| Extensions(EXT) 파일 |

### <a name="inheritance-model"></a>상속 모델

응용 프로그램에서 RP 정책 파일을 호출하면 B2C의 ID 경험 프레임워크에서 기본 파일, 확장 파일 및 마지막으로 RP 정책 파일의 모든 요소를 추가하여 현재 적용 중인 정책을 효과적으로 조합합니다.  RP 파일에서 동일한 형식 및 이름이라는 요소는 확장에 있는 동일한 요소를 재정의하고 확장의 요소는 기본 요소를 재정의합니다.

Azure AD B2C의 **기본 제공 정책**은 위에 표시된 3개의 파일 패턴을 따르지만 개발자는 포털이 확장 파일에 대한 변경 내용을 백그라운드에서 수행하는 동안 개발자는 RP(신뢰 당사자) 파일만을 확인할 수 있습니다.  모든 Azure AD B2C는 Azure B2C 팀의 제어 아래에서 자주 업데이트되는 기본 정책 파일을 공유합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)
