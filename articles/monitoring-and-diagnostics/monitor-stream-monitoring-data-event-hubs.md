---
title: Event Hubs로 Azure 모니터링 데이터 스트리밍 | Microsoft Docs
description: 모든 Azure 모니터링 데이터를 이벤트 허브에 스트리밍하여 파트너 SIEM 또는 분석 도구로 데이터를 가져오는 방법을 알아봅니다.
author: johnkemnetz
manager: robb
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/05/2018
ms.author: johnkem
ms.openlocfilehash: 9cc4eb8d8f1494a7ea7a63297751f8e251aedf05
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>이벤트 허브로 Azure 모니터링 데이터를 스트리밍하여 외부 도구에서 사용

Azure Monitor는 Azure 환경의 모든 모니터링 데이터에 액세스할 수 있는 단일 파이프라인을 제공하므로 파트너 SIEM 및 모니터링 도구를 쉽게 설정하여 해당 데이터를 사용할 수 있습니다. 이 문서에서는 외부 도구에서 수집할 수 있는 단일 Event Hubs 네임스페이스 또는 이벤트 허브로 보내기 위해 Azure 환경에서 여러 계층의 데이터를 설정하는 과정을 안내합니다.

## <a name="what-data-can-i-send-into-an-event-hub"></a>이벤트 허브에 어떤 데이터를 보낼 수 있나요? 

Azure 환경에서 모니터링 데이터에는 여러 '계층'이 있으며, 각 계층에서 데이터에 액세스하는 방법은 약간 다릅니다. 이러한 계층은 일반적으로 다음과 같이 설명할 수 있습니다.

- **응용 프로그램 모니터링 데이터:** Azure에서 작성하고 실행되는 코드의 성능 및 기능에 대한 데이터입니다. 응용 프로그램 모니터링 데이터의 예로 성능 추적, 응용 프로그램 로그 및 사용자 원격 분석이 있습니다. 응용 프로그램 모니터링 데이터는 일반적으로 다음 방법 중 하나로 수집됩니다.
  - [Application Insights SDK](../application-insights/app-insights-overview.md)와 같은 SDK를 사용하여 코드를 계측합니다.
  - 응용 프로그램이 실행되고 있는 컴퓨터에서 새 응용 프로그램 로그를 수신 대기하는 모니터링 에이전트(예: [Windows Azure 진단 에이전트](./azure-diagnostics.md) 또는 [Linux Azure 진단 에이전트](../virtual-machines/linux/diagnostic-extension.md))를 실행합니다.
- **게스트 OS 모니터링 데이터:** 응용 프로그램이 실행되고 있는 운영 체제에 대한 데이터입니다. 게스트 OS 모니터링 데이터의 예로 Linux syslog 또는 Windows 시스템 이벤트가 있습니다. 이러한 유형의 데이터를 수집하려면 [Windows Azure 진단 에이전트](./azure-diagnostics.md) 또는 [Linux Azure 진단 에이전트](../virtual-machines/linux/diagnostic-extension.md)와 같은 에이전트를 설치해야 합니다.
- **Azure 리소스 모니터링 데이터:** Azure 리소스의 작업에 대한 데이터입니다. 가상 머신과 같은 일부 Azure 리소스 종류의 경우, Azure 서비스 내부에서 모니터링할 게스트 OS 및 응용 프로그램이 있습니다. 네트워크 보안 그룹과 같은 다른 Azure 리소스의 경우, 해당 리소스에서 실행되는 게스트 OS 또는 해당 응용 프로그램이 없으므로 리소스 모니터링 데이터가 사용할 수 있는 가장 높은 수준의 데이터입니다. 이 데이터는 [리소스 진단 설정](./monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings)을 사용하여 수집할 수 있습니다.
- **Azure 플랫폼 모니터링 데이터:** Azure 구독 또는 테넌트의 운영 및 관리에 대한 데이터와 Azure 자체의 상태 및 작업에 대한 데이터입니다. 플랫폼 모니터링 데이터의 예로 서비스 상태 데이터 및 Active Directory 감사를 포함한 [활동 로그](./monitoring-overview-activity-logs.md)가 있습니다. 이 데이터도 진단 설정을 사용하여 수집할 수 있습니다.

