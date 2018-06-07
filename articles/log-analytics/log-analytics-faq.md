---
title: Log Analytics FAQ | Microsoft Docs
description: Azure Log Analytics 서비스에 대해 자주 묻는 질문에 대한 답변입니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 22da58df653b31c46145ebbbd1f6f6a26b0e9f29
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="log-analytics-faq"></a>Log Analytics FAQ
Microsoft FAQ는 Microsoft Azure의 Log Analytics에 대해 자주 묻는 질문의 목록입니다. Log Analytics에 대한 추가 질문이 있으면 [토론 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="general"></a>일반

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. Log Analytics는 Azure Security Center와 같은 에이전트를 사용합니까?

a. 2017년 6월 초에 Azure Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집하고 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션 FAQ](../security-center/security-center-platform-migration-faq.md)를 참조하세요.

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. AD 및 SQL 평가 솔루션에서 수행하는 검사는 무엇입니까?

A. 다음 쿼리는 현재 수행하는 모든 검사에 대한 설명을 보여 줍니다.

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

추가 검토를 위해 결과를 Excel로 내보낼 수 있습니다.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>Q: System Center Operations Manager 콘솔에서 OMS와 다르게 표시되는 이유는 무엇입니까?

A: 사용 중인 Operations Manager의 업데이트 롤업에 따라 *System Center Advisor*, *Operational Insights* 또는 *Log Analytics*에 대한 노드가 표시될 수 있습니다.

*OMS* 에 대한 텍스트 문자열 업데이트가 수동으로 가져와야 하는 관리 팩에 포함됩니다. 현재 텍스트 및 기능을 보려면 최신 System Center Operations Manager 업데이트 롤업 기술 자료 문서의 지침을 따르고 콘솔을 새로 고칩니다.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>Q: Log Analytics의 온-프레미스 버전이 있나요?

A: 아니요. Log Analytics는 대량의 데이터를 처리 및 저장하는 확장성 있는 클라우드 서비스입니다. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Q. Log Analytics에서 더 이상 데이터를 수집하지 않는 경우 어떻게 해결하나요?

A: 2018년 4월 2일 전에 생성된 작업 영역 및 구독이 *무료* 가격 책정 계층에 있는 경우 일당 500MB 초과의 데이터가 전송된다면 당일 나머지 시간 동안 데이터 수집은 중지합니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다.  

Log Analytics는 *Heartbeat* 형식의 이벤트를 만들며, 데이터 수집이 중지되었는지 여부를 확인하는 데 사용할 수 있습니다. 

일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. `Heartbeat | summarize max(TimeGenerated)`

