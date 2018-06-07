---
title: Azure Lab Services 정보 | Microsoft 문서 도구
description: Lab Services를 사용하면 개발자, 테스터, 교육자, 학생 및 기타 사람들이 사용할 수 있는 가상 머신으로 랩을 쉽게 만들고, 관리하고, 보안을 유지할 수 있는 방법에 대해 알아봅니다.
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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: b42183369b9ad88c77a05a91fdba8fe0efca2a8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="an-introduction-to-azure-lab-services-formerly-azure-devtest-labs"></a>Azure Lab Services(이전 Azure DevTest Labs) 소개
Azure Lab Services를 사용하면 클라우드에서 팀 환경(예: 개발 환경, 테스트 환경, 클래스룸 랩 환경)을 신속하게 설정할 수 있습니다. 랩 소유자는 랩을 만들고, Windows 또는 Linux 가상 머신을 프로비전하고, 필요한 소프트웨어 및 도구를 설치하고, 랩 사용자가 사용할 수 있게 합니다. 랩 사용자는 랩의 VM(가상 머신)에 연결하여 그것들을 일상 업무, 단기 프로젝트 또는 클래스룸 연습에 사용합니다. 사용자가 랩에서 리소스를 활용하기 시작하면 랩 관리자는 여러 랩의 비용 및 사용을 분석하고, 조직 또는 팀의 비용을 최적화하기 위해 포괄적인 정책을 설정할 수 있습니다.

> [!IMPORTANT]
> Azure DevTest Labs는 새로운 유형의 랩(Azure Lab Services)으로 확장되고 있습니다! 
> 
> Azure Lab Services를 사용하면 클래스룸 랩과 같은 관리되는 랩을 만들 수 있습니다. 서비스 자체는 VM 가동에서 오류 처리 및 인프라 확장에 이르기까지 관리되는 랩의 모든 인프라 관리를 처리합니다. 관리되는 랩은 현재 미리 보기로 제공됩니다. 일단 미리 보기가 끝나면 새로운 랩 유형과 기존 DevTest Labs는 Azure Lab Services의 포괄적인 새로운 공통 이름인 Azure Lab Services를 사용하여 모든 유형의 랩 유형이 계속 발전합니다. 

## <a name="key-capabilities"></a>주요 기능
Azure Lab Services는 다음과 같은 주요 기능/특징을 지원합니다. 

- **랩의 빠르고 유연한 설정**. 랩 소유자는 Azure Lab Services를 사용하여 필요에 따라 랩을 신속하게 설정할 수 있습니다. 이 서비스는 관리되는 랩의 모든 Azure 인프라 작업을 처리하거나 랩 소유자가 랩 소유자의 구독에서 인프라를 자체 관리하고 사용자 지정할 수 있는 옵션을 제공합니다. 이 서비스는 서비스가 관리하는 랩을에 대한 인프라의 기본 확장 및 복구 기능을 제공합니다. 
- **랩 사용자를 위한 간소화된 경험**. 랩 사용자는 클래스룸 랩과 같은 관리되는 랩에서 등록 코드로 랩에 등록하고 언제든지 랩에 액세스하여 랩 리소스를 사용할 수 있습니다. 랩 소유자는 DevTest Labs에서 만든 사용자 지정 랩에서 가상 머신을 만들고 액세스하며, 데이터 디스크를 관리 및 재사용하고, 재사용 할 수 있는 비밀을 설정하도록 랩 사용자들에게 권한을 부여할 수 있습니다.  
- **비용 최적화 및 분석**. 랩 소유자는 랩 일정을 설정하여 가상 머신를 자동으로 종료하고 시작할 수 있습니다. 랩 소유자는 랩의 가상 머신에 사용자가 액세스할 수 있는 시간대를 지정하고, 사용자 또는 랩별로 사용 정책을 설정하여 비용을 최적화하고, 랩에서 사용 및 활동 동향을 분석하기 위해 일정을 설정할 수 있습니다. 클래스룸 랩과 같은 관리되는 랩의 경우 현재 보다 작은 하위 집합의 비용 최적화 및 분석 옵션을 사용할 수 있습니다. 
- **내장된 보안**. 랩 소유자는 랩에 대한 개인 가상 네트워크와 서브넷을 설정하고 공유되는 공용 IP 주소를 사용할 수 있습니다. 랩 사용자는 ExpressRoute 또는 사이트 간 VPN으로 구성된 가상 네트워크를 사용하여 안전하게 리소스에 액세스할 수 있습니다. (현재 DevTest Labs에서만 사용 가능)
- **워크 플로와 도구에 통합**. Azure Lab Services를 사용하여 랩을 조직의 웹 사이트 및 관리 시스템에 통합할 수 있습니다. 지속적인 통합/지속적인 배포(CI/CD) 도구 내에서 환경을 자동으로 프로비전할 수 있습니다. (현재 DevTest Labs에서만 사용 가능)

