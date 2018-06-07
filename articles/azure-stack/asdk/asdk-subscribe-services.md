---
title: "이 자습서에서는 Azure 스택 서비스를 구독 하는 방법을 배웁니다 | Microsoft Docs"
description: "이 자습서에서는 Azure 스택을 서비스에 대 한 새 구독 만들기 및 테스트 가상 컴퓨터를 만들어 제안을 테스트 하는 방법을 보여 줍니다."
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
ms.openlocfilehash: d2adda5613b9a10bc3314045b9d68b0f3196f19a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-create-and-test-a-subscription"></a>자습서: 만들기 및 구독 테스트
이 자습서를 제공 하는 서비스를 포함 하는 구독을 만들고 다음 테스트는 방법을 보여 줍니다. 테스트를 위해 됩니다에 로그인 하 고 [사용자 포털](https://portal.local.azurestack.external) 클라우드 관리자는 제품을 구독 한 다음 가상 컴퓨터를 만듭니다.

> [!TIP]
> 더 많은 고급 평가 환경을 더 할 수 있습니다 [특정 사용자에 대 한 구독을 만들](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm#create-a-subscription-as-a-cloud-operator) 하 고 사용자 포털에서 해당 사용자로 로그인 합니다. 

이 자습서에서 만든 서비스를 구독 하는 방법을 보여 줍니다.는 [이전 자습서](asdk-offer-services.md)합니다.

학습 내용:

> [!div class="checklist"]
> * 제품 구독 
> * 제품 테스트

## <a name="subscribe-to-an-offer"></a>제품 구독
사용자로 제공 하는 서비스를 구독 하려면 Azure 스택 연산자에서 제공 하는 서비스를 검색 하는 Azure 스택 사용자 포털에 로그인 해야 합니다.

1. 에 로그인 하 고 [사용자 포털](https://portal.local.azurestack.external) 클릭 **구독**합니다.

   ![구독 가져오기](media/asdk-subscribe-services/get-subscription.png)

2. **표시 이름** 필드에서 구독의 이름을 입력합니다. 클릭 **제공** 에 사용할 수 있는 서비스 중 하나를 선택는 **제안을 선택** 섹션 및 클릭 **만들기**합니다.

   ![제품 만들기](media/asdk-subscribe-services/create-subscription.png)

   > [!TIP]
   > 이제 사용자 포털 구독 사용을 시작 하려면 새로 고쳐야 합니다.

3. 방금 만든 구독을 보려면 클릭 **더 많은 서비스**, 클릭 **구독**, 새 구독을 클릭 합니다. 제공 하는 서비스를 구독 한 후에 새로운 서비스는 새 구독의 일부로 포함 되었는지 확인 하기 위해 포털을 새로 고칩니다. 이 예제에서는 **가상 컴퓨터** 추가 되었습니다.

   ![구독 보기](media/asdk-subscribe-services/view-subscription.png)


## <a name="test-the-offer"></a>제품 테스트
에 로그인 한는 [사용자 포털](https://portal.local.azurestack.external), 새 구독 기능을 사용 하 여 가상 컴퓨터를 프로 비전 하 여 제안을 테스트할 수 있습니다. 

> [!NOTE]
> 이 테스트에 Azure 스택 시장에 추가 된 Windows Server 2016 데이터 센터 VM 이미지에 필요한는 [이전 자습서](asdk-marketplace-item.md)합니다. 

1. 에 로그인 하 고 [사용자 포털](https://portal.local.azurestack.external)합니다.

2. 사용자 포털에서 클릭 **가상 컴퓨터** > **추가** > **Windows Server 2016 Datacenter**, 클릭 하 고 **만들기** .

3. 에 **기본 사항** 섹션에서 입력 한 **이름**, **사용자 이름**, 및 **암호**, 선택는 **구독**, 만들기는 **리소스 그룹** (또는 기존 템플릿을 선택)을 클릭 한 다음 **확인**합니다.

4. 에 **크기를 선택** 섹션에서 클릭 **표준 A1**, 클릭 하 고 **선택**합니다.  

5. 설정 블레이드에서 기본값을 적용 하 고 클릭 **확인**합니다.

6. 에 **요약** 섹션에서 클릭 **확인** 가상 컴퓨터를 만듭니다.  

7. 새 가상 컴퓨터를 보려면 클릭 **가상 컴퓨터**, 한 다음 새 가상 컴퓨터를 검색 하 고 해당 이름을 클릭 합니다.

    ![모든 리소스](media/asdk-subscribe-services/view-vm.png)

> [!NOTE]
> 가상 컴퓨터 배포를 완료 하려면 몇 분 정도 걸립니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서 배운 내용:

> [!div class="checklist"]
> * 제품 구독 
> * 제품 테스트

서식 파일을 사용 하 여 VM을 만드는 방법을 알아보려면 다음 자습서를 진행 합니다.

> [!div class="nextstepaction"]
> [템플릿에서 가상 컴퓨터 만들기](asdk-create-vm-template.md)