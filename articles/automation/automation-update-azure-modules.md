---
title: Azure Automation에서 Azure 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반 Azure PowerShell 모듈을 즉시 업데이트하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fe263346d8794b3dc85b6420d8b9b02efa5f9684
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법

기본적으로 각 Automation 계정에 가장 일반적인 Azure PowerShell 모듈이 제공됩니다. Azure 팀이 Azure 모듈을 정기적으로 업데이트하므로 포털에서 새 버전을 사용할 수 있으면 Automation 계정에서 모듈을 업데이트하는 방법이 제공됩니다.  

제품 그룹에 의해 모듈이 정기적으로 업데이트되므로 매개 변수의 이름이 바뀌거나 cmdlet을 완전히 사용 중단하는 등 변경 유형에 따라 Runbook에 부정적인 영향을 미칠 수 있는 변경 사항이 포함된 cmdlet에 발생할 수 있습니다. 자동화하는 프로세스 및 Runbook에 영향을 주지 않으려면 계속하기 전에 테스트 및 유효성 검사를 수행하는 것이 좋습니다. 이 용도의 전용 Automation 계정이 없는 경우 하나 만들어 PowerShell 모듈 업데이트와 같은 반복적인 변경 외에도 Runbook을 개발하는 동안 다양한 시나리오와 순열을 테스트하는 데 사용하는 것이 좋습니다 결과 유효성을 검사하고 필요한 변경 사항을 적용한 후에는 수정이 필요한 모든 Runbook의 마이그레이션 조정을 계속하고 프로덕션 환경에서 설명된 대로 다음과 같은 업데이트를 수행합니다.

## <a name="updating-azure-modules"></a>Azure 모듈 업데이트

1. Automation 계정의 모듈 계정 페이지에는 **Azure 모듈 업데이트**라는 옵션이 있습니다. 이 옵션은 항상 사용됩니다.<br><br> ![모듈 페이지의 Azure 모듈 업데이트 옵션](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. **Azure 모듈 업데이트**를 클릭하면 계속 진행할 것인지 묻는 확인 알림이 나타납니다.<br><br> ![Azure 모듈 업데이트 알림](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. **예**를 클릭하면 모듈 업데이트 프로세스가 시작됩니다. 업데이트 프로세스에서 다음 모듈을 업데이트하는 데 15-20분 정도 걸립니다.

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    모듈이 이미 최신 상태이면 프로세스가 몇 초 이내에 완료됩니다. 업데이트 프로세스가 완료되면 알림이 표시됩니다.<br><br> ![Azure 모듈 업데이트 상태 업데이트](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation은 예약된 새 작업이 실행될 때 Automation 계정의 최신 모듈을 사용합니다.    

Runbook에서 이러한 Azure PowerShell 모듈의 cmdlet을 사용하는 경우 최신 모듈을 사용하도록 매달 이 업데이트 프로세스를 수행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* 통합 모듈에 대해 알아보고 Automation을 다른 시스템, 서비스 또는 솔루션과 통합하는 사용자 지정 모듈을 만드는 방법을 알아보려면 [통합 모듈](automation-integration-modules.md)을 참조하세요.

* [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) 또는 [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md)를 사용하여 Automation Runbook 및 구성 포트폴리오의 릴리스를 중앙에서 관리하고 제어하는 원본 제어 통합을 고려합니다.  