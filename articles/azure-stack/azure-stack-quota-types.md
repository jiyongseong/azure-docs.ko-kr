---
title: Azure 스택에서 할당량 유형 | Microsoft Docs
description: Azure 스택에서 서비스 및 리소스에 사용할 수 있는 서로 다른 할당량 형식을 검토 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: xiaofmao
ms.openlocfilehash: 52d469236d607a145430134c24c4237346dafb4c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="quota-types-in-azure-stack"></a>Azure 스택에서 할당량 유형

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

[할당량](azure-stack-plan-offer-quota-overview.md#plans) 사용자 구독을 프로 비전 하거나 사용할 수 있는 리소스의 한계를 정의 합니다. 예를 들어 할당량에는 최대 5 개의 Vm을 만들려면 사용자에 통합할 수 있습니다. 각 리소스는 자체 유형의 할당량을 가질 수 있습니다.

## <a name="compute-quota-types"></a>할당량 유형 계산
| **형식** | **기본값** | **설명** |
| --- | --- | --- |
| 가상 컴퓨터의 최대 수 | 20 | 이 위치에 구독을 만들 수 있는 가상 컴퓨터의 최대 수입니다. |
| 가상 컴퓨터 코어의 최대 수 | 50 | 이 위치에 구독을 만들 수 있는 코어의 최대 수 (예를 들어 A3 VM에 4 개의 코어). |
| 가용성 집합의 수를 최대 | 10 | 이 위치에 만들 수 있는 가용성 집합의 최대 수입니다. |
| 가상 컴퓨터 크기의 최대 수 설정 | 20 | 이 위치에 만들 수 있는 가상 컴퓨터 크기 집합의 최대 수입니다. |



## <a name="storage-quota-types"></a>저장소 할당량 유형
| **항목** | **기본값** | **설명** |
| --- | --- | --- |
| 최대 용량 (GB) |500 |이 위치에서 구독을 사용할 수 있는 총 저장소 용량입니다. |
| 저장소 계정의 총 수 |20 |이 위치에 구독을 만들 수 있는 저장소 계정 최대 수입니다. |

> [!NOTE]  
> 저장소 할당량 적용 되기 전에 최대 2 시간이 걸릴 수 있습니다. 
> 


## <a name="network-quota-types"></a>네트워크 할당량 유형
| **항목** | **기본값** | **설명** |
| --- | --- | --- |
| 최대 공개 Ip |50 |이 위치에서 구독을 만들 수 있는 공용 Ip의 최대 수입니다. |
| 최대 가상 네트워크 |50 |이 위치에서 구독을 만들 수 있는 가상 네트워크의 최대 수입니다. |
| 최대 가상 네트워크 게이트웨이 |1 |이 위치에서 구독을 만들 수 있는 가상 네트워크 게이트웨이 (VPN 게이트웨이)의 최대 수입니다. |
| 최대 네트워크 연결 |2 |이 위치에 있는 모든 가상 네트워크 게이트웨이 간의 구독을 만들 수 있는 네트워크 연결 (지점 간 또는 사이트 간)의 최대 수입니다. |
| 최대 부하 분산 장치 |50 |이 위치에 구독을 만들 수 있는 부하 분산 장치의 최대 수입니다. |
| 최대 NIC 수 |100 |이 위치에서 구독을 만들 수 있는 네트워크 인터페이스의 최대 수입니다. |
| 최대 네트워크 보안 그룹 |50 |이 위치에서 구독을 만들 수 있는 네트워크 보안 그룹의 최대 수입니다. |

## <a name="view-an-existing-quota"></a>기존 할당량을 보려면
1. 클릭 **더 많은 서비스** > **리소스 공급자**합니다.
2. 보려는 할당량을 사용 하 여 서비스를 선택 합니다.
3. 클릭 **할당량**, 보려는 할당량을 선택 합니다.

## <a name="next-steps"></a>다음 단계
[및 자세한 정보 계획, 제안, 할당량에 알아봅니다.](azure-stack-plan-offer-quota-overview.md)

[계획을 만드는 동안 할당량을 만듭니다.](azure-stack-create-plan.md)
