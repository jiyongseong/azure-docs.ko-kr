---
title: Azure 빠른 시작 - Azure Automation 계정 만들기 | Microsoft Docs
description: Azure Automation 계정을 만들고 Runbook을 실행하는 방법을 알아봅니다.
services: automation
author: csand-msft
ms.author: csand
ms.date: 12/13/2017
ms.topic: quickstart
ms.service: automation
ms.component: process-automation
ms.custom: mvc
ms.openlocfilehash: 3accd62415cd7d3a8210b7a05aa1edfd7a380edc
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기

Azure Automation 계정은 Azure를 통해 만들 수 있습니다. 이 방법은 Automation 계정 및 관련 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다. 이 빠른 시작에서는 Automation 계정을 만들고 계정에서 Runbook을 실행하는 방법을 안내합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure에 로그인

## <a name="create-automation-account"></a>Automation 계정 만들기

1. Azure의 왼쪽 위 모서리에서 **리소스 만들기** 단추를 클릭합니다.

1. **모니터링 + 관리**를 선택한 다음 **Automation**을 선택합니다.

1. 계정 정보를 입력합니다. Azure에 대한 인증을 간소화하는 아티팩트를 자동으로 사용하도록 설정하기 위해 **Azure 실행 계정 만들기**에서 **예**를 선택합니다. 완료되면 **만들기**를 클릭하여 Automation 계정 배포를 시작합니다.

    ![페이지에서 Automation 계정에 대한 정보 입력](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

1. Automation 계정은 Azure 대시보드에 고정되어 있습니다. 배포가 완료되면 Automation 계정 개요가 자동으로 열립니다.

    ![Automation 계정 개요](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook 실행

자습서 Runbook 중 하나를 실행합니다.

1. **프로세스 자동화** 아래에서 **Runbook**을 클릭합니다. Runbook 목록이 표시됩니다. 기본적으로 여러 자습서 Runbook을 계정에서 사용할 수 있습니다.

    ![Automation 계정 Runbook 목록](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. **AzureAutomationTutorialScript** Runbook을 선택합니다. 이 작업은 Runbook 개요 페이지를 엽니다.

    ![Runbook 개요](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. **시작**을 클릭하고 **Runbook 시작** 페이지에서 **확인**을 클릭하여 Runbook을 시작합니다.

    ![Runbook 작업 페이지](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. **작업 상태**가 **실행 중**이 되면 **출력** 또는 **모든 로그**를 클릭하여 Runbook 작업 출력을 봅니다. 이 자습서 Runbook의 경우 출력은 Azure 리소스 목록입니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, Automation 계정 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 Automation 계정에 대한 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Automation 계정을 배포하고, Runbook 작업을 시작하고, 작업 결과를 확인했습니다. Azure Automation에 대한 자세한 내용을 알아보려면 첫 번째 Runbook을 만드는 빠른 시작으로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Automation 빠른 시작 - Runbook 만들기](./automation-quickstart-create-runbook.md)
