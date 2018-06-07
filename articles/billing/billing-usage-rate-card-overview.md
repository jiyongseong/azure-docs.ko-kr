---
title: Azure 청구 API | Microsoft Docs
description: Azure 리소스 소비 및 추세에 대한 통찰력을 제공하는 데 사용되는 Azure 청구 사용량 및 RateCard API에 대한 자세한 정보를 제공합니다.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: mobandyo
ms.openlocfilehash: e26d8ad1f08eb711dc22b45d48f8dc326d8c17c7
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Azure Billing API를 사용하여 프로그래밍 방식으로 Azure 사용량에 대한 통찰력을 얻기
Azure Billing API를 사용하여 사용량 및 리소스 데이터를 기본 설정된 데이터 분석 도구로 끌어옵니다. Azure 리소스 사용량 및 RateCard API를 통해 비용을 정확하게 예측하고 관리할 수 있습니다. API는 Azure Resource Manager가 노출한 API의 제품군의 일부로서 리소스 공급자로 구현됩니다.  

## <a name="azure-invoice-download-api-preview"></a>Azure 청구서 다운로드 API(미리 보기)
[옵트인이 완료되면](billing-manage-access.md#opt-in) 미리 보기 버전의 [청구서 API](/rest/api/billing)를 사용하여 청구서를 다운로드합니다. 기능은 다음과 같습니다.

* **Azure 역할 기반 Access Control** - [Azure Portal](https://portal.azure.com) 또는 [Azure PowerShell cmdlet](/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다.
* **날짜 필터링** - 모든 청구서를 청구일 기간 종료 날짜의 반대 순서로 가져오려면 `$filter` 매개 변수를 사용합니다. 

> [!NOTE]
> 이 기능은 미리 보기의 첫 번째 버전이며 이전 버전과 호환되지 않는 변경 내용이 적용될 수 있습니다. 현재 특정 구독 제품(EA, CSP, AIO는 지원되지 않음) 및 Azure Germany에서 사용할 수 없습니다.

## <a name="azure-resource-usage-api-preview"></a>Azure 리소스 사용량 API(미리 보기)
Azure [리소스 사용량 API](https://msdn.microsoft.com/library/azure/mt219003)를 사용하여 예상된 Azure 사용량 데이터를 가져올 수 있습니다. API에는 다음이 포함됩니다.

* **Azure 역할 기반 Access Control** - [Azure Portal](https://portal.azure.com) 또는 [Azure PowerShell cmdlet](/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 구독의 사용 현황 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 청구서 읽기 권한자, 읽기 권한자, 소유자 또는 참가자 역할에 추가해야 합니다.
* **매시간 또는 매일 집계** -호출자는 Azure 사용 데이터를 매시간 버킷 또는 매일 버킷으로 할지 지정할 수 있습니다. 기본값은 매일입니다.
* **인스턴스 메타데이터(리소스 태그 포함)** – 정규화된 리소스 uri(/subscriptions/{subscription-id}/..)와 같은 인스턴스 수준 정보, 리소스 그룹 정보 및 리소스 태그를 가져옵니다. 이 메타데이터를 통해 청구 구간 같은 사용 사례에 대한 태그로 사용을 프로그래밍 방식으로 명확하게 할당할 수 있습니다.
* **리소스 메타데이터** - 측정기 이름, 측정기 범주, 측정기 하위 범주, 단위 및 하위 지역과 같은 리소스 세부 정보를 통해 호출자는 사용된 것을 더 잘 이해할 수 있습니다. Azure Portal, Azure 사용 CSV, EA 청구 CSV 및 다른 공용 환경을 통해 리소스 메타데이터 용어를 정렬하기 위해 노력하여 환경을 통해 데이터를 서로 연결할 수 있도록 합니다.
* **다양한 제품 유형에 대한 사용 현황** - [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription)를 제외한 종량제, MSDN, 금액 약정, 금액 크레딧 및 EA와 같은 제품 유형에 대한 사용 현황 데이터가 제공됩니다.

## <a name="azure-resource-ratecard-api-preview"></a>Azure 리소스 RateCard API(미리 보기)
[Azure 리소스 RateCard API](https://msdn.microsoft.com/library/azure/mt219005)를 사용하여 각 예상 가격 정보 및 사용 가능한 Azure 리소스의 목록을 가져옵니다. API에는 다음이 포함됩니다.

* **Azure 역할 기반 Access Control** - [Azure Portal](https://portal.azure.com) 또는 [Azure PowerShell cmdlet](/powershell/azure/overview)을 통해 액세스 정책을 구성하여 사용자 또는 응용 프로그램이 RateCard 데이터에 액세스를 얻도록 지정할 수 있습니다. 호출자가 인증에 대한 표준 Azure Active Directory 토큰을 사용해야 합니다. 호출자가 특정 Azure 구독에 대한 사용 데이터에 액세스하려면 호출자를 판독기, 소유자 또는 참가자 역할에 추가해야 합니다.
* **종량제, MSDN, 금액 약정 및 금액 크레딧 제품에 대한 지원(EA 및 [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card)는 지원되지 않음)** - 이 API는 Azure 제품 수준 속도 정보를 제공합니다.  API의 호출자는 제안 정보를 전달하여 리소스 세부 정보 및 속도를 가져와야 합니다. 현재 EA는 등 당 사용자 지정된 속도를 제공하기 때문에 EA 속도를 제공할 수 없습니다. 

## <a name="scenarios"></a>시나리오
다음은 사용 및 RateCard Api의 조합을 사용하여 가능하게 된 일부 시나리오입니다.

* **해당월 Azure 소비** - 사용 및 RateCard API 조합을 사용하여 해당 월 동안 클라우드 소비에 대한 높은 통찰력을 얻을 수 있습니다. 사용 및 요금 예측량의 매시간 및 매일 버킷을 분석할 수 있습니다.
* **경고 설정** – 사용 및 RateCard API를 사용하여 예상되는 클라우드 사용량 및 요금을 가져오고 리소스 기반 또는 통화 기반 경로를 설정합니다.
* **예측 청구** – 예상된 소비량 및 클라우드 소비량을 얻을 수 있으며 청구 주기가 끝날 때 청구를 예측할 기계 학습 알고리즘을 적용합니다.
* **사전 소비 비용 분석** – RateCard API를 사용하여 워크로드를 Azure에 이동하면 예상 사용량에 대해 얼마가 청구되는지 예측할 수 있습니다. 다른 클라우드 또는 사설 클라우드에 기존 워크로드를 가진 경우 Azure 속도로 사용을 매핑하여 Azure 소비를 더 잘 예측할 수 있습니다. 이러한 예측을 통해 제품을 피벗하고 약정 금액 및 금액 크레딧과 같은 종량제뿐만 아니라 다른 제품 유형 간에 비교 및 대조하는 기능을 제공할 수 있습니다. 또한 API를 사용하면 지역별 비용 차이를 볼 수 있으며 배포 의사 결정에 도움이 되는 가정 비용 분석을 수행할 수 있습니다.
* **가상 분석** -
  
  * 워크로드를 Azure 리소스의 다른 하위 지역에서 실행하는 것이 비용 효율적인지 또는 다른 구성에서 실행하는 것이 비용 효율적인지 여부를 확인할 수 있습니다. Azure 리소스 비용은 사용 중인 Azure 지역에 따라 달라질 수 있습니다.
  * 다른 Azure 제품 유형에서 Azure 리소스에 더 나은 속도를 제공하는지 결정할 수도 있습니다.
  
## <a name="partner-solutions"></a>파트너 솔루션
[Cloud Cruiser 및 Microsoft Azure 청구 API 통합](billing-usage-rate-card-partner-solution-cloudcruiser.md)에서는 [Azure Pack용 Cloud Cruiser의 Express](http://www.cloudcruiser.com/partners/microsoft/)가 WAP(Windows Azure Pack) 포털에서 어떻게 직접적으로 작동하는지 설명합니다. 사용자는 단일 사용자 인터페이스에서 Microsoft Azure 개인 또는 호스팅된 공용 클라우드의 운영 및 재무 측면을 완벽하게 관리할 수 있습니다.   

## <a name="next-steps"></a>다음 단계
* 다음 GitHub에서 코드 샘플을 확인합니다.
  * [청구서 API 코드 샘플](https://go.microsoft.com/fwlink/?linkid=845124)

  * [사용량 API 코드 샘플](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API 코드 샘플](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요. 