모든 계층의 데이터는 이벤트 허브로 보낼 수 있으며, 그런 다음 이 이벤트 허브에서 파트너 도구로 끌어올 수 있습니다. 다음 섹션에서는 각 계층의 데이터를 이벤트 허브로 스트리밍하도록 구성하는 방법에 대해 설명합니다. 이러한 단계에서는 모니터링할 자산이 해당 계층에 이미 있다고 가정합니다.

## <a name="set-up-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 설정

시작하기 전에 먼저 [Event Hubs 네임스페이스 및 이벤트 허브](../event-hubs/event-hubs-create.md)를 만들어야 합니다. 이 네임스페이스 및 이벤트 허브는 모든 모니터링 데이터에 대한 대상입니다. Event Hubs 네임스페이스는 같은 액세스 정책을 공유하는 Event Hubs의 논리적 그룹으로, 저장소 계정은 해당 저장소 계정 내에서 개별 Blob을 갖기 쉽습니다. Event Hubs 네임스페이스 및 만드는 Event Hubs에 대한 몇 가지 세부 사항을 적어두세요.
* 표준 Event Hubs 네임스페이스를 사용하는 것이 좋습니다.
* 일반적으로 하나의 처리량 단위만 필요합니다. 로그 사용량이 증가함에 따라 강화해야 할 경우, 항상 나중에 네임스페이스에 대한 처리량 단위 수를 수동으로 늘리거나 자동 인플레이션을 사용하도록 설정할 수 있습니다.
* 처리량 단위 수를 사용하여 이벤트 허브에 대한 처리량 비율을 높일 수 있습니다. 파티션 수를 사용하면 많은 소비자 간에 소비량의 균형을 이룰 수 있습니다. 단일 파티션으로 최대 20MBps, 초당 약 20,000개의 메시지까지 처리할 수 있습니다. 데이터를 소비하는 도구에 따라, 여러 파티션을 통한 소비를 지원할 수도있 고 지원하지 않을 수도 있습니다. 설정할 파티션 수를 잘 모를 경우 4개의 파티션부터 시작하는 것이 좋습니다.
* 이벤트 허브의 메시지 보존 기간을 7일로 설정하는 것이 좋습니다. 소비 도구가 하루 넘게 다운될 경우 이러한 기능을 통해 도구가 중단된 지점을 선택하도록 할 수 있습니다(최대 7일이 경과된 이벤트).
* 이벤트 허브에 대한 기본 소비자 그룹을 사용하는 것이 좋습니다. 2개의 다른 도구에서 동일한 이벤트 허브의 동일한 데이터를 소비하려는 경우가 아니면, 다른 소비자 그룹을 만들거나 별도의 소비자 그룹을 사용할 필요가 없습니다.
* Azure 활동 로그의 경우 Event Hubs 네임스페이스를 선택하면, Azure Monitor는 해당 네임스페이스 내에 ‘insights-logs-operationallogs’라는 이벤트 허브를 만듭니다. 다른 로그 형식의 경우, 기존 이벤트 허브에서 선택하거나(동일한 insights-logs-operationallogs 이벤트 허브를 재사용할 수 있음) Azure Monitor에서 로그 범주별로 이벤트 허브를 만들도록 할 수 있습니다.
* 일반적으로 이벤트 허브의 데이터를 사용하는 컴퓨터에서 포트 5671 및 5672를 열어야 합니다.

[Azure Event Hubs FAQ](../event-hubs/event-hubs-faq.md)도 참조하세요.

## <a name="how-do-i-set-up-azure-platform-monitoring-data-to-be-streamed-to-an-event-hub"></a>Azure 플랫폼 모니터링 데이터를 이벤트 허브로 스트리밍하도록 설정하려면 어떻게 해야 할까요?

