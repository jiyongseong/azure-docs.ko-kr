---
title: Azure Log Analytics 작업 영역 삭제 | Microsoft Docs
description: 개인 구독에서 작업 영역을 만들었거나 작업 영역 모델을 재구성한 경우 Log Analytics 작업 영역을 삭제하는 방법을 알아봅니다.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 084b8fa0addbe2ddeee699b58c9b0de1a6f27d99
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Azure Portal에서 Azure Log Analytics 작업 영역 삭제
이 문서에서는 Azure Portal을 사용하여 더 이상 필요하지 않을 수 있는 Log Analytics 작업 영역을 삭제하는 방법을 보여 줍니다. 

## <a name="to-delete-a-workspace"></a>작업 영역을 삭제하려면 
Log Analytics 작업 영역을 삭제하면 30일 내에 작업 영역과 관련된 모든 데이터가 서비스에서 삭제됩니다.  서비스 작업에 부정적인 영향을 미칠 수 있는 중요한 데이터 및 구성이 있을 수 있으므로 작업 영역을 삭제할 때는 주의해야 합니다. 다음과 같이 Log Analytics에 데이터를 저장하는 다른 Azure 서비스 및 소스를 고려합니다.

* Application Insights
* Azure Security Center
* Azure Automation
* Windows 및 Linux 가상 머신에서 실행되는 에이전트
* 사용자 환경의 Windows 및 Linux 컴퓨터에서 실행되는 에이전트
* System Center Operations Manager
* 관리 솔루션 

작업 영역에 보고하도록 구성된 모든 에이전트 및 System Center Operations Manager 관리 그룹은 분리된 상태로 유지됩니다.  진행하기 전에 작업 공간과 통합되어 있는 에이전트, 솔루션 및 기타 Azure 서비스의 인벤토리를 작성하십시오.   
 
관리자이고 여러 사용자가 작업 영역과 연결되어 있는 경우 해당 사용자와 작업 영역 간의 연결이 끊어집니다. 사용자가 다른 작업 영역과 연결되어 있으면 다른 작업 영역에서 Log Analytics를 계속 사용할 수 있습니다. 그러나 다른 작업 영역과 연결되어 있지 않으면 Log Analytics를 사용하기 위해 작업 영역을 만들어야 합니다. 

1. [Azure 포털](http://portal.azure.com)에 로그인합니다. 
2. Azure Portal의 왼쪽 아래 모서리에 있는 **추가 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
3. Log Analytics 구독 창에서 작업 영역을 선택한 다음 가운데 창 위쪽에서 **삭제**를 클릭합니다.<br><br> ![작업 영역 속성 창에서 옵션 삭제](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. 작업 영역 삭제를 확인할지 묻는 확인 메시지 창이 나타나면 **예**를 클릭합니다.<br><br> ![작업 영역 삭제 확인](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

