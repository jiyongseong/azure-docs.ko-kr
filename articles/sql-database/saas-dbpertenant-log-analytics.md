---
title: SQL Database 다중 테넌트 앱과 함께 Log Analytics 사용 | Microsoft Docs
description: 다중 테넌트 Azure SQL Database SaaS 앱을 사용하여 Log Analytics 설정 및 사용
keywords: SQL Database 자습서
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 02c380c78fa773b56a3c8b666e890836a3d8e54b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942062"
---
# <a name="set-up-and-use-log-analytics-with-a-multitenant-sql-database-saas-app"></a>다중 테넌트 SQL Database SaaS 앱을 사용하여 Log Analytics 설정 및 사용

이 자습서에서는 Azure [Log Analytics](/azure/log-analytics/log-analytics-overview)를 설정하고 사용하여 탄력적 풀 및 데이터베이스를 모니터링합니다. 이 자습서는 [성능 모니터링 및 관리 자습서](saas-dbpertenant-performance-monitoring.md)를 기반으로 합니다. 이는 Azure Portal에서 제공된 모니터링 및 경고를 강화하기 위해 Log Analytics를 사용하는 방법을 보여 줍니다. Log Analytics를 사용하면 수천 개의 탄력적 풀과 수십만 개의 데이터베이스를 모니터링할 수 있습니다. Log Analytics는 여러 Azure 구독에서 다양한 응용 프로그램과 Azure 서비스의 모니터링을 통합할 수 있는 단일 모니터링 솔루션을 제공합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Log Analytics를 설치 및 구성합니다.
> * Log Analytics를 사용하여 풀 및 데이터베이스를 모니터링합니다.

이 자습서를 수행하려면 다음 필수 조건이 완료되었는지 확인합니다.

* Wingtip Tickets SaaS 테넌트별 데이터베이스 앱이 배포되어 있습니다. 5분 안에 배포를 마치려면 [Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 배포 및 살펴보기](saas-dbpertenant-get-started-deploy.md)를 참조하세요.
* Azure PowerShell이 설치되었습니다. 자세한 내용은 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

SaaS 시나리오 및 패턴에 대한 논의와 모니터링 솔루션의 요구 사항에 미치는 영향은 [성능 모니터링 및 관리 자습서](saas-dbpertenant-performance-monitoring.md)를 참조하세요.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-log-analytics"></a>Log Analytics를 사용하여 데이터베이스와 탄력적 풀의 성능 모니터링 및 관리

Azure SQL Database의 경우 Azure Portal에서 데이터베이스 및 풀에 대한 모니터링 및 경고 기능을 사용할 수 있습니다. 이 기본 제공 모니터링 및 경고는 편리하지만 리소스 특정적이기도 합니다. 즉, 대규모 설치를 모니터링하거나 리소스 및 구독에서 통합된 보기를 제공하기에는 덜 적합합니다.

대규모 시나리오에서는 모니터링 및 경고에 Log Analytics를 사용할 수 있습니다. 별도의 Azure 서비스인 Log Analytics를 사용하면 다수의 서비스에 있는 작업 영역에서 수집된 진단 로그와 원격 분석에 대해 분석을 수행할 수 있습니다. Log Analytics에서 기본 제공되는 쿼리 언어와 데이터 시각화 도구를 사용하여 운영 데이터 분석을 수행할 수 있습니다. SQL 분석 솔루션은 몇 가지 사전 정의된 탄력적 풀 및 데이터베이스 모니터링과 함께 보기 및 쿼리 경고를 제공합니다. Log Analytics에는 사용자 지정 뷰 디자이너도 제공됩니다.

Log Analytics 작업 영역 및 분석 솔루션은 Azure Portal과 Operations Management Suite에서 모두 열 수 있습니다. Azure Portal이 더 최신 액세스 방법이지만 일부 지역에서는 Operations Management Suite 포털이 더 나을 수 있습니다.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>테넌트에서 워크로드를 시뮬레이션하여 성능 진단 데이터 만들기 

1. PowerShell ISE에서 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Demo-PerformanceMonitoringAndManagement.ps1*을 엽니다. 이 자습서에서 몇 가지 부하 생성 시나리오를 실행할 수도 있으므로 이 스크립트를 계속 열어 둡니다.
2. 더욱 유용한 모니터링 컨텍스트를 만들기 위해 일군의 테넌트를 프로비전합니다(아직 수행하지 않은 경우). 이 작업에는 몇 분이 소요됩니다.

   a. **$DemoScenario = 1**, _테넌트의 배치 프로비전_을 설정합니다.

   나. F5 키를 눌러 스크립트를 실행하여 추가로 17개의 테넌트를 배포합니다.

3. 이제 로드 생성기를 시작하여 모든 테넌트에서 시뮬레이션된 로드를 실행합니다.

    a. **$DemoScenario = 2**, _Generate normal intensity load (approx. 30 DTU)_ 를 설정합니다.

    나. 스크립트를 실행하려면 F5 키를 누릅니다.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 스크립트 가져오기

Wingtip Tickets SaaS 다중 테넌트 데이터베이스 스크립트 및 응용 프로그램 소스 코드는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 사용할 수 있습니다. Wingtip Tickets PowerShell 스크립트를 다운로드하고 차단을 해제하는 방법은 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)을 참조하세요.

## <a name="install-and-configure-log-analytics-and-the-azure-sql-analytics-solution"></a>Log Analytics 및 Azure SQL 분석 솔루션 설치 및 구성

