---
title: Azure Application Gateway용 WAF(웹 응용 프로그램 방화벽) 소개 | Microsoft Docs
description: 이 페이지에서는 Application Gateway용 WAF(웹 응용 프로그램 방화벽)의 개요를 제공합니다.
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/16/2018
ms.author: amsriva
ms.openlocfilehash: 35a7e60dc65b54cbddfcd85fa0e704823152889e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="web-application-firewall-waf"></a>WAF(웹 응용 프로그램 방화벽)

WAF(웹 응용 프로그램 방화벽)는 일반적인 악용 및 취약점으로부터 웹 응용 프로그램에 대해 중앙 집중화된 보호를 제공하는 Application Gateway의 기능입니다. 

웹 응용 프로그램 방화벽은 [OWASP 핵심 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의 규칙에 기반합니다. 웹 응용 프로그램의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 공격으로는 SQL 삽입 공격, 사이트 간 스크립팅 공격 등이 있습니다. 응용 프로그램 코드로 이러한 공격을 방어하기란 매우 어려울 수 있으며 응용 프로그램 토폴로지의 여러 계층에서 엄격한 유지 관리, 패치 적용 및 모니터링이 필요할 수 있습니다. 중앙 집중식 웹 응용 프로그램 방화벽을 통해 보안 관리가 훨씬 간단해지고 응용 프로그램 관리자에게 위협 또는 침입으로부터 효과적인 보호를 제공합니다. 또한 WAF 솔루션은 각각의 웹 응용 프로그램을 보호하는 대신 중앙의 위치에서 알려진 취약점에 패치를 적용하여 보다 신속하게 보안 위협에 대응할 수 있습니다. 기존 Application Gateway는 웹 응용 프로그램 방화벽을 사용한 Application Gateway로 쉽게 변환될 수 있습니다.

![imageURLroute](./media/application-gateway-web-application-firewall-overview/WAF1.png)

Application Gateway는 응용 프로그램 배달 컨트롤러로 작동하여 SSL 종료, 쿠키 기반 세션 선호도, 라운드 로빈 부하 분산, 콘텐츠 기반 라우팅, 다중 웹 사이트 호스트 및 향상된 보안 기능을 제공합니다. Application Gateway가 제공하는 향상된 보안 기능으로는 SSL 정책 관리, 종단 간 SSL 지원이 포함됩니다. 이제 ADC 제품에 직접 통합되는 WAF(웹 응용 프로그램 방화벽)에 의해 응용 프로그램 보안이 강화되었습니다. 그 중 하나로 웹 응용 프로그램을 관리하고 일반적인 웹 취약점 공격으로부터 보호할 수 있는 구성이 용이한 중앙 위치를 제공합니다.

## <a name="benefits"></a>이점

다음은 Application Gateway 및 웹 응용 프로그램 방화벽이 제공하는 핵심적인 장점입니다.

### <a name="protection"></a>보호

* 백 엔드 코드를 수정하지 않고 웹 취약점 공격으로부터 웹 응용 프로그램을 보호합니다.

* 동시에 여러 웹 응용 프로그램을 Application Gateway 뒤에 두고 보호합니다. Application Gateway는 WAF를 사용하여 웹 공격으로부터 보호될 수 있는 단일 게이트웨이 뒤에서 최대 20개의 웹 사이트를 호스트하도록 지원합니다.

### <a name="monitoring"></a>모니터링

* 실시간 WAF 로그를 사용하여 공격으로부터 웹 응용 프로그램을 모니터링합니다. 이 로그는 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)와 통합되어 WAF 경고 및 로그를 추적하고 추세를 쉽게 모니터링합니다.

* WAF는 Azure Security Center에 통합됩니다. Azure Security Center를 통해 모든 Azure 리소스의 보안 상태를 중앙에서 살펴볼 수 있습니다.

### <a name="customization"></a>사용자 지정

* WAF 규칙 및 규칙 그룹을 사용자 지정하여 응용 프로그램 요구 사항에 맞게 거짓 긍정을 방지할 수 있는 기능입니다.

## <a name="features"></a>기능

웹 응용 프로그램 방화벽은 기본적으로 CRS 3.0으로 미리 구성되거나 2.2.9를 사용하도록 선택할 수 있습니다. CRS 3.0 제품은 2.2.9를 통해 거짓 긍정을 줄입니다. [필요에 맞게 규칙을 사용자 지정](application-gateway-customize-waf-rules-portal.md)하는 기능이 제공됩니다. 웹 응용 프로그램 방화벽이 보호하는 일반적인 웹 취약점에는 다음이 포함됩니다.

