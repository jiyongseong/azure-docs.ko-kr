---
title: Azure Dev Spaces를 공유하는 방법 | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
manager: douge
ms.openlocfilehash: 140a487a0a605e0f03122868af0d2d12bcdba35b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34197660"
---
# <a name="share-azure-dev-spaces"></a>Azure Dev Spaces 공유

Azure Dev Spaces를 사용하여 팀의 다른 사용자와 개발 공간을 공유할 수 있습니다. 각 개발자는 다른 사용자를 중단시킬 걱정 없이 자체 공간에서 작업할 수 있습니다. 또한 하나의 공간에서 함께 작업하면 모의 개체를 만들거나 종속성을 시뮬레이션할 필요 없이 종단 간 코드를 테스트할 수 있습니다. 자세한 내용은 [팀 개발 알아보기](../get-started-nodejs.md#learn-about-team-development) 가이드를 참조하세요.

여러 개발자를 위한 환경을 설정하려면:
1. Azure에서 Dev Space를 만듭니다. [.NET Core 및 VS Code](../get-started-netcore.md), [.NET Core 및 Visual Studio](../get-started-netcore-visualstudio.md) 또는 [Node.js 및 VS Code](../get-started-nodejs.md)를 선택합니다. 선택한 Azure 구독에 대한 소유자 또는 기여자 액세스 권한이 있어야 합니다.
1. Azure Dev Space의 **리소스 그룹**을 각 팀 멤버에 대해 [기여자 액세스 권한을 부여](/azure/active-directory/role-based-access-control-configure)하도록 구성합니다. 이 명령을 실행하여 환경의 리소스 그룹을 확인할 수 있습니다. `azds resource list`
1. 해당 환경에서 개발하기 위해 팀 멤버에게 **환경을 선택**하도록 요청합니다.
     * **명령줄 또는 VS Code**: 액세스 권한이 있는 기존 Azure Dev Spaces를 보려면: `azds resource list` 환경을 선택하려면: `azds resource select`
     * **Visual Studio IDE**: Visual Studio에서 프로젝트를 열고, 시작 설정 드롭다운에서 **Azure Dev Spaces**를 선택합니다. 열린 대화 상자에서 기존 개발 환경을 선택합니다.

![Visual Studio 시작 설정 드롭다운](../media/get-started-netcore-visualstudio/LaunchSettings.png)