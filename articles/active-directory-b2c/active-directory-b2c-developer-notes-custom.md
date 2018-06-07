---
title: 'Azure Active Directory B2C: 사용자 지정 정책을 사용하는 개발자 정보 | Microsoft Docs'
description: 사용자 지정 정책으로 Azure AD B2C를 구성 및 유지 관리하는 개발자를 위한 정보
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/13/2017
ms.author: davidmu
ms.openlocfilehash: cf76f158d7e87a2f2f902551fdc751b295b6915b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Azure Active Directory B2C 사용자 지정 정책 공개 미리 보기에 대한 릴리스 정보
사용자 지정 정책 기능 집합은 현재 모든 Azure Active Directory B2C(Azure AD B2C) 고객에 대해 공개 미리 보기에서 평가용으로 제공됩니다. 이 기능 집합은 가장 복잡한 ID 솔루션을 구축하는 고급 ID 개발자를 대상으로 합니다.  

현재, 이 기능 집합을 사용하려면 개발자가 XML 파일 편집을 통해 ID 경험 프레임워크를 직접 구성해야 합니다. 이 구성 방법은 강력하고 복잡합니다. ID 경험 프레임워크를 사용하는 고급 ID 개발자는 연습을 완료하고 참조 문서를 확인하여 시간을 투자할 계획입니다. 

## <a name="features-included-in-this-public-preview"></a>이 공개 미리 보기에 포함된 기능
공개 검토에 포함된 새 기능을 통해 개발자는 다음 작업을 수행할 수 있습니다.<br>

* 사용자 지정 정책을 사용하여 사용자 지정 인증 사용자 경험을 작성 및 업로드합니다. 
   * 클레임 공급자 간의 교환으로 사용자 경험을 단계별로 설명합니다. 
   * 사용자 경험에서 조건부 분기를 정의합니다. 
* 사용자 지정 인증 사용자 경험에서 REST API 사용 서비스를 통합합니다.  
* OpenIDConnect 표준과 호환되는 ID 공급자와의 페더레이션을 추가합니다. <br>
* SAML 2.0 프로토콜을 준수하는 ID 공급자와의 페더레이션을 추가합니다. 

## <a name="terms-of-the-public-preview"></a>공개 미리 보기 약관

* 새 기능의 사용은 평가 목적으로만 권장됩니다.<br>
* 새 기능은 프로덕션 환경에서는 사용할 수 없습니다.<br>
* Service Level Agreement(서비스 수준 약정)는 새 기능에 적용되지 않습니다. <br>
* 정식 지원 채널을 통해 지원 요청을 정리할 수 있습니다. <br>
* 일반 공급에 대해 정해진 날짜는 없습니다.<br>
* Microsoft의 재량에 따라 어떠한 이유로든, Azure AD B2C 제품 정관의 범위를 벗어나는 시나리오 및 사용자 경험을 고객 ID 및 액세스 관리(CIAM) 플랫폼으로 서비스하도록 플래그를 지정하고 거부 또는 제한할 수 있습니다.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>사용자 지정 정책 기능 집합 개발자의 책임
수동 정책 구성은 Azure AD B2C의 기본 플랫폼에 대해 낮은 수준의 액세스 권한을 부여하며 완전히 사용자 지정할 수 있는 보안 프레임워크를 생성합니다. 다각적인 사용자 지정 ID 공급자, 트러스트 관계, 외부 서비스와의 통합 및 단계별 워크플로 관계로 인해, 이를 사용하는 고급 개발자의 요구가 더욱 증가합니다.

공개 미리 보기를 최대한 활용하기 위해 사용자 지정 정책 기능 집합을 사용하는 개발자는 다음 지침을 준수하는 것이 좋습니다.
* ID 경험 프레임워크의 구성 언어 및 키/암호 관리에 익숙해집니다.
* 시나리오 및 사용자 지정 통합을 직접 소유합니다.
* 체계적인 시나리오 테스트를 수행합니다.
* 최소 1개의 개발/테스트 환경과 1개의 프로덕션 환경을 구축하여 소프트웨어 개발 및 스테이징 모범 사례를 준수합니다.
* 사용자와 통합된 ID 공급자 및 서비스에 대한 새로운 개발 정보를 바로 입수합니다. 예를 들어 기밀 변경 내용과 예정되었거나 갑작스럽게 진행되는 서비스 변경 내용을 추적합니다.
* 활성 모니터링을 설정하고 프로덕션 환경의 응답성을 모니터링합니다.
* Azure 구독에서 연락처 전자 메일 주소를 최신 상태로 유지하고 Microsoft 라이브 사이트 팀 전자 메일에 즉시 응답합니다.
* Microsoft 라이브 사이트 팀에서 권고할 때 시기 적절하게 조치를 취합니다. 

