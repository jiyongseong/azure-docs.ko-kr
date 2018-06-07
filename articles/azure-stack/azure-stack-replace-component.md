---
title: "Azure 스택 배율 단위 노드에 하드웨어 구성 요소를 바꾸기 | Microsoft Docs"
description: "Azure 스택 통합 시스템 하드웨어 구성 요소를 대체 하는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/29/2018
ms.author: mabrigg
ms.openlocfilehash: 7018f0122ab1ef11d64cce8a9adf58419d0e9ba7
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Azure 스택 배율 단위 노드에 하드웨어 구성 요소를 대체 합니다.

*적용 대상: Azure 스택 시스템 통합*

이 문서에서는 비 핫 스왑 가능한 되는 하드웨어 구성 요소를 대체 하는 일반적인 프로세스를 설명 합니다. 실제 바꾸는 단계가 달라 집니다 (oem) 하드웨어 공급 업체에 따라. Azure 스택 통합 시스템에만 적용 되는 자세한 단계에 대 한 공급 업체의 필드 (FRU) 교체 장치 설명서를 참조 하십시오.

비 핫 스왑 가능한 구성 요소는 다음과 같습니다.

- CPU*
- 메모리 *
- 마더보드/베이스 보드 관리 컨트롤러 (BMC) / 비디오 카드
- 디스크 컨트롤러/호스트 버스 어댑터 (HBA) / 백플레인
- 네트워크 어댑터 (NIC)
- 운영 체제 디스크 *
- 데이터 드라이브 (핫 스왑, 예를 들어 pci-e 추가 기능 카드를 지원 하지 않는 드라이브) *

* 이러한 구성 요소 핫 스왑을 지원할 수 있지만 공급 업체 구현에 따라 달라질 수 있습니다. 자세한 단계에 대 한 OEM 공급 업체의 FRU 설명서를 참조 하십시오.

다음 흐름 다이어그램에서는 비 핫 스왑 가능한 하드웨어 구성 요소를 대체 하는 일반적인 FRU 프로세스를 보여 줍니다.

![구성 요소 교체 흐름을 보여 주는 흐름 다이어그램](media/azure-stack-replace-component/replacecomponentflow.PNG)

*이 작업 하지 않아도 될 하드웨어의 실제 조건에 기반 합니다.

* * OEM 하드웨어 공급 업체의 구성 요소 교체 및 펌웨어 다를 수 업데이트를 수행 하는 여부를 기반으로 지원 계약 합니다.

## <a name="review-alert-information"></a>경고 정보를 검토 합니다.

Azure 스택 상태 및 모니터링 시스템 저장소 공간 다이렉트에 의해 제어 되는 데이터 드라이브 및 네트워크 어댑터의 상태를 추적 합니다. 다른 하드웨어 구성 요소를 추적 하지 않습니다. 다른 하드웨어 구성에 대 한 경고는 모니터링 하드웨어 수명 주기 호스트에서 실행 되는 솔루션 공급 업체 관련 하드웨어에서 발생 합니다.  

## <a name="component-replacement-process"></a>구성 요소 교체 프로세스

다음 구성 요소 교체 프로세스에 대 한 고급 개요를 제공합니다. OEM 제공 FRU 설명서를 참조 하지 않고 다음이 단계를 수행 하지 마십시오.

1. 사용 하 여는 [방전](azure-stack-node-actions.md#scale-unit-node-actions) 배율 단위 노드 유지 관리 모드로 설정 하는 작업입니다. 이 작업 하지 않아도 될 하드웨어의 실제 조건에 기반 합니다.

   > [!NOTE]
   > 어떤 경우 든, 하나의 노드만 종료 되 고 수는 S2D 위반 하지 않고 동시에 전원을 끌 (저장소 공간 다이렉트)입니다.

2. 배율 단위 노드 유지 관리 모드에 있으면 후 사용 하 여는 [전원을 끄고](azure-stack-node-actions.md#scale-unit-node-actions) 동작 합니다. 이 작업 하지 않아도 될 하드웨어의 실제 조건에 기반 합니다.

   > [!NOTE]
   > 전원 끄기 작동 하지 않는 경우 경우 베이스 보드 관리 컨트롤러 (BMC) 웹 인터페이스를 대신 사용 합니다.

3. 손상 된 하드웨어 구성 요소를 대체 합니다. OEM 하드웨어 공급 업체의 구성 요소 교체를 수행 하는지 여부를 지원 계약에 따라 달라질 수 없습니다.  
4. 펌웨어를 업데이트 합니다. 하드웨어 수명 주기 호스트를 사용 하 여 대체 하드웨어 구성 요소에 수준이 적용 된 승인 된 펌웨어를 확인 하 여 공급 업체별 펌웨어 업데이트 프로세스를 따릅니다. OEM 하드웨어 공급 업체가이 단계를 수행 하는지 여부를 하는 것은 지원 계약에 따라 달라질 수 없습니다.  
5. 사용 하 여는 [복구](azure-stack-node-actions.md#scale-unit-node-actions) 동작에는 배율 단위 배율 단위 노드를 다시 가져오기입니다.
6. 권한 있는 끝점을 사용 하 여 [가상 디스크 복구의 상태를 확인](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)합니다. 새 데이터 드라이브에 전체 저장소 복구 작업을 시스템 로드에 따라 여러 시간이 걸릴 수 있습니다 및 공간을 사용 합니다.
7. 복구 작업이 완료 된 후 모든 활성 경고가 자동으로 종료 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- 핫 스왑 가능한 실제 디스크를 교체 하는 방법에 대 한 정보를 참조 하십시오. [디스크 교체](azure-stack-replace-disk.md)합니다.
- 실제 노드를 교체 하는 방법에 대 한 정보를 참조 하십시오. [배율 단위 노드 대체](azure-stack-replace-node.md)합니다.
