---
title: Azure Automation 개요
description: Azure Automation을 사용하여 인프라 및 응용 프로그램의 수명 주기를 자동화하는 방법을 알아봅니다.
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, 업데이트 관리, 변경 내용 추적, 인벤토리, Runbook, Python, 그래픽
ms.date: 03/15/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: ce30a9eb11cd951ca7d8197c15c3becba333f5a9
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="an-introduction-to-azure-automation"></a>Azure Automation에 대한 소개

Azure Automation은 Azure 및 비Azure 환경에서 일관된 관리를 제공하는 클라우드 기반 자동화 및 구성 서비스를 제공합니다. 프로세스 자동화, 업데이트 관리 및 구성 기능으로 구성됩니다. Azure Automation은 작업과 리소스의 배포, 조작 및 서비스 해제를 수행하는 동안 완벽한 제어를 제공합니다.
이 문서에서는 Azure Automation에 대한 간단한 개요 및 몇 가지 일반적인 질문에 대한 답변을 제공합니다. 다양한 기능에 대한 자세한 내용은 이 개요의 링크를 방문하세요.

## <a name="azure-automation-capabilities"></a>Azure Automation 기능

![Automation 기능 개요](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>프로세스 자동화

Azure Automation은 빈번하고, 시간 소모적이며, 오류가 발생하기 쉬운 클라우드 관리 작업을 자동화할 수 있는 기능을 제공합니다. 이 자동화를 사용하면 비즈니스 가치를 창출하는 작업에 집중할 수 있습니다. 또한 오류를 줄이고 효율성을 높여 운영 비용을 낮출 수 있습니다. Azure 서비스와 종단 간 프로세스를 배포, 구성 및 관리하는 데 필요한 다른 공용 시스템을 통합할 수 있습니다. 이 서비스를 사용하면 PowerShell 또는 Python에서 [Runbook을 그래픽으로 만들](automation-runbook-types.md) 수 있습니다. 하이브리드 Runbook 작업자를 사용하면 온-프레미스 환경에서 오케스트레이션하여 관리를 통합할 수 있습니다. [웹후크](automation-webhooks.md)는 ITSM, DevOps 및 모니터링 시스템에서 자동화를 트리거하여 요청을 수행하고 지속적인 업데이트 및 작업을 보장할 수 있는 방법을 제공합니다.

### <a name="configuration-management"></a>구성 관리

Azure Automation [DSC(Desired State Configuration)](automation-dsc-overview.md)는 엔터프라이즈 환경에 필요한 서비스를 제공하는 PowerShell DSC에 대한 클라우드 기반 솔루션입니다. Azure Automation에서 DSC 리소스를 관리하고 Azure 클라우드의 DSC 끌어오기 서버에서 가상 또는 실제 머신에 구성을 적용합니다. 노드가 할당된 구성에서 벗어난 경우와 같이 중요한 이벤트를 알려주는 풍부한 보고서를 제공합니다. 클라우드 또는 온-프레미스의 실제 및 가상 머신, Windows 또는 Linux에서 컴퓨터 구성을 모니터링하고 자동으로 업데이트할 수 있습니다.

설치된 응용 프로그램 및 다른 구성 항목에 대한 가시성을 위해 게스트 내 리소스에 대한 인벤토리를 가져올 수 있습니다. 풍부한 보고 및 검색 기능을 사용하여 운영 체제 내에 구성된 설정을 이해하는 데 도움이 되는 자세한 정보를 빠르게 찾을 수 있습니다. 서비스, 디먼, 소프트웨어, 레지스트리 및 파일의 변경 내용을 추적하여 문제의 원인을 빠르게 식별할 수 있습니다. 또한 DSC를 사용하면 환경에서 원하지 않는 변경이 발생한 경우를 진단하고 경고할 수 있습니다.

### <a name="update-management"></a>업데이트 관리

Azure Automation을 사용하여 하이브리드 환경에서 Windows 및 Linux 시스템을 업데이트합니다. Azure, 온-프레미스 및 기타 클라우드에서 업데이트 준수에 대한 가시성을 얻을 수 있습니다. 정의된 유지 관리 기간 내에서 업데이트 설치를 오케스트레이션하기 위한 배포 일정을 만들 수 있습니다. 업데이트를 컴퓨터에 설치하지 않는 경우 해당 업데이트를 배포에서 제외할 수 있습니다.

### <a name="shared-capabilities"></a>공유 기능

Azure Automation은 대규모 환경을 더 쉽게 자동화하고 구성할 수 있게 하는 일단의 공유 리소스로 구성됩니다.

* **[역할 기반 액세스 제어](automation-role-based-access-control.md)** - 제작 기능을 제공하지 않고도 작업을 실행할 수 있게 하는 Automation 운영자 역할을 사용하여 계정에 대한 액세스를 제어합니다.
* **[변수](automation-variables.md)** - Runbook 및 구성에서 사용할 수 있는 콘텐츠를 보관하는 방법을 제공합니다. Runbook과 이를 참조하는 구성을 수정하지 않고도 값을 변경할 수 있습니다.
* **[자격 증명](automation-credentials.md)** - 런타임에 Runbook 및 구성에서 사용할 수 있는 중요한 정보를 안전하게 저장합니다.
* **[인증서](automation-certificates.md)** - 런타임에 저장하고 사용할 수 있으므로 인증 및 배포된 리소스의 보호에 사용할 수 있습니다.
* **[연결](automation-connections.md)** - 연결 리소스의 시스템에 연결할 때 일반 정보가 포함된 정보의 이름/값 쌍을 저장합니다. 연결은 런타임에 사용하기 위해 모듈 작성자가 Runbook 및 구성에 정의합니다.
* **[일정](automation-schedules.md)** - 미리 정의된 시간에 자동화를 트리거하기 위해 서비스에서 사용됩니다.
* **[원본 제어와의 통합](automation-source-control-integration.md)** - 구성을 원본 제어 시스템에서 Runbook 또는 구성을 확인할 수 있는 코드로 승격합니다.
* **[PowerShell 모듈](automation-integration-modules.md)** - Azure 및 다른 시스템을 관리하는 데 사용됩니다. Microsoft, 타사, 커뮤니티 또는 정의된 사용자 지정 cmdlet 및 DSC 리소스를 Automation 계정으로 가져옵니다.

### <a name="windows-and-linux"></a>Windows 및 Linux

Azure Automation은 하이브리드 클라우드 환경과 Windows 및 Linux에서도 작동하도록 설계되었습니다. 배포된 작업 및 실행 중인 운영 체제를 일관되게 자동화하고 구성하는 방법을 제공합니다.

### <a name="community-gallery"></a>커뮤니티 갤러리

Runbook 및 모듈에 대한 [Automation 갤러리](automation-runbook-gallery.md)를 탐색하여 PowerShell 갤러리 및 Microsoft 스크립트 센터에서 프로세스를 빠르게 통합하고 제작할 수 있습니다.

## <a name="common-scenarios-for-automation"></a>일반적인 자동화 시나리오

Azure Automation은 인프라와 응용 프로그램의 수명 주기 전체를 관리합니다. 조직에서 작업을 제공하고 유지 관리하는 방법에 대한 지식을 시스템에 전송합니다. PowerShell, DSC(Desired State Configuration), Python 및 그래픽 Runbook과 같은 공용 언어로 작성합니다. 대상 지정, 보고 및 준수를 위해 배포되는 리소스에 대한 전체 인벤토리를 가져옵니다. 잘못된 구성이 발생하고 운영 준수를 향상시킬 수 있는 변경 내용을 식별합니다.

* **리소스 빌드/배포** - Runbook 및 Azure Resource Manager 템플릿을 사용하여 하이브리드 환경에서 VM을 배포합니다. Jenkins 및 Visual Studio Team Services와 같은 개발 도구에 통합합니다.
* **VM 구성** - 인프라 및 응용 프로그램에 대해 원하는 구성을 사용하여 Windows 및 Linux 컴퓨터를 평가하고 구성합니다.
* **모니터** - 문제의 원인이 되는 컴퓨터의 변경 내용을 식별하고 관리 시스템으로 수정하거나 에스컬레이션합니다.
* **보호** - 보안 경고가 발생하면 VM을 격리합니다. 게스트 내 요구 사항을 설정합니다.
* **제어** - 팀에 대한 역할 기반 액세스 제어를 설정합니다. 사용되지 않는 리소스를 복구합니다.

## <a name="pricing-for-automation"></a>Automation 가격

Azure Automation에 대한 가격은 [가격](https://azure.microsoft.com/pricing/details/automation/) 페이지에서 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Automation 계정 만들기](automation-quickstart-create-account.md)
