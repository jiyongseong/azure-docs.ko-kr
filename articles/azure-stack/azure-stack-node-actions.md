---
title: Azure 스택으로 단위 노드 작업 크기를 조정 | Microsoft Docs
description: 노드 상태 보고는 전원 켜기, 전원 끄기, 드레이닝을 사용 하 고 통합 하는 Azure 스택 시스템 노드 작업을 다시 시작 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.openlocfilehash: 202854157dee28f3ab3dc73c6f22508a8bf510b3
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Azure 스택에서 배율 단위 노드 작업

*적용 대상: Azure 스택 시스템 통합*

이 문서는 배율 단위와 해당 관련된 노드에의 상태를 확인 하는 방법 및 사용 가능한 노드 동작을 사용 하는 방법을 설명 합니다. 전원이 켜져 있고, 전원 끄기 포함 하 방전, 다시 시작 및 복구 하는 노드 동작 합니다. 일반적으로 부분으로 또는 노드 복구 시나리오에 대 한 필드 교체 하는 동안 이러한 노드 동작을 사용 합니다.

> [!Important]  
> 이 문서에 설명 된 모든 노드 작업은 한 번에 대상 하나의 노드만이 있어야 합니다.


## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>배율 단위와 해당 노드에 대 한 상태 확인

관리자 포털에서 배율 단위와 해당 관련된 노드에의 상태를 쉽게 볼 수 있습니다.

배율 단위의 상태를 보려면:

1. 에 **지역 관리** 타일에서 지역을 선택 합니다.
2. 왼쪽 아래에서 **인프라 리소스**선택, **확장 단위**합니다.
3. 결과에서 배율 단위를 선택 합니다.
 
여기에서 다음 정보를 볼 수 있습니다.

- 영역 이름
- 시스템의 종류
- 총 논리 코어
- 총 메모리
- 개별 노드 및 해당 상태;의 목록 하나는 실행 또는 중지 합니다.

![각 노드에 대 한 실행 상태를 표시 하는 배율 단위 타일](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>배율 단위 노드에 대 한 정보 보기

개별 노드를 선택 하는 경우에 다음 정보를 볼 수 있습니다.

- 영역 이름
- 서버 모델
- 베이스 보드 관리 컨트롤러 (BMC)의 IP 주소
- 작동 상태
- 코어의 총 수
- 총 메모리 양
 
![각 노드에 대 한 실행 상태를 표시 하는 배율 단위 타일](media/azure-stack-node-actions/NodeActions.PNG)

여기에서 배율 단위 노드 작업을 수행할 수도 있습니다.

## <a name="scale-unit-node-actions"></a>배율 단위 노드 작업

배율 단위 노드에 대 한 정보를 볼 때와 같은 노드 작업을 수행할 수도 있습니다.

- 전원 켜기 및 전원 끄기
- 드레이닝 및 다시 시작
- 복구

노드의 작동 상태는 사용할 수 있는 옵션을 결정 합니다.

### <a name="power-off"></a>전원 끄기

**전원을 끄고** 동작으로 기록 디버깅이 노드. 패턴은 전원 단추를 눌러 마치는 같습니다. 그렇게 **하지** 운영 체제에 종료 신호를 보냅니다. 계획 된 작업 끄지, 먼저 배율 단위 노드를 드레이닝 있는지 확인 합니다.

이 작업은 일반적으로 노드 응답 하지 않는 상태 이며 더 이상 요청에 응답 하는 경우에 사용 됩니다.

> [!Important] 
> 이 기능은 PowerShell을 통해 사용할 수만 있습니다. 나중에 다시로 Azure 스택 관리자 포털에서 사용할 수 있습니다.


전원 끄기 PowerShell 통해를 실행 합니다.

````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
```` 

전원 끄기 작동 하지 않는 경우으 나 가능성이, BMC 웹 인터페이스를 대신 사용 합니다.

### <a name="power-on"></a>전원 켜기

**전원을 켜 세요** 동작으로 기록 디버깅이 노드. 패턴은 전원 단추를 눌러 마치는 같습니다. 

> [!Important] 
> 이 기능은 PowerShell을 통해 사용할 수만 있습니다. 나중에 다시로 Azure 스택 관리자 포털에서 사용할 수 있습니다.

실행 하려면 전원 PowerShell 통해 작업에서:

````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
````

전원 동작에 작동 하지 않는 경우으 나 가능성이, BMC 웹 인터페이스를 대신 사용 합니다.

### <a name="drain"></a>드레이닝

**방전** 작업이 해당 특정 배율 단위로 나머지 노드로 배포 하 여 모든 활성 작업 evacuates 합니다.

이 작업은 일반적으로 필드 전체 노드 교체과 같은 부분을 교체 하는 동안 사용 됩니다.

> [!IMPORTANT]
> 사용자가 알렸습니다는 계획 된 유지 관리 기간 동안에 노드를 드레이닝 있는지 확인 합니다. 어떤 조건 활성 작업 중단을 경험할 수 있습니다.

실행 하려면 PowerShell 통해를 드레이닝 할 작업:

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>다시 시작

**Resume** 작업 배출 된 노드를 다시 시작 하 고 워크 로드 배치에 대 한 활성 표시 합니다. 노드에서 실행 중이 던 이전 작업 다시 실패 하지 않습니다. (한 노드를 선택한 다음 전원 끄기, 노드 다시 전원을 경우를 드레이닝 경우 표시 되지 않았습니다 워크 로드 배치에 대 한 활성으로 합니다. 준비가 되 면 사용 해야 다시 시작 작업을 활성으로 노드를 표시 합니다.)

실행 하려면 PowerShell 통해 다시 시작 작업:

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>복구

**복구** 노드를 복구 하는 작업입니다. 다음 시나리오 중 하나에 사용 합니다.

- 전체 노드 교체 (사용 또는 새 데이터 디스크 제외)
- 하드웨어 구성 요소 오류 후 (필드 교체 장치 (FRU) 설명서에서 advise) 하는 경우 대체 합니다.

> [!IMPORTANT]
> 노드 또는 개별 하드웨어 구성 요소를 교체 해야 하는 경우 정확한 단계는 OEM 하드웨어 공급 업체의 FRU 설명서를 참조 하십시오. FRU 설명서 하드웨어 구성 요소를 바꾼 후 복구 작업을 실행 해야 하는지 여부를 지정 합니다.  

복구 작업을 실행 하는 경우 BMC IP 주소를 지정 해야 합니다. 

실행 하려면 PowerShell 통해 복구 작업:

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


