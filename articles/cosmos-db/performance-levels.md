---
title: 사용 중지된 Azure Cosmos DB 성능 수준 | Microsoft Docs
description: Azure Cosmos DB에서 이전에 사용할 수 있는 S1, S2 및 S3 성능 수준에 대해 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 7dc21c71-47e2-4e06-aa21-e84af52866f4
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e565f4ee4d25afb29627e6beca99fd2998cd6396
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>S1, S2 및 S3 성능 수준 사용 중지

> [!IMPORTANT] 
> 이 문서에서 설명하는 S1, S2 및 S3 성능 수준은 이제 사용 중지되어 새 Azure Cosmos DB 계정에 더 이상 사용할 수 없습니다.
>

이 문서에서는 S1, S2 및 S3 성능 수준에 대한 개요를 간략히 설명하고, 이러한 성능 수준을 사용하는 컬렉션을 2017년 말부터 단일 파티션 컬렉션으로 마이그레이션하는 방법에 대해 설명합니다. 이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- [S1, S2 및 S3 성능 수준의 사용이 중지되는 이유는?](#why-retired)
- [단일 파티션 컬렉션 및 분할된 컬렉션을 S1, S2, S3 성능 수준과 비교하면 어떻습니까?](#compare)
- [내 데이터에 중단 없이 액세스하려면 어떻게 해야 합니까?](#uninterrupted-access)
- [마이그레이션하면 내 컬렉션이 어떻게 변경됩니까?](#collection-change)
- [단일 파티션 컬렉션으로 마이그레이션하면 요금 청구는 어떻게 변경됩니까?](#billing-change)
- [저장 용량이 10GB 이상 필요한 경우 어떻게 해야 합니까?](#more-storage-needed)
- [계획된 마이그레이션 이전에 S1, S2 및 S3 성능 수준을 변경할 수 있습니까?](#change-before)
- [내 컬렉션이 마이그레이션된 시기는 어떻게 알 수 있습니까?](#when-migrated)
- [S1, S2, S3 성능 수준에서 단일 파티션 컬렉션으로 마이그레이션하려면 어떻게 해야 합니까?](#migrate-diy)
- [EA 고객에게 미치는 영향은?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>S1, S2 및 S3 성능 수준의 사용이 중지되는 이유는?

S1, S2 및 S3 성능 수준은 표준 Azure Cosmos DB 제품이 제공하는 유연성을 제공하지 않습니다. S1, S2, S3 성능 수준에서는 처리량과 저장소 용량이 둘 다 미리 설정되었으며 탄력성을 제공하지 않았습니다. 이제 Azure Cosmos DB는 처리량과 저장소를 사용자 지정할 수 있는 기능을 제공하므로 크기 조정 기능을 필요에 따라 훨씬 더 유연하게 제공할 수 있습니다.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>단일 파티션 컬렉션 및 분할된 컬렉션을 S1, S2, S3 성능 수준과 비교하면 어떻습니까?

다음 표에서는 단일 파티션 컬렉션, 분할된 컬렉션 및 S1, S2, S3 성능 수준에서 사용할 수 있는 처리량 및 저장소 옵션을 비교합니다. 다음은 미국 동부 2 지역의 예입니다.

|   |분할된 컬렉션|단일 파티션 컬렉션|S1|S2|S3|
|---|---|---|---|---|---|
|최대 처리량|Unlimited|10,000RU/s|250RU/s|1,000RU/s|2,500RU/s|
|최소 처리량|2,500RU/s|400RU/s|250RU/s|1,000RU/s|2,500RU/s|
|최대 저장소|Unlimited|10 GB|10 GB|10 GB|10 GB|
|가격(월별)|처리량: $6/100RU/s<br><br>저장소: $0.25/GB|처리량: $6/100RU/s<br><br>저장소: $0.25/GB|$25(미화)|$50(미화)|$100(미화)|

EA 고객입니까? 그렇다면 [EA 고객에게 미치는 영향은?](#ea-customer)을 참조하세요.

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>내 데이터에 중단 없이 액세스하려면 어떻게 해야 합니까?

아무 작업도 수행할 필요 없이 Cosmos DB에서 전적으로 마이그레이션을 처리합니다. S1, S2 또는 S3 컬렉션이 있는 경우 현재 컬렉션은 2017년 말부터 단일 파티션 컬렉션으로 마이그레이션됩니다. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>마이그레이션하면 내 컬렉션이 어떻게 변경됩니까?

S1 컬렉션이 있는 경우 400RU/s 처리량의 단일 파티션 컬렉션으로 마이그레이션됩니다. 400RU/s는 단일 파티션 컬렉션에서 사용할 수 있는 최소 처리량입니다. 그러나 400RU/s 단일 파티션 컬렉션에 대한 비용은 250RU/s S1 컬렉션에 지불한 비용과 거의 동일하며, 사용 가능한 여분의 150RU/s에 대해 지불하지 않습니다.

S2 컬렉션이 있는 경우 1,000RU/s 단일 파티션 컬렉션으로 마이그레이션됩니다. 처리량 수준은 변경되지 않습니다.

S3 컬렉션이 있는 경우 2,500RU/s 단일 파티션 컬렉션으로 마이그레이션됩니다. 처리량 수준은 변경되지 않습니다.

이러한 각각의 경우에서 컬렉션을 마이그레이션한 후에는 처리량 수준을 사용자 지정하거나 필요에 따라 크기를 조정하여 대기 시간이 짧은 액세스를 사용자에게 제공할 수 있습니다. 컬렉션을 마이그레이션한 후에 처리량 수준을 변경하려면 Azure Portal에서 Cosmos DB 계정을 열고 [크기 조정]을 클릭한 다음 컬렉션을 선택하고 다음 스크린샷과 같이 처리량 수준을 조정하면 됩니다.

![Azure Portal에서 처리량을 조정하는 방법](./media/performance-levels/portal-scale-throughput.png)

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-im-migrated-to-the-single-partition-collections"></a>단일 파티션 컬렉션으로 마이그레이션한 후 요금 청구는 어떻게 변경됩니까?

미국 동부 지역에 각각 10개의 S1 컬렉션(각각 1GB 저장소 사용)이 있고, 이러한 10개 S1 컬렉션을 400RU/s(최소 수준)의 10개 단일 파티션 컬렉션으로 마이그레이션한다고 가정합니다. 한 달 동안 10개의 단일 파티션 컬렉션을 유지하는 경우 청구서는 다음과 같이 표시됩니다.

![10개 S1 컬렉션 및 10개 단일 파티션 컬렉션에 대한 가격 비교](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>저장 용량이 10GB 이상 필요한 경우 어떻게 해야 합니까?

S1, S2 또는 S3 성능 수준 컬렉션 또는 단일 파티션 컬렉션이 있든 간에 모두 10GB 저장소를 사용할 수 있는 경우 Cosmos DB 데이터 마이그레이션 도구를 사용하여 거의 무제한 저장소가 있는 분할된 컬렉션으로 데이터를 마이그레이션할 수 있습니다. 분할된 컬렉션의 이점에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](sql-api-partition-data.md)을 참조하세요. 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>계획된 마이그레이션 이전에 S1, S2 및 S3 성능 수준을 변경할 수 있습니까?

S1, S2 및 S3 성능을 갖춘 기존 계정만 포털을 통하거나 프로그래밍 방식으로 성능 수준 계층을 변경할 수 있습니다. S1, S3 또는 S3에서 단일 파티션 컬렉션으로 변경하면 S1, S2 또는 S3 성능 수준으로 되돌릴 수 없습니다.

<a name="when-migrated"></a>

## <a name="how-will-i-know-when-my-collection-has-migrated"></a>내 컬렉션이 마이그레이션된 시기는 어떻게 알 수 있습니까?

마이그레이션은 2017년 말에 시행됩니다. S1, S2 또는 S3 성능 수준을 사용하는 컬렉션이 있는 경우 마이그레이션을 수행하기 전에 Cosmos DB 팀에서 메일로 연락을 드립니다. 마이그레이션이 완료되면 Azure Portal에서 컬렉션에 표준 가격 정책이 적용되었음을 보여 줍니다.

![표준 가격 책정 계층으로 마이그레이션된 컬렉션을 확인하는 방법](./media/performance-levels/portal-standard-pricing-applied.png)

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>S1, S2, S3 성능 수준에서 단일 파티션 컬렉션으로 마이그레이션하려면 어떻게 해야 합니까?

Azure Portal을 사용하거나 프로그래밍 방식으로 S1, S2 및 S3 성능 수준에서 단일 파티션 컬렉션으로 마이그레이션할 수 있습니다. 계획된 마이그레이션 이전에 이 작업을 직접 수행하여 단일 파티션 컬렉션에서 사용할 수 있는 유연한 처리량 옵션을 활용할 수 있거나 2017년 말에 사용자를 위해 컬렉션을 마이그레이션할 것입니다.

**Azure Portal을 사용하여 단일 파티션 컬렉션으로 마이그레이션하려면**

1. [**Azure Portal**](https://portal.azure.com)에서 **Azure Cosmos DB**를 클릭한 다음 수정할 Cosmos DB 계정을 선택합니다. 
 
    **Azure Cosmos DB**가 이동 표시줄에 없는 경우 >를 클릭하고 **데이터베이스**까지 스크롤한 다음 **Azure Cosmos DB**, 계정을 차례로 선택합니다.  

2. 리소스 메뉴의 **컨테이너** 아래에서 **규모**를 클릭하고 드롭다운 목록에서 수정할 컬렉션을 선택한 다음 **가격 책정 계층**을 클릭합니다. 미리 정의된 처리량을 사용하는 계정은 S1, S2 또는 S3의 가격 책정 계층을 보유합니다.  **가격 책정 계층 선택** 페이지에서 **표준**을 클릭하여 사용자 정의 처리량을 변경한 다음 **선택**을 클릭하여 변경 내용을 저장합니다.

    ![처리량 값을 변경하는 위치를 보여 주는 설정 페이지의 스크린 샷](./media/performance-levels/change-performance-set-thoughput.png)

3. **규모** 페이지로 돌아가면 **가격 책정 계층**이 **표준**으로 변경되어 있으며 **처리량(RU/s)** 상자에 기본값인 400이 표시됩니다. 처리량을 400~10,000개 RU/s( [요청 단위](request-units.md)/초) 사이로 설정합니다. 월별 예상 비용을 제공하기 위해 페이지 아래쪽의 **월별 예상 비용**이 자동으로 업데이트됩니다. 

    >[!IMPORTANT] 
    > 변경 내용을 저장하고 표준 가격 책정 계층으로 이동하면 S1, S2 또는 S3 성능 수준으로 롤백할 수 없습니다.

4. **저장**을 클릭하여 변경 내용을 저장합니다.

    더 많은 처리량(10,000RU/s 초과) 또는 더 많은 저장소(10GB 초과)가 필요하다고 판단되는 경우 분할된 컬렉션을 만들 수 있습니다. 단일 파티션 컬렉션을 분할된 컬렉션으로 마이그레이션하려면 [단일 파티션에서 분할된 컬렉션으로 마이그레이션](sql-api-partition-data.md#migrating-from-single-partition)을 참조하세요.

    > [!NOTE]
    > S1, S2 또는 S3에서 표준 계층으로 변경하는 데에는 최대 2분 정도 걸릴 수 있습니다.
    > 
    > 

**.NET SDK를 사용하여 단일 파티션 컬렉션으로 마이그레이션하려면**

컬렉션의 성능 수준 변경에 대한 다른 옵션은 Azure Cosmos DB SDK를 통하는 것입니다. 이 섹션에서는 [SQL .NET API](sql-api-sdk-dotnet.md)를 사용한 컬렉션의 성능 수준 변경에 대해서만 다루고 있으나, 다른 SDK의 경우에도 프로세스는 유사합니다.

다음 코드 조각에서는 컬렉션 처리량을 5,000RU/s로 변경합니다.
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

offer 메서드에 대한 자세한 내용 및 추가 예제를 보려면 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx)을 방문하세요.

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>EA 고객에게 미치는 영향은?

EA 고객에게 적용한 가격은 현재 계약이 종료될 때까지 보호됩니다.

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB의 가격 책정 및 데이터 관리에 대한 자세한 내용은 다음 리소스를 참조하세요.

1.  [Cosmos DB의 데이터 분할](sql-api-partition-data.md). 단일 파티션 컨테이너와 분할된 컨테이너 간의 차이점을 이해하고 매끄럽게 크기를 조정하는 분할 전략을 구현하는 데 유용한 팁을 알아봅니다.
2.  [Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/). 처리량 프로비전 및 저장소 소비 비용에 대해 알아봅니다.
3.  [요청 단위](request-units.md) 읽기, 쓰기, 쿼리와 같은 다양한 작업 유형의 처리량 사용에 대해 알아봅니다.
