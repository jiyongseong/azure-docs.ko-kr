---
title: Azure 스택에 대 한 실제 메모리 용량 관리 | Microsoft Docs
description: 모니터링 하 고 Azure 스택에 대 한 사용 가능한 저장 공간을 관리 합니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: Thomas.Roettinger
ms.openlocfilehash: dc572353c2e27ddfbae2398f1aece56586955e26
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
---
<!---Loc Comment: Please, check the comment in coversation section---> 
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Azure 스택에 대 한 실제 메모리 용량 관리

*적용 대상: Azure 스택 시스템 통합*

Azure 스택에 대 한 사용 가능한 총 메모리 용량을 늘리려면 추가 메모리를 추가할 수 있습니다. Azure 스택에서 실제 서버도 라고는 *배율 단위 노드*합니다. 단일 배율 단위의 구성원 인 모든 배율 단위 노드는 동일한 메모리 양이 있어야 합니다.

> [!note]  
> 계속 하기 전에 있는지 하드웨어 제조업체의 설명서를 참조 하십시오.는 제조업체에 문의 실제 메모리 업그레이드를 지원 합니다. OEM 하드웨어 공급 업체 지원 계약 공급 업체에서 물리적 서버 랙에 배치와 장치 펌웨어 업데이트를 수행 한다고 필요할 수 있습니다.

다음 흐름 다이어그램에서는 각 배율 단위 노드에 메모리를 추가 하는 일반적인 프로세스를 보여 줍니다.

![배율 단위 노드마다에 메모리를 추가 합니다.](media\azure-stack-manage-storage-physical-capacity\process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>메모리 기존 노드를 추가
다음 단계에는 추가 메모리 프로세스의 상위 수준 개요를 제공합니다. 

> [!Warning]  
OEM 제공 설명서를 참조 하지 않고 다음이 단계를 수행 하지 마십시오.

> [!Warning]  
롤링 메모리 업그레이드를 지원 하지 않으므로 전체 배율 단위를 닫아야 합니다.

1. 설명 하는 단계를 사용 하 여 Azure 스택 중지는 [Start 및 stop Azure 스택](azure-stack-start-and-stop.md) 문서.
2. 각 물리적 컴퓨터 하드웨어 제조업체의 설명서를 사용 하 여 메모리를 업그레이드 합니다.
3. 시작의 단계를 사용 하 여 Azure 스택에 [Start 및 stop Azure 스택](azure-stack-start-and-stop.md) 문서.

## <a name="next-steps"></a>다음 단계

 - 찾아를 복구 하 고 비즈니스 요구에 따라 저장소 용량을 회수 스택 Azure에서에서 저장소 계정을 관리 하는 방법을 알아보려면 참조 [스택 Azure에서에서 저장소 계정을 관리](azure-stack-manage-storage-accounts.md)합니다.
 - Azure 스택 클라우드 운영자는 모니터링 하 고 Azure 스택 배포의 저장소 용량을 관리, 참조 [Azure 스택에 대 한 저장소 용량 관리](azure-stack-manage-storage-shares.md)합니다. 
