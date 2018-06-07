---
title: Azure Active Directory B2C란? | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 응용 프로그램 로그인 환경을 만들고 관리하는 방법을 살펴봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.openlocfilehash: ca9e45a214639da86cf8e0c4a39b3e3d6b6d6491
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory-b2c"></a>Azure Active Directory B2C란?

Azure AD(Azure Active Directory) B2C는 응용 프로그램을 사용할 때 고객이 자신의 프로필을 등록, 로그인 및 관리하는 방법을 사용자 지정하고 제어할 수 있는 ID 관리 서비스입니다. 여기에는 iOS, Android 및 .NET용으로 개발된 응용 프로그램이 포함됩니다. Azure AD B2C를 사용하면 고객의 ID를 보호하면서 이러한 작업을 수행할 수 있습니다.

Azure AD B2C에 등록된 응용 프로그램을 구성하여 다양한 ID 관리 작업을 수행할 수 있습니다. 일부 사례:

- 고객이 가입하여 등록된 응용 프로그램을 사용할 수 있도록 설정
- 가입한 고객이 로그인하여 응용 프로그램 사용을 시작할 수 있도록 설정
- 가입한 고객이 자신의 프로필을 편집할 수 있도록 설정
- 응용 프로그램에서 다단계 인증을 사용하도록 설정
- 고객이 특정 ID 공급자를 통해 가입 및 로그인할 수 있도록 설정
- 사용자가 작성한 API에 대한 응용 프로그램으로부터의 액세스 권한 부여
- 가입 및 로그인 환경 사용자 지정
- 응용 프로그램에 대한 Single Sign-On 관리

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>Azure AD B2C를 사용하기 전의 고려 사항

- 고객이 내 응용 프로그램과 상호 작용하는 방법
- 고객에게 제공하고자 하는 UI(사용자 인터페이스)
- 고객이 내 응용 프로그램에서 선택하게 할 ID 공급자 
- 내 로그인 프로세스에서 추가 API 실행의 필요 여부

### <a name="customer-interaction"></a>고객 상호 작용

