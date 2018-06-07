---
title: 포함 파일
description: 포함 파일
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
단일 구독 내에서 각각 특정 계층에 프로비전되고 각 계층에서 허용되는 서비스 수로만 제한되는 여러 서비스를 만들 수 있습니다. 예를 들어, 기본 계층에 최대 12개의 서비스를 만들고, 동일한 구독 내의 S1 계층에 12개의 다른 서비스를 만들 수 있습니다. 계층에 대한 자세한 내용은 [Azure Search에 대한 SKU 또는 계층 선택](../articles/search/search-sku-tier.md)을 참조하세요.

최대 서비스 제한은 요청 시 설정할 수 있습니다. 동일한 구독 내에서 더 많은 서비스가 필요한 경우 Azure 지원에 문의하세요.

| 리소스            | 체험&nbsp;<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| 최대 서비스    |1     | 12    | 12  | 6  | 6  | 6     |
| SU의 최대 크기&nbsp;<sup>2</sup> |해당 없음 |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> 체험 계층은 전용이 아닌 공유 리소스를 기반으로 합니다. 강화는 공유 리소스에서 지원되지 않습니다.

<sup>2</sup> SU(검색 단위)는 *복제본* 또는 *파티션*으로 할당되는 청구 가능 단위입니다. 저장소, 인덱싱 및 쿼리 작업에 대해 두 리소스가 모두 필요합니다. SU 계산에 대한 자세한 내용은 [쿼리 및 인덱스 작업을 위한 리소스 수준 확장](../articles/search/search-capacity-planning.md)을 참조하세요. 