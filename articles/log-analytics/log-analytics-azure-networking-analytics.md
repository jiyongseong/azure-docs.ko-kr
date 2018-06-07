---
title: Log Analytics의 Azure Networking Analytics 솔루션 | Microsoft Docs
description: Log Analytics의 Azure Networking Analytics 솔루션을 사용하여 Azure 네트워크 보안 그룹 로그와 Azure Application Gateway 로그를 검토할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: richrund
ms.openlocfilehash: 12172e81ed6b4d79ee200ee1ca79803ad58d6d19
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Log Analytics의 Azure 네트워킹 모니터링 솔루션

Log Analytics는 네트워크를 모니터링하기 위해 다음과 같은 솔루션을 제공합니다.
* NPM(네트워크 성능 모니터)
 * 네트워크 상태 모니터링
* 검토할 Azure Application Gateway 분석
 * Azure Application Gateway 로그
 * Azure Application Gateway 메트릭
* 클라우드 네트워크에서 네트워크 작업을 모니터링하고 감사하는 솔루션
* [트래픽 분석](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
* Azure 네트워크 보안 그룹 분석

## <a name="network-performance-monitor-npm"></a>NPM(네트워크 성능 모니터)

[네트워크 성능 모니터](https://docs.microsoft.com/azure/networking/network-monitoring-overview) 관리 솔루션은 네트워크의 상태, 가용성 및 연결 가능성을 모니터링하는 네트워크 모니터링 솔루션입니다.  다음 항목 간의 연결을 모니터링하는 데 사용됩니다.

* 공용 클라우드 및 온-프레미스
* 데이터 센터 및 사용자 위치(지점)
* 다중 계층 응용 프로그램의 다양한 계층을 호스팅하는 서브넷

자세한 내용은 [네트워크 성능 모니터](https://docs.microsoft.com/azure/networking/network-monitoring-overview)를 참조하세요.

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway 및 네트워크 보안 그룹 분석
솔루션을 사용하려면:
1. Log Analytics에 관리 솔루션을 추가하고
2. Log Analytics 작업 영역에 대한 진단을 지시하는 진단을 사용합니다. Azure Blob Storage에 로그를 작성할 필요는 없습니다.

Application Gateway 및 네트워킹 보안 그룹 중 하나 또는 둘 다에 대해 진단 및 해당 솔루션을 사용할 수 있습니다.

특정 리소스 유형에 진단 로깅을 사용하도록 설정하지 않고 솔루션을 설치하면 해당 리소스의 대시보드 블레이드가 비어 있고 오류 메시지가 표시됩니다.

> [!NOTE]
> 2017년 1월 Application Gateway 및 네트워크 보안 그룹에서 Log Analytics로 로그를 보내도록 지원하는 방법이 변경되었습니다. **Azure Networking Analytics(사용되지 않음)** 솔루션을 표시하는 경우 수행해야 할 단계는 [이전 Networking Analytics 솔루션에서 마이그레이션](#migrating-from-the-old-networking-analytics-solution)을 참조하세요.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Azure 네트워킹 데이터 수집 세부 정보 검토
Azure Application Gateway 분석 및 네트워크 보안 그룹 분석 관리 솔루션은 Azure Application Gateway 및 네트워크 보안 그룹에서 직접 진단 로그를 수집합니다. Azure Blob Storage에 로그를 작성할 필요가 없으며 데이터를 수집하는 데 에이전트가 필요하지 않습니다.

다음 표에서는 Azure Application Gateway 분석 및 네트워크 보안 그룹 분석에서 데이터가 수집되는 방법에 대한 데이터 수집 방법 및 기타 세부 정보를 보여 줍니다.

| 플랫폼 | 직접 에이전트 | Systems Center Operations Manager 에이전트 | Azure | Operations Manager 필요 여부 | 관리 그룹을 통해 전송되는 Operations Manager 에이전트 데이터 | 수집 빈도 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |기록될 때 |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Log Analytics의 Azure Application Gateway 분석 솔루션

![Azure Application Gateway 분석 기호](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Application Gateway에는 다음 로그가 지원됩니다.

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Application Gateway에는 다음 메트릭이 지원됩니다.

* 5분 처리량

### <a name="install-and-configure-the-solution"></a>솔루션 설치 및 구성
다음 지침을 사용하여 Azure Application Gateway 분석 솔루션을 설치하고 구성합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 Azure Application Gateway 분석 솔루션을 사용하도록 설정합니다.
2. 모니터링할 [Application Gateway](../application-gateway/application-gateway-diagnostics.md)에 대해 진단 로깅을 사용하도록 설정합니다.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>포털에서 Azure Application Gateway 진단 사용 설정

1. Azure Portal에서 모니터링할 Application Gateway 리소스로 이동합니다.
2. *진단 로그*를 선택하여 다음 페이지를 엽니다.

   ![Azure Application Gateway 리소스 이미지](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. *진단 사용*을 클릭하여 다음 페이지를 엽니다.

   ![Azure Application Gateway 리소스 이미지](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. 진단을 사용하려면 *상태*에서 *켜기*를 클릭합니다.
5. *Log Analytics로 보내기* 확인란을 클릭합니다.
6. 기존 Log Analytics 작업 영역을 선택하거나 작업 영역을 새로 만듭니다.
7. 수집할 각 로그 유형에 대해 **로그** 아래의 확인란을 클릭합니다.
8. *저장*을 클릭하여 Log Analytics로의 진단 로깅을 활성화합니다.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell을 사용하여 Azure 네트워크 진단 사용 설정

다음 PowerShell 스크립트는 응용 프로그램 게이트웨이에 진단 로깅을 사용하도록 설정하는 방법에 대한 예제를 제공합니다.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Azure Application Gateway 분석 사용
![Azure Application Gateway 분석 타일 이미지](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

[개요]에서 **Azure Application Gateway 분석** 타일을 클릭한 후 로그 요약을 확인한 후 다음 범주에 대한 세부 정보를 파악할 수 있습니다.

* Application Gateway 액세스 로그
  * Application Gateway 액세스 로그에 대한 클라이언트 및 서버 오류
  * 각 Application Gateway의 시간당 요청
  * 각 Application Gateway의 시간당 실패한 요청
  * Application Gateway에 대한 사용자 에이전트별 오류
* Application Gateway 성능 
  * Application Gateway에 대한 호스트 상태
  * Application Gateway 실패한 요청에 대해 최대값 및 95번째 백분위수

![Azure Application Gateway 분석 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Azure Application Gateway 분석 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

**Azure Application Gateway 분석** 대시보드의 블레이드 중 하나에서 요약 정보를 검토한 다음 하나를 클릭하여 로그 검색 페이지에서 해당 항목에 대한 세부 정보를 봅니다.

로그 검색 페이지에서, 시간별 결과, 자세한 결과 및 로그 검색 기록을 볼 수 있습니다. 패싯으로 필터링하여 결과 범위를 좁힐 수 있습니다.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Log Analytics의 Azure 네트워크 보안 그룹 분석 솔루션

![Azure 네트워크 보안 그룹 분석 기호](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

네트워크 보안 그룹에는 다음 로그가 지원됩니다.

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>솔루션 설치 및 구성
다음 지침을 사용하여 Azure Networking Analytics 솔루션을 설치 및 구성합니다.

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview)에서 또는 [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 Azure 네트워크 보안 그룹 분석 솔루션을 사용하도록 설정합니다.
2. 모니터링할 [네트워크 보안 그룹](../virtual-network/virtual-network-nsg-manage-log.md) 리소스에 대해 진단 로깅을 사용하도록 설정합니다.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>포털에서 Azure 네트워크 보안 그룹 진단 사용 설정

1. Azure Portal에서 모니터링할 네트워크 보안 그룹 리소스로 이동합니다.
2. *진단 로그*를 선택하여 다음 페이지를 엽니다.

   ![Azure 네트워크 보안 그룹 리소스 이미지](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. *진단 사용*을 클릭하여 다음 페이지를 엽니다.

   ![Azure 네트워크 보안 그룹 리소스 이미지](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. 진단을 사용하려면 *상태*에서 *켜기*를 클릭합니다.
5. *Log Analytics로 보내기* 확인란을 클릭합니다.
6. 기존 Log Analytics 작업 영역을 선택하거나 작업 영역을 새로 만듭니다.
7. 수집할 각 로그 유형에 대해 **로그** 아래의 확인란을 클릭합니다.
8. *저장*을 클릭하여 Log Analytics로의 진단 로깅을 활성화합니다.

### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell을 사용하여 Azure 네트워크 진단 사용 설정

다음 PowerShell 스크립트는 네트워크 보안 그룹에 진단 로깅을 사용하도록 설정하는 방법에 대한 예제를 제공합니다.
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Azure 네트워크 보안 그룹 분석 사용
[개요]에서 **Azure 네트워크 보안 그룹 분석** 타일을 클릭한 후 로그 요약을 확인한 후 다음 범주에 대한 세부 정보를 파악할 수 있습니다.

* 네트워크 보안 그룹 차단 흐름
  * 차단된 흐름이 있는 네트워크 보안 그룹 규칙
  * 차단된 흐름이 있는 MAC 주소 
* 네트워크 보안 그룹 허용 흐름
  * 허용된 흐름이 있는 네트워크 보안 그룹 규칙
  * 허용된 흐름이 있는 MAC 주소 

![Azure 네트워크 보안 그룹 분석 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Azure 네트워크 보안 그룹 분석 대시보드 이미지](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

**Azure 네트워크 보안 그룹 분석** 대시보드의 블레이드 중 하나에서 요약 정보를 검토한 다음 하나를 클릭하여 로그 검색 페이지에서 해당 항목에 대한 세부 정보를 봅니다.

로그 검색 페이지에서, 시간별 결과, 자세한 결과 및 로그 검색 기록을 볼 수 있습니다. 패싯으로 필터링하여 결과 범위를 좁힐 수 있습니다.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>이전 Networking Analytics 솔루션에서 마이그레이션
2017년 1월 Azure Application Gateway 및 Azure 네트워크 보안 그룹에서 Log Analytics로 로그를 보내도록 지원하는 방법이 변경되었습니다. 이러한 변경은 다음과 같은 이점을 제공합니다.
+ 저장소 계정을 사용할 필요 없이 Log Analytics에 로그 직접 기록
+ Log Analytics에서 사용할 수 있는 로그가 생성된 이후의 대기 시간 감소
+ 구성 단계 감소
+ 모든 유형의 Azure 진단을 위한 공통 형식

업데이트된 솔루션을 사용하려면 다음을 수행합니다.

1. [Azure Application Gateway에서 Log Analytics로 직접 보내도록 진단을 구성합니다](#enable-azure-application-gateway-diagnostics-in-the-portal).
2. [Azure 네트워크 보안 그룹에서 Log Analytics로 직접 보내도록 진단을 구성합니다](#enable-azure-network-security-group-diagnostics-in-the-portal).
2. [솔루션 갤러리에서 Log Analytics 솔루션 추가](log-analytics-add-solutions.md)에서 설명한 프로세스를 사용하여 *Azure Application Gateway 분석* 및 *Azure 네트워크 보안 그룹 분석* 솔루션을 사용하도록 설정합니다.
3. 새 데이터 형식을 사용하도록 저장된 쿼리, 대시보드 또는 경고를 업데이트합니다.
  + 형식은 AzureDiagnostics로 변경합니다. ResourceType을 사용하여 Azure 네트워킹 로그로 필터링할 수 있습니다.

    | 다음 위치 대신 | 사용: |
    | --- | --- |
    | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; where ResourceType="APPLICATIONGATEWAYS" and OperationName=="ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; where ResourceType=="APPLICATIONGATEWAYS" and OperationName=ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + 이름에 \_s, \_d 또는 \_g 접미사가 있는 필드의 경우 첫 번째 문자를 소문자로 변경합니다.
   + 이름에 \_o 접미사가 있는 모든 필드의 경우 데이터는 중첩된 필드 이름에 기반하여 개별 필드로 분할합니다.
4. *Azure Networking Analytics(사용되지 않음)* 솔루션을 제거합니다.
  + PowerShell을 사용하는 경우 `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`를 사용합니다.

변경 전에 수집된 데이터는 새 솔루션에서 볼 수 없습니다. 이전 형식 및 필드 이름을 사용하여 이 데이터를 계속 쿼리할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>다음 단계
* [Log Analytics의 로그 검색](log-analytics-log-searches.md)을 사용하여 자세한 Azure 진단 데이터를 확인합니다.