Azure 플랫폼 모니터링 데이터는 다음 두 가지 주요 원본에서 제공됩니다.
1. [Azure 활동 로그](./monitoring-overview-activity-logs.md): Resource Manager의 만들기, 업데이트 및 삭제 작업, 구독의 리소스에 영향을 줄 수 있는 [Azure 서비스 상태](../service-health/service-health-overview.md)의 변경 내용, [리소스 상태](../service-health/resource-health-overview.md) 전환 및 기타 여러 유형의 구독 수준 이벤트가 포함되어 있습니다. [이 문서에서는 Azure 활동 로그에 나타나는 모든 범주의 이벤트에 대해 자세히 설명합니다](./monitoring-activity-log-schema.md).
2. [Azure Active Directory 보고](../active-directory/active-directory-reporting-azure-portal.md): 로그인 활동 및 특정 테넌트 내의 변경 감사 내역에 대한 기록이 포함되어 있습니다. Azure Active Directory 데이터는 아직 이벤트 허브로 스트리밍할 수 없습니다.

### <a name="stream-azure-activity-log-data-into-an-event-hub"></a>Azure 활동 로그 데이터를 이벤트 허브로 스트리밍

Azure 활동 로그에서 Event Hubs 네임스페이스로 데이터를 보내려면 구독에 로그 프로필을 설정해야 합니다. [이 가이드에 따라](./monitoring-stream-activity-logs-event-hubs.md) 구독에 로그 프로필을 설정합니다. 이 작업은 모니터링하려는 구독별로 한 번만 수행합니다.

> [!TIP]
> 로그 프로필에서는 현재 'insights-operational-logs'라는 이름으로 이벤트 허브를 만드는 Event Hubs 네임스페이스만 선택할 수 있습니다. 사용자 고유의 이벤트 허브 이름은 아직 로그 프로필에 지정할 수 없습니다.

## <a name="how-do-i-set-up-azure-resource-monitoring-data-to-be-streamed-to-an-event-hub"></a>Azure 리소스 모니터링 데이터를 이벤트 허브로 스트리밍하도록 설정하려면 어떻게 해야 할까요?

Azure 리소스는 두 가지 유형의 모니터링 데이터를 내보냅니다.
1. [리소스 진단 로그](./monitoring-overview-of-diagnostic-logs.md)
2. [metrics](monitoring-overview-metrics.md)

두 가지 유형의 데이터는 리소스 진단 설정을 사용하여 이벤트 허브로 전송됩니다. [이 가이드에 따라](./monitoring-stream-diagnostic-logs-to-event-hubs.md) 특정 리소스에 대한 리소스 진단 설정을 지정합니다. 로그를 수집하려는 각 리소스에 대한 리소스 진단 설정을 지정합니다.

