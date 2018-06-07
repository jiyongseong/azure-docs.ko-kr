---
title: Azure Analysis Services 규모 확장 | Microsoft Docs
description: 규모 확장으로 Azure Analysis Services 서버 복제
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ee9210953306fbe317e9ed63c02fb90452ffbd15
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services 규모 확장

규모 확장을 통해 클라이언트 쿼리를 쿼리 풀의 여러 *쿼리 복제본* 간에 분산시켜 높은 쿼리 워크로드 동안 응답 시간을 줄일 수 있습니다. 또한 클라이언트 쿼리가 작업 처리에 의해 부정적인 영향을 받지 않도록 쿼리 풀에서 처리가 구분될 수도 있습니다. Azure Portal 또는 Analysis Services REST API를 사용하여 규모 확장을 구성할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

일반적인 서버 배포에서는 한 서버가 처리 서버 및 쿼리 서버 역할을 모두 수행합니다. 서버 모델에 대한 클라이언트 쿼리 수가 서버 계획에 대한 QPU(쿼리 처리 단위)를 초과하거나 높은 쿼리 워크로드와 동시에 모델 처리가 발생하면 성능이 저하될 수 있습니다. 

규모 확장을 사용하면 최대 7개의 추가 쿼리 복제본으로 쿼리 풀을 만들 수 있습니다(총 8개, 서버 포함). 중요한 시간에 QPU 요구를 충족시키기 위해 쿼리 복제본 수를 확장하고 언제든지 처리 서버를 쿼리 풀에서 분리할 수 있습니다. 모든 쿼리 복제본은 서버와 동일한 지역에 만들어집니다.

쿼리 풀에 있는 쿼리 복제본의 수와 관계없이 처리 워크로드는 쿼리 복제본 간에 분산되지 않습니다. 단일 서버가 처리 서버 역할을 수행합니다. 쿼리 복제본은 쿼리 풀의 각 복제본 간에 동기화된 모델에 대한 쿼리만 제공합니다. 

처리 작업이 완료되면 처리 서버와 쿼리 복제본 서버 간에 동기화를 수행해야 합니다. 처리 작업을 자동화할 때는 처리 작업이 성공적으로 완료될 때 동기화 작업을 구성하는 것이 중요합니다. PowerShell 또는 REST API를 사용하거나 포털에서 수동으로 동기화를 수행할 수 있습니다.

> [!NOTE]
> 규모 확장은 표준 가격 책정 계층의 서버에서 사용할 수 있습니다. 각 쿼리 복제본은 서버와 동일한 요금으로 청구됩니다.

> [!NOTE]
> 규모 확장은 서버의 사용 가능한 메모리 양을 증가시키지 않습니다. 메모리를 늘리려면 계획을 업그레이드해야 합니다.

## <a name="monitor-qpu-usage"></a>QPU 사용량 모니터링

 서버의 규모 확장이 필요한지 확인하려면 메트릭을 사용하여 Azure Portal에서 서버를 모니터링하십시오. QPU가 정기적으로 최대로 출력되는 지점까지 증가하는 경우 모델에 대한 쿼리 수가 계획의 QPU 한도를 초과하고 있음을 의미합니다. 쿼리 풀 작업 큐 길이 메트릭은 쿼리 스레드 풀 큐의 쿼리 수가 사용 가능한 QPU를 초과하면 증가합니다. 자세한 내용은 [서버 메트릭 모니터링](analysis-services-monitor.md)을 참조하세요.

## <a name="configure-scale-out"></a>규모 확장 구성

### <a name="in-azure-portal"></a>Azure Portal에서

1. Azure Portal에서 **규모 확장**을 클릭합니다. 슬라이더를 사용하여 쿼리 복제본 서버 수를 선택합니다. 선택한 복제본 수는 기존 서버에 추가됩니다.

2. 쿼리 서버에서 처리 서버를 제외하려면 **쿼리 풀에서 처리 서버 구분**에서 [예]를 선택합니다.

   ![규모 확장 슬라이더](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. 새 쿼리 복제본 서버를 프로비전하려면 **저장**을 클릭합니다. 

주 서버에서 테이블 형식 모델은 복제본 서버와 동기화됩니다. 동기화가 완료되면 쿼리 풀은 들어오는 쿼리를 복제본 서버 간에 배포하기 시작합니다. 


## <a name="synchronization"></a>동기화 

새 쿼리 복제본을 프로비전하면 Azure Analysis Services가 해당 모델을 모든 복제본 간에 자동으로 복제합니다. 포털 또는 REST API를 사용하여 수동 동기화를 수행할 수도 있습니다. 모델을 처리할 때는 업데이트가 쿼리 복제본 간에 동기화되도록 동기화를 수행해야 합니다.

### <a name="in-azure-portal"></a>Azure Portal에서

**개요** > 모델 > **모델 동기화**에서.

![규모 확장 슬라이더](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
**동기화** 작업을 사용합니다.

#### <a name="synchronize-a-model"></a>모델 동기화   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>동기화 상태 가져오기  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
PowerShell을 사용하기 전에 [최신 AzureRM 모듈을 설치하거나 업데이트합니다](https://github.com/Azure/azure-powershell/releases). 

쿼리 복제본 수를 설정하려면 [Set-AzureRmAnalysisServicesServer](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)를 사용합니다. 선택적 `-ReadonlyReplicaCount` 매개 변수를 지정합니다.

동기화를 실행하려면 [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance)를 사용합니다.



## <a name="connections"></a>연결

서버의 [개요] 페이지에는 두 개의 서버 이름이 있습니다. 서버에 대한 규모 확장을 아직 구성하지 않은 경우 두 서버 이름은 동일하게 작동합니다. 서버에 대한 규모 확장을 구성한 후에는 연결 형식에 따라 적절한 서버 이름을 지정해야 합니다. 

Power BI Desktop, Excel 및 사용자 지정 앱과 같은 최종 사용자 클라이언트 연결의 경우 **서버 이름**을 사용합니다. 

SSMS, SSDT, PowerShell의 연결 문자열, Azure Function 앱 및 AMO의 경우 **관리 서버 이름**을 사용합니다. 관리 서버 이름에는 특별한 `:rw`(읽기/쓰기) 한정자가 포함됩니다. 모든 처리 작업은 관리 서버에서 수행됩니다.

![서버 이름](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>관련 정보

[서버 메트릭 모니터링](analysis-services-monitor.md)   
[Azure Analysis Services 관리](analysis-services-manage.md) 

