---
title: Azure Active Directory 조건부 액세스의 조건 | Microsoft Docs
description: Azure Active Directory 조건부 액세스에 할당을 사용하여 정책을 트리거하는 방법을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3cb8e598864bccfbea24a2aec5d9387ff903e51c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770624"
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 조건부 액세스의 조건 

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 조건부 액세스 정책에서 정책을 트리거하는 이유(“when this happens”)에 대한 응답(“do this”)을 정의합니다. 

![제어](./media/active-directory-conditional-access-conditions/10.png)


조건부 액세스의 컨텍스트에서:

- "**When this happens**"는 **조건**이라고 합니다. 
- "**Then do this**"는 **액세스 제어**라고 합니다.

조건과 액세스 제어의 조합이 조건부 액세스 정책을 나타냅니다.

![제어](./media/active-directory-conditional-access-conditions/61.png)


조건부 액세스 정책에 구성하지 않은 조건은 적용되지 않습니다. 일부 조건은 환경에 조건부 액세스 정책을 적용하는 데 [필수적](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work)입니다. 

이 문서에서는 조건의 개요 및 조건부 액세스 정책에서 조건을 사용하는 방법을 설명합니다. 

## <a name="users-and-groups"></a>개요

사용자 및 그룹 조건은 조건부 액세스 정책에서 필수입니다. 정책에서 **모든 사용자**를 선택할 수도 있고 특정 사용자 및 그룹을 선택할 수도 있습니다.

![제어](./media/active-directory-conditional-access-conditions/111.png)

선택 옵션:

- **모든 사용자** - 디렉터리에 있는 모든 사용자에게 정책이 적용됩니다. 여기에는 게스트 사용자도 포함됩니다.

- **사용자 및 그룹 선택** - 다음 옵션을 설정할 수 있습니다.

    - **모든 게스트 사용자** - 정책의 대상을 B2B 게스트 사용자로 지정할 수 있습니다. 이 조건은 *userType* 특성이 *guest*로 설정된 모든 사용자 계정과 일치합니다. Azure AD의 초대 흐름에 계정이 생성되는 즉시 정책을 적용해야 하는 경우 이 설정을 사용할 수 있습니다.

    - **디렉터리 역할** - 사용자의 역할 할당을 기반으로 정책의 대상을 지정할 수 있습니다. 이 조건은 *전역 관리자* 또는 *암호 관리자*와 같은 디렉터리 역할을 지원합니다.

    - **사용자 및 그룹 선택** - 특정 사용자 집합을 대상으로 지정할 수 있습니다. 예를 들어 클라우드 앱으로 선택된 HR 앱을 갖고 있는 경우 HR 부서의 모든 구성원을 포함하는 그룹을 선택할 수 있습니다.

그룹은 동적 또는 할당된 보안 및 배포 그룹을 포함하여 Azure AD의 어떤 그룹 유형이라도 상관 없습니다.

정책에서 특정 사용자 또는 그룹을 제외할 수도 있습니다. 정책에서 다단계 인증(MFA)을 적용하는 경우의 일반적인 사용 사례 중 하나는 서비스 계정입니다. 

새 정책을 배포할 때에는 특정 사용자 집합을 대상으로 지정하는 것이 유리합니다. 새 정책에서 정책 동작의 유효성을 검사하기 위해 초기 사용자 집합만 대상으로 지정해야 합니다. 



## <a name="cloud-apps"></a>클라우드 앱 

클라우드 앱은 웹 사이트 또는 서비스입니다. 여기에는 Azure 응용 프로그램 프록시로 보호되는 웹 사이트가 포함됩니다. 지원되는 클라우드 앱에 대한 자세한 설명은 [클라우드 앱 할당](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments)을 참조하세요.    

클라우드 앱 조건은 조건부 액세스 정책에서 필수입니다. 정책에서 **모든 클라우드 앱**을 선택할 수도 있고 특정 앱을 선택할 수도 있습니다.

![제어](./media/active-directory-conditional-access-conditions/03.png)

선택 옵션:

- 조직 전체에 기준 정책을 적용하려면 **모든 클라우드 앱** 선택합니다. 이 선택 옵션의 일반적인 사용 사례 중 하나는 클라우드 앱에 대한 로그인 위험이 감지되면 다단계 인증을 요구하는 정책입니다. **모든 클라우드 앱**에 적용된 정책은 모든 웹 사이트 및 서비스에 대한 액세스에 적용됩니다. 이 설정은 **클라우드 앱 선택** 목록에 표시되는 클라우드 앱으로 제한되지 않습니다.

- 정책에 의해 특정 서비스를 대상으로 지정하는 개별 클라우드 앱. 예를 들어 사용자가 SharePoint Online에 액세스하려면 [규격 장치](active-directory-conditional-access-policy-connected-applications.md)를 사용하도록 요구할 수 있습니다. 이 정책은 사용자가 SharePoint 콘텐츠(예: Microsoft Teams)팀에 액세스할 때 다른 서비스에도 적용됩니다. 

특정 앱을 정책에서 제외할 수도 있지만, 사용자가 액세스하는 서비스에 적용되는 정책이 계속 적용됩니다. 



## <a name="sign-in-risk"></a>로그인 위험

로그인 위험은 로그인 시도를 사용자 계정의 합법적인 소유자가 수행하지 않았을 가능성에 대한 지표입니다(높음, 중간 또는 낮음). Azure AD는 사용자 로그인 시 로그인 위험 수준을 계산합니다. 계산된 로그인 위험 수준을 조건부 액세스 정책의 조건으로 사용할 수 있습니다. 

