---
title: "이 자습서에서는 Azure 스택 혜택 및 계획을 업데이트 하는 방법을 배웁니다 | Microsoft Docs"
description: "이 문서에서는 기존 Azure 스택 제공 및 계획 보기 및 수정 하는 방법을 설명 합니다."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 3b5d2ab5e924f578f5838d11b0d2058aacf67dec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-update-offers-and-plans"></a>자습서: 제안 및 계획 업데이트
Azure 스택 연산자로 원하는 서비스와 사용자가 구독할 수에 대 한 적용 가능한 할당량 포함 하는 계획을 만듭니다. 이러한 *계획 기본* 사용자에 게 제공 해야 하는 핵심 서비스를 포함 하 고 제안 당 한 개의 기본 계획을 하나만 지정할 수 있습니다. 제안을 수정 해야 하는 경우 사용할 수 있습니다 *추가 기능이 계획* 컴퓨터, 저장소, 확장 하는 계획을 수정할 수 있는 또는 초기 제공 하는 기본 계획 할당량을 네트워크입니다. 

하지만 단일 계획에 결합 하는 모든 경우에 따라 최적의 계획 및 추가 기능 계획을 사용 하는 추가 서비스를 제공 하는 기본을 사용할 하는 것이 좋습니다. 예를 들어, 모든 PaaS 서비스 계획 추가 기능으로 처리 된 기본 계획의 일환으로 IaaS 서비스를 제공 하도록 결정할 수 있습니다. 계획은 제한 된 ASDK 환경에서 리소스의 사용을 제어 하도 사용할 수 있습니다. 예를 들어 모든 사용자가 리소스 사용에 주의 해야 합니다 (필요한 서비스)에 따라 비교적 작은 기본 계획이 있을 수 있습니다 및 사용자가 용량에 도달 하는 대로 하는 경고를 발생 시킬 리소스 할당 사용 이미 했으며 자신의 할당 된 계획에 기반 합니다. 사용자는 여기에서 추가 리소스에 대 한 사용 가능한 추가 기능 계획을 선택할 수 있습니다. 

> [!NOTE]
> 사용자는 추가 기능이 계획에 추가 하는 기존 제공 구독 추가 리소스 표시 한 시간 걸릴 수입니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 추가 기능 계획 만들기 
> * 추가 기능 계획을 구독

## <a name="create-an-add-on-plan"></a>추가 기능 계획 만들기
**추가 기능 계획** 기존 제안을 수정 하 여 생성 됩니다.

1. 에 로그인 하 고 [Azure 스택 포털](https://adminportal.local.azurestack.external) 클라우드 관리자입니다.
2. 이전에 사용한 동일한 단계에 따라 [기본 계획을 만들](asdk-offer-services.md) 이전에 제공 되지 않은 서비스를 제공 하는 새 계획을 만듭니다. 이 예제에서는 키 자격 증명 모음 (Microsoft.KeyVault) 서비스를 계획에 포함 됩니다.
3. 관리자 포털에서 클릭 **제공** 한 다음 제안 하는 추가 기능 계획으로 업데이트를 선택 합니다.

   ![](media/asdk-update-offers/1.PNG)

4.  제공 속성의 아래쪽으로 스크롤하여 선택 **추가 기능이 계획**합니다. **추가**를 클릭합니다.
   
    ![](media/asdk-update-offers/2.PNG)

5. 추가할 계획을 선택 합니다. 이 예제에서는 계획 이라고 **키 자격 증명 모음 계획**, 클릭 하 고 **선택** 제품에 계획을 추가 하려면. 계획 제품에 성공적으로 추가 된 알림을 받게 됩니다.
   
    ![](media/asdk-update-offers/3.PNG)

6. 새 추가 기능 계획 있는지 확인 하려면 제품에 포함 된 계획이 나열 된 추가 기능 목록을 검토 합니다.
   
    ![](media/asdk-update-offers/4.PNG)

## <a name="subscribe-to-the-add-on-plan"></a>추가 기능 계획을 구독
추가 기능이 계획에 기존 Azure 스택 구독을 확장 하는 Azure 스택 사용자 포털에 로그인 해야 합니다.

다음이 단계를 사용 하 여 Azure 스택 연산자에서 제공 하는 추가 리소스를 검색 하 고 이전에 구독 하는 서비스를 추가 기능 계획을 추가 합니다.

1. 에 로그인 하 고 [사용자 포털](https://portal.local.azurestack.external)합니다.
2. 계획 추가 기능을 확장 하는 구독을 찾으려면 클릭 **더 많은 서비스**, 클릭 **구독**, 구독을 선택 합니다.
   
    ![](media/asdk-update-offers/5.PNG)

3.  구독 개요에서 클릭 **계획 추가**합니다.
   
    ![](media/asdk-update-offers/6.PNG)

4. 추가 계획 블레이드에서 구독에 추가할 추가 기능 계획을 선택 합니다. 이 예제에서는 **키 자격 증명 모음 계획** 을 선택 합니다. 그런 다음 추가 기능 계획은 성공적으로 얻을 수 있으며, 업데이트 된 구독에 액세스 하려면 포털을 새로 고치려면 할 경우 알림을 받아야 합니다.
   
    ![](media/asdk-update-offers/7.PNG)

5. 마지막으로 추가 기능 계획 성공적으로 추가 되었는지 확인 하는 구독와 연결 된 추가 기능 계획을 검토 합니다.
   
    ![](media/asdk-update-offers/8.PNG)


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 추가 기능 계획 만들기 
> * 추가 기능 계획을 구독

> [!div class="nextstepaction"]
> [템플릿에서 가상 컴퓨터 만들기](asdk-create-vm-template.md)

