---
title: 이 자습서에서는 Azure 스택 제공 만들 | Microsoft Docs
description: 계획 및 할당량을 비롯 한 Azure 스택 제공을 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8bcc2f3077e79ff83ac2e90db0bb0fa53ae83adc
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-offer-azure-stack-iaas-services"></a>자습서: Azure 스택 IaaS 서비스를 제공 합니다.
Azure 스택 클라우드 관리자 (테 넌 트 라고도 함) 사용자가 구독할 수 있는 서비스를 만들 수 있습니다. 구독을 사용 하 여 사용자가 Azure 스택 서비스를 사용할 수 있습니다.

이 자습서에서 업로드 되는 Azure 스택 마켓플레이스 Windows Server 2016 Datacenter 이미지에 따라 가상 컴퓨터를 만드는 사용자가 제공을 만드는 방법을 보여 줍니다.는 [이전 자습서](asdk-marketplace-item.md)합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 제품 만들기
> * 계획 만들기
> * 할당량 설정
> * Public로 설정 제공

Azure 스택 서비스 구독, 이벤트, 및 계획을 사용 하 여 사용자에 게 배달 됩니다. 사용자가 여러 제품을 구독할 수 있습니다. 서비스는 하나 이상의 계획을 가질 수 있습니다 및 다음 다이어그램에 나와 있는 것 처럼 계획에서 하나 이상의 서비스를 가질 수 있습니다.

![구독, 이벤트, 및 계획](media/asdk-offer-services/sop.png)

서비스를 제공 하는 Azure 스택 연산자에서 제공 하 여 계획을 및 할당량을 만들 것인지 묻는 먼저 합니다. 제공 하는 서비스를 만든 후 Azure 스택 사용자를 구독 하는 사용자 포털을 통해 제공 합니다.

## <a name="create-an-offer"></a>제품 만들기
**제공** Azure 스택 연산자를 구입 하거나 구독 하는 사용자에 게 제공 하는 하나 이상의 계획의 그룹입니다.

