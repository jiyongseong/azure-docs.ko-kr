---
title: "Azure Log Analytics 관리 솔루션 추가 | Microsoft Docs"
description: "Azure의 관리 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 취득 규칙의 컬렉션입니다."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f6ec6dd46d28518baf6f105dd232a4b0e77a8c6f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>작업 영역에 Azure Log Analytics 관리 솔루션 추가

Log Analytics 관리 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 **논리**, **시각화** 및 **데이터 취득 규칙** 컬렉션입니다. 이 문서는 Log Analytics에서 지원되는 관리 솔루션 목록 및 Azure Portal을 사용하여 작업 영역에 추가 및 제거하는 방법을 보여줍니다.

관리 솔루션을 사용하면:

* 운영 문제를 보다 빠르게 조사 및 해결하고,
* 다양한 유형의 컴퓨터 데이터를 수집 및 상호 연결하며,
* 솔루션에 노출되는 작업에 사전 대책을 강구할 수 있는 심층적인 통찰력을 가질 수 있습니다.

> [!NOTE]
> Log Analytics에는 로그 검색 기능이 포함되어 있으므로 이 기능을 사용하기 위해 관리 솔루션을 설치할 필요가 없습니다. 하지만 작업 영역에 관리 솔루션을 추가하면 데이터 시각화, 제안된 검색 및 통찰력을 얻을 수 있습니다.

이 문서에서는 Azure Portal Marketplace를 사용하여 작업 영역에 관리 솔루션을 추가합니다. 솔루션을 추가한 후에는 데이터가 사용자 인프라의 서버에서 수집되고 Log Analytics로 전송됩니다. 처리는 일반적으로 몇 분 정도에서 한 시간이 소요됩니다. 서비스에서 데이터를 처리한 후에는 Log Analytics에서 볼 수 있습니다.

관리 솔루션이 더 이상 필요 없으면 손쉽게 제거할 수 있습니다. 관리 솔루션을 제거하면 해당 데이터가 Log Analytics로 전송되지 않습니다. 무료 가격 책정 계층에 있는 경우 솔루션을 제거하면 사용되는 데이터 양을 줄여 일일 데이터 할당량 미만으로 유지하는 데 도움이 됩니다.

## <a name="view-available-management-solutions"></a>사용할 수 있는 관리 솔루션 보기

Azure Marketplace는 [Log Analytics을 위한 관리 솔루션](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions) 목록을 포함합니다.

각 솔루션의 맨 아래에서 **지금 신청** 링크를 클릭하여 Azure Marketplace에서 관리 솔루션을 설치할 수 있습니다.

