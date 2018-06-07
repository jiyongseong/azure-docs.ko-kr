---
title: Azure Dev 공간 | Microsoft Docs
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: 344947b7906d15e819e372e0affe4af3c34ba69b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198761"
---
# <a name="azure-dev-spaces"></a>Azure Dev 공간
Azure Dev 공간은 Kubernetes에서 빠르게 개발하는 데 도움을 줍니다. Azure Dev 공간을 사용하여 Azure Kubernetes 컨테이너에 대한 디버깅 같은 완전한 개발 기능을 추가할 수도 있으며 VS Code, Visual Studio 또는 명령줄 같은 친숙한 도구를 사용하여 클라우드에서 컨테이너를 반복적으로 개발할 수 있습니다. Azure Dev 공간은 고유한 공간에서 개별 코드 분기의 격리가 개발 주기의 중요한 부분을 차지하는 팀 개발과 특히 관련이 있습니다.

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev 공간이 Kubernetes 개발을 단순화하는 방법 

이 접근 방식에는 몇 가지 이점이 있습니다.

* 클라우드 리소스에 대한 모든 권한을 사용하여 프로덕션을 대표하는 인프라 없는 개발 환경을 가져옵니다.
* VS Code 또는 Visual Studio를 사용하여 Kubernetes에서 직접 Node.js 및 .NET Core 컨테이너를 디버깅합니다. 명령줄 인터페이스를 사용하여 다른 언어 모두를 개발할 수 있습니다.
* 비용을 절약하고 새로운 팀 멤버에 대한 로컬 컴퓨터 설치를 최소화하려면 개발 팀에서 Kubernetes 인스턴스를 공유합니다.
* 격리 상태에서 코드를 개발하고, 종속성을 복제하거나 목업하지 않고 다른 구성 요소로 통합형 테스트를 수행합니다.

[!INCLUDE[](includes/get-started.md)]

![](media/azure-dev-spaces/vscode-overview.png)