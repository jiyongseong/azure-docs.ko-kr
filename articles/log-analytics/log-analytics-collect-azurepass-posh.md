---
title: Log Analytics로 Azure PaaS 리소스 메트릭 수집 | Microsoft Docs
description: Log Analytics에서 보존 및 분석을 위해 PowerShell을 사용하여 Azure PaaS 리소스 메트릭 컬렉션을 사용하도록 설정하는 방법을 알아봅니다.
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
ms.date: 11/13/2017
ms.author: magoedte
ms.openlocfilehash: 8a2c04c2f79f310b7e70e7add7a8d5f318f056d2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="configure-collection-of-azure-paas-resource-metrics-with-log-analytics"></a>Log Analytics로 Azure PaaS 리소스 메트릭의 수집 구성

Azure SQL 및 웹 사이트(Web Apps)처럼, Azure PaaS(Platform as a Service) 리소스는 Log Analytics에 성능 메트릭 데이터를 내보낼 수 있습니다. 이 스크립트를 통해 특정 리소스 그룹이나 구독 전반에 이미 배포된 PaaS 리소스에 대해 메트릭 로깅을 사용할 수 있습니다. 

현재는 Azure Portal을 통해 PaaS 리소스에 대한 메트릭 로깅을 사용하도록 설정할 방법이 없습니다. 따라서 PowerShell 스크립트를 사용해야 합니다. 원시 메트릭 로깅 기능을 Log Analytics 모니터링과 함께 사용하면 Azure 리소스를 대규모로 모니터링할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건
계속하기 전에 다음 Azure Resource Manager 모듈이 컴퓨터에 설치되어 있는지 확인합니다.

- AzureRM.Insights
- AzureRM.OperationalInsights
- AzureRM.Resources
- AzureRM.profile

>[!NOTE]
>PowerShell에서 Azure Resource Manager 명령을 실행하는 경우 호환성을 보장하기 위해 Azure Resource Manager 모듈이 모두 동일한 버전인 것이 좋습니다.
>
최신 버전의 Azure Resource Manager 모듈을 컴퓨터에 설치하려면 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.1#update-azps)을 참조하세요.  

## <a name="enable-azure-diagnostics"></a>Azure 진단 사용  
PaaS 리소스에 대한 Azure 진단 구성은 **Enable-AzureRMDiagnostics.ps1** 스크립트를 실행하여 수행됩니다. 이 스크립트는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Enable-AzureRMDiagnostics/2.52/DisplayScript)에서 사용할 수 있습니다.  이 스크립트는 다음 시나리오를 지원합니다.
  
* 구독에서 하나 이상의 리소스 그룹과 관련된 리소스 지정  
* 구독에서 특정 리소스 그룹과 관련된 리소스 지정  
* 다른 작업 영역으로 전달하도록 리소스 재구성

Azure 진단으로 메트릭을 수집하고 Log Analytics에 직접 보내도록 지원하는 리소스만 지원됩니다.  자세한 목록은 [Log Analytics에서 사용할 Azure 서비스 로그 및 메트릭 수집](log-analytics-azure-storage.md)을 검토하세요. 

다음 단계에 따라 스크립트를 다운로드하고 실행합니다.

1.  Windows 시작 화면에서 **PowerShell**을 입력하고 검색 결과에서 PowerShell을 마우스 오른쪽 단추로 클릭합니다.  **관리자로 실행** 메뉴에서 선택합니다.   
2. 다음 명령을 실행하고 스크립트를 저장할 경로를 제공하여 **Enable-AzureRMDiagnostics.ps1** 스크립트 파일을 로컬에 저장합니다.    

    ```
    PS C:\> save-script -Name Enable-AzureRMDiagnostics -Path "C:\users\<username>\desktop\temp"
    ```

3. `Connect-AzureRmAccount`를 실행하여 Azure와 연결합니다.   
4. 매개 변수 없이 `.\Enable-AzureRmDiagnostics.ps1` 스크립트를 실행하여 구독의 특정 리소스에서 데이터 수집을 사용하도록 설정하거나 `-ResourceGroup <myResourceGroup>` 매개 변수를 사용하여 특정 리소스 그룹의 리소스를 지정합니다.   
5. 구독이 둘 이상 있는 경우 올바른 값을 입력하여 목록에서 적절한 구독을 선택합니다.<br><br> ![스크립트에서 반환된 구독 선택](./media/log-analytics-collect-azurepass-posh/script-select-subscription.png)<br> 그렇지 않으면 사용 가능한 단일 구독이 자동으로 선택됩니다.
6. 그런 다음 스크립트는 구독에 등록된 Log Analytics 작업 영역 목록을 반환합니다.  목록에서 적절한 항목을 선택합니다.<br><br> ![스크립트에서 반환된 작업 영역 선택](./media/log-analytics-collect-azurepass-posh/script-select-workspace.png)<br> 
7. 컬렉션을 활성화할 Azure 리소스를 선택합니다. 예를 들어, 5를 입력하면 SQL Azure Databases에 데이터 수집을 사용하도록 설정됩니다.<br><br> ![스크립트에서 반환된 리소스 종류 선택](./media/log-analytics-collect-azurepass-posh/script-select-resource.png)<br>
   Azure 진단으로 메트릭을 수집하고 Log Analytics에 직접 보내도록 지원하는 리소스만 선택할 수 있습니다.  스크립트는 구독 또는 지정된 리소스 그룹에서 찾은 리소스 목록의 **Metrics** 열 아래에 **True** 값을 표시합니다.    
8. 선택을 확인하라는 메시지가 표시됩니다.  **Y**를 입력하여 정의된 범위에 선택된 모든 리소스에 메트릭 로깅을 사용하도록 설정합니다. 이 예제의 경우 구독의 모든 SQL 데이터베이스입니다.  

선택한 조건과 일치하는 모든 리소스에 대해 스크립트가 실행되고 메트릭 컬렉션을 사용하도록 설정됩니다. 완료되면 구성이 완료되었다고 알려주는 메시지가 표시됩니다.  

완료 직후 Log Analytics 리포지토리에 Azure PaaS 리소스의 데이터가 표시되기 시작합니다.  `AzureMetrics` 유형의 레코드가 생성되며, 이러한 레코드 분석은 [Azure SQL 분석](log-analytics-azure-sql.md) 및 [Azure Web Apps 분석](log-analytics-azure-web-apps-analytics.md) 관리 솔루션에서 지원됩니다.   

## <a name="update-a-resource-to-send-data-to-another-workspace"></a>데이터를 다른 작업 영역에 보내도록 리소스 업데이트
Log Analytics 작업 영역에 데이터를 보내는 리소스가 이미 있는데 나중에 다른 작업 영역을 참조하도록 재구성하려는 경우 `-Update` 매개 변수를 사용하여 스크립트를 실행합니다.  

**예:** 
`PS C:\users\<username>\Desktop\temp> .\Enable-AzureRMDiagnostics.ps1 -Update`

초기 구성을 수행하기 위해 스크립트를 실행했을 때와 동일한 정보를 묻는 메시지가 나타납니다.  

## <a name="next-steps"></a>다음 단계

* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하기 위해 [로그 검색](log-analytics-log-searches.md)에 대해 알아봅니다. 

* [사용자 지정 필드](log-analytics-custom-fields.md)를 사용하여 이벤트 레코드를 개별 필드로 구문 분석합니다.

* 로그 검색을 조직에 의미 있는 방식으로 시각화하는 방법을 이해하려면 [Log Analytics에서 사용할 사용자 지정 대시보드 만들기](log-analytics-dashboards.md)를 검토하세요.
