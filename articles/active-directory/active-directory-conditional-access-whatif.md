---
title: "Azure Active Directory 조건부 액세스 what if 도구 - 미리 보기 | Microsoft Docs"
description: "Azure Active Directory 조건부 액세스 정책의 구성을 테스트하는 방법을 알아봅니다."
services: active-directory
keywords: "앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 19ebb30164eee8e03a3cd8f18b6d575c6eee5438
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-active-directory-conditional-access-what-if-tool---preview"></a>Azure Active Directory 조건부 액세스 what if 도구 - 미리 보기

[조건부 액세스](active-directory-conditional-access-azure-portal.md)는 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있게 해 주는 Azure AD(Azure Active Directory)의 기능입니다. 환경에서 조건부 액세스 정책을 통해 예상되는 결과를 어떻게 알 수 있나요? 이 질문에 답변하기 위해 **조건부 액세스 what if 도구**를 사용할 수 있습니다.

이 문서에서는 이 도구를 사용하여 조건부 액세스 정책을 테스트하는 방법을 설명합니다.

## <a name="what-it-is"></a>정의

**조건부 액세스 what if 정책 도구**를 사용하여 사용자 환경에 조건부 액세스 정책이 미치는 영향을 이해할 수 있습니다. 여러 번의 로그인을 수동으로 수행하여 정책을 시험 사용해보는 대신, 이 도구를 사용하여 사용자의 시뮬레이트된 로그인을 평가할 수 있습니다. 이 시뮬레이션은 이 로그인이 정책에 미치는 영향을 평가하고, 시뮬레이션 보고서를 생성합니다. 이 보고서는 적용된 조건부 액세스 정책뿐만 아니라 [클래식 정책](active-directory-conditional-access-migration.md#classic-policies)(있는 경우)도 나열합니다.    

또한 what if 도구는 특정 사용자에게 적용되는 정책을 빠르게 확인하는 방법을 제공합니다. 예를 들어 문제를 해결해야 하는 경우에 이 정보를 사용할 수 있습니다.  

## <a name="how-it-works"></a>작동 방법

**조건부 액세스 what if 도구**에서, 먼저 시뮬레이트하려는 로그인 시나리오의 설정을 구성해야 합니다. 이러한 설정에는 다음이 포함됩니다.

- 테스트하려는 사용자 

- 사용자가 액세스하려고 하는 클라우드 앱

- 클라우드 구성 앱에 대한 액세스가 수행되는 조건
     
다음 단계로, 설정을 평가하는 시뮬레이션 실행을 시작할 수 있습니다. 사용하도록 설정된 정책만 평가 실행에 속합니다.


평가가 완료되면 이 도구는 영향을 받는 정책에 대한 보고서를 생성합니다.


## <a name="running-the-tool"></a>도구 실행

Azure Portal의 **[조건부 액세스 - 정책](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** 페이지에서 **what if** 도구를 찾을 수 있습니다.

이 도구를 시작하려면 정책 목록 맨 위에 있는 도구 모음에서 **What if**를 클릭합니다.

![What if](./media/active-directory-conditional-access-whatif/01.png)

평가를 실행하려면 먼저 설정을 구성해야 합니다.

## <a name="settings"></a>설정

이 섹션에서는 시뮬레이션 실행의 설정에 대한 정보를 제공합니다.

![What if](./media/active-directory-conditional-access-whatif/02.png)


### <a name="user"></a>사용자

한 명의 사용자만 선택할 수 있습니다. 이 필드는 유일한 필수 필드입니다.

### <a name="cloud-apps"></a>클라우드 앱

이 설정의 기본값은 **모든 클라우드 앱**입니다. 기본 설정은 사용자 환경의 사용 가능한 모든 정책에 대한 평가를 수행합니다. 특정 클라우드 앱에 영향을 주는 정책으로 범위를 좁힐 수 있습니다.


### <a name="ip-address"></a>IP 주소

IP 주소는 [위치 조건](active-directory-conditional-access-locations.md)을 모방하기 위한 단일 IPv4 주소입니다. 이 주소는 사용자가 로그인하는 데 사용하는 장치의 인터넷 연결 주소를 나타냅니다. 예를 들어 [What is my IP address](https://whatismyipaddress.com)(내 IP 주소)로 이동하여 장치의 IP 주소를 확인할 수 있습니다.    

### <a name="device-platforms"></a>장치 플랫폼

이 설정은 [장치 플랫폼 조건](active-directory-conditional-access-conditions.md#device-platforms)을 모방하며, **모든 플랫폼(비지원 플랫폼 포함)**과 같은 설정을 나타냅니다. 
### <a name="client-apps"></a>클라이언트 앱

이 설정은 [클라이언트 앱 조건](active-directory-conditional-access-conditions.md#client-apps)을 모방합니다.
기본적으로 이 설정을 사용하면 **브라우저** 또는 **모바일 앱 및 데스크톱 클라이언트**가 따로 또는 둘 다 선택되어 있는 모든 정책이 평가됩니다. 또한 **EAS(Exchange ActiveSync)**를 적용하는 정책도 감지됩니다. 다음을 선택하여 이 설정의 범위를 좁힐 수 있습니다.

- **브라우저**: 하나 이상의 **브라우저**가 선택된 모든 정책을 평가합니다. 

- **모바일 앱 및 데스크톱 클라이언트**: 적어도 **모바일 앱 및 데스크톱 클라이언트**가 선택된 모든 정책을 평가합니다. 


### <a name="sign-in-risk"></a>로그인 위험

이 설정은 [로그인 위험 조건](active-directory-conditional-access-conditions.md#sign-in-risk)을 모방합니다.   


## <a name="evaluation"></a>평가 

**What if**를 클릭하여 평가를 시작합니다. 평가 결과는 다음으로 구성된 보고서를 제공합니다. 

![What if](./media/active-directory-conditional-access-whatif/03.png)

- 클래식 정책이 환경에 있는지 여부 표시
- 사용자에게 적용되는 정책
- 사용자에게 적용되지 않는 정책


선택한 클라우드 앱에 대해 [클래식 정책](active-directory-conditional-access-migration.md#classic-policies)이 존재하는 경우 해당 표시가 제공됩니다. 이 표시를 클릭하면 클래식 정책 페이지로 리디렉션됩니다. 클래식 정책 페이지에서 클래식 정책을 마이그레이션하거나 사용하지 않도록 설정할 수 있습니다. 이 페이지를 닫고 평가 결과로 돌아갈 수 있습니다.

선택한 사용자에게 적용되는 정책 목록에서 사용자가 충족해야 하는 [권한 부여 컨트롤](active-directory-conditional-access-controls.md#grant-controls) 및 [세션 컨트롤](active-directory-conditional-access-controls.md#session-controls) 목록을 찾을 수도 있습니다.

사용자에게 적용되지 않는 정책 목록에서 이러한 정책이 적용되지 않는 이유를 찾을 수도 있습니다. 나열된 각 정책에 대해 이유는 충족되지 않은 첫 번째 조건을 나타냅니다. 적용되지 않는 정책의 가능한 이유는 추가로 평가되지 않기 때문에 사용되지 않도록 설정된 경우입니다.   



## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.

- 사용자 환경에 대한 조건부 액세스 정책을 구성할 준비가 완료된 경우 [Azure Active Directory의 조건부 액세스 모범 사례](active-directory-conditional-access-best-practices.md)를 참조하세요. 

- 클래식 정책을 마이그레이션하려는 경우 [Azure Portal에서 클래식 정책 마이그레이션](active-directory-conditional-access-migration.md)을 참조하세요.  
