---
title: Azure 진단 로그 | Microsoft Docs
description: 고객은 Azure CDN에 대한 Log Analytics를 사용하도록 설정할 수 있습니다.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: rli
ms.openlocfilehash: 73c19383b791438c2ae899b45e1b4635e9cd5802
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="azure-diagnostic-logs"></a>Azure 진단 로그

Azure 진단 로그를 통해 이제 핵심 분석을 보고 다음을 포함한 하나 이상의 대상에 저장할 수 있습니다.

 - Azure Storage 계정
 - Azure Event Hubs
 - [Log Analytics 작업 영역](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
이 기능은 모든 가격 책정 계층에 대한 CDN 끝점에서 사용할 수 있습니다. 

Azure 진단 로그를 사용하면 사용자 지정 방식으로 사용할 수 있도록 CDN 끝점에서 다양한 원본으로 기본 사용 현황 메트릭을 내보낼 수 있습니다. 예를 들어 다음 유형의 데이터 내보내기를 수행할 수 있습니다.

- 데이터를 Blob Storage로 내보내고, CSV로 내보낸 후 Excel에서 그래프를 생성합니다.
- 데이터를 Event Hubs로 내보내고 다른 Azure 서비스의 데이터와 상관 관계를 설정합니다.
- 데이터를 Log Analytics로 내보내고, 고유한 Log Analytics 작업 공간에서 데이터를 봅니다.

다음 그림에서는 데이터에 대한 일반적인 CDN 핵심 분석 뷰를 보여 줍니다.

![포털 - 진단 로그](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*그림 1 - CDN 핵심 분석 뷰*

진단 로그에 대한 자세한 내용은 [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)를 참조하세요.

## <a name="enable-logging-with-azure-portal"></a>Azure Portal에서 로깅을 사용하도록 설정

CDN 핵심 분석에서 로깅을 사용하도록 설정하려면 아래 단계를 따르세요.

[Azure 포털](http://portal.azure.com)에 로그인합니다. 워크플로에 대해 아직 CDN을 사용하도록 설정하지 않은 경우 계속하기 전에 [Azure CDN을 사용](cdn-create-new-endpoint.md)하도록 설정합니다.

1. 포털에서 **CDN 프로필**로 이동합니다.
2. CDN 프로필을 선택한 다음 **진단 로그**를 사용하도록 설정하려는 CDN 끝점을 선택합니다.

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. **모니터링** 섹션에서 **진단 로그**를 선택합니다.

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Azure Storage에서 로깅을 사용하도록 설정
    
1. Azure Storage를 사용하여 로그를 저장하려면 **저장소 계정에 보관**을 선택하고, **CoreAnalytics**를 선택한 다음, **보존(일)** 아래에서 보존 기간을 선택합니다. 0일의 보존은 로그를 무기한 저장합니다. 
2. 설정에 대한 이름을 입력한 다음 **저장소 계정**을 클릭합니다. 저장소 계정을 선택한 후 **저장**을 클릭합니다.

![포털 - 진단 로그](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*그림 2 - Azure Storage로 로깅*

### <a name="logging-with-log-analytics"></a>Log Analytics로 로깅

Log Analytics를 사용하여 로그를 저장하려면 아래 단계를 따릅니다.

1. **진단 로그** 블레이드에서 **Log Analytics에 보내기**를 선택합니다. 

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. **구성**을 클릭하여 로그 분석 로깅을 구성합니다. OMS 작업 영역 대화 상자에서 이전 작업 영역을 선택하거나 새 작업 영역을 만들 수 있습니다.

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. **새 작업 영역 만들기**를 클릭합니다.

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/07_Create-new.png)

4. 새 Log Analytics 작업 영역 이름을 입력합니다. Log Analytics 작업 영역 이름은 고유해야 하며 문자, 숫자 및 하이픈만 포함해야 합니다. 공백 및 밑줄은 사용할 수 없습니다. 
5. 그런 다음 기존 구독, 리소스 그룹(새로운 또는 기존), 위치 및 가격 책정 계층을 선택합니다. 이 구성을 대시보드에 고정하는 옵션이 있습니다. **확인**을 클릭하여 구성을 완료합니다.

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  작업 영역을 만든 후에 진단 로그 창에 반환됩니다. 새 로그 분석 작업 영역의 이름을 확인합니다.

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    로그 분석 구성을 설정한 후 **CoreAnalytics**를 선택했는지 확인합니다.

6. **저장**을 클릭합니다.

7. 새 Log Analytics 작업 영역을 보려면 Azure Portal 대시보드로 이동하여 로그 분석 작업 영역의 이름을 클릭합니다. OMS 포털 타일을 클릭하여 Log Analytics 영역을 봅니다. 

    ![포털 - 진단 로그](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Log Analytics 작업 영역에서 데이터를 기록할 준비가 되었습니다. 해당 데이터를 사용하려면 이 문서의 후반부에서 다루고 있는 [Log Analytics 솔루션](#consuming-diagnostics-logs-from-a-log-analytics-workspace)을 사용해야 합니다.

로그 데이터 지연에 대한 자세한 내용은 [로그 데이터 지연](#log-data-delays)을 참조하세요.

## <a name="enable-logging-with-powershell"></a>PowerShell을 통해 로깅을 사용하도록 설정

다음 예제는 Azure PowerShell Cmdlet을 통해 진단 로그를 사용하도록 설정하는 방법을 보여 줍니다.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Storage 계정에서 진단 로그 사용

먼저 로그인하고 구독을 선택합니다.

    Connect-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Storage 계정에서 진단 로그를 사용하도록 설정하려면 다음 명령을 사용합니다.

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Log Analytics 작업 영역에서 진단 로그를 활성화하려면 이 명령을 사용합니다.

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Storage에서 진단 로그 사용
이 섹션에서는 CDN 핵심 분석의 스키마를 설명하고, Azure Storage 계정 내에서 이러한 분석을 구성하는 방법을 설명하며, 로그를 CSV 파일로 다운로드하기 위한 샘플 코드를 제공합니다.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage 탐색기 사용
Azure Storage 계정에서 핵심 분석 데이터에 액세스하려면 먼저 저장소 계정의 콘텐츠에 액세스하기 위한 도구가 있어야 합니다. 시중에 여러 도구가 나와 있지만 Microsoft Azure Storage 탐색기가 권장됩니다. 도구를 다운로드하려면 [Azure Storage 탐색기](http://storageexplorer.com/)를 참조하세요. 소프트웨어를 다운로드하여 설치한 후에는 CDN 진단 로그의 대상으로 구성된 동일한 Azure Storage 계정을 사용하도록 구성합니다.

1.  **Microsoft Azure Storage 탐색기**를 엽니다.
2.  저장소 계정을 찾습니다.
3.  이 저장소 계정 아래의 **"Blob 컨테이너"** 노드로 이동한 후 해당 노드를 확장합니다.
4.  **“insights-logs-coreanalytics”** 라는 컨테이너를 선택하고 두 번 클릭합니다.
5.  오른쪽 창에 결과가 표시되고 **“resourceId=”** 와 같은 첫 번째 수준에서 시작됩니다. **PT1H.json** 파일이 보일 때까지 모든 항목을 계속 클릭합니다. 경로에 대한 설명은 다음 메모를 참조하세요.
6.  각 blob **PT1H.json**은 특정 CDN 끝점 또는 사용자 지정 도메인에 대해 1시간의 분석 로그를 표시합니다.
7.  이 JSON 파일 콘텐츠의 스키마는 핵심 분석 로그의 스키마 섹션에 설명되어 있습니다.


> [!NOTE]
> **Blob 경로 형식**
> 
> 핵심 분석 로그는 1시간 마다 생성되고 데이터는 JSON 페이로드로 단일 Azure blob 내부에 수집되고 저장됩니다. Storage 탐색기 도구는 ‘/’를 디렉터리 구분 기호로 해석하고 계층을 표시하므로 Azure blob에 대한 경로는 계층 구조가 있는 것처럼 표시되고 blob 이름을 나타냅니다. blob의 이름은 다음 명명 규칙을 따릅니다. 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**필드 설명:**

|값|설명|
|-------|---------|
|구독 ID    |GUID 형식에서 Azure 구독의 ID입니다.|
|리소스 그룹 이름 |CDN 리소스가 속한 리소스 그룹의 이름입니다.|
|프로필 이름 |CDN 프로필의 이름입니다.|
|끝점 이름 |CDN 끝점의 이름입니다.|
|Year|  4자리 연도 표시(예: 2017)입니다.|
|월| 2자리 월 표시입니다. 01=1월 ... 12=12월|
|일|   2자리 월의 일 표시입니다.|
|PT1H.json| 분석 데이터가 저장되는 실제 JSON 파일입니다.|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>핵심 분석 데이터를 CSV 파일로 내보내기

핵심 분석에 쉽게 액세스할 수 있도록 하기 위해 도구에 샘플 코드를 제공합니다. 이 도구를 사용하여 JSON 파일을 쉼표로 구분된 일반 파일 형식으로 다운로드한 다음, 쉽게 차트를 만들거나 기타 집계를 수행하는 데 사용할 수 있습니다.

이 도구를 사용하는 방법은 다음과 같습니다.

1.  Github 링크를 방문합니다. [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  코드를 다운로드합니다.
3.  지침에 따라 컴파일 및 구성합니다.
4.  도구를 실행합니다.
5.  결과 CSV 파일은 분석 데이터를 간단한 평면 계층으로 표시합니다.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 진단 로그 사용
Log Analytics는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성 및 성능을 유지하는 Azure의 서비스입니다. 이 서비스는 클라우드 및 온-프레미스 환경에서 리소스에 의해 생성되고 여러 원본에 대한 분석을 제공하는 다른 모니터링 도구에서 생성된 데이터를 수집합니다. 

Log Analytics를 사용하려면 앞서 이 문서에서 논의한 Azure Log Analytics 작업 영역에 대한 [로깅을 사용하도록 설정](#enable-logging-with-azure-storage)해야 합니다.

### <a name="using-the-log-analytics-workspace"></a>Log Analytics 작업 영역 사용

 다음 다이어그램에서는 리포지토리의 입력 및 출력의 아키텍처를 보여 줍니다.

![Log Analytics 작업 영역](./media/cdn-diagnostics-log/12_Repo-overview.png)

*그림 3 - Log Analytics 리포지토리*

관리 솔루션을 사용하여 다양한 방법으로 데이터를 표시할 수 있습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)에서 관리 솔루션을 다운로드할 수 있습니다.

각 솔루션의 맨 아래에서 **지금 신청** 링크를 클릭하여 Azure Marketplace에서 관리 솔루션을 설치할 수 있습니다.

### <a name="adding-a-log-analytics-cdn-management-solution"></a>Log Analytics CDN 관리 솔루션 추가

관리 솔루션을 추가하려면 아래 단계를 따르세요.

1.   Azure Portal에 아직 로그인하지 않은 경우 Azure 구독을 사용하여 로그인한 후 대시보드로 이동합니다.
    ![Azure 대시보드](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. **Marketplace** 아래 **새로 만들기** 블레이드에서 **모니터링 + 관리**를 선택합니다.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. **모니터링 + 관리** 블레이드에서 **모두 표시**를 클릭합니다.

    ![모두 표시](./media/cdn-diagnostics-log/15_See-all.png)

4.  검색 상자에서 CDN을 검색합니다.

    ![모두 표시](./media/cdn-diagnostics-log/16_Search-for.png)

5.  **Azure CDN 핵심 분석**을 선택합니다. 

    ![모두 표시](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  **만들기**를 클릭하면 새 Log Analytics 작업 영역을 만들거나 기존 작업 영역을 사용할지 묻습니다. 

    ![모두 표시](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  이전에 만든 작업 영역을 선택합니다. 그런 다음 자동화 계정을 추가해야 합니다.

    ![모두 표시](./media/cdn-diagnostics-log/19_Add-automation.png)

8. 다음 화면은 사용자가 작성해야 하는 자동화 계정 양식을 보여 줍니다. 

    ![모두 표시](./media/cdn-diagnostics-log/20_Automation.png)

9. 자동화 계정을 만든 후 솔루션을 추가할 준비가 되었습니다. **만들기** 단추를 클릭합니다.

    ![모두 표시](./media/cdn-diagnostics-log/21_Ready.png)

10. 이제 솔루션이 작업 영역에 추가되었습니다. Azure Portal 대시보드로 돌아갑니다.

    ![모두 표시](./media/cdn-diagnostics-log/22_Dashboard.png)

    만든 Log Analytics 작업 영역을 클릭하여 작업 영역으로 이동합니다. 

11. **OMS 포털** 타일을 클릭하여 새 솔루션을 확인합니다.

    ![모두 표시](./media/cdn-diagnostics-log/23_workspace.png)

12. 이제 포털은 다음 화면과 같이 표시됩니다.

    ![모두 표시](./media/cdn-diagnostics-log/24_OMS-solution.png)

    데이터에 대한 여러 뷰를 보려면 타일 중 하나를 클릭합니다.

    ![모두 표시](./media/cdn-diagnostics-log/25_Interior-view.png)

    왼쪽 또는 오른쪽으로 스크롤하여 데이터에 대한 개별 뷰를 나타내는 추가 타일을 볼 수 있습니다. 

    타일 중 하나를 클릭하면 데이터에 대한 더 자세한 정보가 제공됩니다.

     ![모두 표시](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>제품 및 가격 책정 계층

[여기](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers)에서 관리 솔루션에 대한 제품 및 가격 책정 계층을 볼 수 있습니다.

### <a name="customizing-views"></a>뷰 사용자 지정

**뷰 디자이너**를 사용하여 데이터에 대한 뷰를 사용자 지정할 수 있습니다. 디자인을 시작하려면 Log Analytics 작업 영역으로 이동한 후 **뷰 디자이너** 타일을 클릭합니다.

![뷰 디자이너](./media/cdn-diagnostics-log/27_Designer.png)

차트 유형을 끌어서 놓고 분석하려는 데이터 세부 정보를 입력할 수 있습니다.

![뷰 디자이너](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>로그 데이터 지연

다음 표에는 **Microsoft의 Azure CDN 표준**, **Akamai의 Azure CDN 표준** 및 **Verizon의 Azure CDN 표준/프리미엄**에 대한 로그 데이터 지연이 표시됩니다.

Microsoft 로그 데이터 지연 | Verizon 로그 데이터 지연 | Akamai 로그 데이터 지연
--- | --- | ---
1시간 지연됩니다. | 1시간 지연되고, 끝점 전파가 완료된 후 나타날 때까지 최대 2시간이 걸릴 수 있습니다. | 24시간 지연되고, 24시간 이전에 만들어진 경우 나타날 때까지 최대 2시간이 걸립니다. 최근에 만든 경우 로그가 나타날 때까지 최대 25시간이 걸릴 수 있습니다.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN 핵심 분석에 대한 진단 로그 유형

현재는 CDN POP/Edge에서 볼 수 있는 HTTP 응답 통계 및 송신 통계를 보여 주는 메트릭이 포함된 핵심 분석 로그만 제공하고 있습니다.

### <a name="core-analytics-metrics-details"></a>핵심 분석 메트릭 정보
다음 표에는 **Microsoft의 Azure CDN 표준**, **Akamai의 Azure CDN 표준** 및 **Verizon의 Azure CDN 표준/프리미엄**에 대한 핵심 분석 로그에서 사용할 수 있는 메트릭 목록이 표시됩니다. 모든 공급자의 모든 메트릭을 사용할 수 있는 것은 아니지만 이러한 차이는 미미합니다. 이 표에는 지정된 메트릭을 공급자에서 사용할 수 있는지 여부도 나와 있습니다. 메트릭은 트래픽이 있는 해당 CDN 끝점에 대해서만 사용할 수 있습니다.


|메트릭                     | 설명 | Microsoft | Verizon | Akamai |
|---------------------------|-------------|----------|---------|--------|
| RequestCountTotal         | 이 기간 동안의 요청 적중의 총 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus2xx | 2xx HTTP 코드(예: 200, 202)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus3xx | 3xx HTTP 코드(예: 300, 302)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus4xx | 4xx HTTP 코드(예: 400, 404)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus5xx | 5xx HTTP 코드(예: 500, 504)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatusOthers | 다른 모든 HTTP 코드의 수(2xx-5xx 이외)입니다. | 예 | 예 |예 |
| RequestCountHttpStatus200 | 200 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니오  |예 |
| RequestCountHttpStatus206 | 206 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니오  |예 |
| RequestCountHttpStatus302 | 302 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니오  |예 |
| RequestCountHttpStatus304 | 304 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니오  |예 |
| RequestCountHttpStatus404 | 404 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니오  |예 |
| RequestCountCacheHit | 캐시 적중을 발생한 모든 요청의 수. 자산이 POP에서 클라이언트로 직접 제공되었습니다. | 예 | 예 | 아니오  |
| RequestCountCacheMiss | 캐시 누락을 발생한 모든 요청의 수. 자산을 클라이언트에 가장 가까운 POP에서 찾을 수 없으므로 원래 시작점에서 검색되었습니다. | 예 | 예 | 아니오 |
| RequestCountCacheNoCache | Edge의 사용자 구성 때문에 캐시되지 못한 자산에 대한 모든 요청의 수 | 예 | 예 | 아니오 |
| RequestCountCacheUncacheable | 자산의 Cache-Control 및 Expires 헤더에 의해 캐시되지 못하여 POP에서 또는 HTTP 클라이언트에 의해 캐시되지 않아야 함을 나타내는 자산에 대한 모든 요청의 수입니다. | 예 | 예 | 아니오 |
| RequestCountCacheOthers | 위에 포함되지 않는 캐시 상태를 갖는 모든 요청의 수 | 아니오 | 예 | 아니오  |
| EgressTotal | 아웃바운드 데이터 전송(GB) | 예 |예 |예 |
| EgressHttpStatus2xx | 2xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다.* | 예 | 예 | 아니오  |
| EgressHttpStatus3xx | 3xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니오  |
| EgressHttpStatus4xx | 4xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니오  |
| EgressHttpStatus5xx | 5xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니오 |
| EgressHttpStatusOthers | 다른 HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니오  |
| EgressCacheHit | CDN POP/Edge의 CDN 캐시에서 직접 전달된 응답에 대한 아웃바운드 데이터 전송입니다. | 예 | 예 | 아니오 |
| EgressCacheMiss. | 가장 가까운 POP 서버에 없으며 원본 서버에서 검색된 응답에 대한 아웃바운드 데이터 전송입니다. | 예 | 예 | 아니오 |
| EgressCacheNoCache | Edge의 사용자 구성 때문에 캐시되지 못한 자산에 대한 아웃바운드 데이터 전송 | 예 | 예 | 아니오 |
| EgressCacheUncacheable | 자산의 Cache-Control 및/또는 Expires 헤더에 의해 캐시되지 못하여 자산에 대한 아웃바운드 데이터 전송. POP에서 또는 HTTP 클라이언트에 의해 캐시되지 않아야 함을 나타냅니다. | 예 | 예 | 아니오 |
| EgressCacheOthers | 다른 캐시 시나리오에 대한 아웃바운드 데이터 전송 | 아니오 | 예 | 아니오 |

* 아웃바운드 데이터 전송은 CDN POP 서버에서 클라이언트로 전달되는 트래픽을 나타냅니다.


### <a name="schema-of-the-core-analytics-logs"></a>핵심 분석 로그의 스키마 

모든 로그는 JSON 형식으로 저장되며 각 항목에는 다음 스키마에 따른 문자열 필드가 있습니다.

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

여기서 *time*은 통계가 보고되는 시간 범위의 시작 시간을 나타냅니다. CDN 공급자가 메트릭을 지원하지 않을 경우 double 또는 정수 값 대신 null 값이 사용됩니다. 이 null 값은 메트릭이 없음을 나타내며 값 0과는 다릅니다. 이러한 메트릭 집합은 끝점에 구성된 도메인당 1개만 유지됩니다.

예제 속성은 다음과 같습니다.

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>추가 리소스

* [Azure 진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Azure CDN 보조 포털을 통한 핵심 분석](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