* SQL 삽입 공격 보호
* 교차 사이트 스크립팅 공격 보호
* 명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 보호
* HTTP 프로토콜 위반 보호
* 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호
* 보트, 크롤러 및 스캐너 방지
* 일반적인 응용 프로그램 구성 오류(예: Apache, IIS 등) 검색

더 자세한 규칙 목록 및 해당 보호는 다음 [핵심 규칙 집합](#core-rule-sets)을 참조하세요.

### <a name="core-rule-sets"></a>핵심 규칙 집합

Application Gateway는 CRS 3.0 및 CRS 2.2.9라는 두 개의 규칙 집합을 지원합니다. 이러한 핵심 규칙 집합은 악의적인 활동으로부터 웹 응용 프로그램을 보호하는 규칙의 컬렉션입니다.

#### <a name="owasp30"></a>OWASP_3.0

제공된 3.0 핵심 규칙 집합에는 다음 테이블에 나와 있는 것처럼 13개의 규칙 그룹이 있습니다. 이러한 규칙 그룹은 각각 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

|RuleGroup|설명|
|---|---|
|**[REQUEST-911-METHOD-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs911)**|메서드(PUT, PATCH< ..)를 잠그는 규칙을 포함합니다.|
|**[REQUEST-913-SCANNER-DETECTION](application-gateway-crs-rulegroups-rules.md#crs913)**| 포트 및 환경 스캐너로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920)**|프로토콜 및 인코딩 문제로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-921-PROTOCOL-ATTACK](application-gateway-crs-rulegroups-rules.md#crs921)**|헤더 주입, 밀수 요청 및 응답 분할로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](application-gateway-crs-rulegroups-rules.md#crs930)**|파일 및 경로 공격으로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](application-gateway-crs-rulegroups-rules.md#crs931)**|RFI(원격 파일 포함)로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](application-gateway-crs-rulegroups-rules.md#crs932)**|원격 코드 실행으로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](application-gateway-crs-rulegroups-rules.md#crs933)**|PHP 주입 공격으로부터 보호하는 규칙을 포함합니다.|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](application-gateway-crs-rulegroups-rules.md#crs941)**|사이트 간 스크립팅으로부터 보호하기 위한 규칙을 포함합니다.|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](application-gateway-crs-rulegroups-rules.md#crs942)**|SQL 주입 공격으로부터 보호하기 위한 규칙을 포함합니다.|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](application-gateway-crs-rulegroups-rules.md#crs943)**|세션 고정 공격으로부터 보호하는 규칙을 포함합니다.|

#### <a name="owasp229"></a>OWASP_2.2.9

제공된 2.2.9 핵심 규칙 집합에는 다음 테이블에 나와 있는 것처럼 10개의 규칙 그룹이 있습니다. 이러한 규칙 그룹은 각각 비활성화할 수 있는 여러 개의 규칙을 포함합니다.

|RuleGroup|설명|
|---|---|
|**[crs_20_protocol_violations](application-gateway-crs-rulegroups-rules.md#crs20)**|프로토콜 위반(잘못된 문자, 요청 본문에서 GET 등)으로부터 보호하는 규칙을 포함합니다.|
|**[crs_21_protocol_anomalies](application-gateway-crs-rulegroups-rules.md#crs21)**|잘못된 헤더 정보로부터 보호하는 규칙을 포함합니다.|
|**[crs_23_request_limits](application-gateway-crs-rulegroups-rules.md#crs23)**|한계를 초과하는 인수 또는 파일로부터 보호하는 규칙을 포함합니다.|
|**[crs_30_http_policy](application-gateway-crs-rulegroups-rules.md#crs30)**|제한된 메서드, 헤더 및 파일 유형으로부터 보호하는 규칙을 포함합니다. |
|**[crs_35_bad_robots](application-gateway-crs-rulegroups-rules.md#crs35)**|웹 크롤러 및 스캐너로부터 보호하는 규칙을 포함합니다.|
|**[crs_40_generic_attacks](application-gateway-crs-rulegroups-rules.md#crs40)**|제네릭 공격(세션 고정, 원격 파일 포함, PHP 주입 등)으로부터 보호하는 규칙을 포함합니다.|
|**[crs_41_sql_injection_attacks](application-gateway-crs-rulegroups-rules.md#crs41sql)**|SQL 주입 공격으로부터 보호하는 규칙을 포함합니다.|
|**[crs_41_xss_attacks](application-gateway-crs-rulegroups-rules.md#crs41xss)**|사이트 간 스크립팅으로부터 보호하는 규칙을 포함합니다.|
|**[crs_42_tight_security](application-gateway-crs-rulegroups-rules.md#crs42)**|경로 통과 공격으로부터 보호하는 규칙을 포함합니다.|
|**[crs_45_trojans](application-gateway-crs-rulegroups-rules.md#crs45)**|백도어 트로이 목마로부터 보호하는 규칙을 포함합니다.|

### <a name="waf-modes"></a>WAF 모드

Application Gateway WAF는 다음 두 가지 모드에서 실행되도록 구성할 수 있습니다.

* **검색 모드** – 검색 모드에서 실행되도록 구성할 경우 Application Gateway WAF는 모든 위협 경고를 모니터링하고 로그 파일에 기록합니다. **진단** 섹션을 사용하여 Application Gateway에 대한 진단 로깅을 켜야 합니다. 또한 WAF 로그가 선택되어 있고 켜져 있는지 확인해야 합니다. 웹 응용 프로그램 방화벽을 감지 모드로 실행하면 들어오는 요청을 차단하지 않습니다.
* **방지 모드** – 방지 모드에서 실행되도록 구성할 경우 Application Gateway는 규칙에 의해 감지된 침입 및 공격을 능동적으로 차단합니다. 공격자는 403 무단 액세스 예외를 수신하고 연결이 종료됩니다. 방지 모드는 이러한 공격을 WAF 로그에 계속 기록합니다.

### <a name="application-gateway-waf-reports"></a>WAF 모니터링

Application Gateway의 상태를 모니터링하는 것이 중요합니다. 웹 응용 프로그램 방화벽 및 보호되는 응용 프로그램의 상태를 모니터링하는 기능은 로깅과 Azure Monitor, Azure Security Center 및 Log Analytics의 통합을 통해 제공됩니다.

![진단](./media/application-gateway-web-application-firewall-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

각 Application Gateway 로그는 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)와 통합됩니다.  이를 통해 WAF 경고 및 로그를 포함하여 진단 정보를 추적할 수 있습니다.  **진단** 탭 아래의 포털에 있는 Application Gateway 리소스 내에서 이러한 기능을 제공하거나 Azure Monitor를 통해 직접 이러한 기능을 제공합니다. Application Gateway에 진단 로그를 사용하는 방법에 대해 알아보려면 [Application Gateway 진단](application-gateway-diagnostics.md)을 방문하세요.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-intro.md)를 사용하면 Azure 리소스의 보안에 대한 향상된 가시성과 제어를 통해 위협을 예방, 검색 및 대응할 수 있습니다. Application Gateway는 [Azure Security Center에 통합](application-gateway-integration-security-center.md)됩니다. Azure Security Center는 사용자 환경을 검사하여 보호되지 않는 웹 응용 프로그램을 검색합니다. 이제 이러한 취약한 리소스를 보호하도록 응용 프로그램 게이트웨이 WAF를 권장할 수 있습니다. Azure Security Center에서 응용 프로그램 게이트웨이 WAF를 직접 만들 수 있습니다.  이러한 WAF 인스턴스는 Azure Security Center와 통합되며 보고를 위해 Azure Security Center에 다시 경고 및 상태 정보를 보냅니다.

![그림 1](./media/application-gateway-web-application-firewall-overview/figure1.png)

#### <a name="logging"></a>로깅

Application Gateway WAF는 감지된 각 위협에 대한 상세 보고를 제공합니다. 로깅은 Azure 진단 로그에 통합되고 경고는 json 형식으로 기록됩니다. 이러한 로그는 [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)와 통합될 수 있습니다.

![imageURLroute](./media/application-gateway-web-application-firewall-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Application Gateway WAF SKU 가격 책정

새 WAF SKU 아래에서 웹 응용 프로그램 방화벽을 사용할 수 있습니다. 이 SKU는 클래식 배포 모델이 아닌 Azure Resource Manager 프로비전 모델에서만 사용할 수 있습니다. 또한 WAF SKU는 중간 규모 및 대규모 Application Gateway 인스턴스 크기로만 제공됩니다. Application Gateway의 모든 제한 사항이 WAF SKU에도 적용됩니다. 가격 책정은 시간 당 게이트웨이 인스턴스 요금 및 데이터 처리 충전을 기반으로 합니다. WAF SKU 시간 당 게이트웨이 가격 책정은 표준 SKU 요금과 다르며 [Application Gateway 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/application-gateway/)에서 찾을 수 있습니다. 데이터 처리 요금은 동일하게 유지됩니다. 규칙 또는 규칙 그룹 당 요금은 없습니다. 동일한 웹 응용 프로그램 방화벽 뒤에서 여러 웹 응용 프로그램을 보호할 수 있으며 여러 응용 프로그램을 지원하는 추가 비용이 없습니다. 


## <a name="next-steps"></a>다음 단계

WAF 기능을 살펴본 후에는 [Application Gateway에서 웹 응용 프로그램 방화벽을 구성하는 방법](application-gateway-web-application-firewall-portal.md)을 참조하세요.

