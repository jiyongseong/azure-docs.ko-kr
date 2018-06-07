---
title: "Azure 스택 통합 시스템에서 배율 단위 노드 바꾸기 | Microsoft Docs"
description: "Azure 스택 통합 시스템에 실제 배율 단위 노드를 바꾸는 방법에 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 468af385833395963ef8acad905b99a9b7e6b8fa
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2018
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Azure 스택 통합 시스템에서 배율 단위 노드를 대체 합니다.

*적용 대상: Azure 스택 시스템 통합*

이 문서에서는 물리적 컴퓨터를 대체 하는 일반적인 프로세스를 설명 (라고도 하는 *배율 단위 노드*) Azure 스택에 시스템을 통합 합니다. 실제 배율 단위 노드 대체 단계는 달라질 수 (oem) 하드웨어 공급 업체에 따라. 시스템에만 적용 되는 자세한 단계에 대 한 공급 업체의 필드 (FRU) 교체 장치 설명서를 참조 하십시오.

다음 흐름 다이어그램에서는 전체 배율 단위 노드를 바꾸는 일반적인 FRU 프로세스를 보여 줍니다.

![바꾸기 노드 프로세스에 대 한 순서도](media/azure-stack-replace-node/replacenodeflow.png)

*이 작업 하지 않아도 될 하드웨어의 실제 조건에 기반 합니다.

## <a name="review-alert-information"></a>경고 정보를 검토 합니다.

배율 단위 노드가 다운 되는 경우 다음과 같은 중요 한 알림 받게 됩니다.

- 네트워크 컨트롤러에 연결 되어 있지 노드
- 가상 컴퓨터 배치에 액세스할 수 없는 노드
- 배율 단위 노드가 오프 라인 상태입니다.

![목록 아래로 배율 단위에 대 한 경고](media/azure-stack-replace-node/nodedownalerts.png)

여는 경우는 **배율 단위 노드가 오프 라인 상태인** 경고, 경고 설명 액세스할 수 있는 배율 단위 노드를 포함 합니다. 또한 하드웨어 수명 주기 호스트에서 실행 되는 OEM 전용 모니터링 솔루션에 추가 경고를 발생할 수 있습니다.

![노드가 오프 라인 경고의 세부 정보](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>배율 단위 노드 교체 프로세스

다음 단계는 배율 단위 노드 교체 프로세스에 개략적으로 제공 됩니다. 시스템에만 적용 되는 자세한 단계에 대 한 OEM 하드웨어 공급 업체의 FRU 설명서를 참조 하십시오. OEM 제공 설명서를 참조 하지 않고 다음이 단계를 수행 하지 마십시오.

1. 사용 하 여는 [방전](azure-stack-node-actions.md#scale-unit-node-actions) 배율 단위 노드 유지 관리 모드로 설정 하는 작업입니다. 이 작업 하지 않아도 될 하드웨어의 실제 조건에 기반 합니다.

   > [!NOTE]
   > 어떤 경우 든, 하나의 노드만 종료 되 고 수는 S2D 위반 하지 않고 동시에 전원을 끌 (저장소 공간 다이렉트)입니다.

2. 여전히 노드가 켜져를 사용 하 여는 [전원을 끄고](azure-stack-node-actions.md#scale-unit-node-actions) 동작 합니다. 이 작업 하지 않아도 될 하드웨어의 실제 조건에 기반 합니다.
 
   > [!NOTE]
   > 전원 끄기 작동 하지 않는 경우 경우 베이스 보드 관리 컨트롤러 (BMC) 웹 인터페이스를 대신 사용 합니다.

1. 물리적 컴퓨터를 대체 합니다. 일반적으로이 OEM 하드웨어 공급 업체에 문의 하 여 수행 됩니다.
2. 사용 하 여는 [복구](azure-stack-node-actions.md#scale-unit-node-actions) 배율 단위에 새 물리적 컴퓨터를 추가할 작업입니다.
3. 권한 있는 끝점을 사용 하 여 [가상 디스크 복구의 상태를 확인](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair)합니다. 새 데이터 드라이브에 전체 저장소 복구 작업을 시스템 로드에 따라 여러 시간이 걸릴 수 있습니다 및 공간을 사용 합니다.
4. 복구 작업이 완료 된 후 모든 활성 경고가 자동으로 종료 되었는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- 핫 스왑 가능한 실제 디스크를 교체 하는 방법에 대 한 정보를 참조 하십시오. [디스크 교체](azure-stack-replace-disk.md)합니다. 
- 비 핫 스왑 가능한 하드웨어 구성 요소를 교체 하는 방법에 대 한 정보를 참조 하십시오. [하드웨어 구성 요소 교체](azure-stack-replace-component.md)합니다.
