---
title: Azure Log Analytics에서 작업 영역 만들기 | Microsoft Docs
description: Log Analytics 작업 영역을 만들어서 클라우드 및 온-프레미스 환경에서 관리 솔루션 및 데이터 수집을 사용하는 방법에 대해 알아봅니다.
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: magoedte
ms.openlocfilehash: e05d620a457edf427c946366a9caca471ac8531e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="create-a-log-analytics-workspace-in-the-azure-portal"></a>Azure Portal에서 Log Analytics 작업 영역 만들기
Azure Portal에서 고유한 데이터 리포지토리, 데이터 원본 및 솔루션을 포함한 고유한 Log Analytics 환경인 Log Analytics 작업 영역을 설정할 수 있습니다.  이 문서에 설명된 단계는 다음 원본에서 데이터를 수집하려는 경우에 필요합니다.

* 구독의 Azure 리소스
* System Center Operations Manager에서 모니터링하는 온-프레미스 컴퓨터
* System Center Configuration Manager의 데이터 수집 
* Azure Storage에서 진단 또는 로그 데이터

Azure VM 및 사용자 환경의 Windows 또는 Linux 컴퓨터 등 다른 원본의 경우 다음 항목을 참조하세요.

*  [Azure Virtual Machines에 대한 데이터 수집](log-analytics-quick-collect-azurevm.md) 
*  [Linux 컴퓨터에 대한 데이터 수집](log-analytics-quick-collect-linux-computer.md)
*  [Windows 컴퓨터에 대한 데이터 수집](log-analytics-quick-collect-windows-computer.md)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure-portal"></a>Azure Portal에 로그인
Azure Portal([https://portal.azure.com](https://portal.azure.com))에 로그인합니다. 

## <a name="create-a-workspace"></a>작업 영역 만들기
1. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. **만들기**를 클릭하고 다음 항목에 대한 옵션을 선택합니다.

  * 새 **OMS 작업 영역**의 이름(예: *DefaultLAWorkspace*)을 지정합니다. 
  * 기본으로 선택된 값이 적절하지 않으면 드롭다운 목록에서 선택하여 연결할 **구독**을 선택합니다.
  * **리소스 그룹**의 경우 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 설정하고 만듭니다.  
  * 사용 가능한 **위치**를 선택합니다.  자세한 내용은 [Log Analytics를 사용할 수 있는 지역](https://azure.microsoft.com/regions/services/)을 참조하세요.
  * 2018년 4월 2일 이후에 만들어진 새 구독에서 작업 영역을 만드는 경우 *GB당* 가격 책정 계획이 자동으로 사용되며 가격 책정 계층을 선택할 수 있는 옵션이 제공되지 않습니다.  4월 2일 전에 만들어진 기존 구독 또는 기존 EA 등록과 연결된 구독에서 작업 영역을 만드는 경우 선호하는 가격 책정 계층을 선택합니다.  특정 계층에 대한 자세한 내용은 [Log Analytics 가격 책정 정보](https://azure.microsoft.com/pricing/details/log-analytics/)를 참조하세요.

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png)<br>  

3. **OMS 작업 영역** 창에서 필요한 정보를 제공한 후에 **확인**을 클릭합니다.  

정보가 확인되고 작업 영역이 만들어지는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이제 사용 가능한 작업 영역이 있으므로 모니터링 원격 분석의 컬렉션을 구성하고, 해당 데이터를 분석하는 로그 검색을 실행하고, 추가 데이터 및 분석 정보를 제공하는 관리 솔루션을 추가할 수 있습니다. 

* Azure 진단 또는 Azure Storage를 사용하여 Azure 리소스의 데이터 수집을 사용하려면 [Log Analytics에서 사용할 Azure 서비스 로그 및 메트릭 수집](log-analytics-azure-storage.md)을 참조하세요.  
* [System Center Operations Manager를 데이터 원본으로 추가](log-analytics-om-agents.md)하여 Operations Manager 관리 그룹을 보고하는 에이전트에서 데이터를 수집하고 Log Analytics 작업 영역 리포지토리에 저장합니다. 
* [Configuration Manager](log-analytics-sccm.md)를 연결하여 계층에서 컬렉션의 멤버인 컴퓨터를 가져옵니다.  
* [관리 솔루션](/log-analytics-add-solutions.md)을 사용할 수 있는지 및 작업 영역에서 솔루션을 추가하거나 제거하는 방법을 검토합니다.