1. 에 로그인 하 고 [Azure 스택 포털](https://adminportal.local.azurestack.external) 클라우드 관리자입니다.

2. 클릭 **새** > **제공 + 계획** > **제공**합니다.

   ![새 제품](media/asdk-offer-services/new-offer.png)

2. 에 **새 제공** 섹션에서 입력 **표시 이름** 및 **리소스 이름**를 선택한 다음 새 또는 기존 **리소스 그룹**합니다. 표시 이름은는 제품의 공개 이름을 사용자에 게 표시 하는입니다. 클라우드 연산자만 제안 된 Azure 리소스 관리자 리소스로 작동 하도록 관리자가 사용 되는 리소스 이름을 볼 수 있습니다.

   ![표시 이름](media/asdk-offer-services/offer-display-name.png)


## <a name="create-a-plan"></a>계획 만들기
기본 제공 정보를 입력 한 후 제품에 하나 이상의 기본 계획을 추가 해야 합니다. 

**계획** 사용자에 게 제공 하는 그룹 서비스와 연결 된 해당 할당량을 Azure 스택 운영자 허용 합니다.

1. 클릭 **계획 기본**, 및는 **계획** 섹션에서 클릭 **추가** 제품에는 새 계획을 추가 하려면.

   ![계획 추가](media/asdk-offer-services/new-plan.png)

2. 에 **새 계획** 섹션에서 입력 **표시 이름** 및 **리소스 이름**합니다. 표시 이름은는 사용자에 게 표시 하는 계획의 이름이입니다. 클라우드 연산자만 Azure 리소스 관리자 리소스에 따라 계획을 사용 하려면 클라우드 운영자는에서 사용 하는 리소스 이름을 볼 수 있습니다.

   ![계획 표시 이름입니다.](media/asdk-offer-services/plan-display-name.png)

3. 그런 다음 계획에 포함할 서비스를 선택 합니다. IaaS 서비스를 제공 하려면 **서비스**선택, **Microsoft.Compute**, **Microsoft.Network**, 및 **Microsoft.Storage**, 한 다음 클릭 **선택**합니다.

   ![계획 서비스](media/asdk-offer-services/select-services.png)


## <a name="set-quotas"></a>할당량 설정
이제에 제안을 서비스를 포함 하는 계획을 각각에 대 한 할당량을 설정 해야 합니다. 

**할당량** 제공 되는 계획에 포함 된 각 서비스에 대 한 사용자를 사용할 수 있는 리소스의 양을 결정 합니다.

1. 클릭 **할당량**, 한 다음 할당량 만들려는 서비스를 선택 합니다. 

   시작 하려면 먼저 계산 서비스에 대 한 할당량을 만듭니다. 네임 스페이스 목록에서 선택 된 **Microsoft.Compute** 네임 스페이스 다음를 클릭 하 고 **새 할당량 만들기**합니다.
   
   ![새 할당량 만들기](media/asdk-offer-services/create-quota.png)

2. 에 **할당량 만들기** 섹션 할당량에 대 한 이름을 입력의 할당량에 대 한 원하는 매개 변수를 설정 하 고 클릭 **확인**합니다.

   ![할당량 이름](media/asdk-offer-services/quota-properties.png)

3. 할당량에 대해 만든 선택은 **Microsoft.Compute** 서비스입니다.

   ![할당량을 선택 합니다.](media/asdk-offer-services/set-quota.png)

4. 네트워크 및 저장소 서비스에 대 한 할당량을 설정 하 고 할당량 섹션에서 확인을 클릭 한 다음에 1-3 단계를 반복 합니다. 다음으로 클릭 **확인** 에 **새 계획** 섹션 계획 설치를 완료 합니다. 

   ![모든 할당량 설정](media/asdk-offer-services/all-quotas-set.png)

5. 클릭 하 여 제안 만들기를 완료 **만들기** 계획 섹션에 맞춰져 있습니다. 알림이 표시 됩니다는 제안을 성공적으로 만들어지고이 사용 가능한 제안을으로 나열 됩니다.

   ![만든 제안](media/asdk-offer-services/offer-complete.png)

## <a name="set-offer-to-public"></a>Public로 설정 제공
서비스는 사용자가 구독할 수 있도록 제공 하는 선택할 때 볼 수에 대 한 공용 수 있어야 합니다. 

제공 될 수 있습니다.
- **공용**: 모든 사용자에 게 표시 합니다.
- **개인**: 클라우드 관리자에만 표시 합니다. 계획 또는 제품을 초안을 작성 하는 동안 유용한 않거나 클라우드 관리자가 사용자에 대 한 각 구독을 만들 경우.
- **역할 해제**: 새 구독자가 접근할 수 없습니다. 클라우드 관리자 사용 하 여 향후 구독을 방지 하지만 현재 구독자를 그대로 남겨 둡니다를 서비스 해제 합니다.

> [!TIP]
> 제품에 대 한 변경 내용을 사용자에 게 즉시 표시 되지 않습니다. 변경 내용을 보려면, 사용자가 로그 아웃 및 로그인을 다시 해야 할 수는 [사용자 포털](https://portal.local.azurestack.external) 에 새 제품을 볼 수 있습니다.

설정 하려면 새 제안을 public에: 
   - 1803 이상 버전: 
     1. 대시보드 메뉴를 클릭 **제공** 다음 만든 제품을 클릭 합니다.

     2. 클릭 **내게 필요한 옵션 상태**, 클릭 하 고 **공용**합니다.

        ![상태 변경](media/asdk-offer-services/change-state.png)

     3. 제안을은 Azure 스택 사용자 포털에서 제공 됩니다.


   - 이전 버전 1803:  
     1. 대시보드 메뉴를 클릭 **제공** 다음 만든 제품을 클릭 합니다.

     2. 클릭 **상태 변경**, 클릭 하 고 **공용**합니다.

        ![공개 상태](media/asdk-offer-services/set-public.png)

     3. 제안을은 Azure 스택 사용자 포털에서 제공 됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 제품 만들기
> * 계획 만들기
> * 할당량 설정
> * Public로 설정 제공

Azure 스택 사용자로 방금 만든 서비스를 구독 하는 방법을 알아보려면 다음 자습서를 진행 합니다.

> [!div class="nextstepaction"]
> [제품 구독](asdk-subscribe-services.md)