## <a name="scenarios"></a>시나리오
Azure Lab Services가 지원하는 몇 가지 시나리오는 다음과 같습니다. 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>클래스룸을 위해 크기 조정이 가능한 컴퓨터 랩을 클라우드에 설치  

- 관리되는 클래스룸 랩 만들기 필요한 서비스를 정확히 알려주시면 랩의 기술적 세부 사항이 아닌, 클래스를 가르치는 데 집중할 수 있도록 랩의 인프라를 만들고 관리합니다. 
- 학생들에게 클래스에 필요한 것으로 정확히 구성된 가상 머신 랩을 제공합니다. 각 학생에게 VM을 클래스 학습에 사용하도록 제한된 시간을 줍니다.  
- 학교의 실제 컴퓨터 랩을 클라우드로 옮깁니다. VM 수를 랩에 설정한 최대 사용량 및 비용 임계 값에 이르기 까지만 자동으로 조정합니다. 
- 마치면 클릭 한 번으로 랩을 삭제합니다. 

### <a name="use-devtest-labs-for-development-environments"></a>개발 환경에 DevTest Labs 사용 
Azure DevTest Labs를 사용하여 여러 주요 시나리오를 구현할 수 있지만 기본 시나리오 중 하나는 DevTest Labs를 사용하여 개발자를 위한 개발 컴퓨터를 호스트하는 것입니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다. 

- 개발자는 필요에 따라 개발 컴퓨터를 신속하게 프로비전할 수 있습니다.
- 재사용 가능한 템플릿 및 아티팩트를 사용하여 Windows 및 Linux 환경을 프로비저닝합니다.
- 개발자는 필요할 때마다 개발 컴퓨터를 쉽게 사용자 지정할 수 있습니다.
- 관리자는 개발자가 개발하는 데 필요한 것보다 많은 VM을 얻을 수 없도록 하고 VM을 사용하지 않을 때 종료되도록 함으로써 비용을 제어할 수 있습니다. 

자세한 내용은 [개발을 위해 DevTest Labs 사용](devtest-lab-developer-lab.md)을 참조하세요. 

### <a name="use-devtest-labs-for-test-environments"></a>테스트 환경에 DevTest Labs 사용
Azure DevTest Labs를 사용하여 여러 주요 시나리오를 구현할 수 있지만 기본 시나리오는 DevTest Labs를 사용하여 테스터를 위한 컴퓨터를 호스트하는 것입니다. 이 시나리오에서 DevTest Labs는 다음과 같은 이점을 제공합니다.

- 테스터가 재사용 가능한 템플릿과 아티팩트를 사용하여 Windows와 Linux 환경을 빠르게 프로비전함으로써 최신 버전의 응용 프로그램을 테스트할 수 있습니다.
- 테스터가 다수의 테스트 에이전트를 프로비전하여 부하 테스트를 강화할 수 있습니다.
- 관리자는 테스터가 테스트하는 데 필요한 것보다 많은 VM을 얻을 수 없도록 하고 VM을 사용하지 않을 때 종료되도록 함으로써 비용을 제어할 수 있습니다.

자세한 내용은 [테스트에 DevTest Labs 사용](devtest-lab-test-env.md)을 참조하세요.

## <a name="user-profiles"></a>사용자 프로필
이 문서에서는 Azure Lab Services에 있는 다른 사용자 프로필을 설명합니다. 

### <a name="lab-account-owner"></a>랩 계정 소유자
Azure 구독을 소유한 조직내 클라우드 리소스의 IT 관리자는 일반적으로 랩 계정 소유자 역할을 하며 다음 작업을 수행합니다.   

- 조직에 대한 랩 계정을 설정합니다.
- 모든 랩에서 정책을 관리하고 구성합니다.
- 조직의 사람들에게 랩 계정으로 랩을 만들 수있는 권한을 부여합니다.

### <a name="lab-creator"></a>랩 작성자 
일반적으로 개발 리드/관리자, 교사, 해커톤 호스트, 온라인 트레이너와 같은 사용자는 랩 계정으로 랩을 만듭니다. 랩 작성자는 다음 작업을 수행합니다. 

- 랩을 만듭니다.
- 랩에서 가상 머신을 만듭니다. 
- 가상 머신에 적절한 소프트웨어를 설치합니다.
- 랩에 액세스할 수 있는 사용자를 지정합니다.
- 랩 사용자에게 랩에 대한 링크를 제공합니다.

### <a name="lab-user"></a>랩 사용자
랩 사용자는 다음 작업을 수행합니다.

- 랩 사용자는 랩 작성자로부터 받은 등록 링크를 사용하여 랩에 등록합니다. 
- 랩의 가상 머신에 연결하여 개발, 테스트 또는 클래스 작업을 하는 데 사용합니다. 

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 랩 설정 시작합니다.

- [클래스룸 랩 설정](tutorial-setup-classroom-lab.md)
- [사용자 지정 랩 설정](tutorial-create-custom-lab.md)
