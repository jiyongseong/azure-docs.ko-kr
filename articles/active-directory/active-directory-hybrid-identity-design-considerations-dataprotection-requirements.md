---
title: "Azure의 하이브리드 ID 디자인 - 데이터 보호 요구 사항 | Microsoft Docs"
description: "하이브리드 ID 솔루션을 계획할 때 이러한 요구 사항을 가장 잘 수행할 수 있도록 비즈니스에 대한 데이터 보호 요구 사항 및 사용할 수 있는 옵션을 식별합니다."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 1a2feca8d24fabda4c191b5a181ab6606c912657
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>강력한 ID 솔루션을 통해 데이터 보안을 향상하기 위한 계획
데이터를 보호하려면 먼저 해당 데이터에 액세스할 수 있는 사용자를 식별하고 인증 및 권한 부여 기능을 제공하기 위해 이 프로세스의 일부로 시스템과 통합할 수 있는 ID 솔루션이 필요합니다. 인증 및 권한 부여는 종종 서로 혼동되고 역할은 오해됩니다. 아래 그림에 표시된 대로 실제로 매우 다릅니다.

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)

**모바일 장치 관리 수명 주기 단계**

하이브리드 ID 솔루션을 계획할 때 이러한 요구 사항을 가장 잘 수행할 수 있도록 비즈니스에 대한 데이터 보호 요구 사항 및 사용할 수 있는 옵션을 이해해야 합니다.

> [!NOTE]
> 데이터 보안에 대한 계획을 완료하면 [Multi-Factor Authentication 요구 사항 확인](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) 을 검토하여 Multi-Factor Authentication에 관한 선택이 이 섹션에서의 결정에 영향받지 않도록 합니다.
> 
> 

## <a name="determine-data-protection-requirements"></a>데이터 보호 요구 사항 결정
이동성의 시대에 회사에는 대부분 공통의 목표가 있습니다. 생산성을 향상시키기 위해 온-프레미스하는 동안 또는 어디서든 원격으로 사용자가 모바일 장치에서 생산성을 높일 수 있도록 합니다. 이것이 공통의 목적일 수 있는 반면 이러한 요구 사항이 있는 회사는 또한 회사의 데이터 보안을 유지하고 사용자의 개인 정보를 유지하기 위해 완화해야 하는 위협의 양에 관한 문제입니다. 각 회사는 이런 점에서 다른 요구 사항이 있을 수 있습니다. 회사가 행동하는 업계에 따라 달라질 수 있는 다른 규정 준수 규칙은 다른 설계 결정으로 이어질 수 있습니다. 

그러나 업계에 관계 없이 탐색 하 고 유효성을 검사해야 할 일부 보안 사항이 있으며 이는 다음 섹션에서 설명합니다.

## <a name="data-protection-paths"></a>데이터 보호 경로
![](./media/hybrid-id-design-considerations/data-protection-paths.png)

**데이터 보호 경로**

위 다이어그램에서 ID 구성 요소는 데이터가 액세스되기 전에 첫번째로 확인됩니다. 그러나 이 데이터는 액세스하는 동안 다양한 상태일 수 있습니다. 이 다이어그램의 각 숫자는 데이터가 특정 시점에 위치할 수 있는 경로를 나타냅니다. 이러한 숫자는 아래에 설명됩니다.

1. 장치 수준에서 데이터 보호.
2. 전송 중에 데이터 보호.
3. 휴지 상태의 온-프레미스에서 데이터 보호.
4. 휴지 상태의 클라우드에서 데이터 보호.

IT가 해당 단계에서 각각 스스로 데어타를 보호할 수 있는 기술적 제어가 직접 하이브리드 ID 솔루션에서 제공되지 않지만 하이브리드 ID 솔루션은 데이터에 대한 액세스를 부여하기 전에 온-프레미스 및 클라우드 ID 관리 리소스를 활용하여 사용자를 식별할 수 있습니다. 하이브리드 ID 솔루션을 계획할 때에는 조직의 요구 사항에 따라 다음 질문에 답변해 보세요.

## <a name="data-protection-at-rest"></a>휴지 상태의 데이터 보호
데이터(장치, 클라우드 또는 온-프레미스)가 휴지 상태인 것과 관계 없이 이런 맥락에서 조직의 필요를 이해하는 평가를 수행하는 것이 중요합니다. 이 영역의 경우 다음 질문을 묻는지를 확인합니다.

* 회사는 미사용 데이터를 보호해야 합니까?
  * 그렇다면 하이브리드 ID 솔루션은 현재 온-프레미스 인프라와 통합할 수 있습니까?
  * 그렇다면 하이브리드 ID 솔루션은 클라우드에 위치한 워크로드와 통합할 수 있나요?
* 클라우드 ID 관리는 사용자의 자격 증명 및 클라우드에 저장된 다른 데이터를 보호할 수 있습니까?

## <a name="data-protection-in-transit"></a>전송 중인 데이터 보호
장치와 데이터 센터 또는 장치와 클라우드 간에 전송 중인 데이터는 보호되어야 합니다. 그러나 전송 중인 상태는 반드시 클라우드 서비스 외부의 구성 요소와 통신 프로세스를 의미하지 않습니다. 또한 예를 들어 두 가상 네트워크 간에 내부적으로 이동합니다. 이 영역의 경우 다음 질문을 묻는지를 확인합니다.

* 회사가 전송 중인 데이터를 보호해야 하나요?
  * 그렇다면 하이브리드 ID 솔루션은 SSL/TLS와 같은 보안 제어와 통합할 수 있나요?
* 클라우드 ID 관리는 서명된 디렉터리 저장소(내부 및 데이터 센터 간) 간에 트래픽을 유지합니까?

## <a name="compliance"></a>규정 준수
규정, 법률 및 규정 준수 요구 사항은 회사가 속해 있는 업계에 따라 달라집니다. 강력하게 규제된 업계에 있는 회사는 규정 준수 문제와 관련된 ID 관리 문제를 해결해야 합니다. SOX(Sarbanes-Oxley), HIPAA(Health Insurance Portability and Accountability Act), GLBA(Gramm-Leach-Bliley Act) 및 PCI DSS(Payment Card Industry Data Security Standard)와 같은 규정은 ID 및 액세스에 관해 매우 엄격합니다. 회사가 도입하는 하이브리드 ID 솔루션에는 하나 이상의 이러한 규정 요구 사항을 충족하는 핵심 기능이 있어야 합니다. 이 영역의 경우 다음 질문을 묻는지를 확인합니다.

* 하이브리드 ID 솔루션은 비즈니스에 대한 규정 요구 사항을 준수합니까?
* 하이브리드 ID 솔루션에는 회사가 규정 요구 사항을 준수할 수 있게 해주는 기본 제공 기능이 있습니다? 

> [!NOTE]
> 각 답변을 주목하고 답변 이유를 이해해야 합니다. [데이터 보호 전략 정의](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) 에서는 사용할 수 있는 옵션과 각 옵션의 장점/단점을 살펴봅니다.  질문에 답변함으로써 비즈니스 요구 사항에 가장 적합한 옵션을 선택할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
 [콘텐츠 관리 요구 사항 결정](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>참고 항목
[설계 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

