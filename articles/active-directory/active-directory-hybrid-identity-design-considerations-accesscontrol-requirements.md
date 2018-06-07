---
title: "Azure의 하이브리드 ID 디자인 액세스 제어 요구 사항 | Microsoft Docs"
description: "하이브리드 환경에서 ID의 기본 요소 및 사용자의 리소스에 대한 액세스 요구 사항 식별에 대해 설명합니다."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 161820e69b0c9d0dc376a62cecceb9cc5e83c8ce
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>하이브리드 ID 솔루션에 대한 액세스 제어 요구 사항 확인
또한 조직이 해당 하이브리드 ID 솔루션을 설계하는 경우 사용자는 이 기회를 사용하여 사용자가 사용할 수 있게 하려는 리소스에 대한 액세스 요구 사항을 검토할 수 있습니다. ID의 네 가지 기본 요소에 대한 데이터 액세스는 다음과 같습니다.

* 관리
* 인증
* 권한 부여
* 감사

다음 섹션에서는 인증 및 권한 부여를 자세히 살펴봅니다. 관리 및 감사는 하이브리드 ID 수명 주기의 일부입니다. 이러한 기능에 대한 자세한 내용은 [하이브리드 ID 관리 작업 결정](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)을 읽습니다.

> [!NOTE]
> 이러한 기본 요소 각각에 대한 자세한 내용은 [ID의 네 가지 기본 요소 - 하이브리드 IT 시대에서 ID 관리](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) 를 읽습니다.
> 
> 

## <a name="authentication-and-authorization"></a>인증 및 권한 부여
인증 및 권한 부여에 대한 여러 시나리오가 있으며 이러한 시나리오에는 회사가 도입할 하이브리드 ID 솔루션에서 충족시켜야 하는 특정 요구 사항이 있습니다. 기업간(B2B) 통신을 포함하는 시나리오는 조직에서 사용하는 인증 및 권한 부여 방법이 비즈니스 파트너와 통신할 수 있는지 확인해야 하므로 IT 관리자에게 추가적인 도전을 요구할 수 있습니다. 인증 및 권한 부여 요구 사항에 대한 설계 프로세스 동안 응답된 다음 질문을 확인합니다.

* 조직이 해당 ID 관리 시스템에 있는 사용자만 인증하고 권한을 부여합니까?
  * B2B 시나리오에 대한 계획이 있습니까?
  * 있다면 어떤 프로토콜(SAML, OAuth, Kerberos, 토큰 또는 인증서)이 양쪽 비즈니스를 연결하는 데 사용할 수 있는지 알고 계십니까?
* 채택하려는 하이브리드 ID 솔루션이 해당 프로토콜을 지원합니까?

고려해야 할 다른 중요한 사항은 사용자 및 파트너가 사용할 인증 리포지토리가 배치될 위치 및 사용할 관리 모델입니다. 다음 두 가지 핵심 옵션을 고려합니다.

* 중앙 집중화됨: 이 모델에서 사용자의 자격 증명, 정책 및 관리는 온-프레미스 또는 클라우드에서 중앙 집중화될 수 있습니다.
* 하이브리드: 이 모델에서 사용자의 자격 증명, 정책 및 관리는 온-프레미스에서 중앙 집중화되고 클라우드에서 복제됩니다.

조직이 어떤 모델을 도입할지는 비즈니스 요구 사항에 따라 달라집니다. 다음 질문에 응답하여 ID 관리 시스템의 위치 및 사용할 관리 모드를 식별합니다.

* 현재 조직에는 ID 관리 온-프레미스가 있습니까?
  * 있다면 유지할 계획입니까?
  * 조직이 따라야 하고 ID 관리 시스템이 위치해야 할 규정 또는 준수 요구 사항이 있습니까?
* 조직은 온-프레미스 나 클라우드에 위치한 앱에 Single Sign-On을 사용합니까?
  * 사용한다면 하이브리드 ID 모델의 도입은 이 프로세스에 영향을 줍니까?

## <a name="access-control"></a>Access Control
인증 및 권한 부여는 사용자의 유효성 검사를 통해 회사 데이터에 액세스할 수 있도록 하는 핵심 요소이며 또한 관리 중인 리소스를 통해 이러한 사용자가 가질 액세스 수준 및 관리자가 가질 액세스 수준을 제어하는 것이 중요합니다. 하이브리드 ID 솔루션은 리소스, 위임 및 역할 기반 액세스 제어에 대한 세부적인 액세스를 제공할 수 있어야 합니다. 액세스 제어에 관하여 다음과 같은 질문에 응답해야 합니다.

* 회사에는 상승된 권한이 있는 둘 이상의 사용자가 있어서 ID 시스템을 관리합니까?
  * 그렇다면 각 사용자는 동일한 수준의 액세스 권한이 필요합니까?
* 회사가 사용자에게 액세스 권한을 위임하여 특정 리소스를 관리해야 합니까?
  * 그렇다면 얼마나 자주 발생합니까?
* 회사가 온-프레미스와 클라우드 리소스 사이에 액세스 제어 기능을 통합해야 합니까?
* 회사가 일부 조건에 따라 리소스에 대한 액세스를 제한해야 합니까?
* 회사에는 일부 리소스에 사용자 지정 제어 액세스가 필요한 응용 프로그램이 있습니까?
  * 그렇다면 해당 앱은 어디에 위치합니까(온-프레미스 또는 클라우드)?
  * 그렇다면 해당 대상 리소스는 어디에 위치합니까(온-프레미스 또는 클라우드)?

> [!NOTE]
> 각 답변을 주목하고 답변 이유를 이해해야 합니다. [데이터 보호 전략 정의](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) 에서는 사용할 수 있는 옵션과 각 옵션의 장점/단점을 살펴봅니다.  질문에 답변하여 비즈니스 요구 사항에 가장 적합한 옵션을 선택할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
[사고 대응 요구 사항 결정](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>참고 항목
[설계 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

