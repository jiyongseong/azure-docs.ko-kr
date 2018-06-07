---
title: SIEM 시스템과 Azure 리소스의 로그 통합 | Microsoft Docs
description: Azure Log Integration, 주요 기능 및 작동 원리에 대해 알아봅니다.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Azure Log Integration 소개

Azure Log Integration을 사용하여 Azure 리소스의 원시 로그를 온-프레미스 SIEM(보안 정보 및 이벤트 관리) 시스템과 통합할 수 있습니다. [Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md)에 대한 커넥터를 SIEM 공급업체에서 제공하지 않는 경우에만 Azure Log Integration을 사용합니다.

Azure 로그를 통합하는 가장 좋은 방법은 SIEM 공급업체의 Azure Monitor 커넥터를 사용하는 것입니다. 커넥터를 사용하려면 [데이터 이벤트 허브에 대한 모니터 스트림 모니터링](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)의 지침을 따르세요. 

하지만, SIEM 공급업체에서 Azure Monitor에 대한 커넥터를 제공하지 않는 경우에는 커넥터를 사용할 수 있을 때까지 임시 해결책으로 Azure Log Integration을 사용할 수 있습니다. Azure Log Integration에서 SIEM을 지원하는 경우에만 Azure Log Integration을 옵션으로 사용할 수 있습니다.

> [!IMPORTANT]
> 가상 머신 로그를 수집하는 것이 주요 관심사인 경우, 이 옵션은 대부분의 SIEM 공급업체 솔루션에 포함됩니다. SIEM 공급업체의 커넥터를 사용하는 것이 항상 기본 대안입니다.

Azure Log Integration은 Azure 리소스에서 Windows 이벤트 뷰어 로그, [Azure 활동 로그](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center 경고](../security-center/security-center-intro.md) 및 [Azure 진단 로그](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)의 Windows 이벤트를 수집합니다. 통합을 통해 SIEM 솔루션에서 온-프레미스 또는 클라우드에 있는 모든 자산에 대한 통합 대시보드를 제공할 수 있습니다. 대시보드를 사용하여 보안 이벤트에 대한 경고를 수신, 집계, 상호 연결 및 분석할 수 있습니다.

> [!NOTE]
> 현재 Azure Log Integration은 Azure 상용 클라우드 및 Azure Government 클라우드만 지원합니다. 다른 클라우드는 지원되지 않습니다.

![Azure Log Integration 프로세스 다이어그램][1]

## <a name="what-logs-can-i-integrate"></a>어떤 로그를 통합할 수 있나요?

Azure에서는 각 Azure 서비스에 대해 광범위한 로깅을 생성합니다. 로그는 다음 세 가지 로그 유형을 나타냅니다.

* **컨트롤/관리 로그**: [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) CREATE, UPDATE 및 DELETE 작업에 대한 가시성을 제공합니다. Azure 활동 로그가 이 로그 유형에 속합니다.
* **데이터 평면 로그**: Azure 리소스를 사용할 때 발생하는 이벤트에 대한 가시성을 제공합니다. 이 로그 유형의 예는 Windows 가상 컴퓨터의 Windows 이벤트 뷰어에 있는 **시스템**, **보안** 및 **응용 프로그램** 채널이 있습니다. 또 다른 예는 Azure Monitor를 통해 구성하는 Azure 진단 로깅입니다.
* **처리된 이벤트**: 사용자를 위해 처리된 경고 정보 및 분석된 이벤트를 제공합니다. 이 이벤트 유형의 예는 Azure Security Center 경고입니다. Azure Security Center에서는 구독을 처리하고 분석하여 현재 보안 상태에 관련된 경고를 제공합니다.

Azure 로그 통합은 ArcSight, QRadar 및 Splunk를 지원합니다. SIEM 공급업체에 문의하여 공급업체에 기본 커넥터가 있는지 평가합니다. 기본 커넥터를 사용할 수 있는 경우, Azure Log Integration을 사용하지 마세요.

사용할 수 있는 다른 옵션이 없는 경우에만 Azure Log Integration을 사용하는 것이 좋습니다. 다음 표에는 권장 사항이 포함되어 있습니다.

|SIEM | 고객이 Azure 로그 통합자를 이미 사용 중인 경우 | 고객이 SIEM 통합 옵션을 조사 중인 경우|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | [Splunk용 Azure Monitor 추가 기능](https://splunkbase.splunk.com/app/3534/)으로 마이그레이션을 시작합니다. | [Splunk 커넥터](https://splunkbase.splunk.com/app/3534/)를 사용합니다. |
|**QRadar** | [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)의 마지막 섹션에 문서화된 QRadar 커넥터로 마이그레이션하거나 사용을 시작합니다. | [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)의 마지막 섹션에 문서화된 QRadar 커넥터를 사용합니다. |
|**ArcSight** | 커넥터를 사용할 수 있을 때까지 Azure 로그 통합자를 계속 사용합니다. 그런 다음, 커넥터 기반 솔루션으로 마이그레이션합니다.  | Azure Log Analytics는 대안으로 사용하는 것이 좋습니다. 커넥터가 제공될 때 마이그레이션 프로세스를 수행할 의향이 없다면 Azure Log Integration에 등록하지 마세요. |

> [!NOTE]
> Azure Log Integration은 무료 솔루션이지만 로그 파일 정보 저장소와 관련된 Azure Storage 비용이 발생합니다.

도움이 필요하면 [지원 요청](../azure-supportability/how-to-create-azure-support-request.md)을 만들 수 있습니다. 서비스에서 **Log Integration**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Log Integration을 소개합니다. Azure Log Integration 및 지원되는 로그 유형에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Log Integration 시작](security-azure-log-integration-get-started.md). 이 자습서에서는 Azure Log Integration 설치 과정을 안내합니다. MAD(Microsoft Azure 진단) 저장소, Azure 활동 로그, Azure Security Center 경고 및 Azure Active Directory 감사 로그의 로그를 통합하는 방법에 대해서도 설명합니다.
* [Azure Log Integration FAQ(질문과 대답)](security-azure-log-integration-faq.md) 이 FAQ는 Azure Log Integration에 대해 자주 묻는 질문과 대답입니다.
* [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)하는 방법에 대해 자세히 알아봅니다.

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
