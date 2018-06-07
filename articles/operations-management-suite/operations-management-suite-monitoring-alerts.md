---
title: "Microsoft 모니터링 제품의 경고 관리 | Microsoft Docs"
description: "경고는 관리자가 주목해야 하는 문제를 나타냅니다.  이 문서는 SCOM(System Center Operations Manager)과 Log Analytics에서 경고를 생성 및 관리하는 방식의 차이에 대해 설명하며 하이브리드 경고 관리 전략에 두 가지 제품을 활용하는 모범 사례를 제공합니다."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Microsoft 모니터링으로 경고 관리
경고는 관리자가 주목해야 하는 문제를 나타냅니다.  OMS(Operations Management Suite)의 Log Analytics 및 SCOM(System Center Operations Manager)은 경고의 생성 방식, 관리 및 분석 방식, 중요 문제가 검색되었음을 알리는 방식에서 분명한 차이가 있습니다.

## <a name="alerts-in-operations-manager"></a>Operations Manager의 경고
SCOM의 경고는 특정 문제를 나타내기 위해 개별 규칙 또는 모니터에 의해 생성됩니다.  모니터는 오류 상태가 될 때 경고를 발생하지만 규칙은 관리되는 개체의 상태와 직접적 관련이 없는 일부 중요 문제를 나타내기 위해 경고를 발생할 수 있습니다.  관리 팩에는 관리하는 응용 프로그램 또는 서비스에 대해 경고를 생성하는 다양한 워크플로가 포함되어 있습니다.  새 관리 팩을 구성하는 프로세스 중에는 중요하지 않다고 판단하는 문제에 대해 과도한 경고를 수신하지 않도록 조정하는 기능이 포함되어 있습니다.

![SCOM 경고 보기](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM은 관리자가 문제 해결을 위해 작업하면서 상태를 변경할 수 있는 경고를 포함하여 완전한 경고 관리를 제공합니다.  문제가 해결되면 관리자가 경고를 닫도록 설정합니다. 그러면 미해결 경고가 더 이상 보기에 표시되지 않습니다.  모니터에서 생성된 경고는 모니터가 정상 상태로 돌아가면 자동으로 해결될 수 있습니다.

![경고 상태](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Log Analytics의 경고
Log Analytics의 경고는 정기 간격으로 자동 실행되는 로그 쿼리에서 생성됩니다.  모든 로그 쿼리에서 경고 규칙을 만들 수 있습니다.  쿼리에서 사용자가 지정한 기준과 일치하는 결과를 반환하면 경고가 생성됩니다.  이 경우 특정 이벤트가 검색될 경우 경고를 생성하는 특정 쿼리일 수 있으며 특정 응용 프로그램과 관련된 오류 이벤트를 찾는 더 일반적인 쿼리를 사용할 수도 있습니다.

Log Analytics 경고는 OMS 리포지토리에 이벤트로 기록되며 로그 쿼리로 검색할 수 있습니다.  이러한 경고는 문제가 해결된 때는 나타낼 수 있도록 SCOM 이벤트와 같은 상태가 없습니다.

![OMS 경고](media/operations-management-suite-monitoring-alerts/oms-alert.png)

SCOM이 Log Analytics의 데이터 원본으로 사용된 경우 SCOM 경고가 생성 및 수정될 때 OMS 리포지토리에 기록됩니다.  

![SCOM 경고](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[경고 관리 솔루션](http://technet.microsoft.com/library/mt484092.aspx) 은 미해결 경고와 다양한 경고 집합을 검색하는 몇 가지 일반 쿼리를 요약하여 제공합니다.  이 정보를 사용하여 SCOM의 보고서보다 경고를 더욱 효과적으로 분석할 수 있습니다.  요약을 상세 데이터로 드릴다운하고 임시 쿼리를 만들어 다양한 경고 집합을 검색할 수 있습니다.

![경고 관리 솔루션](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>알림
SCOM의 알림은 특정 기준과 일치하는 경고에 대응하여 메일 또는 텍스트를 전송합니다.  개체를 모니터링하면서 경고의 심각도, 검색된 문제의 종류, 시간 등의 기준에 따라 다른 사람에게 알림을 전송하는 다양한 알림 구독을 만들 수 있습니다.

많은 수의 관리 팩에 대해 완전한 알림 전략을 구현하려면 적은 수의 구독을 사용합니다.

![SCOM 경고](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

각 [경고 규칙](http://technet.microsoft.com/library/mt614775.aspx)에 전자 메일 알림 작업을 설정하면 Log Analytics에서 경고가 생성되었음을 메일을 통해 알릴 수 있습니다.  Log Analytics에는 SCOM과 같이 단일 규칙으로 여러 경고를 구독하는 기능이 없습니다.  또한 OMS는 사전 구성을 제공하지 않으므로 고유한 경고 규칙을 만들어야 합니다.

![Log Analytics rudrh](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

SCOM 경고는 운영 콘솔에서만 수정할 수 있기 때문에 Log Analytics에서 SCOM 경고를 완전히 관리할 수 없습니다.  하지만 Log Analytics는 SCOM에만 없는 분석 기능을 제공하므로 경고 관리 프로세스에 유용합니다.

## <a name="alert-remediation"></a>경고 수정
[수정](http://technet.microsoft.com/library/mt614775.aspx) 은 경고에서 식별한 문제를 자동으로 수정하려는 시도를 나타냅니다.

SCOM에서는 비정상 상태로 전환된 모니터에 대응하여 진단 및 복구를 실행할 수 있습니다.  이 작업은 모니터가 경고를 생성하는 작업과 동시에 실행됩니다.  진단 및 복구는 일반적으로 에이전트에서 실행되는 스크립트로 구현됩니다.  진단은 검색된 문제에 대한 자세한 정보를 수집하려는 시도를 하며 복구는 문제를 해결하려는 시도를 합니다.

Log Analytics에서는 Log Analytics 경고에 대응하여 [Azure Automation Runbook](https://azure.microsoft.com/documentation/services/automation/)을 시작하거나 웹후크를 호출할 수 있습니다.  Runbook에는 PowerShell에 구현된 복잡한 논리를 포함할 수 있습니다.  스크립트는 Azure에서 실행되며 클라우드에서 사용 가능한 Azure 리소스 또는 외부 리소스에 액세스할 수 있습니다.  Azure 자동화는 로컬 데이터 센터의 서버에서 Runbook을 실행할 수 있지만 현재 이 기능은 Log Analytics 경고에 대응하여 Runbook을 시작할 경우에는 사용할 수 없습니다.

SCOM의 복구와 OMS의 Runbook 모두 PowerShell 스크립트를 포함할 수 있지만 복구는 반드시 관리 팩에 포함해야 하기 때문에 생성 및 관리가 훨씬 더 어렵습니다.  Runbook은 Runbook 작성, 테스트, 관리 기능을 제공하는 Azure 자동화에 저장합니다.

Log Analytics의 데이터 원본으로 SCOM을 사용하는 경우 OMS 리포지토리에 저장된 SCOM 경고를 검색하려면 로그 쿼리를 사용하여 Log Analytics 경고를 만듭니다.  그러면 SCOM 경고에 대응하여 Azure 자동화 Runbook을 실행할 수 있습니다.  물론 Runbook은 Azure에서 실행되기 때문에 온-프레미스 문제를 복구하는 전략으로는 적합하지 않습니다.

## <a name="next-steps"></a>다음 단계
* [SCOM(System Center Operations Manager)의 경고](https://technet.microsoft.com/library/hh212913.aspx)에 대해 자세히 알아봅니다.

