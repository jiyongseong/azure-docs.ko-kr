---
title: 'Azure Active Directory B2C: 사용자 지정 정책 문제 해결 | Microsoft Docs'
description: Azure Active Directory에서 사용자 지정 정책을 사용하는 경우 오류를 해결하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/07/2017
ms.author: davidmu
ms.openlocfilehash: e51990bd2e121b89e7260e0d745d08ecc82ff8a0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies-and-identity-experience-framework"></a>Azure AD B2C 사용자 지정 정책 및 Identity Experience Framework 문제 해결

Azure AD B2C(Azure Active Directory B2C) 사용자 지정 정책을 사용하는 경우 정책 언어 XML 형식으로 Identity Experience Framework를 설정하는 동안 문제가 발생할 수 있습니다.  사용자 지정 정책을 작성하는 방법은 새로운 언어를 배우는 것과 유사할 수 있습니다. 이 문서에서는 문제를 신속하게 검색하고 해결하는 데 유용한 도구와 팁을 설명합니다. 

> [!NOTE]
> 이 문서에서는 Azure AD B2C 사용자 지정 정책 구성의 문제 해결을 중점적으로 다룹니다. 신뢰 당사자 응용 프로그램 또는 ID 라이브러리는 다루지 않습니다.

## <a name="xml-editing"></a>XML 편집

사용자 지정 정책 설정에서 가장 일반적인 오류는 형식이 잘못 지정된 XML입니다. 좋은 XML 편집기가 거의 필수입니다. 좋은 XML 편집기는 XML을 기본적으로 표시하고, 콘텐츠를 색상으로 구분하며, 일반적인 용어를 미리 채우고, XML 요소를 인덱싱된 상태로 유지하며, 스키마로 유효성을 검사할 수 있습니다. 다음은 권장되는 두 XML 편집기입니다.

* [Visual Studio Code](https://code.visualstudio.com/)
* [메모장++](https://notepad-plus-plus.org/)

XML 파일을 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다. 시작 팩의 루트 폴더에서 XML 스키마 정의 TrustFrameworkPolicy_0.3.0.0.xsd를 가져옵니다. 자세한 내용을 보려면 XML 편집기 문서에서 *XML 도구* 및 *XML 유효성 검사*를 찾습니다.

XML 규칙 검토가 도움이 될 수도 있습니다. Azure AD B2C는 검색된 XML 형식 오류를 거부합니다. 경우에 따라 형식이 잘못된 XML로 인해 잘못된 오류 메시지가 발생할 수 있습니다.

## <a name="upload-policies-and-policy-validation"></a>정책 업로드 및 정책 유효성 검사

 XML 파일 업로드 유효성 검사는 자동으로 수행됩니다. 대부분의 오류로 인해 업로드가 실패합니다. 유효성 검사에는 업로드하는 정책 파일이 포함됩니다. 또한 업로드 파일에서 참조하는 파일 체인(신뢰 당사자 정책 파일, 확장 파일 및 기본 파일)이 포함됩니다. 
 
 일반적인 유효성 검사 오류는 다음과 같습니다.

오류 코드 조각: `... makes a reference to ClaimType with id "displaName" but neither the policy nor any of its base policies contain such an element`
* ClaimType 값의 철자가 틀렸거나 ClaimType 값이 스키마에 없습니다.
* ClaimType 값이 정책 파일 중 하나 이상에 정의되어 있어야 합니다. 
    예: ` <ClaimType Id="socialIdpUserId">`
* ClaimType이 확장 파일에 정의되어 있지만 기본 파일의 TechnicalProfile 값에도 사용된 경우 기본 파일을 업로드하면 오류가 발생합니다.

오류 코드 조각: `...makes a reference to a ClaimsTransformation with id...`
* 오류의 원인은 ClaimType 오류와 동일할 수 있습니다.

오류 코드 조각: `Reason: User is currently logged as a user of 'yourtenant.onmicrosoft.com' tenant. In order to manage 'yourtenant.onmicrosoft.com', please login as a user of 'yourtenant.onmicrosoft.com' tenant`
* **\<TrustFrameworkPolicy\>** 및 **\<BasePolicy\>** 요소의 TenantId 값이 대상 Azure AD B2C 테넌트와 일치하는지 확인합니다.  

## <a name="troubleshoot-the-runtime"></a>런타임 문제 해결

* `Run Now` 및 `https://jwt.io`를 사용하여 웹 또는 모바일 응용 프로그램과 별도로 정책을 테스트합니다. 이 웹 사이트는 신뢰 당사자 응용 프로그램처럼 작동합니다. Azure AD B2C 정책에 의해 생성된 JWT(JSON Web Token)의 내용을 표시합니다. Identity Experience Framework에서 테스트 응용 프로그램을 만들려면 다음 값을 사용합니다.
    * 이름: TestApp
    * 웹앱/Web API: 아니요
    * 네이티브 클라이언트: 아니요

* 클라이언트 브라우저와 Azure AD B2C 간의 메시지 교환을 추적하려면 [Fiddler](http://www.telerik.com/fiddler)를 사용합니다. 오케스트레이션 단계에서 사용자 환경이 실패한 위치를 확인하는 데 도움이 될 수 있습니다.

* **개발 모드**에서 **Application Insights**를 사용하여 Identity Experience Framework 사용자 환경의 활동을 추적합니다. **개발 모드**에서는 Identity Experience Framework와 기술 프로필에서 정의된 다양한 클레임 공급자(예: ID 공급자, API 기반 서비스, Azure AD B2C 사용자 디렉터리 및 Azure Multi-Factor-Authentication과 같은 기타 서비스) 간의 클레임 교환을 확인할 수 있습니다.  

## <a name="recommended-practices"></a>권장 사례

**여러 버전의 시나리오를 유지하고 응용 프로그램과 함께 프로젝트에 그룹화합니다.** 기본, 확장 및 신뢰 당사자 파일은 서로 직접 종속됩니다. 그룹으로 저장합니다. 새로운 기능이 정책에 추가되면 별도 작업 버전을 유지합니다. 상호 작용하는 응용 프로그램 코드를 사용하여 사용자 고유의 파일 시스템에 작업 버전을 준비합니다.  응용 프로그램은 테넌트의 여러 다른 신뢰 당사자 정책을 호출할 수 있습니다. Azure AD B2C 정책에서 예상하는 클레임에 종속될 수도 있습니다.

**알려진 사용자 환경으로 기술 프로필을 개발 및 테스트합니다.** 테스트된 시작 팩 정책을 사용하여 기술 프로파일을 설정합니다. 고유한 사용자 환경에 통합하기 전에 개별적으로 테스트합니다.

**테스트된 기술 프로필을 통해 사용자 환경을 개발 및 테스트합니다.** 사용자 환경의 오케스트레이션 단계를 증분 방식으로 변경합니다. 의도한 시나리오를 점진적으로 작성합니다.

## <a name="next-steps"></a>다음 단계

* GitHub에서 [active-directory-b2c-custom-policy-starterpack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)) .zip 파일을 다운로드합니다.
