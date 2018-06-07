---
title: Azure Automation을 사용하여 컴퓨터에 설치된 소프트웨어 검색 | Microsoft Docs
description: 인벤토리를 사용하여 환경 전체에서 컴퓨터에 설치된 소프트웨어를 검색합니다.
services: automation
keywords: 인벤토리, 자동화, 변경, 추적
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.component: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: cb876a8d8019f5a2a7232c3093c6f64a7b2730e1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Azure 및 비Azure 컴퓨터에 설치된 소프트웨어 검색

이 자습서에서는 사용자 환경에 설치된 소프트웨어를 검색하는 방법에 대해 알아봅니다. 컴퓨터의 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대한 인벤토리를 수집하고 볼 수 있습니다. 컴퓨터 구성을 추적하면 환경 전체에서 작동 문제를 정확히 식별하고 컴퓨터의 상태를 더 잘 파악할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 솔루션 활성화
> * Azure VM 등록
> * 비 Azure VM 등록
> * 설치된 소프트웨어 보기
> * 설치된 소프트웨어에 대한 인벤토리 로그 검색

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* Azure 구독. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자, 작업 Runbook 및 Watcher 태스크를 보관할 [Automation 계정](automation-offering-get-started.md)
* 등록할 [가상 머신](../virtual-machines/windows/quick-create-portal.md)

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure Portal ( http://portal.azure.com ) 에 로그인합니다.

## <a name="enable-change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리 사용

먼저 이 자습서에 대한 변경 내용 추적 및 인벤토리를 사용하도록 설정해야 합니다. 이전에 **변경 내용 추적** 솔루션을 사용하도록 설정한 경우에는 이 단계가 필요하지 않습니다.

Automation 계정으로 이동하고, **구성 관리** 아래에서 **인벤토리**를 선택합니다.

Log Analytics 작업 영역 및 Automation 계정을 선택하고 **사용**을 클릭하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

![인벤토리 등록 구성 배너](./media/automation-tutorial-installed-software/enableinventory.png)

솔루션을 사용하도록 설정하려면, 사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용**을 클릭합니다. 필드가 회색으로 표시되면 해당 VM에 대해 다른 자동화 솔루션을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 함을 의미합니다.

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) 작업 영역은 기능 및 서비스(예: 인벤토리)에서 생성된 데이터를 수집하는 데 사용됩니다.
이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.

솔루션을 사용하도록 설정하는 데 최대 15분이 걸릴 수 있습니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다.
솔루션을 사용하도록 설정되면 설치된 소프트웨어에 대한 정보 및 VM에 대한 변경 내용이 Log Analytics로 이동합니다.
이 데이터를 분석에 사용할 수 있게 되는 데 30분에서 6시간까지 걸릴 수 있습니다.

## <a name="onboard-a-vm"></a>VM 등록

Automation 계정에서 **구성 관리** 아래의 **인벤토리**로 이동합니다.

**+ Azure VM 추가**를 선택하면 **가상 컴퓨터** 페이지가 열리고 목록에서 기존 VM을 선택할 수 있습니다. 등록할 VM을 선택합니다. 열리는 페이지에서 **사용**을 클릭하여 VM에서 솔루션을 사용하도록 설정합니다. Microsoft 관리 에이전트가 VM에 배포되고 솔루션을 활성화할 때 구성한 Log Analytics 작업 영역에 설명하도록 에이전트를 구성합니다. 등록을 완료하는 데 몇 분이 걸릴 수 있습니다. 이 시점에서 목록에서 새 VM을 선택하고 다른 VM을 등록할 수 있습니다.

## <a name="onboard-a-non-azure-machine"></a>비Azure 컴퓨터 등록

비 Azure 컴퓨터를 추가하려면 운영 체제에 따라 [Windows](../log-analytics/log-analytics-agent-windows.md) 또는 [Linux](automation-linux-hrw-install.md)에 대한 에이전트를 설치합니다. 에이전트가 설치되면 Automation 계정으로 이동하고 **구성 관리** 아래에서 **인벤토리**로 이동합니다. **관리 컴퓨터**를 클릭하면 솔루션이 활성화되지 않은 Log Analytics 작업 영역에 보고하는 컴퓨터의 목록이 표시됩니다. 사용자 환경에 적합한 옵션을 선택합니다.

