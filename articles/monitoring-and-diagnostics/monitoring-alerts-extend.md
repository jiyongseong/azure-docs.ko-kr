---
title: OMS 포털에서 Azure로 경고 확장(복사) - 개요 | Microsoft Docs
description: OMS 포털에서 Azure Alerts로 경고 복사 프로세스의 개요는 일반적인 고객 문제를 설명합니다.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: vinagara
ms.openlocfilehash: 25dcbad8607a651a7dd4b79f4f418cc473a2bf0e
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166502"
---
# <a name="extend-copy-alerts-from-oms-portal-into-azure"></a>OMS 포털에서 Azure로 경고 확장(복사)
OMS(Operations Management Suite) 포털만 Log Analytics 경고를 표시합니다.  새 경고 환경은 이제 Microsoft Azure의 다양한 서비스와 부분에서 경고 환경을 통합했습니다. Azure Portal에서 Azure Monitor 아래의 **경고**로 사용 가능한 새 환경은 활동 로그 경고, 메트릭 경고, Log Analytics의 로그 경고 및 Application Insights를 포함합니다. 


그러나 일부 사용자의 경우 Log Analytics 및 경고와 같은 관련된 기능의 사용은 [Microsoft OMS(Operations Management Suite) 포털](../operations-management-suite/operations-management-suite-overview.md)을 통합니다. 따라서 Log Analytics의 사용과 함께 기타 Azure 리소스를 쉽게 관리할 수 있도록 Microsoft는 체계적으로 OMS 포털의 기능을 Azure Portal에서도 사용할 수 있도록 해 왔습니다. 이와 관련하여 Azure 경고는 이미 사용자가 Log Analytics에 대한 쿼리 기반 경고를 관리할 수 있도록 허용합니다. 자세한 내용은 [Azure 경고의 로그 경고](monitor-alerts-unified-log.md)를 참조하세요. Azure Monitor의 경고에서 OMS 포털에서 생성된 경고는 이미 적절한 로그 분석 작업 영역 아래에 나열됩니다. 그러나 OMS 포털에서 생성된 이러한 경고에 대한 편집 또는 변경을 위해 사용자는 Azure를 중단하고 OMS 포털을 사용한 다음, 다른 서비스를 관리하는 데 필요한 경우 Azure로 다시 돌아와야 합니다. 이 피해를 줄이기 위해 Microsoft는 이제 사용자가 OMS 포털에서 Azure로 자신의 경고를 확장할 수 있도록 합니다.

## <a name="benefits-of-extending-your-alerts"></a>경고 확장의 이점
Azure Portal을 벗어날 필요가 없는 것에서 발생하는 이점 이외에 OMS 포털에서 Azure로 경고를 확장하는 다른 핵심적인 이점이 있습니다.

- 250개의 경고를 생성 및 볼 수 있는 OMS 포털에서와 달리 Azure Alerts에서는 이 제한 사항이 존재하지 않습니다.
- Azure 경고에서는 OMS 포털을 사용하는 경우의 Log Analytics 경고뿐만 아니라 모든 경고 유형을 관리, 열거 및 볼 수 있습니다.
- [Azure Monitor 역할](monitoring-roles-permissions-security.md)을 사용하여 모니터링 및 경고에 대한 사용자 액세스 제어
- Azure Alerts는 각 경고에 대해 SMS, 음성 통화, Automation Runbook, 웹후크, ITSM 커넥터 등을 포함하여 둘 이상의 작업을 가질 수 있도록 하는 [작업 그룹](monitoring-action-groups.md)을 활용합니다. 

## <a name="process-of-extending-your-alerts"></a>경고 확장의 프로세스
OMS 포털에서 Azure로 경고를 확장하는 프로세스는 어떠한 방법으로도 경고 정의, 쿼리 또는 구성 변경을 포함하지 **않습니다**. Azure에서 필요한 유일한 변경은 이메일 알림, 웹후크 통화, 자동화 Runbook 실행 또는 ITSM 도구에 연결과 같은 모든 작업은 작업 그룹을 통해 수행된다는 것입니다. 따라서 적절한 작업 그룹이 경고와 연결된 경우 Azure로 확장됩니다.

확장 프로세스는 비파괴적이며 방해하지 않으므로 Microsoft는 **2018년 5월 14일**부터 OMS 포털에서 생성된 경고를 Azure 경고로 자동으로 확장합니다. 이 날부터 Microsoft는 경고를 Azure로 확장하는 일정을 시작하고, 점진적으로 모든 경고를 OMS 포털에 표시하고, Azure Portal에서도 관리할 수 있도록 합니다. 

