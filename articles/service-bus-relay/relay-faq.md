---
title: Azure Relay FAQ | Microsoft Docs
description: Azure Relay에 대해 자주 묻는 일부 질문에 답변합니다.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2018
ms.author: sethm
ms.openlocfilehash: d433fb916280e98dd0f2af61728596b8566be71b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-relay-faqs"></a>Azure Relay FAQ

이 문서에서는 [Azure Relay](https://azure.microsoft.com/services/service-bus/)에 대해 자주 묻는 일부 질문에 답변합니다. 또한 일반적인 Azure 가격 책정 및 지원 정보는 [Azure 지원 FAQ](https://azure.microsoft.com/en-in/support/faq/)를 참조하세요.

## <a name="general-questions"></a>일반적인 질문
### <a name="what-is-azure-relay"></a>Azure 릴레이란?
[Azure Relay 서비스](relay-what-is-it.md)는 회사 엔터프라이즈 네트워크 내에 있는 서비스를 공용 클라우드에 더 안전하게 노출하여 하이브리드 응용 프로그램을 간편하게 만듭니다. 방화벽 연결을 열지 않고 회사 네트워크 인프라를 방해하는 변경 사항 없이 서비스를 노출할 수 있습니다.

### <a name="what-is-a-relay-namespace"></a>릴레이 네임스페이스란?
[네임스페이스](relay-create-namespace-portal.md)는 응용 프로그램 내에서 Relay 리소스를 확인하는 데 사용할 수 있는 범위 지정 컨테이너입니다. Relay를 사용하는 네임스페이스를 만들어야 합니다. 시작하는 첫 번째 단계 중 하나입니다.

### <a name="what-happened-to-service-bus-relay-service"></a>Service Bus Relay 서비스는 어떻게 되나요?
이전의 Service Bus Relay 서비스를 이제 [WCF Relay](relay-wcf-dotnet-get-started.md)라고 부릅니다. 평소처럼 이 서비스를 계속 사용할 수 있습니다. 하이브리드 연결 기능은 Azure BizTalk Services에 옮겨진 업데이트된 버전의 서비스입니다. WCF Relay 및 하이브리드 연결은 모두 계속 지원됩니다.

## <a name="pricing"></a>가격
이 섹션에서는 Relay 가격 책정 구조에 대해 자주 묻는 일부 질문에 답변합니다. 또한 일반적인 Azure 가격 책정 정보는 [Azure 지원 FAQ](http://go.microsoft.com/fwlink/?LinkID=185083)를 참조하면 됩니다. Relay 가격 책정에 대한 전체 내용은 [Service Bus 가격 책정 세부 정보][Pricing overview]를 참조하세요.

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>하이브리드 연결 및 WCF 릴레이의 요금은 어떻게 청구되나요?
릴레이 가격 책정에 대한 자세한 내용은 Service Bus 가격 책정 세부 정보 페이지의 [하이브리드 연결 및 WCF 릴레이][Pricing overview] 표를 참조하세요. 페이지에 언급된 가격 외에도 응용 프로그램이 프로비전되는 데이터 센터의 외부에서 송신을 위해 연결된 데이터 전송에 요금이 부과됩니다.

### <a name="how-am-i-billed-for-hybrid-connections"></a>하이브리드 연결의 요금은 어떻게 청구되나요?
하이브리드 연결에 대한 세 가지 예제 청구 시나리오는 다음과 같습니다.

*   시나리오 1:
    *   하이브리드 연결 관리자의 인스턴스와 같은 수신기를 설치하고 한 달 내내 지속적으로 실행했습니다.
    *   해당 월 동안 연결을 통해 3GB의 데이터를 보냈습니다. 
    *   총 청구 금액은 $5입니다.
*   시나리오 2:
    *   하이브리드 연결 관리자의 인스턴스와 같은 수신기를 설치하고 한 달 내내 지속적으로 실행했습니다.
    *   해당 월 동안 연결을 통해 10GB의 데이터를 보냈습니다.
    *   총 청구 금액은 $7.50입니다. 연결 및 초반 5GB에 대해 $5 + 추가 5GB의 데이터에 대해 $2.50입니다.
*   시나리오 3:
    *   A와 B라는 하이브리드 연결 관리자의 인스턴스를 설치하고 한 달 내내 지속적으로 실행했습니다.
    *   해당 월 동안 A 연결을 통해 3GB의 데이터를 보냈습니다.
    *   해당 월 동안 B 연결을 통해 6GB의 데이터를 보냈습니다.
    *   총 청구 금액은 $10.50입니다. A 연결에 대해 $5 + B 연결에 대해 $5 + 연결 B의 6기가바이트에 대해 $0.50입니다.

예제에서 사용되는 가격은 하이브리드 연결 미리 보기 기간 동안에만 적용됩니다. 가격은 하이브리드 연결의 일반 공급 변경 사항에 따라 다릅니다.

### <a name="how-are-hours-calculated-for-relay"></a>Relay의 경우 시간은 어떻게 계산되나요?

WCF Relay는 표준 계층 네임스페이스에서만 사용할 수 있습니다. 그렇지 않은 경우 릴레이의 가격 책정 및 [연결 할당량](../service-bus-messaging/service-bus-quotas.md)은 변경되지 않습니다. 즉, 릴레이는 메시지 수(작업 아님)와 릴레이 시간에 따라 지속적으로 청구됩니다. 자세한 내용은 가격 책정 세부 정보 페이지의 [“하이브리드 연결 및 WCF 릴레이”](https://azure.microsoft.com/pricing/details/service-bus/) 표를 참조하세요.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>특정 릴레이에 둘 이상의 수신기가 연결된 경우 어떻게 하나요?
경우에 따라 단일 릴레이에 연결된 수신기가 여러 개 있을 수 있습니다. 하나 이상의 릴레이 수신기가 연결된 경우 릴레이는 열린 것으로 간주됩니다. 열린 릴레이에 수신기를 추가하면 릴레이 시간이 추가됩니다. 또한 릴레이에 연결된 릴레이 발신자(메시지를 릴레이에 호출하거나 전송하는 클라이언트)의 수는 릴레이 시간을 계산하는 데 영향을 주지 않습니다.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>WCF 릴레이 대한 메시지 측정기를 어떻게 계산하나요?
(**WCF 릴레이에 적용됩니다. 하이브리드 연결에서 메시지의 비용은 없습니다.**)

이전에 설명한 대로 일반적으로 조정된 엔터티(큐, 토픽 및 구독)에 대해 동일한 메서드를 사용하여 릴레이의 청구 가능한 메시지를 계산합니다. 하지만 몇 가지 주목할 차이점이 있습니다.

Service Bus Relay에 메시지를 보내는 경우 메시지를 수신하는 릴레이 수신기로 "완전히" 보낸다고 처리됩니다. 릴레이 수신기에 대한 배달에 이어 Service Bus Relay에 보내기 작업으로 처리되지 않습니다. 릴레이 수신기에 대한 요청-응답 형식 서비스 호출(최대 64KB)은 두 가지 청구 가능 메시지를 발생시킵니다. 하나는 요청에 대한 청구 가능 메시지이며 다른 하나는 응답에 대한 청구 가능 메시지입니다(응답이 64KB 이하라고 가정). 클라이언트와 서비스 간을 조정하는 큐를 사용하는 것과 다릅니다. 클라이언트와 서비스 간을 조정하는 큐를 사용하는 경우 동일한 요청-회신 패턴은 큐에서 서비스로 큐 제거/배달에 이어 큐에 대한 요청 보내기가 필요합니다. 그런 다음 다른 큐로 응답 보내기 및 큐에서 클라이언트로 제거/배달을 수행합니다. 동일한 크기 처리량(최대 64KB)을 가정하면 조정된 큐 패턴에서는 네 개의 청구 가능 메시지가 발생합니다. 릴레이 사용하여 수행할 수 있는 동일한 패턴을 구현하도록 메시지 수의 두 배 요금이 청구됩니다. 물론 지속성 및 부하 평준화 등 패턴을 달성하기 위해 큐를 사용할 수 있다는 이점이 있습니다. 이러한 이점은 추가 비용을 정당화할 수 있습니다.

**netTCPRelay** WCF 바인딩을 사용하여 열린 릴레이는 개별 메시지가 아니라 시스템을 통과하는 데이터의 스트림으로서 메시지를 처리합니다. 이 바인딩을 사용하는 경우, 발신자와 리스너만이 보내고 받은 개별 메시지의 프레임에 대한 가시성이 있습니다. **netTCPRelay** 바인딩을 사용하는 릴레이의 경우 청구 가능한 메시지를 계산하기 위해 모든 데이터를 스트림으로 처리합니다. 이 경우에 Service Bus는 5분 단위로 개별 릴레이를 통해 보내거나 받은 데이터의 총 크기를 계산합니다. 그런 다음 64KB로 총 데이터 양을 나누어서 해당 기간 동안 해당 릴레이에 대한 청구 가능 메시지 수를 결정합니다.

## <a name="quotas"></a>할당량
| 할당량 이름 | 범위 |  메모 | 값 |
| --- | --- | --- | --- |
| 릴레이의 동시 수신기 |엔터티 |추가 연결에 대한 후속 요청이 거부되며 호출 코드에서 예외를 수신합니다. |25 |
| 서비스 네임스페이스의 모든 릴레이 끝점당 동시 릴레이 연결 |네임스페이스 |- |5,000 |
| 서비스 네임스페이스당 릴레이 끝점 |네임스페이스 |- |10000 |
| [NetOnewayRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.netonewayrelaybinding.aspx) and [NetEventRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.neteventrelaybinding.aspx) 릴레이의 메시지 크기 |네임스페이스 |이러한 할당량을 초과하는 들어오는 메시지가 거부되며 호출 코드에서 예외를 수신합니다. |64KB |
| [HttpRelayTransportBindingElement](https://msdn.microsoft.com/library/microsoft.servicebus.httprelaytransportbindingelement.aspx) 및 [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) 릴레이의 메시지 크기 |네임스페이스 |메시지 크기에 제한이 없습니다. |Unlimited |

### <a name="does-relay-have-any-usage-quotas"></a>릴레이에는 사용 할당량이 있습니까?
기본적으로 모든 클라우드 서비스의 경우 Microsoft는 모든 고객의 구독에 대해 계산되는 월별 사용 할당량을 집계합니다. 이번에는 요구가 이러한 제한을 초과할 수 있다는 점을 이해합니다. 이러한 한도를 적절하게 조정할 수 있도록 고객 서비스에 언제든지 문의해 주세요. Service Bus의 경우 집계 사용 할당량은 다음과 같습니다.

* 50억 개의 메시지
* 2백만 릴레이 시간

매달 사용 할당량을 초과하는 계정을 사용하지 않도록 설정하는 권한을 보유하지만 메일 알림을 제공하며 조치를 취하기 전에 고객에게 연락을 여러 번 시도합니다. 이러한 할당량을 초과하는 고객은 초과하는 요금을 지불해야 합니다.

### <a name="naming-restrictions"></a>명명 제한
릴레이 네임스페이스 이름은 6~50자여야 합니다.

## <a name="subscription-and-namespace-management"></a>구독 및 네임스페이스 관리
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>다른 Azure 구독으로 네임스페이스를 마이그레이션하려면 어떻게 해야 하나요?

Azure 구독 간에 네임스페이스를 이동하려면 [Azure Portal](https://portal.azure.com) 또는 PowerShell 명령을 사용할 수 있습니다. 네임스페이스를 다른 구독으로 이동하려면 네임스페이스가 이미 활성화되어야 합니다. 명령을 실행하는 사용자는 원본 및 대상 구독 모두에 대한 관리자 사용자여야 합니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 구독 간에 Azure Relay 네임스페이스를 마이그레이션하려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md#use-portal)을 참조하세요. 

#### <a name="powershell"></a>PowerShell

PowerShell을 사용하여 Azure 구독 간에 네임스페이스를 이동하려면 다음 명령 시퀀스를 사용합니다. 이 작업을 실행하려면 네임스페이스 이미 활성 상태여야 하며 PowerShell 명령을 실행하는 사용자는 원본 및 대상 구독의 관리자 사용자여야 합니다.

```powershell
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>문제 해결
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Azure Relay API에 의해 생성된 일부 예외 및 수행할 수 있는 권장 조치는 무엇인가요?
일반적인 예외 및 수행할 수 있는 권장 조치에 대한 설명은 [릴레이 예외][Relay exceptions]를 참조하세요.

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>공유 액세스 서명이란 무엇이고 어떤 언어를 사용하여 서명을 생성할 수 있나요?
공유 액세스 서명(SAS)은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. Node, PHP, Java, C 및 C#에서 자신의 서명을 생성하는 방법에 대한 내용은 [공유 액세스 서명을 사용한 Service Bus 인증][Shared Access Signatures] 문서를 참조하세요.

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>릴레이 끝점을 허용 목록에 추가할 수 있나요?
예. 릴레이 클라이언트는 FQDN(정규화된 도메인 이름)을 사용하여 Azure Relay 서비스에 연결합니다. 고객은 DNS 허용 목록을 지원하는 방화벽에 `*.servicebus.windows.net`에 대한 항목을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [네임스페이스 만들기](relay-create-namespace-portal.md)
* [.NET 시작](relay-hybrid-connections-dotnet-get-started.md)
* [노드 시작](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared access signatures]: ../service-bus-messaging/service-bus-sas.md
