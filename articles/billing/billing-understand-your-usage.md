---
title: Azure 세부 사용량 이해
description: Azure 구독에 대한 세부 사용량 CSV 섹션을 읽고 이해하는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: tonguyen
ms.openlocfilehash: 0f698bff1506484e1dd4afe22f7c14e0a710937c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="understand-terms-on-your-microsoft-azure-detailed-usage-charges"></a>Microsoft Azure 세부 사용 요금 조건 이해 

세부 사용 요금 CSV 파일에는 현재 청구 기간에 대한 일별 요금과 측정기 수준 사용 요금이 포함되어 있습니다. 

세부 사용량 파일을 가져오려면 [Azure 청구서 및 일별 사용 현황 데이터를 받는 방법](billing-download-azure-invoice-daily-usage-date.md)을 참조하세요.
스프레드시트 응용 프로그램에서 열 수 있는 쉼표로 구분된 값(.csv) 파일 형식으로 제공됩니다. 사용할 수 있는 두 가지 버전이 표시되면 버전 2를 다운로드합니다. 최신 파일 형식입니다.

사용 요금은 구독에 대한 총 **월별** 요금입니다. 사용 요금에서는 신용 또는 할인이 고려되지 않습니다.

>[!VIDEO https://www.youtube.com/embed/p13S350M2Vk]

## <a name="detailed-terms-and-descriptions-of-your-detailed-usage-file"></a>세부 사용량 파일에 대한 자세한 내용 및 설명

다음 섹션에서는 자세한 사용 현황 파일 버전 2에 표시된 중요한 용어에 대해 설명합니다.

### <a name="statement"></a>문

자세한 사용 현황 CSV 파일의 위쪽 섹션에는 해당 월 청구 기간 동안 사용된 서비스가 표시됩니다. 다음 표에는 이 섹션에 표시되는 용어와 설명이 나와 있습니다.

| 용어 | 설명 |
| --- | --- |
|청구 기간 |측정기가 사용된 청구 기간입니다. |
|미터 범주 |사용 현황의 최상위 서비스를 식별합니다. |
|측정기 하위 범주 |요율에 영향을 줄 수 있는 Azure 서비스의 유형을 정의합니다. |
|미터 이름 |사용 중인 측정기에 대한 측정 단위를 식별합니다. |
|측정기 영역 |데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
|SKU |각 Azure 측정기에 대한 고유한 시스템 식별자를 식별합니다. |
|단위 |서비스 요금이 청구되는 단위를 식별합니다. GB, 시간, 10,000초 등을 예로 들 수 있습니다. |
|사용량 |청구 기간 동안 사용된 측정기의 양입니다. |
|포함된 수량 |현재 청구 기간 동안 무료로 제공되는 측정기의 양입니다. |
|초과 수량 |소비된 수량과 포함된 수량 간의 차이를 나타냅니다. 이 수량에 대해 요금이 청구됩니다. 제품에 포함된 수량이 없는 종량제 제품의 경우 이 합계는 소비된 수량과 동일합니다. |
|약정 기간 내 |6개월 또는 12개월 제품과 관련된 약정 금액에서 공제된 측정기 요금을 나타냅니다. 측정기 요금은 시간순으로 공제됩니다. |
|통화 |현재 청구 기간에 사용된 통화입니다. |
|초과분 |6개월 또는 12개월 제품과 관련된 약정 금액을 초과하는 측정기 요금을 나타냅니다. |
|약정 요율 |6개월 또는 12개월 제품과 관련된 총 약정 금액을 기반으로 하는 약정 요율을 나타냅니다. |
|비용 |청구 가능 단위당 청구되는 요율입니다. |
|값 |평균 수량 열과 요율 열을 곱한 결과를 보여 줍니다. 소비된 수량이 포함된 수량을 초과하지 않으면 이 열에 청구되는 비용이 없습니다. |

### <a name="daily-usage"></a>일일 사용량

CSV 파일의 일일 사용량 섹션에는 청구 요금에 영향을 주는 사용 현황 세부 정보가 표시됩니다. 다음 표에는 이 섹션에 표시되는 용어와 설명이 나와 있습니다.

| 용어 | 설명 |
| --- | --- |
|사용 날짜 |측정기가 사용된 날짜입니다. |
|미터 범주 |이 사용 현황이 속한 최상위 서비스를 식별합니다. |
|측정기 ID |가격 결제에 사용하는 대금 청구 측정기 식별자입니다. |
|측정기 하위 범주 |요율에 영향을 줄 수 있는 Azure 서비스 유형을 정의합니다. |
|측정기 이름 |사용 중인 측정기에 대한 측정 단위를 식별합니다. |
|측정기 영역 |데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
|단위 |측정기 요금이 청구되는 단위를 식별합니다. GB, 시간, 10,000초 등을 예로 들 수 있습니다. |
|사용량 |해당 날짜에 사용된 측정기의 양입니다. |
|리소스 위치 |측정기가 실행되고 있는 데이터 센터를 식별합니다. |
|사용되는 서비스 |사용한 Azure 플랫폼 서비스입니다. |
|리소스 그룹 |배포된 측정기가 실행되는 리소스 그룹입니다. <br/><br/>자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)를 참조하세요. |
|인스턴스 ID | 측정기에 대한 식별자입니다. <br/><br/> 식별자를 만들 때 측정기에 대해 지정한 이름을 포함합니다. 리소스의 이름 또는 정규화된 리소스 ID입니다. 자세한 내용은 [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources)를 참조하세요. |
|태그들 | 측정기에 할당하는 태그입니다. 태그를 사용하여 청구 레코드를 그룹화합니다.<br/><br/>예를 들어 측정기를 사용하는 부서는 비용을 배분하는 데 태그를 사용할 수 있습니다. 태그를 내보내도록 지원하는 서비스에는 [Azure Resource Manager API](https://docs.microsoft.com/rest/api/resources/resources)를 사용하여 프로비전된 가상 머신, 저장소 및 네트워킹 서비스가 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)을 참조하세요. |
|추가 정보 |서비스 특정 메타데이터입니다. 예를 들어 가상 컴퓨터용 이미지 형식입니다. |
|서비스 정보 1 |구독에서 서비스가 속한 프로젝트 이름입니다. |
|서비스 정보 2 |선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. |

## <a name="how-do-i-make-sure-that-the-charges-in-my-detailed-usage-file-are-correct"></a>세부 사용량 파일의 요금이 맞는지 확인하려면 어떻게 해야 하나요?
세부 사용량 파일에 더 자세한 내용을 보려는 요금이 있는 경우 [Microsoft Azure 청구서 이해](./billing-understand-your-bill.md)를 참조하세요.

## <a name="external"></a>외부 서비스 요금은 어떤가요?
외부 서비스(또는 Marketplace 주문)는 독립 서비스 공급업체에서 제공하며 별도로 청구됩니다. Azure 청구서에 요금이 표시되지 않습니다. 자세한 내용은 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)를 참조하세요.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?)하여 문제를 신속하게 해결하세요.