![조건](./media/active-directory-conditional-access-conditions/22.png)

이 조건을 사용하려면 [Azure Active Directory Id 보호](active-directory-identityprotection.md)를 사용하도록 설정해야 합니다.
 
이 조건의 일반적인 사용 사례는 다음과 같은 정책입니다.

- 비합법적 사용자가 클라우드 앱에 액세스할 수 없도록 로그인 위험이 높은 사용자를 차단합니다. 
- 로그인 위험 수준이 보통인 사용자에게 다단계 인증을 요구합니다. 다단계 인증을 사용하면 로그인이 합법적 계정 소유자에 의해 수행된다는 추가 신뢰를 제공할 수 있습니다.

자세한 내용은 [위험한 로그인](active-directory-identityprotection.md#risky-sign-ins)을 참조하세요.  

## <a name="device-platforms"></a>장치 플랫폼

장치 플랫폼은 다음 장치에서 실행되는 운영 체제를 특징으로 합니다. Azure AD는 장치에서 제공하는 정보(예: 사용자 에이전트)를 사용하여 플랫폼을 식별합니다. 이 정보는 검증된 정보가 아니므로 액세스를 차단하거나 Intune 정책 준수를 요구하거나 장치를 도메인에 가입할 것을 요구하여 모든 플랫폼에 정책을 적용하는 것이 좋습니다. 기본값은 모든 장치 플랫폼에 정책을 적용하는 것입니다. 


![조건](./media/active-directory-conditional-access-conditions/24.png)

지원되는 장치 플랫폼의 전체 목록은 [장치 플랫폼 조건](active-directory-conditional-access-technical-reference.md#device-platform-condition)을 참조하세요.


이 조건의 일반적인 사용 사례는 클라우드 앱에 대한 액세스를 [관리 장치](active-directory-conditional-access-policy-connected-applications.md#managed-devices)로 제한하는 정책입니다. 장치 플랫폼 조건을 포함하여 더 많은 시나리오를 보려면 [Azure Active Directory 앱 기반 조건부 액세스](active-directory-conditional-access-mam.md)를 참조하세요.



## <a name="device-state"></a>장치 상태

장치 상태 조건은 하이브리드 Azure AD가 조인되고, 호환되는 것으로 표시된 장치를 조건부 액세스 정책에서 제외할 수 있습니다. 이는 추가적인 세션 보안을 제공하기 위해 관리되지 않는 장치에만 정책을 적용해야 하는 경우에 유용합니다. 예를 들어 장치가 관리되지 않는 경우 Microsoft Cloud App Security 세션 제어만 적용합니다. 


![조건](./media/active-directory-conditional-access-conditions/112.png)

관리되지 않는 장치에 대한 액세스를 차단하려면 [장치 기반 조건부 액세스](active-directory-conditional-access-policy-connected-applications.md)를 구현해야 합니다.


## <a name="locations"></a>위치

위치를 사용하여 연결 시도가 시작된 위치를 기반으로 조건을 정의할 수 있습니다. 
     
![조건](./media/active-directory-conditional-access-conditions/25.png)

이 조건의 일반적인 사용 사례는 다음과 같은 정책입니다.

- 사용자가 회사 네트워크 외부에서 서비스에 액세스할 때 다단계 인증을 요구합니다.  

- 특정 국가 또는 지역에서 서비스에 액세스하는 사용자를 차단합니다. 

자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건](active-directory-conditional-access-locations.md)을 참조하세요.


## <a name="client-apps"></a>클라이언트 앱

클라이언트 앱 조건을 사용하여 다음과 같은 다양한 유형의 응용 프로그램에 정책을 적용할 수 있습니다.

- 웹 사이트 및 서비스
- 모바일 앱 및 데스크톱 응용 프로그램. 

![조건](./media/active-directory-conditional-access-conditions/04.png)

응용 프로그램이 다음과 같이 분류됩니다.

- 기밀 클라이언트에 웹 SSO 프로토콜, SAML, WS-Fed 또는 OpenID Connect를 사용하는 경우 웹 사이트 또는 서비스로 분류됩니다.

- 네이티브 클라이언트에 모바일 앱 OpenID Connect를 사용하는 경우 모바일 앱 또는 데스크톱 응용 프로그램으로 분류됩니다.

조건부 액세스 정책에서 사용할 수 있는 클라이언트 앱의 전체 목록은 [Azure Active Directory 조건부 액세스 기술 참조](active-directory-conditional-access-technical-reference.md#client-apps-condition)를 참조하세요.

이 조건의 일반적인 사용 사례는 다음과 같은 정책입니다.

- 장치에 대량의 데이터를 다운로드하는 모바일 및 데스크톱 응용 프로그램을 위한 [규격 장치](active-directory-conditional-access-policy-connected-applications.md)를 요구하고, 모든 장치에서 브라우저 액세스를 허용합니다.

- 웹 응용 프로그램 액세스를 차단하지만, 모바일 및 데스크톱 응용 프로그램 액세스는 허용합니다.

웹 SSO 및 최신 인증 프로토콜 사용 외에도, 대부분의 스마트폰에 기본적으로 제공되는 메일 앱처럼 Exchange ActiveSync를 사용하는 메일 응용 프로그램에 이 조건을 적용할 수 있습니다. 현재 레거시 프로토콜을 사용하는 클라이언트 앱은 AD FS를 사용하여 보호해야 합니다.

 자세한 내용은 다음을 참조하세요.

- [Azure Active Directory 조건부 액세스를 위한 SharePoint Online 및 Exchange Online 설정](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory 앱 기반 조건부 액세스](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 