Azure AD B2C는 모든 고객 환경에 대해 [OpenID Connect](https://openid.net/connect/)를 지원합니다. OpenID Connect의 Azure AD B2C 구현에서 응용 프로그램은 Azure AD B2C로 인증 요청을 발급하여 이러한 사용자 이동을 시작합니다. 요청의 결과는 `id_token`입니다. 이 보안 토큰은 고객 ID를 나타냅니다.

Azure AD B2C를 사용하는 모든 응용 프로그램은 Azure Portal을 사용하여 Azure AD B2C 테넌트에 등록되어야 합니다. 등록 프로세스는 값을 수집하고 응용 프로그램에 할당합니다. 이 값에는 응용 프로그램을 고유하게 식별하는 응용 프로그램 ID와, 응답을 다시 전달하는 데 사용할 수 있는 리디렉션 URI가 포함됩니다.

모든 응용 프로그램의 상호 작용은 높은 수준에서 비슷한 패턴을 따릅니다.

1. 응용 프로그램은 고객이 정책을 실행하도록 합니다.
2. 고객은 정책 정의에 따라 정책을 완료합니다.
3. 응용 프로그램은 보안 토큰을 수신합니다.
4. 응용 프로그램은 보안 토큰을 사용하여 보호된 리소스에 액세스를 시도합니다.
5. 리소스 서버가 보안 토큰의 유효성을 검사하여 액세스 권한을 부여할 수 있는지 확인합니다.
6. 응용 프로그램은 주기적으로 보안 토큰을 새로 고칩니다.

이러한 단계는 빌드 중인 응용 프로그램의 유형에 따라 약간씩 다를 수 있습니다.

Azure AD B2C는 순서대로 ID 공급자, 고객, 기타 시스템 및 로컬 디렉터리와 상호 작용하여 ID 작업을 완료합니다. 예를 들어 고객을 로그인하게 하고 새 고객을 등록하거나 암호를 다시 설정합니다. 다자간 트러스트를 설정하고 이러한 단계를 완료하는 기본 플랫폼을 ID 경험 프레임워크라고 합니다. 이 프레임워크 및 정책(사용자 경험 또는 신뢰 프레임워크 정책이라고도 함)은 명시적으로 행위자, 동작, 프로토콜, 완료할 단계의 순서를 정의합니다.

Azure AD B2C는 여러 가지 방법으로 응용 프로그램에 대한 서비스 거부 및 암호 공격으로부터 보호합니다. Azure AD B2C는 SYN 쿠키, 속도 및 연결 제한과 같은 검색 및 완화 기술을 사용하여 서비스 거부 공격으로부터 리소스를 보호합니다. 무차별 암호 대입 공격 및 사전 암호 공격에 대해서는 완화도 포함됩니다.

#### <a name="built-in-policies"></a>기본 제공 정책

Azure AD B2C에 전송되는 각 요청은 정책을 지정합니다. 정책은 응용 프로그램이 Azure AD B2C와 상호 작용하는 방식을 제어합니다. 가입, 로그인, 프로필 편집 등과 같이 가장 일반적인 ID 작업에 대해서는 기본 제공 정책이 미리 정의되어 있습니다.  예를 들어 등록 정책을 사용하면 다음 설정을 구성하여 동작을 제어할 수 있습니다.

- 고객이 응용 프로그램 가입에 사용할 수 있는 소셜 계정
- 이름, 우편 번호 등, 고객에게서 수집한 데이터
- Multi-Factor Authentication
- 가입 페이지의 모양과 느낌
- 응용 프로그램에 반환되는 정보

#### <a name="custom-policies"></a>사용자 지정 정책 

[사용자 지정 정책](active-directory-b2c-overview-custom.md)은 Azure AD B2C 테넌트에서 ID 경험 프레임워크의 동작을 정의하는 구성 파일입니다. 다양한 작업을 수행할 수 있게 사용자 지정 정책은 완전히 편집할 수 있습니다. 사용자 지정 정책은 계층 구조 체인에서 서로를 참조하는 하나 또는 여러 XML 형식 파일로 표시됩니다. 

Azure AD B2C 테넌트에서는 필요에 따라 다양한 형식의 여러 사용자 지정 정책을 사용할 수 있고 다른 응용 프로그램에서 다시 사용할 수 있습니다. 이러한 유연성 덕분에 코드 변경 없이 또는 최소한의 변경으로 고객 ID 환경을 정의하고 수정할 수 있습니다. HTTP 인증 요청에 특수 쿼리 매개 변수를 추가하여 정책을 사용할 수 있습니다.

사용자 지정 정책은 다음과 같은 방식으로 사용자 경험을 제어하는 데 사용됩니다.

- 추가 정보를 수집하기 위해 API와의 상호 작용을 정의하거나, 고객이 제공한 클레임을 확인하거나, 외부 프로세스를 트리거합니다.
- API로부터의 클레임 또는 *migrationStatus* 같은 디렉터리에서의 클레임을 기반으로 동작을 변경합니다.
- 기본 제공 정책에서 다루지 않는 모든 워크플로. 로그인 환경 중 고객으로부터 추가적인 정보를 수집하거나 리소스 액세스를 위해 권한 부여 검사를 수행하는 것을 예로 들 수 있습니다.

### <a name="identity-providers"></a>ID 공급자

ID 공급자는 사용자 ID를 인증하고 보안 토큰을 발급하는 서비스입니다. Azure AD B2C에서는 Microsoft 계정, Facebook, Amazon 등을 비롯한 다양한 ID 공급자를 테넌트에서 구성할 수 있습니다. 

Azure AD B2C 테넌트에서 ID 공급자를 구성하려면 응용 프로그램 식별자 또는 클라이언트 식별자와, 자신이 만든 ID 공급자 응용 프로그램의 암호나 클라이언트 암호를 기록해야 합니다. 그런 다음, 이 식별자와 암호를 사용하여 응용 프로그램을 구성합니다.

### <a name="user-interface-experience"></a>사용자 인터페이스 환경

고객에게 표시되는 대부분의 HTML 및 CSS 콘텐츠는 제어가 가능합니다. 페이지 UI 사용자 지정 기능을 사용하면 모든 정책의 모양과 느낌을 사용자 지정할 수 있습니다. 또한 응용 프로그램과 Azure AD B2C 간에 브랜드와 시각적 개체 일관성을 유지할 수 있습니다.

Azure AD B2C는 소비자의 브라우저에서 코드를 실행하고 CORS(원본 간 리소스 공유)라는 최신 방법을 사용합니다. 먼저, 사용자 지정된 HTML 콘텐츠가 있는 정책에서 URL을 지정합니다. Azure AD B2C는 UI 요소를 URL에서 로드된 HTML 콘텐츠와 병합한 다음 고객에게 해당 페이지를 표시합니다.

쿼리 문자열에서 Azure AD B2C에 매개 변수를 보낼 수 있습니다. 매개 변수를 HTML 끝점으로 전달하면 페이지 콘텐츠를 동적으로 변경할 수 있습니다. 예를 들어 웹 또는 모바일 응용 프로그램에서 전달한 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 변경할 수 있습니다.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>Azure AD B2C를 시작하는 방법

Azure AD B2C에서 테넌트는 조직을 나타내며 사용자의 디렉터리입니다. 각 Azure AD B2C 테넌트는 서로 전혀 다르고 다른 Azure AD B2C 테넌트와 별개입니다. 테넌트는 응용 프로그램을 사용하기 위해 가입한 고객 관련 정보를 포함합니다. 암호, 프로필 데이터, 권한 등을 예로 들 수 있습니다.

모든 기능을 활성화하고 사용 요금을 지불하기 위해 Azure 구독에 Azure AD B2C테넌트를 연결해야 합니다. Azure AD B2C 고객이 응용 프로그램에 로그인하도록 허용하려면 Azure AD B2C 테넌트에 응용 프로그램을 등록해야 합니다.

Azure AD B2C를 사용하도록 응용 프로그램을 구성하기에 앞서 Azure AD B2C 테넌트를 만들고 응용 프로그램을 등록해야 합니다. 응용 프로그램을 등록하려면 [자습서: Azure AD B2C를 통한 가입 및 로그인을 사용하도록 응용 프로그램 등록](tutorial-register-applications.md)의 단계를 완료합니다.
  
ASP.NET 웹 응용 프로그램 개발자인 경우 [자습서: 웹 응용 프로그램이 Azure AD B2C를 사용하여 계정을 인증하도록 설정](active-directory-b2c-tutorials-web-app.md)의 단계를 사용하여 계정을 인증합니다.

데스크톱 응용 프로그램 개발자인 경우 [자습서: 데스크톱 응용 프로그램이 Azure AD B2C를 사용하여 계정을 인증하도록 설정](active-directory-b2c-tutorials-desktop-app.md)의 단계를 사용하여 계정을 인증합니다.

Node.js를 사용한 단일 페이지 응용 프로그램 개발자인 경우 [자습서: 단일 페이지 응용 프로그램이 Azure AD B2C를 사용하여 계정을 인증하도록 설정](active-directory-b2c-tutorials-spa.md)의 단계를 사용하여 계정을 인증합니다.

## <a name="next-steps"></a>다음 단계

자습서를 계속해 나가면서 가입 및 로그인 환경에 대한 응용 프로그램 구성을 시작해 봅니다.

> [!div class="nextstepaction"]
> [자습서: Azure AD B2C를 가입 및 로그인에 사용하도록 응용 프로그램 등록](tutorial-register-applications.md)
