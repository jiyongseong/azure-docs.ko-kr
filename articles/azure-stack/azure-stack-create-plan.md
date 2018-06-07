---
title: Azure 스택에서 계획 만들기 | Microsoft Docs
description: 클라우드 관리자는 가상 컴퓨터를 프로 비전 하는 구독자 수 있는 계획을 만듭니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: b1bfff16c4f51a9fa53204930df78cbd2cf19b8d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="create-a-plan-in-azure-stack"></a>Azure Stack에서 계획 만들기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

[계획](azure-stack-key-features.md)은 하나 이상의 서비스에 대한 그룹화입니다. 공급자 사용자에 게 제공 하는 계획을 만들 수 있습니다. 차례로 사용자가 구독할 계획 및 여기에 포함 하는 서비스를 사용 하 여 제공 합니다. 이 예에서는 계산, 네트워크 및 저장소 리소스 공급자를 포함 하는 계획을 만드는 방법을 보여 줍니다. 이 계획 구독자가 가상 컴퓨터를 프로 비전 하는 기능을 제공 합니다.

1. Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external)합니다.

2. 계획 및 사용자가 구독할 수 있는 제품을 만들려면 선택 **새로** > **제공 + 계획** > **계획**합니다.  
   ![계획 선택](media/azure-stack-create-plan/select-plan.png)

3. 에 **새 계획** 창에서 전체 **표시 이름** 및 **리소스 이름**합니다. 표시 이름은는 사용자에 게 표시 하는 계획의 이름이입니다. 관리자만이 관리자는 Azure 리소스 관리자 리소스에 따라 계획을 사용 하려면 사용 하는 이름을 리소스 이름을 볼 수 있습니다.  
   ![세부 정보 지정](media/azure-stack-create-plan/plan-name.png)

4. 새 **리소스 그룹**, 또는 계획에 대 한 컨테이너로 기존 템플릿을 선택 합니다.  
   ![리소스 그룹을 지정 합니다.](media/azure-stack-create-plan/resource-group.png)

5. 선택 **서비스** 다음에 대 한 확인란을 선택 하 고 **Microsoft.Compute**, **Microsoft.Network**, 및 **Microsoft.Storage**합니다. 다음으로, 선택 **선택** 구성을 저장 합니다. 확인란에는 각 옵션 위에 마우스를 이동할 때 표시 됩니다.  
   ![서비스 선택](media/azure-stack-create-plan/services.png)

6. 선택 **할당량**, **Microsoft.Storage (로컬)**, 기본 할당량 또는 선택 중 하나를 선택 합니다 **새 할당량 만들기** 할당량을 사용자 지정할 수 있습니다.  
   ![할당량](media/azure-stack-create-plan/quotas.png)

7. 새 할당량을 만드는 경우 입력 한 **이름** 할당량에 대 한 > 할당량 값을 지정 > 선택 **확인**합니다. **할당량 만들기** 창이 닫히고 합니다.
   ![새 할당량](media/azure-stack-create-plan/new-quota.png)

   만든 새 할당량을 선택 합니다. 할당량을 선택 하면 할당 되 고 선택 창을 닫습니다.  
   ![할당량 할당](media/azure-stack-create-plan/assign-quota.png)

8. 반복 6-7 단계를 만들고 할당에 대 한 할당량 **Microsoft.Network (로컬)** 및 **Microsoft.Compute (로컬)** 합니다.  세 서비스에 모두 할당 된 할당량이, 하면 다음 이미지와 비슷하게 나타납니다.  
   ![전체 할당량 할당](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 에 **할당량** 창 선택 **확인**, 한 다음는 **새 계획** 창 선택 **만들기** 계획을 만들 합니다.  
    ![계획 만들기](media/azure-stack-create-plan/create.png)
10. 새 계획을 보려면 선택 **모든 리소스**계획에 대 한 다음 검색 하 고 해당 이름을 선택 합니다. 사용 하 여 리소스의 목록이 긴 경우 **검색** 이름으로 계획을 찾으려고 합니다.  
   ![계획을 검토](media/azure-stack-create-plan/plan-overview.png)

### <a name="next-steps"></a>다음 단계
[제품 만들기](azure-stack-create-offer.md)
