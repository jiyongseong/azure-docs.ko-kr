---
title: Azure Virtual Machine에서 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 등록하는 방법
description: Azure Automation에 포함된 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 사용하여 Azure Virtual Machine을 등록하는 방법을 알아봅니다.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 2fbfd733a57d0e2f91d119b614917abf172b8379
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193097"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Azure Virtual Machine에서 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션 등록

Azure Automation은 운영 체제 보안 업데이트를 관리하고, 변경 내용을 추적하며, 컴퓨터에 설치된 항목을 재고 자산으로 처리(인벤토리)하기 위한 솔루션을 제공합니다. 컴퓨터를 등록하는 여러 가지 방법이 있으며, 가상 머신, [Automation 계정](automation-onboard-solutions-from-automation-account.md) 또는 [Runbook](automation-onboard-solutions.md)에서 솔루션을 등록할 수 있습니다. 이 문서에서는 Azure Virtual Machine에서 이러한 솔루션을 등록하는 방법에 대해 설명합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure에 로그인

## <a name="enable-the-solutions"></a>솔루션을 사용하도록 설정

기존 가상 머신으로 이동하고, **작업** 아래에서 **업데이트 관리**, **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

Log Analytics 작업 영역 및 Automation 계정을 선택하고, **사용**을 클릭하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

![업데이트 솔루션 등록](media/automation-onboard-solutions-from-vm/onboard-solution.png)

다른 솔루션으로 이동하고 **사용**을 클릭하면, 이전에 사용하도록 설정된 솔루션과 동일한 작업 영역 및 Automation 계정을 사용하므로 Log Analytics 및 Automation 계정 드롭다운 상자가 비활성화됩니다.

![업데이트 솔루션 등록](media/automation-onboard-solutions-from-vm/onboard-solutions2.png)

> [!NOTE]
> **변경 내용 추적** 및 **인벤토리**는 동일한 솔루션을 사용하며, 하나가 활성화되면 다른 솔루션도 활성화됩니다.

## <a name="scope-configuration"></a>범위 구성

각 솔루션은 작업 영역 내의 범위 구성을 사용하여 솔루션을 가져오는 컴퓨터를 대상으로 합니다. 범위 구성은 솔루션 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색 그룹입니다. 범위 구성에 액세스하려면 Automation 계정의 **관련 리소스** 아래에서 **작업 영역**을 선택한 다음, **작업 영역 데이터 원본** 아래의 작업 영역에서 **범위 구성**을 선택합니다.

선택한 작업 영역에 업데이트 관리 또는 변경 내용 추적 솔루션이 아직 없는 경우 다음과 같은 범위 구성이 생성됩니다.

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

선택된 작업 영역에 이미 솔루션이 있는 경우입니다. 솔루션은 다시 배포되지 않고 범위 구성이 추가되지 않습니다.

이 구성 중 하나에서 줄임표(...)를 클릭하고 **편집**을 선택합니다. **범위 구성 편집** 페이지에서 **컴퓨터 그룹 선택**을 선택하여 **컴퓨터 그룹** 페이지를 엽니다. 이 페이지에는 범위 구성을 만드는 데 사용되는 저장된 검색이 표시됩니다.

## <a name="saved-searches"></a>저장된 검색

컴퓨터가 업데이트 관리 또는 변경 내용 추적 및 인벤토리 솔루션에 추가되면 작업 영역에서 두 가지 저장된 검색 중 하나에 추가됩니다. 이러한 저장된 검색은 이러한 솔루션을 대상으로 하는 컴퓨터가 포함된 쿼리입니다.

작업 영역으로 이동하고 **일반** 아래에서 **저장된 검색**을 선택합니다. 다음 표에는 이러한 솔루션에서 사용하는 두 가지 저장된 검색이 나와 있습니다.

|Name     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 업데이트        | Updates__MicrosoftDefaultComputerGroup         |

저장된 검색 중 하나를 선택하여 그룹을 채우는 데 사용된 쿼리를 살펴봅니다. 다음 이미지에서는 쿼리 및 해당 결과를 보여 줍니다.

![저장된 검색](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>다음 단계

솔루션을 사용하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

* [자습서 - VM 업데이트 관리](automation-tutorial-update-management.md)

* [자습서 - VM에 설치된 소프트웨어 식별](automation-tutorial-installed-software.md)

* [자습서 - VM에 대한 변경 내용 문제 해결](automation-tutorial-troubleshoot-changes.md)
