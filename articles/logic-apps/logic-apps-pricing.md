---
title: 가격 책정 및 대금 청구 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps의 가격 책정 및 대금 청구 방식 알아보기
services: logic-apps
author: kevinlam1
manager: cfowler
editor: ''
documentationcenter: ''
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/11/2018
ms.author: klam
ms.openlocfilehash: 3f01ac12bb3987d564f8d9f70706ae6aabd0a0ee
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166287"
---
# <a name="logic-apps-pricing-model"></a>Logic Apps 가격 책정 모델

Azure Logic Apps를 사용하여 클라우드에 자동화된 확장 가능한 통합 워크플로를 만들고 실행할 수 있습니다. 다음은 Logic Apps의 청구 및 가격 책정 방식에 대한 세부 정보입니다. 

## <a name="consumption-pricing-model"></a>소비 가격 책정 모델

새로 만든 논리 앱을 사용하면 사용한 만큼에 대해서만 비용을 지불합니다. 새 논리 앱은 소비 계획 및 가격 책정 모델을 사용합니다. 즉, 논리 앱 인스턴스에 의해 수행되는 모든 작업 실행이 계량됩니다. 논리 앱 정의의 모든 단계는 작업이며 여기에는 트리거, 제어 흐름 단계, 기본 제공 작업에 대한 호출 및 커넥터에 대한 호출이 포함됩니다. 자세한 내용은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요.

<a name="triggers"></a>

## <a name="triggers"></a>트리거

트리거는 특정 이벤트가 발생할 때 논리 앱 인스턴스를 만드는 특수 작업입니다. 트리거는 논리 앱이 계량되는 방식에 영향을 주는 다양한 방법으로 작동합니다.

* **폴링 트리거** – 이 트리거는 논리 앱 인스턴스를 만들고 워크플로를 시작하는 조건을 충족하는 메시지에 대한 엔드포인트를 지속적으로 확인합니다. 논리 앱 인스턴스를 만들지 않은 경우에도 각 폴링 요청은 실행으로 간주되고 계량됩니다. 폴링 간격을 설정하려면 논리 앱 디자이너를 통해 트리거를 설정합니다.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **웹후크 트리거** – 이 트리거는 클라이언트가 특정 끝점에 요청을 보낼 때까지 대기합니다. 웹후크 끝점으로 전송된 각 요청은 작업 실행으로 계산됩니다. 예를 들어, 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.

* **되풀이 트리거** – 이 트리거는 트리거에 구성한 되풀이 간격을 기반으로 논리 앱 인스턴스를 만듭니다. 예를 들어 3일마다 또는 더 복잡한 일정으로 실행되는 되풀이 트리거를 설정할 수 있습니다.

트리거 실행은 트리거 기록 섹션 아래 논리 앱의 개요 창에서 찾을 수 있습니다.

## <a name="actions"></a>작업

기본 제공 작업(예: HTTP, Azure Functions 또는 API Management 및 제어 흐름 단계)은 원시 작업으로 계량되며 해당 유형이 있습니다. [커넥터](https://docs.microsoft.com/connectors)를 호출하는 작업에는 "ApiConnection" 유형이 있습니다. 이러한 커넥터는 표준 또는 엔터프라이즈 커넥터로 분류되며 해당 [가격 책정][pricing]을 기준으로 계량됩니다. 

성공 및 실패한 모든 실행 작업은 작업 실행으로 집계되고 계량됩니다. 조건이 충족되지 않아 건너뛴 작업 또는 완료되기 전에 논리 앱이 종료되어 실행되지 않은 작업은 작업 실행으로 간주되지 않습니다. 사용이 중지된 논리 앱은 새 인스턴스를 인스턴스화할 수 없으므로 사용이 중지되는 동안 비용이 청구되지 않습니다.

> [!NOTE]
> 논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

루프 내에서 실행되는 작업은 루프의 각 주기당 계산됩니다. 예를 들어, 항목이 10개인 목록을 처리하는 "for each" 루프의 단일 작업은 목록 항목 수(10)에 루프의 작업 수(1)를 곱하고 루프 시작에 대한 1을 더하여 계산됩니다. 이 예제의 경우 계산은 (10 * 1) + 1이므로 작업 실행은 11개가 됩니다.

## <a name="integration-account-usage"></a>통합 계정 사용량

소비 기반 사용량에는 추가 비용 없이 Logic Apps의 [B2B/EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 탐색, 개발 및 테스트할 수 있는 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md)이 포함됩니다. 지역마다 하나의 통합 계정이 있으며, EDI 거래 업체 및 규약, 맵, 스키마, 어셈블리, 인증서 및 일괄 처리 구성과 같은 [아티팩트의 특정 수](../logic-apps/logic-apps-limits-and-config.md)까지 저장할 수 있습니다.

또한 Logic Apps에는 지원되는 Logic Apps SLA와 함께 기본 및 표준 통합 계정이 제공됩니다. 메시지 처리만 사용하려는 경우 또는 대규모 비즈니스 엔터티와 거래 파트너 관계가 있는 소규모 비즈니스 파트너 역할을 수행하려는 경우에는 기본 통합 계정을 사용할 수 있습니다. 표준 통합 계정은 더 복잡한 B2B 관계를 지원하며 관리할 수 있는 엔터티의 수를 늘립니다. 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Logic Apps에 대한 자세한 정보][whatis]
* [첫 번째 논리 앱 만들기][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md