> [!TIP]
> [정책 규칙에서 DeployIfNotExists 효과를 사용하여](../azure-policy/policy-definition.md#policy-rule) Azure Policy를 통해 특정 범위 내의 모든 리소스가 항상 진단 설정으로 지정되도록 할 수 있습니다. 현재 DeployIfNotExists는 기본 제공 정책에서만 지원됩니다.

## <a name="how-do-i-set-up-guest-os-monitoring-data-to-be-streamed-to-an-event-hub"></a>게스트 OS 모니터링 데이터를 이벤트 허브로 스트리밍하도록 설정하려면 어떻게 해야 할까요?

게스트 OS 모니터링 데이터를 이벤트 허브로 보내려면 에이전트를 설치해야 합니다. Windows 또는 Linux의 경우 이벤트 허브로 보낼 데이터와 구성 파일에서 데이터를 보내야 하는 이벤트 허브를 지정하고, 해당 구성 파일을 VM에서 실행되는 에이전트로 전달합니다.

### <a name="stream-linux-data-to-an-event-hub"></a>Linux 데이터를 이벤트 허브로 스트리밍

[Linux Azure 진단 에이전트](../virtual-machines/extensions/diagnostics-linux.md)를 사용하여 Linux 컴퓨터에서 이벤트 허브로 모니터링 데이터를 보낼 수 있습니다. 이렇게 하려면 LAD 구성 파일의 보호 설정 JSON에서 이벤트 허브를 싱크로 추가합니다. [Linux Azure 진단 에이전트에 이벤트 허브 싱크를 추가하는 방법에 대한 자세한 내용은 이 문서를 참조하세요](../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> 게스트 OS 모니터링 데이터는 포털의 이벤트 허브로 스트리밍하도록 설정할 수 없습니다. 대신 구성 파일을 수동으로 편집해야 합니다.

### <a name="stream-windows-data-to-an-event-hub"></a>Windows 데이터를 이벤트 허브로 스트리밍

[Windows Azure 진단 에이전트](./azure-diagnostics.md)를 사용하여 Windows 컴퓨터에서 이벤트 허브로 모니터링 데이터를 보낼 수 있습니다. 이렇게 하려면 WAD 구성 파일의 privateConfig 섹션에서 이벤트 허브를 싱크로 추가합니다. [Windows Azure 진단 에이전트에 이벤트 허브 싱크를 추가하는 방법에 대한 자세한 내용은 이 문서를 참조하세요](./azure-diagnostics-streaming-event-hubs.md).

> [!NOTE]
> 게스트 OS 모니터링 데이터는 포털의 이벤트 허브로 스트리밍하도록 설정할 수 없습니다. 대신 구성 파일을 수동으로 편집해야 합니다.

## <a name="how-do-i-set-up-application-monitoring-data-to-be-streamed-to-event-hub"></a>응용 프로그램 모니터링 데이터를 이벤트 허브로 스트리밍하도록 설정하려면 어떻게 해야 할까요?

응용 프로그램 모니터링 데이터를 사용하려면 코드가 SDK를 통해 계측되어야 하므로, 응용 프로그램 모니터링 데이터를 Azure의 이벤트 허브로 라우팅하는 범용 솔루션이 없습니다. 그러나 [Azure Application Insights](../application-insights/app-insights-overview.md)는 Azure 응용 프로그램 수준 데이터를 수집하는 데 사용할 수 있는 하나의 서비스입니다. Application Insights를 사용하는 경우 다음을 수행하여 모니터링 데이터를 이벤트 허브로 스트리밍할 수 있습니다.

1. 저장소 계정에 Application Insights 데이터의 [연속 내보내기를 설정](../application-insights/app-insights-export-telemetry.md)합니다.

2. [Blob 저장소에서 데이터를 가져오고](../connectors/connectors-create-api-azureblobstorage.md#use-an-action) [이를 메시지로 이벤트 허브에 푸시하는](../connectors/connectors-create-api-azure-event-hubs.md#send-events-to-your-event-hub-from-your-logic-app) 타이머 트리거 논리 앱을 설정합니다.

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>내 이벤트 허브로 보내지는 모니터링 데이터를 사용하여 수행할 수 있는 작업은 무엇인가요?

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 파트너 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다. 대부분의 도구에서는 이벤트 허브 연결 문자열과 Azure 구독에 대한 특정 권한을 사용하여 이벤트 허브에서 데이터를 읽을 수 있습니다. 다음은 Azure Monitor와 통합되는 완전하지 않은 도구 목록입니다.

* **IBM QRadar** - Microsoft Azure DSM 및 Microsoft Azure 이벤트 허브 프로토콜은 [IBM 지원 웹 사이트](http://www.ibm.com/support)에서 다운로드할 수 있습니다. [Azure와의 통합에 대해 여기서 자세히 알아볼 수 있습니다](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk** - Splunk 설정에 따라 두 가지 접근 방법이 있습니다.
    1. [Splunk용 Azure Monitor 추가 기능](https://splunkbase.splunk.com/app/3534/)은 Splunkbase와 오픈 소스 프로젝트에서 사용할 수 있습니다. [해당 설명서는 여기에 있습니다](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Splunk 인스턴스에 추가 기능을 설치할 수 없는 경우(예: 프록시를 사용 중이거나 Splunk Cloud에서 실행 중인 경우), [이벤트 허브의 새 메시지로 트리거되는 이 함수](https://github.com/sebastus/AzureFunctionForSplunkVS)를 사용하여 이러한 이벤트를 Splunk HTTP 이벤트 수집기로 전달할 수 있습니다.
* **SumoLogic** - 이벤트 허브에서 데이터를 사용하도록 SumoLogic을 설정하기 위한 지침은 [여기서 사용할 수 있습니다](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub).

## <a name="next-steps"></a>다음 단계
* [저장소 계정에 활동 로그 보관](monitoring-archive-activity-log.md)
* [Azure 활동 로그 개요 알아보기](monitoring-overview-activity-logs.md)
* [활동 로그 이벤트를 기반으로 경고 설정](insights-auditlog-to-webhook-email.md)

