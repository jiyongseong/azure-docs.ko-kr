---
title: "Azure Stack의 상태 및 경고 모니터링 | Microsoft Docs"
description: "Azure Stack에서 상태 및 경고를 모니터링하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: 446df7922422ccfcf3fbb92ecf153c6dec2f6197
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Azure Stack의 상태 및 경고 모니터링

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택 인프라 모니터링 상태 및 Azure 스택 영역에 대 한 경고를 볼 수 있도록 하는 기능이 포함 되어 있습니다. **지역 관리** 기본 공급자 구독에 대 한 관리자 포털에서 기본적으로 고정 된 타일 Azure 스택의 배포 된 영역을 모두 나열 합니다. 타일 각 지역에 대 한 활성 위험 및 경고 경고의 수를 표시 하 고 상태 및 Azure 스택의 경고 기능에 프로그램 진입점입니다.

 ![지역 관리 타일](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Azure Stack의 상태 이해

 상태 및 경고 상태 리소스 공급자에 의해 관리 됩니다. Azure Stack 구성 요소는 Azure Stack이 배포 및 구성되는 동안 상태 리소스 공급자에 등록합니다. 이 등록 상태 및 각 구성 요소에 대 한 알림을 표시할 수 있습니다. Azure Stack의 상태는 간단한 개념입니다. 등록된 구성 요소 인스턴스가 있으면 해당 구성 요소의 상태에 최악의 활성 경고 심각도인 경고 또는 위험이 반영됩니다.
 
 ## <a name="view-and-manage-component-health-state"></a>보기 및 구성 요소 상태 관리
 
 Azure 스택 연산자로 관리자 포털 및 REST API 및 PowerShell을 통해 구성 요소의 상태를 볼 수 있습니다.
 
포털에서 상태를 보려면의 원하는 영역을 클릭는 **지역 관리** 바둑판식으로 배열입니다. 인프라 역할 및 리소스 공급자의 상태를 볼 수 있습니다.

![인프라 역할 목록](media/azure-stack-monitor-health/image2.png)

리소스 공급자 또는 인프라 역할 더 자세한 정보를 보려면 클릭 수 있습니다.

> [!WARNING]
>인프라 역할을 클릭 하 고 역할 인스턴스를 클릭 한 다음 많은 경우 다시 시작을 시작 하려면 옵션 또는 종료 합니다. 통합된 된 시스템에 업데이트를 적용 하는 경우에 이러한 작업을 사용 하지 마십시오. 또한 수행 **하지** Azure 스택 개발 키트 환경에서 이러한 옵션을 사용 합니다. 통합된 시스템 환경에 대해서만 이러한 옵션은 디자인 되었습니다. 인프라 역할당 둘 이상의 역할 인스턴스는 합니다. 개발 키트에서 역할 인스턴스 (특히 AzS Xrp01)를 다시 시작 하면 시스템이 불안정 합니다. 지원 문제 해결을 위해 게시에 귀하의 문제는 [Azure 스택 포럼](https://aka.ms/azurestackforum)합니다.
>
 
## <a name="view-alerts"></a>경고 보기

각 Azure 스택 영역에 대 한 활성 경고의 목록은에서 직접 사용할 수는 **지역 관리** 블레이드입니다. 기본 구성에서 첫 번째 타일은는 **경고** 타일을 중요 한 대 한 요약 및 경고 알림 영역에 표시 합니다. 이 블레이드에서 빠른 액세스를 위해 대시보드를 다른 타일 처럼 경고 타일을 고정할 수 있습니다.   

![경고를 표시하는 경고 타일](media/azure-stack-monitor-health/image3.png)

상단 부분의 선택 하 여는 **경고** 지역에 대 한 모든 활성 경고의 목록으로 이동 타일입니다. 타일 내에서 **위험** 또는 **경고** 줄 항목을 선택하면 필터링된 경고 목록(위험 또는 경고)으로 이동됩니다. 

![필터링된 경고 알림](media/azure-stack-monitor-health/image4.png)
  
**경고** 블레이드 상태 (활성 또는 닫힘) 및 심각도 (위험 또는 경고) 모두에서 필터링 하는 기능을 지원 합니다. 기본 보기에는 모든 활성 경고가 표시 됩니다. 모든 종결된 경고는 7일 후 시스템에서 제거됩니다.

![위험 또는 경고 상태로 필터링하는 필터 창](media/azure-stack-monitor-health/image5.png)

**보기 API** 작업 목록 보기를 생성 하는 데 사용 된 REST API를 표시 합니다. 이 작업에는 쿼리 알림을 사용할 수 있는 REST API 구문을 사용 하 여 살펴볼 빠른 방법을 제공 합니다. 기존 데이터 센터 모니터링, 보고 및 솔루션 티켓와 자동화 또는 통합에 대 한이 API를 사용할 수 있습니다. 

![REST API를 보여 주는 보기 API 옵션](media/azure-stack-monitor-health/image6.png)

경고 세부 정보를 보려면 특정 경고를 클릭할 수 있습니다. 경고 세부 정보는 경고와 관련 되어 영향을 받는 구성 요소와 경고의 소스를 빠르게 탐색할 수를 사용 하도록 설정 하는 모든 필드를 표시 합니다. 예를 들어 다음과 같은 경고가 인프라 역할 인스턴스 중 하나가 오프 라인으로 전환 되었거나 액세스할 수 없는 경우에 발생 합니다.  

![경고 세부 정보 블레이드에서](media/azure-stack-monitor-health/image7.png)

인프라의 역할 인스턴스를 다시 온라인 상태가 되 면이 경고가 자동으로 닫힙니다. 아니지만 일부 경고는 자동으로 기본 문제가 해결 될 때 닫힙니다. 선택 하는 것이 좋습니다 **경고 닫기** 수정 단계를 수행 합니다. 문제가 지속 되 면 Azure 스택 새 경고를 생성 합니다. 이 문제를 해결 하는 경우 경고 닫힌 상태를 유지 하 고 추가 작업이 필요 합니다.

## <a name="next-steps"></a>다음 단계

[Azure 스택에서 업데이트를 관리](azure-stack-updates.md)

[Azure 스택의 영역 관리](azure-stack-region-management.md)
