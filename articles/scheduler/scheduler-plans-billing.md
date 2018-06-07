---
title: Azure Scheduler의 버전 및 요금 청구
description: Azure Scheduler의 버전 및 요금 청구
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Azure Scheduler의 버전 및 요금 청구
## <a name="job-collection-plans"></a>작업 컬렉션 버전
작업 컬렉션은 Azure Scheduler에서 청구 가능한 엔터티입니다. 작업 컬렉션은 많은 작업을 포함하며 Standard, P10 Premium, P20 Premium 등, 3가지 버전으로 제공됩니다.

| **작업 컬렉션 버전** | **작업 컬렉션당 최대 작업 수** | **최대 되풀이** | **구독당 최대 작업 컬렉션** | **제한** |
|:--- |:--- |:--- |:--- |:--- |
| **Standard** |작업 컬렉션당 50개 작업 |1분당 1회. 1분에 한 번 이상 작업을 실행할 수 없습니다. |구독에는 최대 100개의 Standard 작업 컬렉션이 허용됩니다. |Scheduler의 모든 기능 액세스 |
| **P10 Premium** |작업 컬렉션당 50개 작업 |1분당 1회. 1분에 한 번 이상 작업을 실행할 수 없습니다. |구독에는 최대 10,000개의 P10 Premium 작업 컬렉션이 허용됩니다. 자세한 내용은 <a href="mailto:wapteams@microsoft.com">저희에게 문의</a>하세요. |Scheduler의 모든 기능 액세스 |
| **P20 Premium** |작업 컬렉션당 1,000개 작업 |1분당 1회. 1분에 한 번 이상 작업을 실행할 수 없습니다. |구독에는 최대 10,000개의 P20 Premium 작업 컬렉션이 허용됩니다. 자세한 내용은 <a href="mailto:wapteams@microsoft.com">저희에게 문의</a>하세요. |Scheduler의 모든 기능 액세스 |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>작업 컬렉션 버전의 업그레이드 및 다운그레이드
작업 컬렉션 버전은 Standard, P10 Premium 및 P20 Premium 버전 간에 언제든 업그레이드 또는 다운그레이드할 수 있습니다.

## <a name="billing-and-azure-plans"></a>청구 및 Azure 버전
Standard 작업 컬렉션이 100개가 넘을 경우(Standard 청구 단위 10개) 모든 작업 컬렉션을 Premium 버전으로 하는 것이 더 낫습니다.

Standard 작업 컬렉션이 하나이고 Premium 작업 컬렉션이 하나라면 Standard 청구 단위 1개 *와* Premium 청구 단위 1개가 청구됩니다. Scheduler 서비스는 Standard 또는 Premium 버전으로 설정된 활성 작업 컬렉션의 수에 따라 청구되며 이에 대해서는 다음 두 섹션에서 상세히 설명하겠습니다.

## <a name="standard-billable-units"></a>Standard 청구 가능 단위
Standard 청구 가능 단위는 최대 10개의 Standard 작업 컬렉션을 포함할 수 있습니다. Standard 작업 컬렉션에는 작업 컬렉션당 최대 50개의 작업이 있을 수 있으므로 Standard 청구 단위 1개에서는 최대 500개 작업(매월 최대 약 2,200만 개의 작업 실행)이 있을 수 있습니다.

Standard 작업 컬렉션 수가 1~10인 경우 1개의 Standard 청구 단위에 대해 청구됩니다. Standard 작업 컬렉션 수가 11~20인 경우 2개의 Standard 청구 단위에 대해 청구됩니다. Standard 작업 컬렉션 수가 21~30인 경우 3개의 Standard 청구 단위에 대해 청구되는 식입니다.

## <a name="p10-premium-billable-units"></a>P10 Premium 청구 가능 단위
P10 Premium 청구 가능 단위는 최대 10,000개의 P10 Premium 작업 컬렉션을 포함할 수 있습니다. P10 Premium 작업 컬렉션에는 작업 컬렉션당 최대 50개의 작업이 있을 수 있습니다. 따라서, 1개의 프리미엄 청구 단위로 구독에서 최대 500,000개 작업(매월 최대 약 220억 개의 작업 실행)이 있을 수 있습니다.

Standard 작업 컬렉션 수가 1~10,000인 경우 1개의 P10 Premium 청구 단위에 대해 청구됩니다. Standard 작업 컬렉션 수가 10,001~20,000인 경우 2개의 P10 Premium 청구 단위에 대해 청구됩니다.

따라서 P10 Premium 작업 컬렉션은 Standard 작업 컬렉션과 동일하게 작동하지만, 응용 프로그램에 작업 컬렉션이 많은 경우 경제적입니다.

## <a name="p20-premium-billable-units"></a>P20 Premium 청구 가능 단위
P20 Premium 청구 가능 단위는 최대 5,000개의 P20 Premium 작업 컬렉션을 포함할 수 있습니다. P20 Premium 작업 컬렉션에는 작업 컬렉션당 최대 1,000개의 작업이 있을 수 있습니다. 따라서, 1개의 프리미엄 청구 단위로 구독에서 최대 5,000,000개 작업(매월 최대 약 2200억 개의 작업 실행)이 있을 수 있습니다.

P20 Premium 작업 컬렉션은 P10 Premium 작업 컬렉션과 동일한 기능을 제공하지만, 작업 컬렉션당 훨씬 더 많은 수의 작업을 지원하며, P10 Premium에 비해 총 작업 수도 훨씬 더 많으므로 확장성도 높아집니다.

## <a name="billing-and-active-status"></a>청구 및 활성 상태
전체 구독이 청구상의 문제로 일시적으로 비활성화되지 않는 한 작업 컬렉션은 항상 활성 상태입니다. 작업 컬렉션이 청구되지 않게 하는 유일한 방법은 *Free* 버전으로 설정하거나 작업 컬렉션을 삭제하는 것입니다.

단일 작업에서 작업 컬렉션 안의 모든 작업을 비활성화할 수 있으나 작업 컬렉션의 청구 상태가 바뀌지는 않으며 작업 컬렉션은 *계속* 청구됩니다. 마찬가지로 빈 작업 컬렉션도 활성 상태로 간주되어 청구됩니다.

## <a name="pricing"></a>가격
가격 세부 정보는 [Scheduler 가격 책정](https://azure.microsoft.com/pricing/details/scheduler/)을 참조하세요.

## <a name="see-also"></a>참고 항목
 [Scheduler란?](scheduler-intro.md)

 [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure Portal에서 Scheduler 사용 시작](scheduler-get-started-portal.md)

 [Azure Scheduler REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure Scheduler 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Azure Scheduler 아웃바운드 인증](scheduler-outbound-authentication.md)

