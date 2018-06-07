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
ms.openlocfilehash: b4062aab5a453505ef4586f422a124d4bbf715cb
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
저장소는 디스크 공간이나 인덱스, 문서 또는 기타 고급 리소스의 *최대 수*의 하드 제한 중에서 먼저 충족되는 조건에 따라 제한됩니다. 다음 표에서는 저장소 제한에 대해 설명합니다. 인덱스, 문서 및 기타 개체에 대한 최대 제한은 [리소스별 제한](../articles/search/search-limits-quotas-capacity.md#index-limits)을 참조하세요.

| 리소스 | 무료 | 기본&nbsp;<sup>1</sup> | S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>2</sup> |
| -------- | --- | --- | --- | --- | --- | --- |
| SLA(서비스 수준 계약)<sup>3</sup>  |아니오 |예 |예 |예 |예 |예 |
| 파티션당 저장소 |50MB |2 GB |25GB |100GB |200GB |200GB |
| 서비스당 파티션 |해당 없음 |1 |12 |12 |12 |3 |
| 파티션 크기 |해당 없음 |2 GB |25GB |100GB |200GB |200GB |
| 복제본 |해당 없음 |3 |12 |12 |12 |12 |

<sup>1</sup> 기본 계층에는 고정된 파티션 하나만 있습니다. 이 계층에서 추가 SU는 향상된 쿼리 워크로드에 더 많은 복제본을 할당하는 데 사용됩니다.

<sup>2</sup> S3 HD에 3개의 파티션 하드 제한이 있으며, S3에 대한 파티션 제한보다 낮습니다. S3 HD에 대한 인덱스 수가 매우 높기 때문에 낮은 파티션 제한이 설정되었습니다. 컴퓨팅 리소스(스토리지 및 처리) 및 콘텐츠(인덱스 및 문서) 모두에 대해 서비스 제한이 존재하는 경우 콘텐츠 제한에 먼저 도달합니다.

<sup>3</sup> SLA(서비스 수준 계약)는 전용 리소스에서 청구 가능한 서비스에 대해 제공됩니다. 체험 서비스 및 미리 보기 기능에는 SLA가 없습니다. 청구 가능한 서비스의 경우 서비스에 충분한 중복성을 프로비전할 때 SLA가 적용됩니다. 쿼리(읽기) SLA에는 두 개 이상의 복제본이 필요합니다. 쿼리 및 인덱싱(읽기/쓰기) SLA에는 세 개 이상의 복제본이 필요합니다. 파티션의 수는 SLA 고려사항이 아닙니다. 