---
title: "Azure Active Directory 하이브리드 ID 디자인 고려 사항 - 개요 | Microsoft Docs"
description: "하이브리드 ID 설계 고려 사항 가이드의 개요 및 콘텐츠 맵"
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 94e67c5ea0028419e9bf74420e2bb46709b3df01
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Azure Active Directory 하이브리드 ID 설계 고려 사항
소비자 기반 장치가 기업 전반에 퍼지고 있기 때문에 클라우드 기반 소프트웨어 SaaS 응용 프로그램은 쉽게 채택할 수 있습니다. 결과적으로 내부 데이터 센터 및 클라우드 플랫폼에 걸쳐 사용자의 응용 프로그램 액세스를 제어하도록 유지하는 것은 어렵습니다.  

Microsoft의 ID 솔루션은 온-프레미스 및 클라우드 기반 기능을 확장하며 이는 위치에 관계 없이 모든 리소스에 인증 및 권한 부여에 대한 단일 사용자 ID를 만듭니다. 하이브리드 ID라고 합니다. Microsoft 솔루션을 사용하여 하이브리드 ID에 대한 여러 설계 및 구성 옵션이 있으며 일부 경우에 어떤 조합이 조직의 요구에 가장 적합한지 확인하기 어려울 수 있습니다. 

이 하이브리드 ID 설계 고려 사항 가이드는 조직에 대해 비즈니스 및 기술 요구에 가장 맞는 하이브리드 ID 솔루션을 설계하는 방법을 이해하는 데 도움이 됩니다.  이 가이드는 조직의 고유한 요구 사항을 만족하는 하이브리드 ID 솔루션을 설계하는 데 도움이 되는 일련의 단계 및 작업을 자세히 설명합니다. 단계 및 작업 전체에 걸쳐 가이드는 서비스 품질(예: 가용성, 확장성, 성능, 관리 효율성 및 보안) 수준 요구 사항을 충족하기 위해 조직에 사용할 수 있는 관련 기술 및 기능 옵션을 보여줍니다. 

특히 하이브리드 ID 설계 고려 사항 가이드 목표로 다음 질문에 대답합니다. 

* 요구 사항에 가장 적당한 기술 또는 문제 도메인에 대한 하이브리드 ID 특정 설계를 이끌어내기 위해 무엇을 질문하고 대답해야 합니까?
* 기술 또는 문제 도메인에 대한 하이브리드 ID 솔루션을 설계하려면 어떤 일련의 작업을 완료해야 합니까? 
* 요구 사항에 맞도록 어떤 하이브리드 ID 기술 및 구성 옵션을 사용할 수 있습니까? 비즈니스를 위한 최상의 옵션을 선택 수 있게 하는 해당 옵션 간의 장단점은 무엇입니까?

## <a name="who-is-this-guide-intended-for"></a>이 가이드는 누구를 위해 마련되었습니까?
 CIO, CITO, 수석 ID 설계자, 엔터프라이즈 설계자 및 IT 설계자는 중간 규모 또는 대규모 조직에 하이브리드 ID 솔루션의 설계를 담당합니다.

## <a name="how-can-this-guide-help-you"></a>이 가이드가 어떻게 도움이 됩니까?
클라우드 기반 ID 관리 시스템을 현재 온-프레미스 ID 솔루션과 통합할 수 있는 하이브리드 ID 솔루션을 설계하는 방법을 이해하는 데 이 가이드를 사용할 수 있습니다. 

다음 그래픽은 IT 관리자가 클라우드 및 온-프레미스에 위치한 응용 프로그램에 Single Sign-On(SSO)을 사용자가 사용할 수 있도록 Microsoft Azure Active Directory를 사용하여 현재 온-프레미스에 있는 Windows Server Active Directory 솔루션을 통합하도록 관리할 수 있는 하이브리드 ID 솔루션의 예를 보여줍니다.

![](./media/hybrid-id-design-considerations/hybridID-example.png)

위의 그림은 최종 사용자 인증 프로세스에 단일 환경을 제공하고 해당 리소스를 관리하는 IT를 용이하게 하기 위해 온-프레미스 기능을 통합하는 클라우드 서비스를 활용하는 하이브리드 ID 솔루션의 예입니다. 매우 일반적인 시나리오일 수 있지만 모든 조직의 하이브리드 ID 설계는 여러 요구 사항으로 인해 그림1에 설명된 예제와 다를 수 있습니다. 

이 가이드는 조직의 고유한 요구 사항을 만족하는 하이브리드 ID 솔루션을 설계하기 위해 수행할 수 있는 일련의 단계 및 작업을 제공합니다. 다음 단계 및 작업 전체에 걸쳐 가이드는 조직의 기능 및 서비스 품질 수준 요구 사항을 충족하기 위해 사용할 수 있는 관련 기술 및 기능 옵션을 표시합니다.

**가정**: Windows Server, Active Directory Domain Services 및 Azure Active Directory를 사용한 경험이 있습니다. 이 문서에서는 이러한 솔루션이 자체적으로 또는 통합된 솔루션에서 비즈니스 요구를 충족하는 방법을 찾는다고 가정합니다.

## <a name="design-considerations-overview"></a>설계 고려 사항 개요
이 문서는 요구 사항을 만족하는 하이브리드 ID 솔루션을 설계하기 위해 수행할 수 있는 일련의 단계 및 작업을 제공합니다. 단계는 정렬된 순서로 표시됩니다. 그러나 설계 선택에서 충돌로 인해 이후 단계에서 설명하는 설명 고려 사항은 이전 단계에서 결정한 사항을 변경할 수도 있습니다. 문서 전체에서 잠재적인 설계 충돌을 경고하기 위해 최선을 다합니다. 

문서 내에서 고려 사항을 모두 통합하는 데 필요한 횟수 만큼 단계를 반복한 후에 사용자의 요구 사항에 가장 맞는 설계에 도달합니다. 

| 하이브리드 ID 단계 | 항목 목록 |
| --- | --- |
| ID 요구 사항 확인 |[비즈니스 요구 사항 결정](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [디렉터리 동기화 요구 사항 결정](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Multi-Factor Authentication 요구 사항 결정](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [하이브리드 ID 채택 전략 정의](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| 강력한 ID 솔루션을 통해 데이터 보안을 향상하기 위한 계획 |[데이터 보호 요구 사항 결정](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [콘텐츠 관리 요구 사항 결정](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [액세스 제어 요구 사항 확인](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [사고 대응 요구 사항 결정](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [데이터 보호 전략 정의](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) |
| 하이브리드 ID 수명 주기에 대한 계획 |[하이브리드 ID 관리 작업 결정](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [동기화 관리](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [하이브리드 ID 관리 채택 전략 결정](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="download-this-guide"></a>이 가이드 다운로드
[Technet 갤러리](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288)에서 하이브리드 ID 설계 고려 사항 가이드의 pdf 버전을 다운로드할 수 있습니다. 