## <a name="features-by-stage-and-known-issues"></a>단계 및 알려진 문제별 기능
사용자 지정 정책/ID 경험 프레임워크 기능은 지속적으로 빠르게 개발되고 있습니다.  다음 표는 기능/구성 요소 가용성 색인입니다.

[https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)에서 Stack Overflow에 대한 질문을 게시하세요.


### <a name="identity-providers-tokens-protocols"></a>ID 공급자, 토큰, 프로토콜
외부 구성 요소 및 응용 프로그램 인터페이스

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | 예: Google+ |
| IDP-OAUTH2 |  | x |  | 예: Facebook  |
| IDP-OAUTH1 |  | x |  | 예: Twitter |
| IDP-SAML |  | x |  | 예: Salesforce, ADFS |
| IDP-WSFED | x |  |  |  |
| 신뢰 당사자 OAUTH |  | x |  |  |
| 신뢰 당사자 OIDC |  | x |  |  |
| 신뢰 당사자 SAML | x |  |  |  |
| 신뢰 당사자 WSFED | x |  |  |  |
| 기본 및 인증서 인증을 사용하는 REST API |  | x |  | 예: Azure Functions |


### <a name="component-support"></a>구성 요소 지원


| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi Factor Authentication |  | x |  |  |
| 로컬 디렉터리로서의 Azure Active Directory |  | x |  |  |
| 2FA을 위한 Azure 전자 메일 하위 시스템 |  | x |  |  |
| 다중 언어 지원|  | x |  |  |
| 암호 복잡성 | x |  |  |  |


### <a name="content-definition"></a>콘텐츠 정의

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   오류 페이지, api.error |  | x |  |  |
|   IDP 선택 페이지, api.idpselections |  | x |  |  |
|   등록을 위한 IDP 선택, api.idpselections.signup |  | x |  |  |
|   암호 찾기, api.localaccountpasswordreset |  | x |  |  |
|   로컬 계정 로그인, api.localaccountsignin |  | x |  |  |
|   로컬 계정 등록, api.localaccountsignup |  | x |  |  |
|   MFA 페이지, api.phonefactor |  | x |  |  |
|   자체 어설션(예: 소셜 계정 등록), api.selfasserted |  | x |  |  |
|   자체 어설션 프로필 업데이트, api.selfasserted.profileupdate |  | x |  |  |
|   통합 등록 또는 로그인 페이지, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>App-IEF 통합
| 기능 | 개발 | 미리 보기 | GA | 메모 |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| 쿼리 문자열 매개 변수 id_token_hint | x |  |  |  |
| 쿼리 문자열 매개 변수 domain_hint |  | x |  | 클레임으로 사용 가능, IDP로 전달될 수 있음 |
| 쿼리 문자열 매개 변수 login_hint |  | x |  | 클레임으로 사용 가능, IDP로 전달될 수 있음 |
| client_assertion을 통해 UserJourney에 JSON 삽입 | x |  |  | 지원 중단 예정 |
| UserJourney에 Id_token_hint로 JSON 삽입 | x |  |  | JSON을 전달하는 진행 방법 |


### <a name="session-management"></a>세션 관리

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|---------------------------------|-------------|---------|----|-------|
| SSO 세션 공급자 |  | x |  |  |
| 외부 로그인 세션 공급자 |  | x |  |  |
| SAML SSO 세션 공급자 |  | x |  |  |


### <a name="security"></a>보안
| 기능 | 개발 | 미리 보기 | GA | 메모 |
|---------------------------------------------|-------------|---------|----|-------|
| 정책 키 - 생성, 수동, 업로드 |  | x |  |  |
| 정책 키 - RSA/Cert, 비밀 |  | x |  |  |


### <a name="developer-interface"></a>개발자 인터페이스
| 기능 | 개발 | 미리 보기 | GA | 메모 |
|---------------------------------------------|-------------|---------|----|-------|
| Azure Portal-IEF UX |  | x |  |  |
| Application Insights UserJourney 로그  |  | x |  |  |
| Application Insights 이벤트 로그 |x|  |  |  |



## <a name="next-steps"></a>다음 단계
[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)
