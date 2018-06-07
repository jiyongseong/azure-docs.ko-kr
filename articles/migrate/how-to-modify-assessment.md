---
title: Azure Migrate 평가 설정 사용자 지정 | Microsoft Docs
description: Azure Migration Planner를 사용하여 VMware VM을 Azure에 마이그레이션하기 위해 평가를 설정 및 실행하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 05/03/2018
ms.author: raynew
ms.openlocfilehash: 5054da16a6a02dddb8539011d3baa18f2bb9914a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777048"
---
# <a name="customize-an-assessment"></a>평가 사용자 지정

[Azure Migrate](migrate-overview.md)는 기본 속성으로 평가를 만듭니다. 평가를 만든 후에는 이 문서의 지침을 사용하여 기본 속성을 수정할 수 있습니다.


## <a name="edit-assessment-properties"></a>평가 속성 편집

1. 마이그레이션 프로젝트의 **평가** 페이지에서 평가를 선택하고 **속성 편집**을 클릭합니다.
2. 다음 표에 따라 속성을 수정합니다.

    **설정** | **세부 정보** | **기본값**
    --- | --- | ---
    **대상 위치** | 마이그레이션할 Azure 위치입니다.<br/><br/> Azure Migrate는 현재 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 인도 중부, 미국 중부, 중국 동부, 중국 북부, 동아시아, 미국 동부, 독일 중부, 독일 북동부, 미국 동부 2, 일본 동부, 일본 서부, 한국 중부, 한국 남부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 인도 남부, 영국 남부, 영국 서부, US Gov 애리조나, US Gov 텍사스, US Gov 버지니아, 미국 서중부, 유럽 서부, 인도 서부, 미국 서부 및 미국 서부 2를 비롯한 30개 지역을 지원합니다. |  미국 서부 2가 기본 위치입니다.
    **저장소 이중화** | 마이그레이션 후 Azure VM이 사용하게 될 저장소 중복 유형입니다. | 기본값은 [LRS(로컬 중복 저장소)](../storage/common/storage-redundancy-lrs.md)입니다. Azure Migrate는 관리 디스크 기반 평가만 지원하고 관리 디스크는 LRS만 지원하므로 현재는 속성에 LRS 옵션만 있습니다.
    **크기 조정 기준** | Azure Migrate가 Azure에 사용할 VM의 적정 크기를 산정하는 데 사용되는 기준입니다. *성능 기반* 크기 조정을 수행하거나, 성능 기록을 고려하지 않고 *온-프레미스로* VM 크기를 조정할 수 있습니다. | 기본 옵션은 성능 기반 크기 조정입니다.
    **성능 기록** | VM의 성능을 평가하는 데 고려할 기간입니다. 이 속성은 크기 조정 기준이 *성능 기반 크기 조정*인 경우에만 적용됩니다. | 기본값은 1일입니다.
    **백분위 수 사용률** | 적정 크기를 산정하는 데 고려되는 성능 샘플 집합의 백분위수 값입니다. 이 속성은 크기 조정 기준이 *성능 기반 크기 조정*인 경우에만 적용됩니다.  | 기본값은 95번째 백분위수입니다.
    **VM 시리즈** | 크기 조정 시 고려할 VM 시리즈를 지정할 수 있습니다. 예를 들어 Azure에서 A 시리즈 VM으로 마이그레이션하지 않으려는 프로덕션 환경이 있는 경우 목록 또는 시리즈에서 A 시리즈를 제외하고 선택한 시리즈에서만 크기 조정을 수행할 수 있습니다. | 기본적으로 모든 VM 시리즈가 선택됩니다.
    **가격 책정 계층** | 대상 Azure VM의 [가격 책정 계층(기본/표준)](../virtual-machines/windows/sizes-general.md)을 지정할 수 있습니다. 예를 들어 프로덕션 환경을 마이그레이션할 계획이라면 VM 대기 시간이 짧은 대신 더 많은 비용이 발생할 수 있는 표준 계층을 고려하는 것이 좋습니다. 반면, 개발-테스트 환경을 사용하는 경우 VM 대기 시간이 긴 대신 비용이 저렴한 기본 계층을 고려하는 것이 좋습니다. | 기본적으로 [표준](../virtual-machines/windows/sizes-general.md) 계층이 사용됩니다.
    **쾌적 인자** | Azure Migrate는 평가 중에 버퍼(쾌적 인자)를 고려합니다. 이 버퍼는 VM의 컴퓨터 사용률 데이터(CPU, 메모리, 디스크 및 네트워크)를 기반으로 적용됩니다. 쾌적 인자는 계절별 사용량, 성능 기록 부족, 향후 사용량 증가 가능성 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 VM은 일반적으로 2코어 VM이라는 결과가 나옵니다. 그러나 쾌적 인자가 2.0x이면 결과는 4코어 VM이 됩니다. | 기본 설정은 1.3x입니다.
    **제안** | 등록된 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. | [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)가 기본값입니다.
    **통화** | 청구 통화입니다. | 기본값은 미국 달러입니다.
    **할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. | 기본 설정은 0%입니다.
    **Azure 하이브리드 혜택** | 소프트웨어 보증이 있고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-use-benefit/)을 받을 수 있는지 여부를 지정합니다. [예]로 설정하면 Windows 외 Azure 가격이 Windows VM에 적용됩니다. | 기본값은 [예]입니다.

3. **저장**을 클릭하여 평가를 업데이트합니다.


## <a name="next-steps"></a>다음 단계

평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
