---
title: Marketplace용 VM 제품 테스트 | Microsoft Docs
description: Azure Marketplace용 VM 이미지를 테스트하는 방법을 이해합니다.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mbaldwin
ms.openlocfilehash: 7f225f113dd465f79d5d195b59a809fe3be6a1f8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>스테이징에서 Azure Marketplace용 VM 제품 테스트
스테이징이란 SKU를 Marketplace에 배포하기 전에 기능을 테스트하고 확인할 수 있는 사설 "샌드박스"에 배포하는 것을 의미합니다. 그러면 SKU는 배포한 고객에게 표시되는 것처럼 스테이징으로 표시됩니다. VM 이미지는 인증되어야만 스테이징으로 푸시될 수 있습니다.

## <a name="step-1-push-your-offer-to-staging"></a>1단계: 제품을 스테이징으로 푸시
1. **게시** 탭에서 **스테이징으로 푸시**를 클릭합니다.
   
    ![drawing](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. 게시 포털에서 오류에 대한 알림이 표시되는 경우 오류를 해결합니다.
3. **스테이징된 제품에 액세스할 수 있는 사람** 대화 상자에서 [Azure Preview 포털](https://portal.azure.com)에서 제품을 미리 보는 데 사용할 Azure 구독 목록을 입력합니다.
   
   > [!NOTE]
   > Virtual Machines 및 솔루션 템플릿의 경우 CSP, DreamSpark 또는 Azure in Open 형식의 구독을 허용 목록에 **추가하지 마세요** .
   > 
   > 

    > Virtual Machines의 경우 **스테이징으로 푸시**버튼을 클릭하면 백그라운드에서 다음 단계가 수행됩니다. 게시 포털의 게시 탭에서 각 단계의 진행 상태를 볼 수 있습니다. 이 페이지에서 목표에 따라 수정이 필요한 오류 정보가 있는지를 정기적으로 확인해야 합니다(상태가 "스테이징됨"으로 표시될 때까지).

    > - 처음에 스테이징 요청이 VHD의 유효성을 검사하는 인증 팀으로 이동됩니다. 그러나 요청에 마케팅 변경만 포함되는 경우 인증 단계가 건너뛰어 집니다.
    > - 인증이 완료되면 제품의 복제가 모든 Azure 데이터 센터에서 시작됩니다. 일반적으로 복제가 완료되는 데는 24~48시간이 걸리지만 VHD의 크기에 따라 1주일까지 걸릴 수도 있습니다. 그러나 요청에 마케팅 변경만 포함되는 경우 복제가 더 빨라집니다.
    > - 복제가 완료되면 [Azure 포털](http:/portal.azure.com)에서 제품을 사용할 수 있게 됩니다. 이때 상태는 게시 포털에서 "스테이징됨"이 됩니다. 스테이징된 제품은 제품이 스테이징되는 구독과 연결된 전자 메일 ID를 사용할 때만 [Azure 포털](http:/portal.azure.com) 에 표시됩니다.

1. 이전 단계에서 나열된 Azure 구독 중 하나를 사용하여 [Azure Preview 포털](https://portal.azure.com) 에 로그인합니다.
2. 제품을 찾고 VM 이미지 지점의 유효성을 검사합니다.
   
   * 마케팅 콘텐츠가 Marketplace에 올바르게 표시되는지 확인합니다.
   * VM 이미지의 종단 간 배포
     
      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> 제품을 프로덕션으로 푸시할 준비가 되었다고 게시 포털을 통해 Microsoft에 알릴 때까지[**게시** 탭 > **"프로덕션으로 푸시 승인 요청"** 버튼 클릭] 제품은 스테이징으로 유지됩니다. 이 시간 동안 팀의 모든 구성원이 제품을 나열할 준비가 되었는지 확인할 수 있습니다.
> 
> 스테이징 플랫폼은 게시자가 미리 보기 모드에서 제품을 테스트할 수 있도록 설계되었습니다. 이 플랫폼은 상용으로는 사용하지 않는 것이 좋습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
제품이 "스테이징"되었고 제품의 기능 및 마케팅 콘텐츠를 테스트했으므로 최종 게시 단계인 **4단계**, [Marketplace에 제품 배포](marketplace-publishing-push-to-production.md)를 진행할 수 있습니다.

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

