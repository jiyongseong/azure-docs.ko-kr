---
title: "Azure의 하이브리드 ID 디자인 - 인시던트 대응 요구 사항 | Microsoft Docs"
description: "IT에서 활용할 수 있는 하이브리드 ID 솔루션에 대한 모니터링 및 보고 기능을 확인하여 잠재적인 위협을 식별하고 완화하는 작업을 수행합니다."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: a3d2a459-599b-4b67-8e51-7369ee25082d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 1521c3c7353d206df8a95b2cce1f3358c22fc438
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>하이브리드 ID 솔루션에 대한 인시던트 대응 요구 사항 확인
대규모 또는 중간 규모 조직에서는 대개 [보안 인시던트 대응](https://technet.microsoft.com/library/cc700825.aspx) 을 준비하여 IT가 인시던트의 수준에 따라 동작을 취합니다. ID 관리 시스템은 대상에 대해 특정 동작을 수행한 사용자를 식별하는 데 사용될 수 있기 때문에 인시던트 대응 프로세스에서 중요한 구성 요소입니다. 하이브리드 ID 솔루션은 IT에서 활용할 수 있는 모니터링 및 보고 기능을 제공하여 잠재적인 위협을 식별하고 완화하는 작업을 수행해야 합니다. 일반적인 인시던트 대응 계획에서 계획의 일부를 다음과 같은 단계로 해야 합니다.

1. 초기 평가.
2. 인시던트 통신.
3. 피해 제어 및 위험 감소.
4. 손상 및 심각도의 식별.
5. 증거 보존.
6. 적절한 당사자에 알림.
7. 시스템 복구.
8. 설명서.
9. 피해 및 비용 평가.
10. 프로세스 및 계획 수정.

손상 및 심각도의 식별 단계 중에 손상된 시스템, 액세스된 파일을 식별하고 해당 파일의 중요도를 결정할 필요가 있습니다. 하이브리드 ID 시스템은 이러한 요구 사항을 충족하여 해당 변경을 수행한 사용자를 식별하는 데 도움이 되어야 합니다. 

## <a name="monitoring-and-reporting"></a>모니터링 및 보고
또한 시스템이 기본 제공 감사 및 보고 기능을 제공하는 경우 ID 시스템은 주로 초기 평가 단계에서 유용할 수 있습니다. 초기 평가 중에 IT 관리자가 의심스러운 활동을 식별할 수 있거나 시스템이 미리 구성된 작업에 기초하여 자동으로 트리거할 수 있어야 합니다. 작업이 많다는 점은 공격의 가능성을 나타낼 수 있지만 다른 경우에 잘못 구성된 시스템 때문에 침입 감지 시스템에서 거짓 긍정을 이끌어낼 수도 있습니다. 

ID서 관리 시스템은 IT 관리자를 도와 해당하는 의심스러운 활동을 식별하고 보고해야 합니다. 일반적으로 이러한 기술 요구 사항은 모든 시스템을 모니터링하고 잠재적인 위협 요소를 강조 표시할 수 있는 보고 기능에 의해 수행될 수 있습니다. 인시던트 대응 요구 사항을 고려하는 동안 아래의 질문을 사용하여 하이브리드 ID 솔루션을 설계하도록 합니다.

* 회사는 보안 인시던트 대응에 대비하고 있습니까?
  * 그렇다면 현재 ID 관리 시스템이 프로세스의 일부로 사용됩니까?
* 회사가 다른 장치에서 사용자의 의심스러운 로그인 시도를 식별해야 합니까?
* 회사가 잠재적으로 공격에 노출된 사용자의 자격 증명을 검색해야 합니까?
* 회사가 사용자의 액세스 및 동작을 감사해야 합니까?
* 사용자가 자신의 암호를 재설정하는 경우 회사가 알아야 합니까?

## <a name="policy-enforcement"></a>정책 적용
손상 제어 및 위험 감소 단계 동안 잠재적 공격의 실제 및 잠재적인 영향을 신속하게 줄이는 것이 중요합니다. 이 시점에서 수행할 동작은 주 및 부 동작 간의 차이를 만들 수 있습니다. 정확한 대응은 조직 및 직면하는 공격의 특성에 따라 달라집니다. 초기 평가에서 계정이 손상되었다고 결론을 얻은 경우 이 계정을 차단하는 정책을 적용해야 합니다. ID 관리 시스템이 활용되는 하나의 예입니다. 진행 중인 인시던트에 반응하기 위해 정책이 어떻게 적용될지를 고려하는 동안 아래의 질문을 사용하여 하이브리드 ID 솔루션을 설계하도록 합니다.

* 필요한 경우 회사가 네트워크 액세스에서 사용자를 블록하는 정책이 준비되어 있습니까?
  * 그렇다면 현재 솔루션이 채택하려는 하이브리드 ID 관리 시스템과 통합됩니까?
* 회사가 격리된 사용자에 대해 조건부 액세스를 적용해야 합니까? 

> [!NOTE]
> 각 답변을 주목하고 답변 이유를 이해해야 합니다. [데이터 보호 전략 정의](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) 에서는 사용할 수 있는 옵션과 각 옵션의 장점/단점을 살펴봅니다.  질문에 답변함으로써 비즈니스 요구 사항에 가장 적합한 옵션을 선택할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
[데이터 보호 전략 정의](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>참고 항목
[설계 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

