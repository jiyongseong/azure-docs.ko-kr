---
title: "Azure Stack의 지역 관리 | Microsoft Docs"
description: "Azure Stack의 지역 관리를 간략하게 설명합니다."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 0a19490ce276eec9a46bc0ea4343e3449a9dc93c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="region-management-in-azure-stack"></a>Azure Stack의 지역 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure 스택에 개념이 영역을 Azure 스택 인프라를 구성 하는 하드웨어 리소스를 구성 하는 논리적 엔터티입니다. 영역 관리 내 Azure 스택 인프라가 수명 주기를 성공적으로 운영 하는 데 필요한 모든 리소스를 찾을 수 있습니다.

하나의 통합 된 시스템 배포 (라고 하는 *Azure 스택 클라우드*)는 단일 지역을 구성 합니다. 각 Azure 스택 개발 키트에 이라는 하나의 영역 **로컬**합니다. 를 사용 하는 두 번째 Azure 스택 통합 시스템을 배포 하거나 별도 하드웨어에서 개발 키트의 다른 인스턴스를 설정 하는 경우이 Azure 스택 클라우드 다른 영역입니다.

## <a name="information-available-through-the-region-management-tile"></a>영역 관리 타일을 통해 사용할 수 있는 정보
Azure 스택 집합이에 제공 관리 기능 영역에서 **지역 관리** 바둑판식으로 배열입니다. 이 타일은 기본 대시보드 관리자 포털에서 Azure 스택 운영자에 게 사용할 수 있습니다. 이 타일을 통해 모니터링 하 고 Azure 스택 국가 및 지역별은 해당 구성 요소를 업데이트할 수 있습니다.

 ![지역 관리 타일](media/azure-stack-manage-region/image1.png)

 영역 관리 타일의 영역을 클릭 하면 다음 정보를 액세스할 수 있습니다.

  ![영역 관리 블레이드의 창 설명](media/azure-stack-manage-region/image2.png)

1. **리소스 메뉴**합니다. 여기에서 특정 인프라 관리 영역에 액세스할 수 있습니다 및 보기 및 저장소 계정 및 가상 네트워크와 같은 사용자 리소스를 관리 합니다.

2. **경고**. 이 타일은 시스템 수준 경고를 나열하고 각 경고에 대한 세부 정보를 제공합니다.

3. **업데이트**. 이 타일에 현재 버전의 Azure 스택 인프라, 사용 가능한 업데이트 및 업데이트 내역을 볼 수 있습니다. 통합된 시스템 사용자를 업데이트할 수 있습니다.

4. **리소스 공급자**. 리소스 공급자는 Azure 스택을 실행 하는 데 필요한 구성 요소에서 제공 하는 사용자 기능을 관리 하는 곳입니다. 각 리소스 공급자는 관리 환경이 제공 됩니다. 이 환경에는 특정 공급자에 대한 경고, 메트릭 및 리소스 공급자에 한정된 기타 관리 기능이 포함될 수 있습니다.

5. **인프라 역할**. 인프라 역할은 Azure Stack을 실행하는 데 필요한 구성 요소입니다. 경고를 보고 하는 인프라 역할에 대해서만 나열 됩니다. 역할을 클릭하면 특정 역할과 연결된 경고 및 이 역할이 실행되고 있는 역할 인스턴스를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
[상태 모니터 및 경고를 Azure 스택](azure-stack-monitor-health.md)

[Azure 스택에서 업데이트를 관리](azure-stack-updates.md)
