---
title: Azure DevTest Lab 정보 | Microsoft Docs
description: DevTest Lab에서 Azure 가상 머신을 쉽게 만들고 관리하고 모니터링할 수 있는 방법 알아보기
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: bf9e83a1dfed0aac7f48cd47dc12bf56c7cb1b64
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs 정보
## <a name="overview"></a>개요
개발자와 테스터는 클라우드로 이동하여 해당 환경을 만들고 관리하면서 발생하는 지연을 해결할 방법을 찾고 있습니다.  Azure에서는 환경 지연 문제가 해결되고 비용 효율적인 새 구조 안에서 셀프 서비스가 가능합니다.  하지만 개발자와 테스터는 셀프 서비스되는 환경을 구성하기 위해 여전히 상당한 시간을 소비해야 합니다. 또한 의사 결정자는 너무 많은 프로세스 오버헤드를 추가하지 않으면서 클라우드를 활용하여 비용 절감을 최대화하는 방법을 잘 모르고 있습니다.

Azure DevTest Lab은 개발자와 테스터가 낭비를 최소화하고 비용을 제어하면서 Azure에서 빠르게 환경을 만들 수 있도록 돕는 서비스입니다. 재사용이 가능한 템플릿과 아티팩트를 사용하여 Windows와 Linux 환경을 빠르게 프로비전함으로써 최신 버전의 응용 프로그램을 테스트할 수 있습니다. 배포 파이프라인과 DevTest Lab을 쉽게 통합하여 주문형 환경으로 프로비전할 수 있습니다. 여러 개의 테스트 에이전트를 프로비전하여 부하 테스트를 확장하고 교육 및 데모를 위해 미리 프로비전된 환경을 만듭니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

DevTest Lab은 클라우드에서 개발자 및 테스트 환경을 만들고 구성하고 관리하는 데 다음과 같은 이점을 제공합니다.

## <a name="worry-free-self-service"></a>걱정할 필요가 없는 셀프 서비스
DevTest Lab을 사용하면 사용자당 VM 수(가상 머신) 및 랩당 VM 수와 같은 정책을 랩에서 설정할 수 있기 때문에 더 쉽게 비용을 제어할 수 있습니다. 또한 DevTest Lab을 사용하면 VM을 자동으로 종료하거나 시작하는 정책을 만들 수 있습니다.

## <a name="quickly-get-to-ready-to-test"></a>빠른 테스트 준비
DevTest Lab을 사용하면 팀에서 응용 프로그램 개발 및 테스트를 시작하는 데 필요한 모든 사항이 미리 프로비전된 환경을 만들 수 있습니다. 응용 프로그램의 마지막 빌드가 설치된 환경을 클레임하고 바로 작업을 시작합니다. 또는 더 빠르고 간결한 환경을 만들기 위해 컨테이너를 사용합니다.

## <a name="create-once-use-everywhere"></a>한 번 만들어서 어디에나 사용
팀 또는 조직 내에서 환경 템플릿 및 아티팩트(모두 원본 컨트롤에 있음)를 캡처하고 공유하여 개발자 환경 및 테스트 환경을 쉽게 만들 수 있습니다.

## <a name="integrates-with-your-existing-toolchain"></a>기존 도구 체인과 통합
미리 만들어져 있는 플러그 인 또는 Microsoft의 API를 활용하여 원하는 CI(지속적인 통합) 도구, IDE(통합 개발 환경) 또는 자동화된 릴리스 파이프라인에서 직접 개발/테스트 환경을 프로비전합니다. Microsoft의 포괄적인 명령줄 도구를 사용할 수도 있습니다.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>다음 단계
[DevTest Lab 개념](devtest-lab-concepts.md)

