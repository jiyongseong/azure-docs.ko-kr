---
title: Azure Marketplace에 제품 배포 | Microsoft Docs
description: '제품(예: 가상 머신 이미지, 개발자 서비스, 데이터 서비스)을 Azure Marketplace에 배포하기 위한 지침을 알아보고 연습합니다.'
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: mbaldwin
ms.openlocfilehash: 8df7b0e49e17612743b02596e99f7d1fbe8c6803
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>Azure Marketplace에 제품 배포
제품에 만족(즉, 고객 시나리오, 마케팅 콘텐츠 등을 테스트)하고 시작할 준비가 되면 **게시** 탭에서 **프로덕션으로 푸시**를 요청합니다.  

1. 게시 포털의 연습 페이지에 나오는 네 가지 단계를 완료해야 하며 녹색으로 표시되어야 합니다. Virtual Machine 제품의 경우 다음 지침을 따라야 합니다.
   
    ![drawing][img-pubportal-walkthru-checked]
2. 왼쪽의 목록에서 **게시** 탭을 선택합니다.
   
    ![drawing][img-pubportal-menu-publish]
3. **프로덕션으로 푸시 승인 요청**버튼을 클릭합니다. 요청이 이루어지면 승인 팀이 마지막 검토를 실행한 다음 제품을 Azure Marketplace에서 판매할 수 있게 됩니다.
   
    ![drawing][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> Virtual Machines의 경우 프로덕션으로 푸시 승인 요청 버튼을 클릭하면 백그라운드에서 다음 단계가 수행됩니다. 게시 포털의 게시 탭에서 각 단계의 진행 상태를 볼 수 있습니다. 이 페이지에서 목표에 따라 수정이 필요한 오류 정보가 있는지를 정기적으로 확인해야 합니다(상태가 "나열됨"으로 표시될 때까지).
> 
> * 처음에 프로덕션 요청이 VHD의 유효성을 검사하는 인증 팀으로 이동됩니다. 그러나 이미 등록된 제품을 업데이트하려고 하며 요청에 마케팅 변경만 포함되는 경우 인증 단계가 건너뛰어 집니다.
> * 다음 단계에서 요청은 제품의 마케팅 콘텐츠를 확인하는 콘텐츠 유효성 검사 팀으로 이동됩니다.
> * 위의 단계가 성공적으로 수행되면 제품이 프로덕션 환경에서 승인됩니다. 이때 상태는 게시 포털에서 "나열됨"이 됩니다. 그러나 상태가 "나열됨"이라고 해서 프로세스가 완료된 것을 의미하는 것은 아닙니다. Azure Marketplace에서 제품을 사용할 수 있게 되려면 다음 단계를 완료해야 합니다.
> * 위의 단계에서 프로덕션 환경에서 제품이 승인되면 제품의 복제가 모든 Azure 데이터 센터에서 시작됩니다. 일반적으로 복제가 완료되는 데는 24~48시간이 걸리지만 VHD의 크기에 따라 1주일까지 걸릴 수도 있습니다. 그러나 이미 등록된 제품을 업데이트하려고 하며 마케팅 변경만 포함되는 경우 복제는 더 빨라집니다.
> * 복제가 완료되면 Azure Marketplace에서 제품을 사용할 수 있게 됩니다.
> 
> **초안** 상태(즉, **스테이징으로 푸시**, **프로덕션으로 푸시** 상태가 아님)에 있는 동안에는 제품을 언제든지 삭제할 수 있습니다. **기록** 탭에서 초안을 삭제하려면 페이지의 맨 아래에 있는 **초안 삭제** 단추를 클릭합니다.
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>모든 Virtual Machine 제품에 대한 프로덕션 검사 목록
* Microsoft Azure Certified 파트너인지 확인합니다.
* SKU가 솔루션 템플릿의 일부인 경우에만 SKU 탭에서 "솔루션 템플릿을 통해 항상 구입되어야 하기 때문에 Marketplace에서 이 SKU를 숨깁니다." 옵션이 "예"로 표시됩니다. 다른 모든 경우에는 이 옵션이 항상 "아니요"로 표시됩니다.
* 주의: 일단 SKU가 나열되면 SKU 표시 유형 설정을 변경하지 않아야 합니다. 이 기능은 지원되지 않습니다.
* 로고가 아래에서 설명한 Azure Marketplace 로고 지침에 부합하는지 확인합니다.
* 제품 및 SKU 설명은 달라야 합니다.
* SKU의 제목 및 제품의 자세한 설명은 달라야 합니다.
* SKU 제목 및 제품 요약은 달라야 합니다.
* 여러 SKU가 있는 제품에서 SKU 제목을 서로 달라야 합니다.

**Azure Marketplace 로고 지침**

* Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.
* Azure 포털의 테마 색은 흰색과 검은색입니다. 따라서 로고의 배경색으로 이러한 색을 사용하지 않도록 합니다. Azure 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다. 투명한 배경을 사용하는 경우 로고/텍스트가 흰색이나 검은색이 아닌지 확인합니다.
* 로고의 배경에 그라데이션 효과는 사용하지 않습니다.
* 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 않습니다.
* 로고의 모양과 느낌은 평면적이어야 하며 그라데이션은 사용하지 않습니다.
* 로고를 확장할 수 없습니다.

**대표 로고에 대한 추가 지침:**

* 대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. **그러나 일단 업로드되면 대표 로고를 게시 포털에서 삭제할 수 없습니다. 이때 파트너는 대표 아이콘에 대한 Azure Marketplace 지침을 따라야 합니다. 그러지 않으면 제품이 프로덕션으로 승인되지 않습니다.**
* 게시자 표시 이름, SKU 제목 및 제품의 자세한 요약은 흰색 글꼴 색으로 표시됩니다. 따라서 대표 아이콘의 백그라운드는 밝은 색으로 두지 않아야 합니다. 대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.
* 제품이 나열되면 대표 로고 안에 게시자 표시 이름, SKU 제목, 제품 긴 요약과 만들기 버튼이 프로그래밍 방식으로 포함됩니다. 따라서 대표 로고를 디자인할 때 이런 내용을 입력하지 않아야 합니다. 텍스트(즉, 게시자 표시 이름, SKU 제목, 제품의 자세한 요약)는 프로그래밍 방식으로 포함되므로 오른쪽에 빈 공간을 남겨둡니다. 텍스트의 오른쪽 빈 공간은 415x100이어야 합니다(왼쪽에서 370px만큼 오프셋됨).

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>이미 나열된 Virtual Machine 제품에 대한 추가 프로덕션 검사 목록
* 회사에서 동일한 제품 이름 가진 제품을 이미 제공했는지 확인합니다. 그렇다면 중복된 새 제품을 만드는 대신 기존 제품에 새 버전의 SKU를 추가해야 합니다.
* 동일한 SKU의 두 버전 간에 데이터 디스크를 변경하지 않아야 합니다.
* Azure Marketplace는 기존 고객의 요금 청구에 영향을 주므로 나열된 SKU의 가격 변경을 지원하지 않습니다. SKU를 사용할 수 있는 지역에서는 나열된 SKU의 가격를 변경하지 않아야 합니다. 그러나 새 SKU를 추가하거나 기존 SKU에 새 지역을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
제품이 작동되면 고객 시나리오를 테스트하여 모든 계약 및 기능 작업이 스테이징 환경에서 테스트되고 유효성이 검사된 것처럼 프로덕션 환경에서 제대로 작동하는지 테스트합니다.

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png
