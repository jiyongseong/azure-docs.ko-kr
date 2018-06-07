---
title: Azure 스택에는 제안을 만듭니다. | Microsoft Docs
description: 클라우드 관리자 Azure 스택에서 사용자가 제공을 만드는 방법에 설명 합니다.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2018
ms.author: brenduns
ms.openlocfilehash: 6a59d0c8144492ef907e5c395f05e4e8a16678a5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="create-an-offer-in-azure-stack"></a>Azure Stack에서 제품 만들기

[제공](azure-stack-key-features.md) 은 공급자를 구입 하거나 구독 하는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다. 이 문서를 포함 하는 제품을 만드는 방법을 보여 줍니다.는 [만든 계획을](azure-stack-create-plan.md) 마지막 단계에서입니다. 이 제품은 구독자가 가상 컴퓨터를 프로 비전 하는 기능을 제공 합니다.

1. Azure 스택 관리자 포털에 로그인 (https://adminportal.local.azurestack.external) > 클릭 **새로** > **테 넌 트 제공 + 계획** > **제공**합니다.

   ![](media/azure-stack-create-offer/image01.png)
2. 에 **새 제공** 창에서 전체 **표시 이름** 및 **리소스 이름**, 선택한 다음 새 또는 기존 **리소스 그룹**합니다. 표시 이름은 제안에 대 한 친숙 한 이름입니다. 이 대화명은 구독 경우에 사용자에 게 표시 하는 제품에 대 한 유일한 정보입니다. 따라서를 통해 사용자는 제안 추가 되는 내용 이해 하는 직관적인 이름을 사용 해야 합니다. 관리자만 리소스 이름을 볼 수 있습니다. 관리자가 Azure 리소스 관리자 리소스로 제품 작업을 하는 데 사용하는 이름입니다.

   ![](media/azure-stack-create-offer/image01a.png)
3. 클릭 **계획 기본** 열려는 **계획** 창에 제품을 포함 하 고 클릭 하려는 계획을 선택 **선택**합니다. **만들기** 를 클릭하면 제품이 만들어집니다.

   ![](media/azure-stack-create-offer/image02.png)
4. 이 요청을 만든 후 해당 상태를 변경할 수 있습니다. 제안 이루어져야 합니다 *공용* 사용자가 구독 하는 경우 전체 보기를 얻을 수 있습니다. 제공 될 수 있습니다.
   - **공용**: 사용자에 게 표시 합니다.
   - **개인**: 클라우드 관리자에만 표시 합니다. 계획 또는 제품을 초안을 작성 하는 동안 유용한 클라우드 관리자가 또는 [사용자에 대 한 각 구독을 만들](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)합니다.
   - **역할 해제**: 새 구독자가 접근할 수 없습니다. 클라우드 관리자 사용 하 여 향후 구독을 방지 하지만 현재 구독자를 그대로 남겨 둡니다를 서비스 해제 합니다.

   > [!TIP]  
   > 제품에 대 한 변경 내용을 사용자에 게 즉시 표시 되지 않습니다. 변경 내용을 보려면, 사용자가 로그 아웃 및 로그인에 새 제품을 확인 하기 위해 사용자 포털을 다시 해야 할 수 있습니다. 

   제품의 상태를 변경 합니다. 

   - **버전 1803 이상**:  
     이 요청에 대 한 개요 블레이드에서 클릭 **내게 필요한 옵션 상태**, like 사용 하려는 상태를 선택 *공용*, 클릭 하 고 **저장**합니다. 
 
     ![내게 필요한 옵션 상태를 선택 합니다.](media/azure-stack-create-offer/change-state.png) 

     또는 제공 하는 서비스에 액세스 한 후 있습니다 수 goto **설정을 제공**, 선택한 후 **내게 필요한 옵션 상태** 상태를 변경 합니다. 

   - **이전 버전 1803**:  
     클릭 **모든 리소스**, 새 제안에 대 한 검색를 클릭 하 고 새 제안을 클릭 **상태 변경**, 클릭 하 고 **공용**합니다.

  
   > [!NOTE] 
   > PowerShell를 사용 하 여 기본 제안, 계획 및 할당량을 만들 수 있습니다. 자세한 내용은 참조 [Azure 스택 서비스 관리자 추가 정보](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin)합니다.
   >


### <a name="next-steps"></a>다음 단계
[구독 만들기](azure-stack-subscribe-plan-provision-vm.md)      
[가상 머신 프로비전](azure-stack-provision-vm.md)