> [!NOTE]
> 2018년 5월 14일부터 Microsoft는 경고를 Azure로 확장하는 프로세스를 자동으로 시작합니다. 모든 작업 영역 및 경고가 확장되지는 않습니다. 대신 앞으로 몇 주 안에 Microsoft에서 전면적으로 경고를 자동으로 확장하기 시작할 것입니다. 따라서 OMS 포털의 경고는 2018년 5월 14일에 바로 Azure로 자동 확장되지 않으며, 사용자가 이 시간 동안 [경고를 수동으로 확장](monitoring-alerts-extend-tool.md)할 수 있습니다.

Log Analytics 작업 영역의 경고가 Azure로 확장에 대한 일정을 예약하면 계속해서 작동하고 어떤 방식으로든 모니터링을 손상하지 **않습니다**. 예약된 경우 경고는 일시적으로 수정/편집이 가능하지 않을 수 있지만 새 Azure 경고는 이 짧은 시간에 만들어지도록 계속할 수 있습니다. 이 짧은 기간 동안 경고의 모든 편집 또는 생성이 OMS 포털에서 수행되는 경우 사용자는 Azure Log Analytics 또는 Azure Alerts로 계속하는 옵션을 갖습니다.

 ![예약된 기간 동안 경고에 대한 사용자 작업은 Azure로 리디렉션됨](./media/monitor-alerts-extend/ScheduledDirection.png)

> [!NOTE]
> OMS 포털에서 Azure로 경고 확장은 요금이 청구되지 않으며 쿼리 기반 Log Analytics 경고에 대한 Azure 경고의 사용량은 [Azure Monitor 가격 책정 정책](https://azure.microsoft.com/pricing/details/monitor/)에 명시된 제한 및 조건 내에서 사용되는 경우 청구되지 않습니다.  

사용자는 자발적으로 해당 경고를 Azure에서 관리할 수 있도록 선택하여 이 날 짜 이전에 경고 확장의 이점을 활용할 수 있습니다.

### <a name="how-to-voluntarily-extending-your-alerts"></a>자발적으로 자신의 경고를 확장하는 방법
OMS 사용자가 Azure 경고로 쉽게 전달할 수 있도록 Microsoft는 경고 확장을 활성화하는 도구를 만들었습니다. Microsoft OMS 포털 고객은 OMS 포털의 마법사에서 또는 새로운 API를 사용하는 프로그래밍 방식으로 해당 경고를 Azure로 확장할 수 있습니다. 자세한 내용은 [OMS 포털 및 API를 사용하여 Azure로 경고 확장](monitoring-alerts-extend-tool.md)을 참조하세요.


## <a name="usage-after-extending-your-alerts"></a>경고 확장 후 사용
명시된 대로 Microsoft Operation Management Suite에서 생성된 경고는 Azure Alerts로 확장된 후 Azure에서 관리할 수 있습니다. 경고는 계속해서 OMS 포털 - 경고 설정 섹션에 나열됩니다. 아래에 나와 있습니다.

 ![Azure로 확장된 후 경고를 나열하는 OMS 포털](./media/monitor-alerts-extend/PostExtendList.png)

OMS 포털에서 수행된 편집 또는 생성과 같은 경고에 대한 모든 작업의 경우 사용자는 Azure Alerts로 투명하게 연결됩니다. 

> [!NOTE]
> OMS의 경고에 대한 추가 또는 편집 작업 시 사용자가 Azure로 투명하게 이동되므로 사용자는 [Azure Monitor 및 Azure 경고를 사용하기에 적절한 권한](monitoring-roles-permissions-security.md)으로 올바르게 매핑됩니다.

경고 만들기는 경고가 Azure로 확장된 후 약간만 변경하여 이전과 같이 기존 [Log Analytics API](../log-analytics/log-analytics-api-alerts.md) 및 [Log Analytics 리소스 템플릿](../monitoring/monitoring-solutions-resources-searches-alerts.md)에서 계속되며, 작업 그룹이 일정에 연결되어야 합니다.

## <a name="next-steps"></a>다음 단계

* [OMS에서 Azure로 경고 확장을 시작](monitoring-alerts-extend-tool.md)하는 도구를 알아봅니다.
* 새 [Azure 경고 환경](monitoring-overview-unified-alerts.md)에 대해 자세히 알아봅니다.
* [Azure Alerts의 로그 경고](monitor-alerts-unified-log.md)에 대해 알아봅니다.
