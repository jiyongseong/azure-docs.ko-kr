---
title: Application Gateway에 대한 액세스 로그, 성능 로그, 백 엔드 상태 및 메트릭 모니터링
description: Application Gateway에 대한 액세스 및 성능 로그를 사용하고 관리하는 방법을 알아봅니다
services: application-gateway
author: amitsriva
manager: rossort
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/2/2018
ms.author: amitsriva
ms.openlocfilehash: 982ae712320cb390b1822de6a7a3980ebfb6251e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Application Gateway에 대한 백 엔드 상태, 진단 로그 및 메트릭

Azure Application Gateway를 사용하여 다음과 같은 방법으로 리소스를 모니터링할 수 있습니다.

* [백 엔드 상태](#back-end-health) - Application Gateway는 Azure Portal과 PowerShell을 통해 백 엔드 풀에 있는 서버의 상태를 모니터링하는 기능을 제공합니다. 또한 성능 진단 로그를 통해 백 엔드 풀의 상태도 확인할 수 있습니다.

* [로그](#diagnostic-logging) - 로그를 사용하면 모니터링하기 위해 리소스에서 성능, 액세스 및 기타 데이터를 저장하거나 사용할 수 있습니다.

* [메트릭](#metrics): 현재 Application Gateway는 성능 카운터를 보여주는 7개 메트릭을 제공합니다.

## <a name="back-end-health"></a>백 엔드 상태

Application Gateway는 포털, PowerShell 및 CLI(명령줄 인터페이스)를 통해 백 엔드 풀의 개별 멤버에 대한 상태를 모니터링하는 기능을 제공합니다. 또한 성능 진단 로그를 통해 백 엔드 풀에 대해 집계된 상태 요약도 확인할 수 있습니다. 

백 엔드 상태 보고서는 백 엔드 인스턴스에 대한 Application Gateway 상태 프로브의 결과를 반영합니다. 프로브가 성공하고 백 엔드에서 트래픽을 받을 수 있으면 정상 상태로 간주됩니다. 그렇지 않으면 비정상 상태로 간주됩니다.

> [!IMPORTANT]
> Application Gateway 서브넷에 NSG(네트워크 보안 그룹)가 있는 경우 인바운드 트래픽을 위해 Application Gateway 서브넷에서 65503-65534 포트 범위를 엽니다. 백 엔드 상태 API가 제대로 작동하는 데 이러한 포트가 필요합니다.


### <a name="view-back-end-health-through-the-portal"></a>포털을 통해 백 엔드 상태 보기

포털에서는 백 엔드 상태를 자동으로 제공합니다. 기존 응용 프로그램 게이트웨이에서 **모니터링** > **백 엔드 상태**를 차례로 선택합니다. 

NIC, IP 또는 FQDN과 관계 없이 백 엔드 풀의 각 멤버가 이 페이지에 나열됩니다. 백 엔드 풀 이름, 포트, 백 엔드 HTTP 설정 이름 및 상태도 표시됩니다. 상태의 유효한 값은 **정상**, **비정상** 및 **알 수 없음**입니다.

> [!NOTE]
> 백 엔드 상태가 **알 수 없음**이면 백 엔드에 대한 액세스가 가상 네트워크의 NSG 규칙, UDR(사용자 정의 경로) 또는 사용자 지정 DNS에 의해 차단되지 않도록 합니다 .

![백 엔드 상태][10]

### <a name="view-back-end-health-through-powershell"></a>PowerShell을 통해 백 엔드 상태 보기

다음 PowerShell 코드에서는 `Get-AzureRmApplicationGatewayBackendHealth` cmdlet을 사용하여 백 엔드 상태를 확인하는 방법을 보여 줍니다.

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli-20"></a>Azure CLI 2.0을 통해 백 엔드 상태 보기

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>결과

다음은 응답의 예제를 보여 주는 코드 조각입니다.

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>진단 로그

Azure에서 다양한 유형의 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. 모든 로그는 Azure Blob 저장소에서 추출하고 다양한 도구(예: [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md),Excel 및 Power BI)에서 볼 수 있습니다. 다음 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **활동 로그** - [Azure 활동 로그](../monitoring-and-diagnostics/insights-debugging-with-events.md)(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 확인할 수 있습니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.
* **액세스 로그** - 이 로그를 사용하여 Application Gateway 액세스 패턴을 확인하고 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 입/출력 바이트 수 등 중요한 정보를 분석할 수 있습니다. 액세스 로그는 300초마다 수집됩니다. 이 로그에는 Application Gateway 인스턴스당 하나의 레코드가 포함됩니다. Application Gateway 인스턴스는 'instanceId' 속성으로 식별할 수 있습니다.
* **성능 로그** - 이 로그를 사용하여 Application Gateway 인스턴스를 수행하는 방법을 확인할 수 있습니다. 이 로그는 인스턴스 단위로 처리된 총 요청 수, 처리량(바이트), 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다. 성능 로그는 60초마다 수집됩니다.
* **방화벽 로그** - 이 로그를 사용하면 웹 응용 프로그램 방화벽으로 구성된 응용 프로그램 게이트웨이의 검색 모드 또는 방지 모드를 통해 로깅된 요청을 확인할 수 있습니다.

> [!NOTE]
> 로그는 Azure Resource Manager 배포 모델에 배포된 리소스에만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델에 대해 더 잘 이해하려면 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/resource-manager-deployment-model.md) 문서를 참조하세요.

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **이벤트 허브** - 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.
* **Log Analytics** - 일반적으로 응용 프로그램을 실시간 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

### <a name="enable-logging-through-powershell"></a>PowerShell을 통한 로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 액세스 및 성능 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

1. 로그 데이터를 저장할 저장소 계정의 리소스 ID를 적어 둡니다. 이 값의 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Storage/storageAccounts/\<저장소 계정 이름\>입니다. 구독의 모든 저장소 계정을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다.

    ![포털: 저장소 계정의 리소스 ID](./media/application-gateway-diagnostics/diagnostics1.png)

2. 로깅을 사용할 Application Gateway의 리소스 ID를 적어 둡니다. 이 값은 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<리소스 그룹 이름\>/providers/Microsoft.Network/applicationGateways/\<응용 프로그램 게이트웨이 이름\>입니다. 포털을 사용하여 이 정보를 찾을 수 있습니다.

    ![포털: 응용 프로그램 게이트웨이의 리소스 ID](./media/application-gateway-diagnostics/diagnostics2.png)

3. 다음 PowerShell cmdlet을 사용하여 진단 로깅을 사용하도록 설정합니다.

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>활동 로그에는 별도의 저장소 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 저장소를 사용할 경우 서비스 요금이 부과됩니다.

### <a name="enable-logging-through-the-azure-portal"></a>Azure Portal을 통한 로깅 사용

1. Azure Portal에서 리소스를 찾고 **진단 로그**를 클릭합니다.

   Application Gateway의 경우 다음 세 가지 로그를 사용할 수 있습니다.

   * 액세스 로그
   * 성능 로그
   * 방화벽 로그

2. 데이터 수집을 시작하려면 **진단 켜기**를 클릭합니다.

   ![진단 켜기][1]

3. **진단 설정** 블레이드에서는 진단 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. **Log Analytics**에서 **구성**을 클릭하여 작업 영역을 구성합니다. 또한 이벤트 허브 및 저장소 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

   ![구성 프로세스 시작][2]

4. 기존 Log Analytics 작업 영역을 선택하거나 새로운 작업 영역을 만듭니다. 이 예제에서는 기존 작업 영역을 사용합니다.

   ![Log Analytics 작업 영역의 옵션][3]

5. 설정을 확인하고 **저장**을 클릭합니다.

   ![선택 항목이 있는 진단 설정 블레이드][4]

### <a name="activity-log"></a>활동 로그

Azure에서는 기본적으로 활동 로그를 생성합니다. 이러한 로그는 Azure 이벤트 로그 저장소에 90일 동안 유지됩니다. [이벤트 및 활동 로그 보기](../monitoring-and-diagnostics/insights-debugging-with-events.md) 문서를 참조하여 이러한 로그에 대해 자세히 알아보세요.

### <a name="access-log"></a>액세스 로그

이전 단계에서 설명한 대로 액세스 로그는 각 Application Gateway 인스턴스에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 저장소 계정에 저장됩니다. Application Gateway의 액세스는 각각 다음 예제와 같이 JSON 형식으로 로깅됩니다.


|값  |설명  |
|---------|---------|
|instanceId     | 요청을 처리한 Application Gateway 인스턴스        |
|clientIP     | 요청에 대한 원래 IP        |
|clientPort     | 요청에 대한 원래 포트       |
|httpMethod     | 요청에서 사용된 HTTP 메서드       |
|requestUri     | 받은 요청의 URI        |
|RequestQuery     | **Server-Routed**: 요청을 보낸 백 엔드 풀 인스턴스입니다.</br>**X-AzureApplicationGateway-LOG-ID**: 요청에 사용된 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. </br>**SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드       |
|UserAgent     | HTTP 요청 헤더의 사용자 에이전트        |
|httpStatus     | Application Gateway에서 클라이언트로 반환한 HTTP 상태 코드       |
|httpVersion     | 요청의 HTTP 버전        |
|receivedBytes     | 받은 패킷의 크기(바이트)        |
|sentBytes| 보낸 패킷의 크기(바이트)|
|timeTaken| 요청을 처리하고 응답을 보내는 데 걸리는 시간(밀리초)입니다. 이 값은 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 받은 시점부터 응답 보내기 작업을 완료하는 시점까지의 간격으로 계산됩니다. 걸린 시간(Time-Taken) 필드에는 대개 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간이 포함됩니다. |
|sslEnabled| 백 엔드 풀에 대한 통신에서 SSL이 사용되었는지 여부입니다. 유효한 값은 on과 off입니다.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```

### <a name="performance-log"></a>성능 로그

이전 단계에서 설명한 대로 성능 로그는 각 Application Gateway 인스턴스에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 저장소 계정에 저장됩니다. 성능 로그 데이터는 1분 간격으로 생성됩니다. 다음 데이터가 로깅됩니다.


|값  |설명  |
|---------|---------|
|instanceId     |  성능 데이터가 생성되는 Application Gateway 인스턴스입니다. 다중 인스턴스 응용 프로그램 게이트웨이의 경우 인스턴스마다 하나의 행이 있습니다.        |
|healthyHostCount     | 백 엔드 풀의 정상 호스트 수        |
|unHealthyHostCount     | 백 엔드 풀의 비정상 호스트 수        |
|requestCount     | 처리된 요청 수        |
|latency | 인스턴스와 요청을 처리하는 백 엔드 사이의 요청 대기 시간(밀리초) |
|failedRequestCount| 실패한 요청 수|
|throughput| 마지막 로그 이후의 평균 처리량(초당 바이트 수로 측정됨)|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 대기 시간은 HTTP 요청의 첫 번째 바이트를 받은 시간부터 HTTP 응답의 마지막 바이트를 보낸 시간까지 계산됩니다. 즉 Application Gateway 처리 시간, 백 엔드로의 네트워크 사용 시간 및 백 엔드에서 요청을 처리하는 데 걸린 시간의 합계입니다.

### <a name="firewall-log"></a>방화벽 로그

이전 단계에서 설명한 대로 방화벽 로그는 각 응용 프로그램 게이트웨이에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 또한 이 로그를 사용하려면 응용 프로그램 게이트웨이에서 웹 응용 프로그램 방화벽을 구성해야 합니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 저장소 계정에 저장됩니다. 다음 데이터가 로깅됩니다.


|값  |설명  |
|---------|---------|
|instanceId     | 방화벽 데이터가 생성되는 Application Gateway 인스턴스입니다. 다중 인스턴스 응용 프로그램 게이트웨이의 경우 인스턴스마다 하나의 행이 있습니다.         |
|clientIp     |   요청에 대한 원래 IP      |
|clientPort     |  요청에 대한 원래 포트       |
|requestUri     | 받은 요청의 URL       |
|ruleSetType     | 규칙 집합 유형이며, 사용 가능한 값은 OWASP입니다.        |
|ruleSetVersion     | 사용된 규칙 집합 버전이며, 사용 가능한 값은 2.2.9 및 3.0입니다.     |
|ruleId     | 트리거 이벤트의 규칙 ID        |
|Message     | 사용자에게 친숙한 트리거 이벤트에 대한 메시지이며, 자세한 내용은 세부 정보 섹션에서 제공됩니다.        |
|action     |  요청에서 수행되는 동작이며, 사용할 수 있는 값은 Blocked 및 Allowed입니다.      |
|site     | 로그를 생성한 사이트이며, 현재 규칙이 전역이므로 Global만 나열됩니다.|
|세부 정보     | 트리거 이벤트의 세부 정보        |
|details.message     | 규칙에 대한 설명        |
|details.data     | 규칙과 일치하는 요청 내 특정 데이터         |
|details.file     | 규칙이 포함된 구성 파일        |
|details.line     | 이벤트를 트리거한 구성 파일의 줄 번호       |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
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

### <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [Resource Manager의 활동 작업](../azure-resource-manager/resource-group-audit.md) 문서에 자세히 나와 있습니다.
* **Power BI** - [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우 무료로 사용해볼 수 있습니다. [Power BI용 Azure Activity Logs 콘텐츠 팩](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 미리 구성된 대시보드를 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>액세스, 성능 및 방화벽 로그 보기 및 분석

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)는 Blob 저장소 계정에서 카운터 및 이벤트 로그 파일을 수집할 수 있습니다. 여기에는 로그를 분석하는 시각화 및 강력한 검색 기능이 포함되어 있습니다.

저장소 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.
> 
> 

## <a name="metrics"></a>메트릭

메트릭은 포털에서 성능 카운터를 볼 수 있는 특정 Azure 리소스에 대한 기능입니다. Application Gateway에는 다음 메트릭이 지원됩니다.

- **현재 연결**
- **실패한 요청**
- **정상 호스트 수**

   특정 백 엔드 풀의 정상/비정상 호스트를 표시하도록 백 엔드 풀 기준으로 필터링 할 수 있습니다.


- **응답 상태**

   2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **처리량**
- **총 요청 수**
- **비정상 호스트 수**

   특정 백 엔드 풀의 정상/비정상 호스트를 표시하도록 백 엔드 풀 기준으로 필터링 할 수 있습니다.

응용 프로그램 게이트웨이를 찾아 **모니터링** 아래에서 **메트릭**을 클릭합니다. 사용 가능한 값을 보려면 **메트릭** 드롭다운 목록을 선택합니다.

다음 이미지에서는 최근 30분 동안 표시된 세 가지 메트릭을 포함한 예제가 표시됩니다.

[![](media/application-gateway-diagnostics/figure5.png "메트릭 보기")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

현재 지원되는 메트릭 목록을 보려면 [Azure Monitor에서 지원되는 메트릭](../monitoring-and-diagnostics/monitoring-supported-metrics.md)을 참조하세요.

### <a name="alert-rules"></a>경고 규칙

리소스에 대한 메트릭을 기반으로 하는 경고 규칙을 시작할 수 있습니다. 예를 들어 응용 프로그램 게이트웨이의 처리량이 지정된 기간 동안 임계값보다 높거나 낮거나 같을 때 경고에서 웹후크를 호출하거나 관리자에게 전자 메일을 보낼 수 있습니다.

다음 예제에서는 처리량이 임계값을 위반하면 관리자에게 전자 메일을 보내는 경고 규칙을 만드는 과정을 설명합니다.

1. **메트릭 경고 추가**를 클릭하여 **규칙 추가** 블레이드를 엽니다. 또한 메트릭 블레이드에서 이 블레이드에 연결할 수도 있습니다.

   !["메트릭 경고 추가" 단추][6]

2. **규칙 추가** 블레이드에서 이름, 조건 및 알림 섹션을 입력하고 **확인**을 클릭합니다.

   * **조건** 선택기에서 **보다 큼**, **보다 크거나 같음**, **보다 작음**, **보다 작거나 같음**의 네 가지 값 중 하나를 선택합니다.

   * **기간** 선택기에서 5분에서 6시간 사이의 기간까지 선택합니다.

   * **전자 메일 소유자, 참가자 및 읽기 권한자**를 선택하면 해당 리소스에 액세스할 수 있는 사용자에 따라 동적으로 전자 메일을 보낼 수 있습니다. 그렇지 않으면 **추가 관리자 전자 메일** 상자에서 쉼표로 구분된 사용자 목록을 제공할 수도 있습니다.

   ![규칙 추가 블레이드][7]

임계값을 위반하면 다음 이미지와 비슷한 전자 메일이 도착합니다.

![위반된 임계값에 대한 전자 메일][8]

메트릭 경고를 만들면 경고 목록이 표시됩니다. 모든 경고 규칙에 대한 개요도 제공됩니다.

![경고 및 규칙 목록][9]

경고 알림에 대한 자세한 내용은 [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)를 참조하세요.

웹후크에 대한 자세한 내용 및 경고와 함께 웹후크를 사용하는 방법을 알아보려면 [Azure 메트릭 경고에 대한 웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그 시각화
* [Power BI를 사용하여 Azure 활동 로그 시각화](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물
* [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