* **사용 가능한 모든 컴퓨터에서 사용하도록 설정** - 이 옵션은 이 시점에 Log Analytics 작업 영역에 보고하는 모든 컴퓨터에서 솔루션을 활성화합니다.
* **사용 가능한 모든 컴퓨터 및 이후 컴퓨터에서 사용하도록 설정** - 이 옵션은 Log Analytics 작업 영역에 보고하는 모든 컴퓨터와 이후에 작업 영역에 추가되는 향후 모든 컴퓨터에서 솔루션을 활성화합니다.
* **선택한 컴퓨터에서 사용하도록 설정** - 이 옵션은 선택한 컴퓨터에서만 솔루션을 활성화합니다.

![컴퓨터 관리](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>설치된 소프트웨어 보기

변경 내용 추적 및 인벤토리 솔루션을 사용하도록 설정되면 **인벤토리** 페이지에서 결과를 볼 수 있습니다.

Automation 계정 내에서 **구성 관리** 아래의 **인벤토리**를 선택합니다.

**인벤토리** 페이지에서 **소프트웨어** 탭을 클릭합니다.

**소프트웨어** 탭에는 검색된 소프트웨어를 나열하는 테이블이 있습니다. 소프트웨어는 소프트웨어 이름 및 버전별로 그룹화됩니다.

각 소프트웨어 레코드에 대한 높은 수준의 자세한 정보는 이 테이블에서 볼 수 있습니다. 이러한 세부 정보에는 소프트웨어 이름, 버전, 게시자, 마지막으로 새로 고친 시간(그룹의 컴퓨터에서 보고한 최근의 새로 고침 시간) 및 컴퓨터(해당 소프트웨어가 있는 컴퓨터의 수)가 포함됩니다.

![소프트웨어 인벤토리](./media/automation-tutorial-installed-software/inventory-software.png)

소프트웨어 레코드의 속성 및 해당 소프트웨어가 있는 컴퓨터의 이름을 보려면 행을 클릭합니다.

특정 소프트웨어 또는 소프트웨어 그룹을 찾으려면 소프트웨어 목록 바로 위에 있는 텍스트 상자에서 검색할 수 있습니다.
필터를 사용하면 소프트웨어 이름, 버전 또는 게시자를 기반으로 하여 검색할 수 있습니다.

예를 들어 "Contoso"를 검색하면 "Contoso"가 포함된 이름, 게시자 또는 버전이 있는 모든 소프트웨어가 반환됩니다.

## <a name="search-inventory-logs-for-installed-software"></a>설치된 소프트웨어에 대한 인벤토리 로그 검색

인벤토리는 Log Analytics로 보내는 로그 데이터를 생성합니다. 쿼리를 실행하여 로그를 검색하려면 **인벤토리** 창 위쪽에서 **Log Analytics**를 선택합니다.

인벤토리 데이터는 **ConfigurationData** 형식 아래에 저장됩니다.
다음 샘플 Log Analytics 쿼리는 게시자가 "Microsoft Corporation"인 인벤토리 결과를 반환합니다.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Log Analytics에서 로그 파일을 실행하고 검색하는 방법에 대한 자세한 내용은 [Azure Log Analytics](https://docs.loganalytics.io/index)를 참조하세요.

### <a name="single-machine-inventory"></a>단일 컴퓨터 인벤토리

단일 컴퓨터에 대한 소프트웨어 인벤토리를 보려면 Azure VM 리소스 페이지에서 인벤토리에 액세스하거나 Log Analytics를 사용하여 해당 컴퓨터로 필터링할 수 있습니다.
다음 Log Analytics 쿼리 예제에서는 ContosoVM이라는 컴퓨터의 소프트웨어 목록을 반환합니다.

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 소프트웨어 인벤토리를 보는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 솔루션 활성화
> * Azure VM 등록
> * 비 Azure VM 등록
> * 설치된 소프트웨어 보기
> * 설치된 소프트웨어에 대한 인벤토리 로그 검색

자세히 알아보려면 변경 내용 추적 및 인벤토리 솔루션에 대한 개요로 계속 진행하세요.

> [!div class="nextstepaction"]
> [변경 관리 및 인벤토리 솔루션](automation-change-tracking.md)