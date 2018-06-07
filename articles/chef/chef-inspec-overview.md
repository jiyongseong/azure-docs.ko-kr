---
title: Azure 인프라의 규정 준수 자동화에 대한 InSpec 사용
description: Azure 배포의 문제를 감지하려면 InSpec 사용 방법을 알아봅니다
keywords: azure, chef, devops, 가상 머신, 개요, 자동화, inspec
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259731"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Azure 인프라의 규정 준수 자동화에 대한 InSpec 사용
[InSpec](https://www.chef.io/inspec/)은 응용 프로그램 및 인프라를 감사하고 테스트하기 위한 무료 및 오픈 소스 프레임워크입니다. InSpec는 읽기 쉽고 쓰기 쉬운 InSpec 코드에서 표현하는 필요한 상태를 시스템의 실제 상태와 비교하여 작동합니다. InSpec은 위반을 감지하고 보고서의 형태로 결과물을 표시하지만 재구성을 사용자에게 컨트롤하게 합니다. InSpec을 사용하여 Azure에서 실행 중인 가상 머신 상태의 유효성을 검사할 수 있습니다. 또한 InSpec를 사용하여 구독 내의 리소스 그룹 및 리소스 상태의 유효성을 검사할 수 있습니다.

이 문서에서는 Azure에서 보안 및 규정 준수를 더 쉽게 하기 위한 InSpec의 사용 혜택을 설명합니다.

## <a name="make-compliance-easy-to-understand-and-assess"></a>이해 및 평가하려면 규정 준수 쉽게 하기
InSpec으로 요구 사항을 버전이 지정되고 실행 가능하며 사람이 읽을 수 있는 코드로 변환합니다. 이렇게 함으로써 필요에 따라 예외를 정의하고 사용자 지정하는 경우 테스트를 구성 가능한 프로필로 조직할 수 있습니다.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>전반적인 문제를 검색하고 해당 재구성의 우선 순위를 지정합니다
InSpec 에이전트 없는 검색 모드를 사용하면 노출 수준을 대규모로 신속하게 평가할 수 있습니다. 영향/심각도 채점에 대한 기본 제공 메타데이터는 재구성을 위해 초점을 맞출 영역을 결정하도록 도와줍니다.

## <a name="inspect-machines-data-and-new-saas-apis"></a>컴퓨터, 데이터 및 새 SaaS API 검사
InSpec 클라우드 API 규정 준수 기능은 클라우드 준수에 대한 대략적이고 세분화된 어설션 및 준수에 대한 보고 모두를 지속적으로 수행하게 합니다.

## <a name="satisfy-audits"></a>감사 충족
InSpec을 사용하여 분기별 또는 연도별 등 미리 결정된 기간에 국한되지 않고 언제든 질문을 감사하는 데 대응할 수 있습니다. InSpec은 감사 결과에 놀라지 않고 정확한 준수 상태를 이해하는 감사 주기를 입력하게 합니다.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>규칙에 대한 모호성 및 잘못된 통신 줄이기
문서는 구성 및 프로세스의 해석에 대한 여지를 남겨 놓습니다. 실행 코드는 명확한 의도로 실제 테스트를 우호적으로 평가해야 하는 것에 대한 대화를 제거합니다.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>빠르게 변화하는 위협 및 규정 준수 환경에 대한 이해
InSpec은 같은 날 검색 코드를 쓰고 게시하게 하며, 새 규제에 대한 빠른 대응으로 새 규칙을 작성하게 합니다. 즉, 위협 또는 규제의 변경 내용이 더 이상 응급 상황과 동일하지 않다는 의미입니다.

## <a name="next-steps"></a>다음 단계
* [Azure에서 Chef를 사용하여 Windows 가상 머신 만들기](/azure/virtual-machines/windows/chef-automation)