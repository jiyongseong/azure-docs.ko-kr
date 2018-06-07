---
title: Azure Automation DSC 개요
description: Azure Automation DSC(필요한 상태 구성)의 개요, 용어 및 알려진 문제
keywords: PowerShell DSC, 필요한 상태 구성, PowerShell DSC Azure
services: automation
ms.service: automation
ms.component: dsc
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 83d5b89422a0181c06dbfe3b2bd8975ef7214b9d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="azure-automation-dsc-overview"></a>Azure Automation DSC 개요

Azure Automation DSC는 Azure 서비스로, 모든 클라우드에서 PowerShell DSC(Desired State Configuration) [구성](https://msdn.microsoft.com/powershell/dsc/configurations)을 작성, 관리 및 컴파일하고 [DSC 리소스](https://msdn.microsoft.com/powershell/dsc/resources)를 가져오고 대상 노드에 구성을 할당할 수 있습니다.

## <a name="why-use-azure-automation-dsc"></a>Azure Automation DSC를 사용하는 이유

Azure Automation DSC는 Azure 외부에서 DSC를 사용하는 것보다 몇 가지 장점을 제공합니다.

### <a name="built-in-pull-server"></a>기본 제공 끌어오기 서버

Azure Automation에서는 대상 노드가 구성을 자동으로 수신하고 원하는 상태를 따르며 규정 준수를 다시 보고하도록 [DSC 끌어오기 서버](https://msdn.microsoft.com/powershell/dsc/pullserver)를 제공합니다.
Azure Automation에 있는 기본 제공 끌어오기 서버는 자체 끌어오기 서버를 설정하고 유지 관리할 필요가 없습니다.
Azure Automation은 클라우드 또는 온-프레미스에서 가상 또는 실제 Windows 또는 Linux 컴퓨터를 대상으로 할 수 있습니다.

### <a name="management-of-all-your-dsc-artifacts"></a>모든 DSC 아티팩트 관리

Azure Automation DSC는 Azure Automation에서 PowerShell 스크립팅을 위해 제공하는 것과 동일한 관리 계층을 [PowerShell 필요한 상태 구성](https://msdn.microsoft.com/powershell/dsc/overview)에 제공합니다.

Azure Portal 또는 PowerShell에서 DSC 구성, 리소스 및 대상 노드를 모두 관리할 수 있습니다.

![Azure Automation 블레이드의 스크린샷](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Log Analytics로 보고 데이터 가져오기

Azure Automation DSC로 관리되는 노드는 상세한 보고 상태 데이터를 기본 제공 끌어오기 서버에 보냅니다.
이 데이터를 Log Analytics 작업 영역으로 보내도록 Azure Automation DSC를 구성할 수 있습니다.
DSC 상태 데이터를 Log Analytics 작업 영역으로 보내는 방법을 알아보려면 [Azure Automation DSC 보고 데이터를 Log Analytics로 전달](automation-dsc-diagnostics.md)을 참조하세요.

## <a name="introduction-video"></a>소개 비디오

읽기보다 비디오를 시청하기가 편하신가요? 2015년 5월 Azure Automation DSC가 처음 발표될 당시의 비디오를 다음에서 확인하세요.

>[!NOTE]
>이 비디오에 언급된 개념 및 수명은 정확하지만 이 비디오가 기록된 이후에 Azure Automation DSC는 많이 발전되었습니다.
>일반적으로 사용할 수 있고 Azure 포털의 UI는 보다 강력해졌으며 더 많은 기능을 제공하고 있습니다.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>다음 단계

* Azure Automation DSC로 관리되는 노드를 온보딩하는 방법을 알아보려면 [Azure Automation DSC에 의한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)를 참조하세요.
* Azure Automation DSC 사용을 시작하려면 [Azure Automation DSC 시작하기](automation-dsc-getting-started.md)를 참조하세요.
* DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation DSC에서 구성을 컴파일](automation-dsc-compile.md)을 참조하세요.
* Azure Automation DSC에 대한 PowerShell cmdlet 참조는 [Azure Automation DSC cmdlet ](/powershell/module/azurerm.automation/#automation)을 참조하세요.
* 가격 책정 정보는 [Azure Automation DSC 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
* 연속 배포 파이프라인에서 Azure Automation DSC를 사용하는 예제는 [Azure Automation DSC 및 Chocolatey를 사용하여 IaaS VM에 연속 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