Log Analytics는 구성이 필요한 별도의 서비스입니다. Log Analytics는 Log Analytics 작업 영역에서 로그 데이터, 원격 분석 및 메트릭을 수집합니다. Log Analytics 작업 영역은 Azure의 다른 리소스와 마찬가지로 생성이 필요합니다. 작업 영역을 자신이 모니터링하는 응용 프로그램과 동일한 리소스 그룹에서 만들 필요는 없습니다. 대부분의 경우 이렇게 하는 것이 적합합니다. Wingtip Tickets 앱의 경우 하나의 리소스 그룹을 사용하면 작업 영역이 응용 프로그램과 함께 삭제되도록 할 수 있습니다.

1. PowerShell ISE에서 *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\Demo-LogAnalytics.ps1*을 엽니다.
2. 스크립트를 실행하려면 F5 키를 누릅니다.

이제 Azure Portal에서 또는 Operations Management Suite 포털에서 Log Analytics를 열 수 있습니다. Log Analytics 작업 영역에서 원격 분석을 수집하여 표시하는 데는 몇 분 정도 시간이 걸립니다. 시스템의 데이터 수집 시간이 길어질수록 더 유용한 데이터가 수집됩니다. 

## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics 및 SQL Analytics 솔루션을 사용하여 풀 및 데이터베이스 모니터링


이 연습에서는 Log Analytics 및 Operations Management Suite 포털을 열고 데이터베이스 및 풀에 대해 원격 분석이 수집되는 과정을 살펴봅니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다. **모든 서비스**를 선택하여 Log Analytics를 엽니다. 그런 다음, Log Analytics를 검색합니다.

   ![Log Analytics 열기](media/saas-dbpertenant-log-analytics/log-analytics-open.png)

2. 이름이 _wtploganalytics-&lt;user&gt;_ 인 작업 영역을 선택합니다.

3. **개요**를 선택하여 Azure Portal에서 Log Analytics를 엽니다.

   ![개요](media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > 솔루션이 활성화되려면 몇 분 정도 소요될 수 있습니다. 

4. **Azure SQL 분석** 타일을 선택하여 엽니다.

    ![개요 타일](media/saas-dbpertenant-log-analytics/overview.png)

5. 이 솔루션의 보기는 가로로 스크롤됩니다. 하단에 자체 스크롤 막대가 있습니다. 필요한 경우 페이지를 새로 고칩니다.

6. 요약 페이지를 살펴보려면 타일이나 개별 데이터베이스를 선택하여 드릴 다운 탐색기를 엽니다.

    ![Log Analytics 대시보드](media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

7. 필터 설정을 변경하여 시간 범위를 수정합니다. 이 자습서에서는 **마지막 1시간**을 선택합니다.

    ![시간 필터](media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

8. 데이터베이스를 하나 선택하여 해당 데이터베이스의 쿼리 사용률과 메트릭을 살펴봅니다.

    ![데이터베이스 분석](media/saas-dbpertenant-log-analytics/log-analytics-database.png)

9. 사용률 메트릭을 보려면 분석 페이지를 오른쪽으로 스크롤합니다.
 
     ![데이터베이스 메트릭](media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

10. 분석 페이지를 왼쪽으로 스크롤하고, **리소스 정보** 목록에 있는 서버 타일을 선택합니다.  

    ![리소스 정보 목록](media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    서버의 풀과 데이터베이스를 보여 주는 페이지가 열립니다.

    ![풀과 데이터베이스가 있는 서버](media/saas-dbpertenant-log-analytics/log-analytics-server.png)

11. 풀을 선택합니다. 풀 페이지에서 오른쪽으로 스크롤하여 풀 메트릭을 봅니다. 

    ![풀 메트릭](media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


12. Log Analytics 작업 영역으로 돌아가서 **OMS 포털**을 선택하여 작업 영역을 엽니다.

    ![Operations Management Suite 포털 타일](media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

Operations Management Suite 포털에서 작업 영역의 로그와 메트릭 데이터를 자세히 살펴볼 수 있습니다. 

Log Analytics의 모니터링 및 경고는 Azure Portal의 각 리소스에 정의된 경고와 달리 작업 영역에 있는 데이터에 대한 쿼리를 바탕으로 합니다. 쿼리가 경고를 바탕으로 하기 때문에 데이터베이스마다 일일이 경고를 설정하는 대신 모든 데이터베이스를 대상으로 하는 하나의 경고를 정의할 수 있습니다. 쿼리는 작업 영역에서 사용 가능한 데이터를 통해서만 제한됩니다.

Log Analytics를 사용하여 쿼리를 실행하고 경고를 설정하는 방법에 대한 자세한 내용은 [Log Analytics에서 경고 규칙 작업](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts-creating)을 참조하세요.

SQL Database용 Log Analytics는 작업 영역의 데이터 크기에 따라 과금됩니다. 이 자습서에서는 일일 500MB로 제한되는 무료 작업 영역을 만들었습니다. 이 한도에 도달하면 작업 영역에 더 이상 데이터가 추가되지 않습니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Log Analytics를 설치 및 구성합니다.
> * Log Analytics를 사용하여 풀 및 데이터베이스를 모니터링합니다.

[테넌트 분석 자습서](saas-dbpertenant-log-analytics.md)를 체험해 보세요.

## <a name="additional-resources"></a>추가 리소스

* [초기 Wingtip Tickets SaaS 테넌트별 데이터베이스 응용 프로그램 배포를 기반으로 하는 추가 자습서](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
