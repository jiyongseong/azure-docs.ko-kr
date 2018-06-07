---
title: "Azure 스택에서 공용 IP 주소 사용량을 볼 | Microsoft Docs"
description: "관리자는 지역에서 소비 하는 공용 IP 주소를 볼 수 있습니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 50bf01d6de6105d3041c6bb88e803f3d110f751d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Azure 스택에서 공용 IP 주소 사용량 보기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

클라우드 관리자는 다음을 볼 수 있습니다.
 - 테 넌 트에 할당 된 공용 IP 주소의 수입니다.
 - 할당에 계속 사용할 수 있는 공용 IP 주소의 수입니다.
 - 해당 위치에 할당 된 공용 IP 주소의 비율입니다.

**공용 IP 풀 사용** 타일은 공용 IP 주소 풀에서 사용 된 공용 IP 주소의 수를 표시 합니다. 각 IP 주소에 대 한 타일에는 인스턴스, 패브릭 인프라 서비스 및 테 넌 트에서 명시적으로 생성 된 공용 IP 주소 리소스가 테 넌 트 IaaS VM에 대 한 사용을 표시 합니다.

타일의 목적은 Azure 스택 연산자가이 위치에 사용 되는 공용 IP 주소의 수의 의미를 부여 하는 것입니다. 수에는 이러한 실행 중인지 확인 낮은이 리소스에 대 한 관리자 수 있습니다.

**공용 IP 주소** 아래에서 메뉴 항목 **테 넌 트 리소스** 만 해당 공용 IP 주소 나열 된 *명시적으로 만든 테 넌 트에서*합니다. 메뉴 항목을 찾을 수 있습니다는 **리소스 공급자**, **네트워크** 창. 수가 **사용** 에 공용 IP 주소는 **공용 IP 풀 사용** 타일은 항상 (보다 큰)와 다른에 숫자는 **공용 IP 주소** 아래의바둑판식으로배열 **리소스를 테 넌 트**합니다.

## <a name="view-the-public-ip-address-usage-information"></a>공용 IP 주소 사용 정보 보기
지역에서 사용 되는 공용 IP 주소의 총 수를 보려면:

1. Azure 스택 관리자 포털에서 선택 **더 많은 서비스**아래 **관리 리소스**선택, **리소스 공급자**합니다.
2. 목록에서 **리소스 공급자**선택, **네트워크**합니다.
3. **네트워크** 창에 표시 됩니다는 **공용 IP 풀 사용량** 타일에 **개요** 섹션.

![네트워크 리소스 공급자 창](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**사용** 번호 수를 나타내는 공용 IP 주소 풀에서 공용 IP 주소를 할당 합니다. **무료** 번호가 나타내는 공용 IP 주소 수에서 공용 IP 주소 풀 할당 되지 않은 지는 계속 사용할 수 있습니다. **%Used** 사용 하거나 그 위치에 공용 IP 주소 풀에 공용 IP 주소의 총 수의 백분율 주소를 할당 된 숫자의 수를 나타냅니다.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>테 넌 트 구독에서 생성 된 공용 IP 주소를 확인 합니다.
선택 **공용 IP 주소** 아래 **테 넌 트 리소스**합니다. 특정 지역에서 테 넌 트 구독에 의해 명시적으로 생성 하는 공용 IP 주소 목록을 검토 합니다.

![테 넌 트 공용 IP 주소](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

동적으로 할당 된 일부 공용 IP 주소 목록에 표시 되는지 확인할 수 있습니다. 그러나 주소 아직과 연결 되지 않았습니다. 네트워크 리소스 공급자에는 있지만 아직 네트워크 컨트롤러에는 주소 리소스를 만들었습니다.

인터페이스, 네트워크 인터페이스 카드 (NIC), 부하 분산 장치 또는 가상 네트워크 게이트웨이를 바인딩할 때까지 네트워크 컨트롤러는 리소스에 주소를 할당 하지 않습니다. 공용 IP 주소는 인터페이스를 바인딩할 네트워크 컨트롤러는 IP 주소를 할당 합니다. 에 표시 된 주소는 **주소** 필드입니다.

## <a name="view-the-public-ip-address-information-summary-table"></a>공용 IP 주소 정보 요약 테이블을 보려면
다른 경우에는 주소 하나의 목록에 표시 되는지 여부를 결정 하는 공용 IP 주소 할당 됩니다.

| **공용 IP 주소 할당 사례** | **사용량 요약에 표시** | **테 넌 트 공용 IP 주소 목록에 표시** |
| --- | --- | --- |
| 아직 할당 되지 않은 NIC 또는 부하 분산 (임시)에 동적 공용 IP 주소 |아니요 |예 |
| 동적 공용 IP 주소는 NIC 또는 부하 분산 장치에 할당 합니다. |예 |예 |
| 고정 공용 IP 주소는 테 넌 트 NIC 또는 부하 분산 장치에 할당 합니다. |예 |예 |
| 고정 공용 IP 주소 인프라 서비스 패브릭 끝점에 할당 합니다. |예 |아니요 |
| 공용 IP 주소 IaaS VM 인스턴스에 대 한 암시적으로 만들고 가상 네트워크에서 아웃 바운드 NAT를 사용 합니다. Vm 인터넷 정보를 보낼 수 있도록 테 넌 트 VM 인스턴스를 만들 때마다 내부적 만들어집니다. |예 |아니요 |

## <a name="next-steps"></a>다음 단계
[Azure 스택에서 저장소 계정을 관리합니다](azure-stack-manage-storage-accounts.md)