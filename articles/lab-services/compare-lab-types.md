---
title: Azure Lab Services에서 여러 유형의 랩 비교 | Microsoft Docs
description: Azure Lab Services (이전의 DevTest Labs)를 사용하여 만들 수 있는 여러 유형의 랩을 설명하고 비교합니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 22a1c90dd1a1ca305431d91a801e5293a6d08703
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34361185"
---
# <a name="compare-managed-and-devtest-labs-in-azure-lab-services"></a>Azure Lab Services에서 관리되는 랩 및 DevTest 랩 비교
두 가지 유형의 랩, 즉 Azure Lab Services를 사용하여 **관리되는 랩** 및 Azure DevTest Labs를 사용하여 **사용자 지정 랩**을 만들 수 있습니다. 단지 랩에서 필요한 것을 입력하고 랩에 필요한 인프라를 설정하고 관리하도록 하려면 **관리되는 랩** 중 하나를 선택합니다. 현재 **클래스룸 랩**은 Azure Lab Services로 만들 수 있는 관리되는 랩의 유일한 유형입니다. 자신의 인프라를 관리하려면 Azure DevTest Labs를 사용하여 **사용자 지정 랩**을 만듭니다.

다음 섹션에서는 이러한 랩을 대한 자세한 정보를 제공합니다. 

## <a name="managed-labs"></a>관리되는 랩
관리되는 랩은 특정 요구에 맞는 다양한 유형의 랩을 제공합니다. 현재 Azure Lab Services는 **클래스룸 랩** 만을 관리되는 랩으로 지원합니다. 관리되는 랩을 사용하면 최소한의 설정으로 바로 시작할 수 있습니다. 서비스 자체는 VM 가동에서 오류 처리 및 인프라 확장에 이르기까지 랩의 모든 인프라 관리를 처리합니다. 관리되는 랩을 만들려면 먼저 조직의 랩 계정을 만들어야 합니다. 랩 계정은 조직의 모든 랩을 관리하는 중앙 계정 역할을 합니다. 

이러한 관리되는 랩에서 Azure 리소스를 만들고 사용할 때 이 서비스는 내부 Microsoft 구독에 리소스를 만들고 관리합니다. 그것들은 자신의 Azure 구독에서 만들어지지 않았습니다. 이 서비스는 내부 Microsoft 구독에서 이러한 리소스의 사용을 추적합니다. 이 사용량은 랩 계정이 포함된 Azure 구독에 청구됩니다.   

다음은 몇 가지 **관리되는 랩의 사용 사례**입니다. 

- 학생들에게 클래스에 필요한 것으로 정확히 구성된 가상 머신 랩을 제공합니다. 각 학생에게 VM을 숙제나 개인 프로젝트에 사용하도록 제한된 시간을 줍니다.
- 계산 집약적인 또는 그래픽 집약적인 연구를 수행하기 위해 고성능 컴퓨팅 VM 풀을 설정합니다. 필요에 따라 VM을 실행하고 완료되면 컴퓨터를 정리합니다. 
- 학교의 실제 컴퓨터 랩을 클라우드로 옮깁니다. VM 수를 랩에 설정한 최대 사용량 및 비용 임계 값에 이르기 까지만 자동으로 조정합니다.  
- hackathon 호스팅용 가상 머신 랩을 신속하게 프로비전합니다. 마치면 클릭 한 번으로 랩을 삭제합니다. 


## <a name="devtest-labs"></a>DevTest 랩
자신의 구독 내 모든 인프라 및 구성을 직접 관리하려는 시나리오가 있을 수 있습니다. 이렇게 하려면 Azure Portal의 Azure DevTest Labs에서 사용자 지정 랩을 만드는 방법을 알아봅니다. 이러한 랩인 경우 랩 계정을 만들 필요가 없습니다. 이러한 랩은 랩 계정(관리되는 랩에 대해 존재)에 표시되지 않습니다.  

다음은 몇 가지 **DevTest 랩의 사용 사례**입니다. 

- 회의에서 hackathon 또는 실습 세션을 호스트할 가상 머신 랩을 신속하게 프로비전합니다. 마치면 클릭 한 번으로 랩을 삭제합니다. 
- 응용 프로그램으로 구성된 VM 풀을 만들고 팀이 버그 잡기를 위해 가상 머신을 쉽게 사용할 수 있게 합니다.  
- 개발자에게 필요한 모든 도구로 구성된 가상 머신을 제공합니다. 자동 시작 및 종료 일정을 계획하여 비용을 최소화합니다. 
- 배포의 일부로 반복적으로 테스트 컴퓨터 랩을 만듭니다. 마쳤다면 최신 비트를 테스트하고 테스트 컴퓨터를 정리합니다. 
- 규모와 성능 테스트를 위해 다르게 구성된 다양한 가상 머신 및 여러 테스트 에이전트를 설정합니다. 
- 최신 버전의 제품으로 구성된 랩을 사용하여 고객에게 교육 세션을 제공합니다. 각 고객에게 랩에서 사용할 제한된 시간을 줍니다. 


## <a name="managed-labs-vs-devtest-labs"></a>관리되는 랩 및 DevTest 랩
다음 표는 Azure Lab Services에서 지원하는 두 가지 유형의 랩을 비교합니다. 

| 기능 | 관리되는 랩 | DevTest 랩 |
| -------- | ----------------  | ---------- |
| 랩에서 Azure 인프라 관리. |  서비스에 의한 자동 관리 | 직접 관리  |
| 인프라 문제에 대한 기본 복원력 | 서비스에 의한 자동 처리 | 직접 관리  |
| 구독 관리 | 서비스는 서비스를 지원하는 Microsoft 구독 내 리소스 할당을 처리합니다. 크기 조정은 서비스에 의해 자동 처리됩니다. | 자신의 Azure 구독으로 직접 관리합니다. 구독에는 자동 크기 조정 기능이 없습니다. |
| 랩 내에서 Azure Resource Manager 배포 | 사용할 수 없음 | 사용 가능 |

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 랩 설정 시작합니다.

- [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)
- [사용자 지정 랩 설정](tutorial-create-custom-lab.md)
