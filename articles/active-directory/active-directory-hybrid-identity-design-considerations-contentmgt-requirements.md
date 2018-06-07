---
title: "Azure의 하이브리드 ID 디자인 - 콘텐츠 관리 요구 사항 | Microsoft Docs"
description: "비즈니스의 콘텐츠 관리 요구 사항을 확인하는 방법을 설명합니다. 일반적으로 사용자에게 자신만의 장치가 있는 경우 사용하는 응용 프로그램에 따라 변경되는 여러 자격 증명이 있을 수 있습니다. 개인 자격 증명을 사용하여 생성된 콘텐츠와 회사 자격 증명을 사용하여 생성된 콘텐츠를 구분하는 것이 중요합니다. ID 솔루션은 최종 사용자의 개인 정보를 보호하고 데이터 유출 방지 기능을 개선하면서, 클라우드 서비스와 상호 작용하여 최종 사용자에게 원활한 환경을 제공할 수 있어야 합니다."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 71e33ec82c3db6fb7efa52dd12315e309658aab9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>하이브리드 ID 솔루션에 대한 콘텐츠 관리 요구 사항 확인
비즈니스의 콘텐츠 관리 요구 사항에 대한 이해는 어떤 하이브리드 ID 솔루션을 사용할지 결정하는 데 직접적인 영향을 미칠 수 있습니다. 여러 장치 및 사용자가 자신의 장치를 가져오는 기능([BYOD](https://aka.ms/byodcg))이 확산됨에 따라 회사는 회사만의 데이터도 보호해야 하지만 사용자의 개인 정보도 보호해야 합니다. 일반적으로 사용자에게 자신만의 장치가 있는 경우 사용하는 응용 프로그램에 따라 변경되는 여러 자격 증명이 있을 수 있습니다. 개인 자격 증명을 사용하여 생성된 콘텐츠와 회사 자격 증명을 사용하여 생성된 콘텐츠를 구분하는 것이 중요합니다. ID 솔루션은 최종 사용자의 개인 정보를 보호하고 데이터 유출 방지 기능을 개선하면서, 클라우드 서비스와 상호 작용하여 최종 사용자에게 원활한 환경을 제공할 수 있어야 합니다. 

ID 솔루션은 아래 그림에 표시된 것처럼 콘텐츠 관리를 제공하기 위해 여러 기술적 컨트롤에서 활용합니다.

![](./media/hybrid-id-design-considerations/securitycontrols.png)

**ID 관리 시스템을 활용하는 보안 컨트롤**

일반적으로 콘텐츠 관리 요구 사항은 다음과 같은 영역에서 ID 관리 시스템을 활용합니다.

* 개인 정보 보호: 리소스를 소유하는 사용자를 식별하고 적절한 컨트롤을 적용하여 무결성을 유지합니다.
* 데이터 분류: 분류에 따라 사용자 또는 그룹 및 개체에 대한 액세스 수준을 식별합니다. 
* 데이터 유출 방지: 유출을 막기 위해 데이터 보호를 담당하는 보안 컨트롤은 ID 시스템과 상호 작용하여 사용자의 ID를 확인해야 합니다. 이는 감사 내역을 위해서도 중요합니다.

> [!NOTE]
> 데이터 분류에 대한 모범 사례 및 지침에 대한 자세한 내용은 [클라우드 준비를 위한 데이터 분류](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) 를 참조하세요.
> 
> 

하이브리드 ID 솔루션을 계획할 때에는 조직의 요구 사항에 따라 다음 질문에 답변해 보세요.

* 회사에 데이터 프라이버시를 시행하기 위한 보안 컨트롤이 있나요?
  * 보안 컨트롤이 있다면 그러한 보안 컨트롤이 채택하려는 하이브리드 ID 솔루션과 통합될 수 있나요?
* 회사에서 데이터 분류를 사용하나요?
  * 그렇다면 현재 솔루션이 채택하려는 하이브리드 ID 솔루션과 통합될 수 있나요?
* 현재 회사에 데이터 유출에 대한 솔루션이 있나요? 
  * 그렇다면 현재 솔루션이 채택하려는 하이브리드 ID 솔루션과 통합될 수 있나요?
* 회사가 리소스에 대한 액세스를 감사해야 하나요?
  * 그렇다면 어떤 유형의 리소스인가요?
  * 그렇다면 어떤 수준의 정보가 필요한가요?
  * 그렇다면 감사 로그는 어디에 있어야 하나요? 온-프레미스 또는 클라우드에 있어야 하나요?
* 회사가 중요한 데이터(SSN, 신용 카드 번호 등)를 포함하는 메일을 암호화해야 하나요?
* 회사가 외부 비즈니스 파트너와 공유하는 모든 문서/내용을 암호화해야 하나요?
* 회사가 특정 유형의 메일에 회사 정책을 적용해야 하나요(전체 회신 금지, 전달 금지)?

> [!NOTE]
> 각 답변을 주목하고 답변 이유를 이해해야 합니다. [데이터 보호 전략 정의](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) 에서는 사용할 수 있는 옵션과 각 옵션의 장점/단점을 살펴봅니다.  질문에 답변함으로써 비즈니스 요구 사항에 가장 적합한 옵션을 선택할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
[액세스 제어 요구 사항 확인](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>참고 항목
[설계 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

