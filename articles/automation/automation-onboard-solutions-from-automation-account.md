---
title: Azure Automation에서 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 등록하는 방법
description: Azure Automation에 포함된 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 사용하여 Azure Virtual Machine을 등록하는 방법을 알아봅니다.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 2f5d664b660d43e61dba46d13aff1ced796de884
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193355"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션 등록

Azure Automation은 운영 체제 보안 업데이트를 관리하고, 변경 내용을 추적하며, 컴퓨터에 설치된 항목을 재고 자산으로 처리(인벤토리)하기 위한 솔루션을 제공합니다. 컴퓨터를 등록하는 여러 가지 방법이 있으며, 가상 머신, [Automation 계정](automation-onboard-solutions-from-vm.md) 또는 [Runbook](automation-onboard-solutions.md)에서 솔루션을 등록할 수 있습니다. 이 문서에서는 Automation 계정에서 이러한 솔루션을 등록하는 방법에 대해 설명합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure에 로그인

## <a name="enable-solutions"></a>솔루션 사용

Automation 계정으로 이동하고, **구성 관리** 아래에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

Log Analytics 작업 영역 및 Automation 계정을 선택하고, **사용**을 클릭하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

![인벤토리 솔루션 등록](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

변경 내용 추적 및 인벤토리 솔루션은 가상 머신에서 [변경 내용 추적](automation-vm-change-tracking.md) 및 [인벤토리](automation-vm-inventory.md)의 기능을 제공합니다. 이 단계에서는 가상 머신에 솔루션을 사용할 수 있습니다.

변경 내용 추적 및 인벤토리 솔루션 등록 알림이 완료되면 **구성 관리** 아래에서 **업데이트 관리**를 클릭합니다.

업데이트 관리 솔루션을 사용하면 Azure Windows VM에 대한 업데이트 및 패치를 관리할 수 있습니다. 사용 가능한 업데이트의 상태를 평가하고, 필수 업데이트의 설치를 예약하고, 배포 결과를 검토하여 업데이트가 VM에 성공적으로 적용되었는지 확인할 수 있습니다. 이 작업을 통해 VM에 대한 솔루션을 사용하도록 설정할 수 있었습니다.

**업데이트 관리** 아래에서 **업데이트 관리**를 선택합니다. 선택한 Log Analytics 작업 영역은 이전 단계에서 사용된 동일한 작업 영역입니다. **사용**을 클릭하여 업데이트 관리 솔루션을 등록합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

![업데이트 솔루션 등록](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>범위 구성

각 솔루션은 작업 영역 내의 범위 구성을 사용하여 솔루션을 가져오는 컴퓨터를 대상으로 합니다. 범위 구성은 솔루션 범위를 특정 컴퓨터로 제한하는 데 사용되는 하나 이상의 저장된 검색 그룹입니다. 범위 구성에 액세스하려면 Automation 계정의 **관련 리소스** 아래에서 **작업 영역**을 선택합니다. 그런 다음, 작업 영역의 **작업 영역 데이터 원본** 아래에서 **범위 구성**을 선택합니다.

선택한 작업 영역에 업데이트 관리 또는 변경 내용 추적 솔루션이 아직 없는 경우 다음과 같은 범위 구성이 생성됩니다.

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

선택된 작업 영역에 이미 솔루션이 있는 경우입니다. 솔루션은 다시 배포되지 않고 범위 구성이 추가되지 않습니다.

## <a name="saved-searches"></a>저장된 검색

컴퓨터가 업데이트 관리 또는 변경 내용 추적 및 인벤토리 솔루션에 추가되면 작업 영역에서 두 가지 저장된 검색 중 하나에 추가됩니다. 이러한 저장된 검색은 이러한 솔루션을 대상으로 하는 컴퓨터가 포함된 쿼리입니다.

Automation 계정 이동하고 **일반** 아래에서 **저장된 검색**을 선택합니다. 다음 표에는 이러한 솔루션에서 사용하는 두 가지 저장된 검색이 나와 있습니다.

|Name     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | 업데이트        | Updates__MicrosoftDefaultComputerGroup         |

저장된 검색 중 하나를 선택하여 그룹을 채우는 데 사용된 쿼리를 살펴봅니다. 다음 이미지에서는 쿼리 및 해당 결과를 보여 줍니다.

![저장된 검색](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>Azure 컴퓨터 등록

Automation 계정에서 **업데이트 관리**의 **구성 관리** 또는 **업데이트 관리** 아래에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

**+ Azure VM 추가**를 클릭하고 목록에서 VM을 선택합니다. **업데이트 관리** 페이지에서 **사용**을 클릭합니다. 그러면 솔루션에 대한 컴퓨터 그룹 저장된 검색에 현재 VM이 추가됩니다.

## <a name="onboard-a-non-azure-machine"></a>비Azure 컴퓨터 등록

Automation 계정에서 **업데이트 관리**의 **구성 관리** 또는 **업데이트 관리** 아래에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

**비Azure 컴퓨터 추가**를 클릭합니다. 그러면 컴퓨터에서 솔루션에 대한 보고를 시작할 수 있도록 컴퓨터에 Microsoft Monitoring Agent를 설치 및 구성하는 방법에 대한 지침이 포함된 새 브라우저 창이 열립니다. System Center Operations Manager에서 현재 관리하고 있는 컴퓨터를 등록하는 경우, 새 에이전트가 필요하지 않으며 작업 영역 정보가 기존 에이전트에 입력됩니다.

## <a name="onboard-machines-in-the-workspace"></a>작업 영역에서 컴퓨터 등록

Automation 계정에서 **업데이트 관리**의 **구성 관리** 또는 **업데이트 관리** 아래에서 **인벤토리** 또는 **변경 내용 추적**을 선택합니다.

**컴퓨터 관리**를 선택합니다. 이렇게 하면 **컴퓨터 관리** 페이지가 열립니다. 이 페이지에서는 선택한 컴퓨터 집합 또는 사용 가능한 모든 컴퓨터에서 솔루션을 사용하도록 설정하거나, 현재의 모든 컴퓨터 및 이후의 모든 컴퓨터에서 솔루션을 사용하도록 설정할 수 있습니다.

![저장된 검색](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>선택한 컴퓨터

하나 이상의 컴퓨터에서 솔루션을 사용하도록 설정하려면, **선택한 컴퓨터에서 사용**을 선택하고 솔루션에 추가하려는 각 컴퓨터 옆에 있는 **추가**를 클릭합니다. 이 작업은 선택한 컴퓨터의 이름을 솔루션에 대한 컴퓨터 그룹 저장된 검색 쿼리에 추가합니다.

### <a name="all-available-machines"></a>사용 가능한 모든 컴퓨터

사용 가능한 모든 컴퓨터에서 솔루션을 사용하도록 설정하려면, **사용 가능한 모든 컴퓨터에서 사용**을 선택합니다. 이렇게 하면 컴퓨터를 개별적으로 추가할 수 있는 컨트롤이 비활성화됩니다. 이 작업은 작업 영역에 보고하는 컴퓨터의 모든 이름을 컴퓨터 그룹 저장된 검색 쿼리에 추가합니다.

### <a name="all-available-and-future-machines"></a>사용 가능한 모든 향후 컴퓨터

사용 가능한 모든 컴퓨터 및 모든 향후 컴퓨터에서 솔루션을 사용하도록 설정하려면, **사용 가능한 모든 향후 컴퓨터에서 사용**을 선택합니다. 이 옵션은 작업 영역에서 저장된 검색 및 범위 구성을 삭제합니다. 그러면 작업 영역에 보고하는 모든 Azure 및 비Azure 컴퓨터에 대한 솔루션이 열립니다.

## <a name="next-steps"></a>다음 단계

솔루션을 사용하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

* [자습서 - VM 업데이트 관리](automation-tutorial-update-management.md)

* [자습서 - VM에 설치된 소프트웨어 식별](automation-tutorial-installed-software.md)

* [자습서 - VM에 대한 변경 내용 문제 해결](automation-tutorial-troubleshoot-changes.md)