특정 컴퓨터를 확인하려면 다음 쿼리를 실행합니다. `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

선택한 시간 범위에 따라 데이터 수집이 중지되면 반환되는 모든 레코드가 표시되지 않습니다.   

다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.

| 데이터 수집 중지 이유                       | 데이터 수집을 다시 시작하려면 |
| -------------------------------------------------- | ----------------  |
| 무료 데이터의 한도 도달<sup>1</sup>       | 수집이 다음 달에 자동으로 다시 시작될 때까지 대기 또는<br> 유료 가격 책정 계층으로 변경 |
| Azure 구독이 다음으로 인해 일시 중단된 상태: <br> 평가판 종료 <br> Azure 암호 만료 <br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)                          | 유료 구독으로 전환 <br> 유료 구독으로 전환 <br> 한도 제거 또는 한도가 재설정될 때까지 대기 |

<sup>1</sup> 작업 영역이 *무료* 가격 책정 계층에 있는 경우 일당 500MB의 데이터를 서비스에 전송하도록 제한됩니다. 일일 한도에 도달하면 데이터 수집이 다음 날까지 중지됩니다. 데이터 수집이 중지되는 동안 전송된 데이터가 인덱싱되지 않으며 검색에 사용할 수 없습니다. 데이터 수집이 다시 시작되면 전송된 새 데이터에 대해서만 프로세스가 발생합니다. 

Log Analytics는 UTC 시간을 사용하고 매일 UTC 자정에 시작됩니다. 작업 영역이 일일 한도에 도달하면 다음 UTC 날의 처음 한 시간 동안 프로세스가 다시 시작됩니다.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. 데이터 수집이 중지될 때 알림을 받을 수 있는 방법은 무엇인가요?

A: [경고 규칙 만들기](log-analytics-alerts-creating.md#create-an-alert-rule)에 설명한 단계를 사용하여 데이터 수집이 중단되는 경우 알림을 받을 수 있습니다.

데이터 수집이 중단되는 경우에 대한 경고를 만드는 시기는 다음을 설정합니다.
- **이름**을 *데이터 수집 중지됨*으로
- **심각도**를 *경고*로
- **쿼리 검색**을 `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`으로
- **시간 창**을 *30분*으로.
- **경고 빈도**를 매 *10*분으로.
- **경고 생성 기준**을 *결과의 수*로
- **결과 수**를 *0보다 큼*으로

이 경고는 15분 이상에 하트비트가 누락된 경우에만 쿼리가 결과를 반환할 때 발생합니다.  [경고 규칙에 작업 추가](log-analytics-alerts-actions.md)에 설명한 단계를 사용하여 경고 규칙에 전자 메일, 웹후크 또는 Runbook 작업을 구성합니다.

## <a name="configuration"></a>구성
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. WAD(Azure 진단)에서 읽어오는 데 사용되는 테이블/Blob 컨테이너의 이름을 변경할 수 있나요?

a. 아니요, 현재 Azure 저장소의 임의 테이블 또는 컨테이너에서 읽을 수 없습니다.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Log Analytics 서비스에서 사용하는 IP 주소는 무엇인가요? 내 방화벽에서 Log Analytics 서비스에 대한 트래픽만 허용하도록 하려면 어떻게 하나요?

a. Log Analytics 서비스는 Azure를 기반으로 빌드됩니다. Log Analytics IP 주소는 [Microsoft Azure 데이터 센터 IP 범위](http://www.microsoft.com/download/details.aspx?id=41653)에 있습니다.

서비스 배포가 수행되면서 Log Analytics 서비스의 실제 IP 주소가 변경됩니다. 방화벽을 통과하도록 허용하는 DNS 이름은 [시스템 요구 사항](log-analytics-concept-hybrid.md#prerequisites)에 문서화됩니다.

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. ExpressRoute를 사용하여 Azure에 연결합니다. Log Analytics 트래픽이 내 ExpressRoute 연결을 사용하나요?

a. 여러 유형의 ExpressRoute 트래픽이 [ExpressRoute 설명서](../expressroute/expressroute-faqs.md#supported-services)에 나와 있습니다.

Log Analytics에 대한 트래픽은 공용 피어링 ExpressRoute 회로를 사용합니다.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. 기존 Log Analytics 작업 영역을 다른 Log Analytics 작업 영역/Azure 구독으로 이동할 간단하고 쉬운 방법이 있나요?

a. `Move-AzureRmResource` cmdlet을 사용하면 Log Analytics 작업 영역을 이동할 수 있으며 한 Azure 구독에서 다른 구독으로 자동화 계정도 이동할 수 있습니다. 자세한 내용은 [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)를 참조하세요.

이러한 변경은 Azure 포털에서 이루어집니다.

데이터를 한 Log Analytics 작업 영역에서 다른 작업 영역으로 이동하거나 Log Analytics 데이터가 저장된 지역을 변경할 수 없습니다.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>Q: Log Analytics를 System Center Operations Manager에 추가하려면 어떻게 하나요?

A: 최신 업데이트 롤업으로 업데이트 및 관리 팩 가져오기를 통해 Operations Manager를 Log Analytics에 연결할 수 있습니다.

>[!NOTE]
>Log Analytics에 Operations Manager 연결은 System Center Operations Manager 2012 SP1 이상에서만 사용할 수 있습니다.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>Q: 에이전트가 Log Analytics와 통신할 수 있는지 어떻게 확인하나요?

A: 에이전트가 OMS와 통신할 수 있는지 확인하려면 제어판, 보안 및 설정, **Microsoft Monitoring Agent**로 이동합니다.

**Azure Log Analytics(OMS)** 탭 아래에서 녹색 확인 표시를 찾습니다. 녹색 확인 표시 아이콘은 에이전트가 Azure 서비스와 통신할 수 있음을 확인합니다.

노란색 경고 아이콘은 에이전트가 Log Analytics와 통신하는 데 문제가 있음을 의미합니다. 한 가지 일반적인 이유는 Microsoft Monitoring Agent 서비스가 중지되었기 때문입니다. 서비스 제어 관리자를 사용하여 서비스를 다시 시작합니다.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>Q: 에이전트가 Log Analytics와의 통신을 중지하도록 하려면 어떻게 하나요?

A: System Center Operations Manager의 OMS 관리 컴퓨터 목록에서 컴퓨터를 제거합니다. Operations Manager는 Log Analytics에 더 이상 보고하지 않도록 에이전트의 구성을 업데이트합니다. Log Analytics에 직접 연결된 에이전트인 경우 제어판, 보안 및 설정, **Microsoft Monitoring Agent**를 통해 통신을 중지할 수 있습니다.
**Azure Log Analytics(OMS)**아래에서 나열된 모든 작업 영역을 제거합니다.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>Q: 내 작업 영역을 특정 Azure 구독에서 다른 구독으로 이동하려고 하는 경우 오류가 발생하는 이유는 무엇인가요?

A: Azure Portal을 사용하는 경우 이동에 대해 작업 영역만 선택되었는지 확인합니다. 솔루션을 선택하지 마십시오. 작업 영역이 이동한 후 자동으로 이동합니다. 

두 Azure 구독에서 권한을 갖는지 확인합니다.

## <a name="agent-data"></a>에이전트 데이터
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. 에이전트를 통해 Log Analytics로 얼마나 많은 데이터를 보낼 수 있나요? 고객 한 명당 최대 데이터 용량이 있나요?
a. 무료 요금제에서는 작업 영역당 일일 용량을 500MB로 설정합니다. 표준 및 프리미엄 요금제에는 업로드되는 데이터 양에 제한이 없습니다. 클라우드 서비스로 Log Analytics는 고객의 데이터 볼륨(일일 테라바이트 단위까지)을 처리하도록 자동 강화되도록 설계되었습니다.

Log Analytics 에이전트는 작은 공간을 갖도록 설계되었습니다. 데이터 볼륨은 사용하도록 설정한 솔루션에 따라 다릅니다. [사용량](log-analytics-usage.md) 페이지에서 데이터 볼륨에 대한 자세한 정보를 확인하고 솔루션별로 정리된 내용을 볼 수 있습니다.

자세한 내용은 OMS 에이전트의 리소스 사용률(공간)을 평가한 후 해당 결과를 보여주는 [고객 블로그](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)를 참조할 수 있습니다.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. 데이터를 Log Analytics로 전송할 때 Microsoft Management Agent(MMA)에 사용된 네트워크 대역폭은 얼마나 되나요?

a. 대역폭은 전송된 데이터 양에 대한 기능입니다. 네트워크를 통해 데이터가 전송되는 동안 데이터가 압축됩니다.

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. 에이전트당 얼마나 많은 데이터가 전송되나요?

a. 에이전트당 전송되는 데이터의 양에 따라 달라집니다.

* 사용하도록 설정한 솔루션
* 수집되는 로그 및 성능 카운터 수
* 로그에 있는 데이터 볼륨

무료 가격 책정 계층은 여러 서버에 등록하고 일반적인 데이터 볼륨을 측정하는 좋은 방법입니다. 전체 사용 현황은 [사용량](log-analytics-usage.md) 페이지에 표시됩니다.

WireData 에이전트를 실행할 수 있는 컴퓨터의 경우 다음 쿼리를 사용하여 전송 중인 데이터의 양을 확인합니다.

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>다음 단계
* [Log Analytics 시작](log-analytics-get-started.md) 에서 Log Analytics에 대한 정보와 Log Analytics를 몇 분 만에 시작 및 실행하는 방법에 대해 알아보세요.