## <a name="add-a-management-solution"></a>관리 솔루션 추가
1. [Azure Portal](https://portal.azure.com)에 아직 로그인하지 않은 경우 Azure 구독을 사용하여 로그인합니다.
2. **리소스 만들기** > **모니터링 + 관리** > **모두 보기**를 선택합니다.  
    ![모니터링 + 관리 블레이드](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. **관리 솔루션** 오른쪽에서 **기타**를 클릭합니다.
5. **관리 솔루션** 블레이드에서 작업 영역에 추가할 관리 솔루션을 선택합니다.  
    ![모니터링 + 관리 블레이드](./media/log-analytics-add-solutions/management-solutions.png)  
6. 관리 솔루션 블레이드에서 관리 솔루션에 대한 정보를 검토한 다음 **만들기**를 클릭합니다.
7. *관리 솔루션 이름* 블레이드에서 관리 솔루션과 연결할 작업 영역을 선택합니다.
8. 필요에 따라 Azure 구독, 리소스 그룹, 위치에 대한 작업 영역 설정을 변경합니다. **Automation 옵션**을 선택할 수도 있습니다. **만들기**를 클릭합니다.  
    ![솔루션 작업 영역](./media/log-analytics-add-solutions/solution-workspace.png)  
9. 작업 영역에 추가한 관리 솔루션 사용을 시작하려면 **Log Analytics** > **구독** > ***작업 영역 이름*** > **개요**로 이동합니다. 관리 솔루션에 대한 새 타일이 표시됩니다. 타일을 클릭하여 열고 솔루션에 대한 데이터가 수집된 후에 솔루션 사용을 시작합니다.

## <a name="remove-a-management-solution"></a>관리 솔루션 제거

1. [Azure Portal](https://portal.azure.com)에서 **Log Analytics** > **구독** > ***작업 영영 이름***으로 이동한 후 ***작업 영영 이름*** 블레이드에서 **솔루션**을 클릭합니다.
2. 관리 솔루션 목록에서 제거할 솔루션을 선택합니다.
3. 작업 영역에 대한 솔루션 블레이드에서 **삭제**를 클릭합니다.  
    ![솔루션 삭제](./media/log-analytics-add-solutions/solution-delete.png)  
4. 확인 대화 상자에서 **예**를 클릭합니다.

## <a name="offers-and-pricing-tiers"></a>제품 및 가격 책정 계층

다음은 각 Operations Management Suite 제품에 속하는 관리 솔루션을 구분한 테이블입니다.
이 테이블에는 각 관리 솔루션에 적용되는 가격 책정 계층도 분류되어 있습니다.
이 테이블의 모든 솔루션은 Azure Portal 및 Log Analytics 포털의 솔루션 갤러리 내에서 얻을 수 있습니다.

| 관리 솔루션                                                                       | 제안                                                                     | 가격 책정 계층<sup>1</sup>                                                 | 메모 |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Activity Log Analytics](log-analytics-activity.md)(활동 로그 분석)                                                                   | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | 90일간 데이터 무료 제공<br>체험 계층 요금에 적용되지 않는 데이터 |
| [AD 평가](log-analytics-ad-assessment.md)                                           | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [AD 복제 상태](log-analytics-ad-replication-status.md)                           | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [에이전트 상태](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | 체험 계층 요금에 적용되지 않는 데이터<br> Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [경고 관리](log-analytics-solution-alert-management.md)                            | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [Application Insights 커넥터(미리 보기)](log-analytics-app-insights-connector.md)                                               | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [Automation Hybrid Worker](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automation 및 제어</li></ul>                                  | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | Log Analytics 작업 영역을 Automation 계정에 연결해야 함 |
| [Azure Application Gateway 분석](log-analytics-azure-networking-analytics.md)    | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [Azure 네트워크 보안 그룹 분석](log-analytics-azure-networking-analytics.md)     | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [Azure SQL Analytics(미리 보기)](log-analytics-azure-sql.md)                                                       | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br>&nbsp;노드당&nbsp;(OMS)                                                                          | Log Analytics 작업 영역을 Automation 계정에 연결해야 함|
| [Azure Web Apps 분석](log-analytics-azure-web-apps-analytics.md)     | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
|[Backup](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Insight and Analytics</li></ul>                                   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)                                                                       | 기존 Backup 자격 증명 모음이 필요합니다.<br> Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [용량 및 성능(미리 보기)](log-analytics-capacity.md)                                                   | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [변경 내용 추적](log-analytics-change-tracking.md)                                       | <ul><li>Automation 및 제어</li></ul>                                  | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | Log Analytics 작업 영역을 Automation 계정에 연결해야 함 |
| [컨테이너](log-analytics-containers.md)                                                 | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [IT Service Management Connector](log-analytics-itsmc-overview.md)                                                | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> &nbsp;노드당&nbsp;(OMS)     | |
| HDInsight HBase 모니터링 <br>(미리 보기)                                                  | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [Key Vault 분석](log-analytics-azure-key-vault.md)                   | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [Logic Apps B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [맬웨어 평가](log-analytics-malware.md)                                            | <ul><li>보안 및 규정 준수</li></ul>                                 | 무료<br> 독립 실행형<br>&nbsp;노드당&nbsp;(OMS)                                                                           | 2017년 6월 19일 이후 보안 및 규정 준수 솔루션을 추가할 경우 작업 영역 가격 책정 계층에 관계 없이 [노드당 비용이 청구됩니다](https://azure.microsoft.com/pricing/details/security-compliance/). 처음 60일 동안 무료로 사용할 수 있습니다.  |
| [네트워크 성능 모니터](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Insight and Analytics</li></ul>                                   | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | |
| [Office 365 분석(미리 보기)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [보안 및 감사](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>보안&nbsp;및&nbsp;규정 준수</li></ul>                       | 무료<br> 독립 실행형<br>&nbsp;노드당&nbsp;(OMS)                                                                           | 보안 이벤트 로그를 수집하려면 이 솔루션이 필요<br>2017년 6월 19일 이후 보안 및 규정 준수 솔루션을 추가할 경우 작업 영역 가격 책정 계층에 관계 없이 [노드당 비용이 청구됩니다](https://azure.microsoft.com/pricing/details/security-compliance/). 처음 60일 동안 무료로 사용할 수 있습니다. |
| [Service Fabric 분석(미리 보기)](log-analytics-service-fabric.md)                     | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [서비스 맵(미리 보기)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Insight and Analytics</li></ul>                      | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | 미국 동부, 유럽 서부 및 미국 중서부에서 사용 가능    |
| [Site Recovery](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Insight and Analytics</li></ul>                                   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)                                                                       | 기존 Site Recovery 자격 증명 모음이 필요합니다.<br> Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [SQL 평가](log-analytics-sql-assessment.md)                                         | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| 작업이 없는 동안 VM 시작/중지<br>(미리 보기)                                              | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | Log Analytics 작업 영역을 Automation 계정에 연결해야 함 |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [System Center Operations Manager 평가(미리 보기)](log-analytics-scom-assessment.md)  | <ul><li>Insight and Analytics</li><li>Log Analytics</li></ul>        | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [업데이트 관리](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automation 및 제어</li></ul>                                  | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | Log Analytics 작업 영역을 Automation 계정에 연결해야 함 |
| [업데이트 준수(미리 보기)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | 데이터 또는 노드 무료 사용<br>체험 계층 요금에 적용되지 않는 데이터입니다.<br> Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [업그레이드 준비](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | 데이터 또는 노드 무료 사용<br>체험 계층 요금에 적용되지 않는 데이터입니다.<br> Azure Portal/Marketplace에서 추가할 수 없습니다. |
| [VMware 모니터링(미리 보기)](log-analytics-vmware.md)                                | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB당&nbsp;(독립 실행형)<br> &nbsp;노드당&nbsp;(OMS)   | |
| [Wire Data 2.0(미리 보기)](log-analytics-wire-data.md)                                                                 | <ul><li>Insight and Analytics</li></ul>                                   | 무료<br> &nbsp;노드당&nbsp;(OMS)                                                                         | 미국 동부, 유럽 서부 및 미국 중서부에서 사용 가능 |

<sup>1</sup> *Standard* 및 *Premium(OMS)* 가격 책정 계층은 2016년 9월 21일 전에 Log Analytics 작업 영역을 만든 고객에게만 제공됩니다.

### <a name="community-provided-management-solutions"></a>커뮤니티 제공 관리 솔루션

커뮤니티 제공 솔루션은 [Azure 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS))에서 또는 저자에게 직접 받을 수 있습니다.

| 관리 솔루션               | 제안                                                                     | 가격 책정 계층                         | 메모 |
| ---                               | ---                                                                       | ---                                   | ---   |
| 모든 커뮤니티 제공 솔루션  | <ul><li>정보&nbsp;및&nbsp;분석</li><li>Log Analytics</li></ul>   | 무료<br> &nbsp;노드당&nbsp;(OMS)     |   Log Analytics 작업 영역을 Automation 계정에 연결해야 함 |




## <a name="data-collection-details"></a>데이터 수집 세부 정보
다음 테이블은 Log Analytics 관리 솔루션 및 데이터 원본에 사용할 데이터 수집 방법 및 기타 세부 정보를 보여줍니다. 이 테이블은 [구독 가격 책정 계층](https://go.microsoft.com/fwlink/?linkid=827926)에 상응하는 솔루션 제품별로 분류되어 있습니다. Activity Log Analytics 솔루션은 모든 가격 책정 계층에서 무료로 사용할 수 있습니다.

Log Analytics Windows 에이전트 및 System Center Operations Manager 에이전트는 기본적으로 동일합니다. Windows 에이전트에는 Log Analytics 작업 영역에 연결하고 프록시를 통해 전달할 수 있는 추가적인 기능이 포함되어 있습니다. Operations Manager 에이전트를 사용하는 경우 Log Analytics와의 통신을 위해 OMS 에이전트로 타게팅되어야 합니다. 이 테이블의 Operations Manager 에이전트는 Operations Manager에 연결되는 OMS 에이전트입니다. 기존 Operations Manager 환경을 Log Analytics에 연결하기 위한 자세한 내용은 [Log Analytics에 Operations Manager 연결](log-analytics-om-agents.md)을 참조하세요.

> [!NOTE]
> 사용하는 에이전트 유형에 따라 다음 조건을 통해 Log Analytics에 데이터를 보내는 방식이 결정됩니다.
> - Windows 에이전트 또는 Operations Manager 연결 OMS 에이전트를 사용합니다.
> - Operations Manager가 필요한 경우 솔루션의 Operations Manager 에이전트 데이터는 항상 OMS Operations Manager 관리 그룹을 사용하여 Log Analytics로 전송됩니다. 또한 Operations Manager가 필요한 경우 솔루션에서 Operations Manager 에이전트에만 사용합니다.
> - Operations Manager가 필요하지 않으며 Operations Manager 에이전트 데이터가 관리 그룹을 통해 Log Analytics로 전송되는 것으로 테이블에 나와 있는 경우 Operations Manager 에이전트 데이터는 항상 관리 그룹을 통해 Log Analytics로 전송됩니다. Windows 에이전트는 관리 그룹을 건너뛰고 데이터를 직접 Log Analytics로 보냅니다.
> - Operations Manager 에이전트 데이터가 관리 그룹을 사용하여 전송되지 않는 경우 데이터가 관리 그룹을 건너뛰고 직접 Log Analytics로 전송됩니다.

### <a name="insight--analytics--log-analytics"></a>정보 및 분석/Log Analytics

| 관리 솔루션 | 플랫폼 | Microsoft Monitoring Agent | Operations Manager 에이전트 | Azure 저장소 | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Activity Log Analytics | Azure |   |   |   |   |   | 알림 시 |
| AD 평가 |Windows |&#8226; |&#8226; |  |  |&#8226; |7 일 |
| AD 복제 상태 |Windows |&#8226; |&#8226; |  |  |&#8226; |5일 |
| 에이전트 상태 | Windows 및 Linux | &#8226; | &#8226; |   |   | &#8226; | 1분 |
| 경고 관리(Nagios) |Linux |&#8226; |  |  |  |  |도착 시 |
| 경고 관리(Zabbix) |Linux |&#8226; |  |  |  |  |1분 |
| 경고 관리(Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3분 |
| Application Insights 커넥터(미리 보기) | Azure |   |   |   |   |   | 알림 시 |
| Azure Application Gateway 분석 | Azure |   |   |   |   |   | 알림 시 |
| Azure 네트워크 보안 그룹 분석 | Azure |   |   |   |   |   | 알림 시 |
| Azure SQL Analytics(미리 보기) |Windows |  |  |  |  |  | 10분 |
| 용량 관리 |Windows |&#8226; |&#8226; |  |  |&#8226; |도착 시 |
| 컨테이너 | Windows 및 Linux | &#8226; | &#8226; |   |   |   | 3분 |
| Key Vault 분석 |Windows |  |  |  |  |  |알림 시 |
| 네트워크 성능 모니터 | Windows | &#8226; | &#8226; |   |   |   | TCP는 5초마다 핸드셰이크를 수행하며 3분마다 데이터가 전송됩니다. |
| Office 365 분석(미리 보기) |Windows |  |  |  |  |  |알림 시 |
| Service Fabric 분석 |Windows |  |  |&#8226; |  |  |5분 |
| 서비스 맵 | Windows 및 Linux | &#8226; | &#8226; |   |   |   | 15초 |
| SQL 평가 |Windows |&#8226; |&#8226; |  |  |&#8226; |7 일 |
| SurfaceHub |Windows |&#8226; |  |  |  |  |도착 시 |
| System Center Operations Manager 평가(미리 보기) | Windows | &#8226; | &#8226; |   |   | &#8226; | 7일 |
| Analytics 업그레이드(미리 보기) | Windows | &#8226; |   |   |   |   | 2일 |
| VMware 모니터링(미리 보기) | Linux | &#8226; |   |   |   |   | 3분 |
| 실시간 데이터 |Windows(2012 R2/8.1 이상) |&#8226; |&#8226; |  |  |  | 1분 |


### <a name="automation--control"></a>Automation and Control

| 관리 솔루션 | 플랫폼 | Microsoft Monitoring Agent | Operations Manager 에이전트 | Azure 저장소 | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automation Hybrid Worker | Windows | &#8226; | &#8226; |   |   |   | 해당 없음 |
| 변경 내용 추적 |Windows |&#8226; |&#8226; |  |  |&#8226; |매시간 |
| 변경 내용 추적 |Linux |&#8226; |  |  |  |  |매시간 |
| 업데이트 관리 | Windows |&#8226; |&#8226; |  |  |&#8226; |하루에 최소 2회 및 업데이트를 설치하고 15분 후 |

### <a name="security--compliance"></a>보안 및 규정 준수

| 관리 솔루션 | 플랫폼 | Microsoft Monitoring Agent | Operations Manager 에이전트 | Azure 저장소 | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 맬웨어 방지 평가 |Windows |&#8226; |&#8226; |  |  |&#8226; |매시간 |
| 보안 및 감사<sup>1</sup> | Windows 및 Linux | 부분 | 부분 | 부분 |   | 부분 | 다양 |

<sup>1</sup>보안 및 감사 솔루션은 Windows, Operations Manager 및 Linux 에이전트에서 로그를 수집할 수 있습니다. 다음 항목에 대한 데이터 수집 정보는 [데이터 원본](#data-sources)을 참조하세요.

- syslog
- Windows 보안 이벤트 로그
- Windows 방화벽 로그
- Windows 이벤트 로그



### <a name="protection--recovery"></a>보호 및 복구

| 관리 솔루션 | 플랫폼 | Microsoft Monitoring Agent | Operations Manager 에이전트 | Azure 저장소 | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Backup | Azure |   |   |   |   |   | 해당 없음 |
| Azure Site Recovery | Azure |   |   |   |   |   | 해당 없음 |


### <a name="data-sources"></a>데이터 원본


| 데이터 원본 | 플랫폼 | Microsoft Monitoring Agent | Operations Manager 에이전트 | Azure 저장소 | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Azure 활동 로그 |Windows |  |  |  |  |  |알림 시 |
| Azure 진단 로그 |Windows |  |  |  |  |  |알림 시 |
| Azure 진단 메트릭 |Windows |  |  |  |  |  |알림 시 |
| ETW |Windows |  |  |&#8226; |  |  |5분 |
| IIS 로그 |Windows |&#8226; |&#8226; |&#8226; |  |  |5분 |
| 성능 카운터 |Windows |&#8226; |&#8226; |  |  |  |예약된 대로, 최소 10초 |
| 성능 카운터 |Linux |&#8226; |  |  |  |  |예약된 대로, 최소 10초 |
| syslog |Linux |&#8226; |  |  |  |  |Azure 저장소: 10분, 에이전트: 도착 시 |
| Windows 보안 이벤트 로그 |Windows |&#8226; |&#8226; |&#8226; |  |  |Azure 저장소: 10분, 에이전트: 도착 시 |
| Windows 방화벽 로그 |Windows |&#8226; |&#8226; |  |  |  |도착 시 |
| Windows 이벤트 로그 |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |Azure 저장소: 10분, 에이전트: 도착 시 |



## <a name="preview-management-solutions-and-features"></a>미리 보기 관리 솔루션 및 기능
서비스를 실행하고 DevOps 방식을 따라 고객과 연계하여 기능 및 솔루션을 개발할 수 있습니다.

비공개 미리 보기 중 소규모 고객 그룹에게 기능 또는 솔루션의 구현에 대한 액세스를 제공하여 피드백을 얻고 개선할 수 있습니다. 이 초기 구현에는 최소한의 기능 및 운영 기능이 있습니다.

우리의 목표는 작동하고 작동하지 않는 항목을 찾을 수 있도록 작업을 빠르게 시도하는 것입니다. 비공개 미리 보기 고객의 의견이 공개 미리 보기에 대한 준비가 되었다고 알릴 때까지 이 프로세스를 통해 반복합니다.

공개 미리 보기 동안 모든 사용자가 기능 또는 솔루션을 사용할 수 있도록 하여 더 많은 피드백을 받고 확장성 및 효율성의 유효성을 검사합니다. 이 단계 동안:

* 미리 보기 기능은 설정 탭에 표시되며 모든 사용자가 사용하도록 설정할 수 있습니다.
* 미리 보기 솔루션은 갤러리를 통해 또는 스크립트를 사용하여 추가할 수 있습니다.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>미리 보기 기능 및 솔루션에 대해 무엇을 알아야 하나요?
새로운 기능 및 관리 솔루션을 소개하게 되어 매우 기쁘게 생각하며 이를 개발하기 위해 고객과 함께 작업하고자 합니다.

미리 보기 기능 및 솔루션이 모든 사용자에 적합한 것은 아닙니다. 따라서 비공개 미리 보기에 참가를 요청하거나 공개 미리 보기를 사용하기 전에 개발 중인 제품에 문제가 없는지 확인하세요.

포털을 통해 미리 보기 기능을 사용하는 경우 기능이 미리 보기 상태임을 알리는 경고가 표시됩니다.

#### <a name="for-both-private-and-public-preview"></a>*비공개* 및 *공개* 미리 보기의 경우
다음 정보는 공개 및 비공개 미리 보기에 모두 적용됩니다.

* 항상 올바르게 작동하지 않을 수 있습니다.
  * 사소한 불편에서 전혀 작동하지 않는 것에 이르기까지 다양한 문제가 있을 수 있습니다.
* 미리 보기의 경우 시스템/환경에 부정적인 영향을 줄 수 있습니다.
  * 사용하는 시스템에 부정적인 영향이 발생하는 것을 방지하려고 노력하지만 때때로 예기치 않은 상황이 발생합니다.
* 데이터 손실.손상이 발생할 수 있습니다.
* 문제를 해결하기 위해 진단 로그 또는 다른 데이터를 수집하도록 요청할 수 있습니다.
* 기능 또는 솔루션은 제거될 수 있습니다(일시적으로 또는 영구적으로).
  * 미리 보기 중 알게 된 것을 기반으로 기능 또는 솔루션을 공개하지 않도록 결정할 수 있습니다.
* 미리 보기는 작동하지 않거나 모든 구성으로 테스트되지 않을 수 있으며 다음을 제한할 수도 있습니다.
  * 사용할 수 있는 운영 체제(예: 미리 보기 중에는 기능이 Linux에만 적용될 수 있음).
  * 사용할 수 있는 에이전트 종류(MMA, Operations Manager)(예: 미리 보기 중에는 기능이 Operations Manager와 함께 작동하지 않을 수 있음).  
* 미리 보기 솔루션 및 기능은 Service Level Agreement(서비스 수준 약정)가 적용되지 않습니다.
* 미리 보기 기능을 사용하면 사용 요금이 발생합니다.
* 기능 또는 기능에 필요한 역량/유용한 솔루션은 누락되거나 불완전할 수 있습니다.
* 기능/솔루션은 모든 지역에서 사용하지 못할 수도 있습니다.
* 기능/솔루션을 지역화되지 않을 수 없습니다.
* 기능/솔루션은 사용할 수 있는 고객 또는 장치 수에 제한이 있을 수 있습니다.
* 구성 작업을 수행하고 솔루션/기능을 사용하려면 스크립트를 사용해야 할 수 있습니다.
* 사용자 인터페이스(UI)는 완전하지 않으며 매일 변경될 수 있습니다.
* 공개 미리 보기는 프로덕션/중요 시스템에 적합하지 않을 수 있습니다.

#### <a name="for-private-preview"></a>*비공개* 미리 보기의 경우
위의 항목 외에 다음 정보가 비공개 미리 보기에 해당됩니다.

* 더 나은 기능/솔루션을 만들 수 있도록 경험에 대한 의견을 제공해 주시기를 기대합니다.
* 설문 조사, 전화 또는 전자 메일을 사용하여 피드백에 관해 연락을 드릴 수 있습니다.
* 기능이 항상 올바르게 작동하지는 않습니다.
* 참여에 대한 비밀 유지 계약(NDA)을 요구하거나 기밀 콘텐츠를 포함할 수 있습니다.
  * 블로그, 트윗 또는 제 3자와 소통하기 전에 미리 보기에 대한 프로그램 관리자 책임을 확인하여 공개에 대한 제한을 이해하세요.
* 프로덕션/중요 시스템에서 실행하지 마십시오.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>비공개 미리 보기 기능 및 솔루션에 대한 액세스를 어떻게 받습니까?
미리 보기에 따라 여러 가지 방법을 통해 비공개 미리 보기에 고객을 초대합니다.

* 월별 고객 설문 조사에 응답하고 연락할 권한을 제공하면 비공개 미리 보기에 초대될 가능성이 향상됩니다.
* Microsoft 계정 팀은 사용자를 지정할 수 있습니다.
* Twitter [msopsmgmt](https://twitter.com/msopsmgmt)에 게시된 세부 정보에 따라 등록할 수 있습니다.
* 자세한 공유 커뮤니티 이벤트에 따라 등록할 수 있습니다. 밋업, 회의 및 온라인 커뮤니티에서 찾아보세요.

## <a name="next-steps"></a>다음 단계
* [로그를 검색](log-analytics-log-searches.md)하여 솔루션에서 수집한 자세한 정보를 확인합니다.
