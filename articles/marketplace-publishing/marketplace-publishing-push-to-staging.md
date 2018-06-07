---
title: Azure Marketplace 배포할 제품 준비 및 테스트 | Microsoft Docs
description: Azure Marketplace에 제품을 배포하기 전에 마케팅 콘텐츠를 제공하고 가격 계획을 구성하며 테스트하는 방법에 대한 자세한 지침입니다.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3ccd2448-895b-477e-adf6-ab655a21d2fa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/17/2016
ms.author: mbaldwin
ms.openlocfilehash: 5bec9ac89487a824b9de9209547f0b96fa5fe678
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="complete-the-offer-creation-with-marketing-content"></a>마케팅 콘텐츠로 제품 만들기 완료
이 게시 프로세스 단계에서는 Azure Marketplace의 특정 마케팅 콘텐츠와 제품 및/또는 SKU에 대한 세부 정보를 제공해야 합니다. 예를 들어 제품 및/또는 SKU를 스테이징으로 푸시하는 데 필요한 제품 설명, 회사 로고, 가격 계획, 세부 계획 정보 및 기타 정보를 제공합니다. 이 정보는 Azure 포털에서 마케팅 콘텐츠로 사용됩니다. [게시 포털][link-pubportal]에서 이 프로세스를 시작합니다.

## <a name="step-1-provide-marketplace-marketing-content"></a>1단계: Marketplace 마케팅 콘텐츠 제공
**영어가 기본값이며 지원되는 유일한 언어입니다.** 필드에 제공된 모든 정보가 영어로 되어 있는지 확인하세요. 스테이징으로 푸시하기 전에는 언제든지 모든 정보를 편집할 수 있습니다.

1. 게시 포털, [https://publish.windowsazure.com](https://publish.windowsazure.com)으로 이동합니다.
2. 왼쪽 메뉴에서 **Marketing** 탭을 클릭합니다.
3. 주 패널에서 **English (US)** 단추를 클릭합니다.
   
   > [!IMPORTANT]
   > 스테이징에 푸시하려면 이미지를 포함하여 모든 필드에 항목이 있어야 합니다.
   > 
   > 

### <a name="details-and-plans"></a>세부 정보 및 계획
1. **세부 정보** 탭에 제품 제목(최대 50자), 제품 요약(최대 100자), 긴 요약(최대 256자), 제품 설명(최대 1300자), 로고 입력
2. **계획** 탭에 계획 제목(최대 50자), 요약(최대 100자), 계획 설명(최대 2000자)을 입력합니다.
   
   > [!NOTE]
   > 다음 HTML 태그를 사용하여 요약, 자세한 요약, 제품 및 계획에 대한 설명의 서식을 지정할 수 있습니다. 허용되는 HTML 태그는 h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], strong, em, b, i입니다.
   > 
   > 
3. 제품 및 계획 설명에 중복 텍스트는 입력하지 않습니다.
4. 계획의 제목 및 자세한 제품 요약에 중복 텍스트는 입력하지 않습니다.
5. 계획의 제목 및 제품 요약에 중복 텍스트는 입력하지 않습니다.
6. 여러 계획이 있는 제품에 대해 동일한 계획 제목을 입력하지 마세요.
7. 각 크기별로 하나씩 PNG 형식으로 필요한 사양(게시 포털에서 언급한)의 이미지를 업로드합니다.
8. 로고가 아래에서 설명한 Azure Marketplace 로고 지침에 부합하는지 확인합니다.
   
   ![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Azure Marketplace 로고 지침**

게시 포털에 업로드되는 모든 로고는 다음 지침에 따라야 합니다.

* Azure 디자인은 단순한 색 팔레트를 사용합니다. 로고의 기본 색상과 보조 색상 수는 적게 유지합니다.
* Azure 포털의 테마 색은 흰색과 검은색입니다. 따라서 로고의 배경색으로 이러한 색을 사용하지 않도록 합니다. Azure 포털에서 로고가 돋보이도록 하는 색을 사용합니다. 간단한 기본 색을 사용하는 것이 좋습니다. **투명한 배경을 사용하는 경우 로고/텍스트가 흰색, 검은색 또는 파란색이 아닌지 확인합니다.**
* 로고의 배경에 그라데이션 효과는 사용하지 않습니다.
* 로고에 회사 또는 브랜드 이름을 포함한 텍스트를 놓지 않습니다. 로고의 모양과 느낌은 평면적이어야 하며 그라데이션은 사용하지 않습니다.
* 로고를 확장할 수 없습니다.
* 작은 로고 크기는 40 X 40픽셀이어야 합니다.
* 중간 로고 크기는 90 X 90픽셀이어야 합니다.
* 큰 로고 크기는 115 X 115픽셀이어야 합니다.
* 넓은 로고 크기는 255 X 115픽셀이어야 합니다.
* 대표 로고 크기는 815 X 290픽셀이어야 합니다.

> [!NOTE]
> 대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. 그러나 일단 업로드되면 대표 로고를 게시 포털에서 삭제할 수 없습니다. 이때 파트너는 대표 아이콘에 대한 Azure Marketplace 지침을 따라야 합니다.
> 
> 

  ![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**대표 로고 아이콘에 대한 추가 지침(선택 사항)**

* 대표 로고는 선택 사항입니다. 게시자는 대표 로고를 업로드하지 않아도 됩니다. **그러나 일단 업로드되면 대표 로고를 게시 포털에서 삭제할 수 없습니다. 이때 파트너는 대표 아이콘에 대한 Azure Marketplace 지침을 따라야 합니다. 그러지 않으면 제품이 프로덕션으로 승인되지 않습니다.**
* 게시자 표시 이름, 계획 제목 및 제품의 자세한 요약은 흰색 글꼴 색으로 표시됩니다. 따라서 대표 아이콘의 백그라운드는 밝은 색으로 두지 않아야 합니다. 대표 아이콘에는 검은색, 흰색 및 투명한 배경이 허용되지 않습니다.
* 제품이 나열되면 대표 로고 안에 게시자 표시 이름, 계획 제목, 자세한 제품 요약과 만들기 버튼이 프로그래밍 방식으로 포함됩니다. 따라서 대표 로고를 디자인할 때 이런 내용을 입력하지 않아야 합니다. 텍스트(즉, 게시자 표시 이름, 계획 제목, 제품의 자세한 요약)는 프로그래밍 방식으로 포함되므로 오른쪽에 빈 공간을 남겨둡니다. 텍스트의 오른쪽 빈 공간은 415x100이어야 합니다(왼쪽에서 370px만큼 오프셋됨).
  
  ![drawing](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### <a name="links"></a>링크
왼쪽 막대의 **Links** 탭에 고객에게 유용한 정보가 있는 링크를 입력합니다. 각 링크의 이름과 URL을 입력합니다.

![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### <a name="sample-images-optional"></a>샘플 이미지(선택 사항)
> [!NOTE]
> 샘플 이미지를 포함하는 것은 선택적인 단계입니다.
> 게시 포털에 여러 샘플 이미지를 업로드할 수 있지만 Azure 포털에는 하나의 이미지(시스템에서 임의로 선택)만 표시됩니다. 따라서 샘플 이미지는 하나만 업로드하는 것이 좋습니다.
> 
> 

왼쪽 메뉴의 **Sample Images** 탭에서 **Upload a new image**를 클릭하여 새 이미지를 업로드합니다. 기존 이미지가 있고 이를 바꾸려는 경우 **Replace image**를 클릭합니다.

![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### <a name="legal"></a>법적 정보
**Legal** 탭에 정책/사용 약관을 연결하는 링크를 제공합니다. 큰 **Terms of Use** 상자에 약관을 입력하거나 붙여넣습니다. 사용 약관의 문자 제한은 1,000,000자입니다.

![drawing](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

**참고:** Virtual Machine 제품의 경우 Azure Portal에서 제품/SKU가 준비되면 아래에 지정된 필드를 변경할 수 없습니다.

* **제품 식별자:** [게시 포털 -> Virtual Machines -> 사용자 제품 -> VM 이미지 탭 -> 제품 식별자]
* **SKU 식별자:** [게시 포털 -> Virtual Machines -> 제품 선택 -> SKU 탭 -> SKU 추가]
* **게시자 네임스페이스:** [게시 포털 -> Virtual Machines -> 연습 탭 -> 회사에 대한 정보 제공("2단계 회사 등록" 참조) -> 게시자 네임스페이스 -> 네임스페이스]

Virtual Machine 제품의 경우 Azure Marketplace에서 제품/SKU가 나열되면 아래에 지정된 필드를 변경할 수 없습니다.

* **제품 식별자:** [게시 포털 -> Virtual Machines -> 제품 선택 -> VM 이미지 -> 제품 식별자]
* **SKU 식별자:** [게시 포털 -> Virtual Machines -> 제품 선택 -> SKU 탭 -> SKU 추가]
* **게시자 네임스페이스:** [게시 포털 -> Virtual Machines -> 연습 탭 -> 회사에 대한 정보 제공(2단계 등록 참조) -> 게시자 네임스페이스 -> 네임스페이스]
* **포트:** [게시 포털 -> Virtual Machines -> 사용자 제품 -> VM 이미지 탭 -> 포트 열기]
* **나열된 SKU의 가격 책정 변경**
* **나열된 SKU의 청구 모델 변경**
* **나열된 SKU의 청구 지역 제거**
* **나열된 SKU의 데이터 디스크 수 변경**

## <a name="step-2-set-your-prices"></a>2단계: 가격 설정
### <a name="pricing-models"></a>가격 책정 모델
| 가격 책정 모델 | 설명 |
| --- | --- |
| 기본 |구입 시 지불하는 월간 정액 요금(예: 10달러/월) |
| 사용량 과금(즉, 사용량, 측정기) |제품의 게시자가 정한 사용당 요금을 지불합니다. 사용자 또는 기능에는 비례 배분을 위한 분수 개념이 없으므로 기능, 사용자 등을 기준으로 한 초과분을 정의할 수 없습니다. 사용 현황은 매시간 파트너에 의해 보고됩니다. 고객은 월별 요금제와 같은 선불제와 달리 월간 청구 주기로 후불로 지불합니다. |
| 평가판 |고객은 제한된 시간 동안 무료로 사용한 후 해당 기간 이후부터 정상 요금으로 지불할 수 있습니다. |
| 무료 계층 |요금제가 항상 무료입니다. |
| 요금제 마이그레이션(즉, 변환 또는 업그레이드/다운그레이드) |사용자가 현재 요금제에서 파트너가 정한 허용 가능한 다른 요금제로 이동하는 개념입니다. |

**제품 유형별 사용 가능한 가격 책정 모델**

> [!IMPORTANT]
> 제품 유형별로 사용 가능한 특정 가격 책정 모델은 다양합니다. 아래 표를 참조하세요.
> 
> 

|  | 기본만 | 사용량 과금만 | 기본 + 사용량 과금 |
| --- | --- | --- | --- |
| 가상 컴퓨터 이미지 |아니요 |예 |아니오 |
| 개발자 서비스 |예 |예 |예 |

### <a name="21-set-your-vm-prices"></a>2.1. VM 가격 설정
가상 머신의 경우 현재, 다음 **3가지 형식의 청구 모델**

* **시간별:** 고객은 VM 크기에 대해 게시자가 설정한 비율에 따라 시간당 단위로 요금이 청구됩니다. SKU의 **시간별 청구** 모델에서 총 가격은 게시자가 청구한 소프트웨어 비용에 Microsoft가 청구한 인프라 비용을 합한 값입니다. 이 총 가격은 고객이 구입을 고려할 때 시간별 및 월별 가격으로 표시됩니다(아래 스크린숏 참조). **게시자는 청구한 소프트웨어 비용의 80%를 수신합니다.** 따라서 적절한 계산 후에 SKU 가격을 설정하세요.
  
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1-01.png)
* **무료 평가판:** 시간별 모델의 또 다른 버전입니다. 여기서 고객은 VM을 배포한 후 처음 30일 동안 소프트웨어 비용을 청구하지 않습니다(무료). 30일 후에는 시간별 모델에서 게시자가 설정한 비율에 따라 시간당 단위로 요금이 청구됩니다.
* **BYOL(사용자 라이선스 필요):** 게시자는 VM에서 실행되는 소프트웨어의 라이선스를 관리합니다.

**중요:** Azure Marketplace에서 제품/SKU가 나열되면 아래에 지정된 필드를 변경할 수 없습니다.

* **나열된 SKU의 가격 책정 변경**
* **나열된 SKU의 청구 모델 변경**
* **나열된 SKU의 청구 지역 제거**
* **나열된 SKU의 데이터 디스크 수 변경**
* **제품 식별자:** [게시 포털 -> Virtual Machines -> 제품 선택 -> VM 이미지 -> 제품 식별자]
* **SKU 식별자:** [게시 포털 -> Virtual Machines -> 제품 선택 -> SKU 탭 -> SKU 추가]
* **게시자 네임스페이스:** [게시 포털 -> Virtual Machines -> 연습 탭 -> 회사에 대한 정보 제공(2단계 등록 참조) -> 게시자 네임스페이스 -> 네임스페이스]
* **포트:** [게시 포털 -> Virtual Machines -> 사용자 제품 -> VM 이미지 탭 -> 포트 열기]

### <a name="sell-to-countries-of-the-sku"></a>SKU "구매처" 국가
SKU를 사용할 수 있는 위치를 신중하게 고려해야 합니다. 일부 국가는 "Microsoft 송금"으로 분류되고, 일부 국가는 "ISV 송금"으로 분류됩니다.

* "Microsoft 송금" 국가에서 Microsoft는 고객으로부터 세금을 수집하고 정부에 세금을 납부(송금)합니다.
* "ISV 송금" 국가에서는 파트너가 고객으로부터 세금을 징수한 후 정부에 납부합니다. "ISV 송금" 국가에서 판매하기로 선택한 경우 선택한 국가에서 세금을 계산하고 지불할 수 있어야 합니다.

> [!NOTE]
> [게시 포털](https://publish.windowsazure.com)에서 가격 책정을 설정하지 않으면 해당 국가에서 SKU를 사용할 수 없습니다. 시간별 및 BYOL SKU 가격 책정을 설정하는 지침이 아래에 나와 있습니다.
> 
> 

### <a name="211-how-to-setup-hourly-pricing-model-for-a-sku"></a>2.1.1 SKU에 대한 시간별 가격 책정 모델을 설정하는 방법
아래의 단계를 수행하여 SKU에 대한 시간별 가격 책정 모델을 설정하세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SKUS** 탭을 클릭합니다.
4. SKU가 "시간당 청구 모델"로 표시되는지 확인합니다. 그렇지 않은 경우 **편집** 버튼을 클릭하여 요금 청구 모델을 되돌립니다. 창이 열립니다. '청구 및 라이선스를 Azure 외부에서 수행(사용자 라이선스 필요)' 확인란을 선택 취소하고 변경 내용을 저장합니다.
5. SKU 배포의 30일 무료 평가판을 사용하도록 설정하려는 경우 "무료 평가판이 있나요?" 질문에 대해 "1개월" 옵션을 선택합니다. 그렇지 않은 경우 "평가판 없음" 옵션을 선택합니다. 이제 아래 제공된 단계를 따릅니다.
6. 왼쪽 메뉴에서 **가격 책정** 탭을 클릭합니다.
7. 기본 지역을 선택합니다.
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_07.png)
8. 모든 코어에 대한 가격을 설정합니다. **SKU에서 지원하지 않더라도 SKU의 모든 코어에 대해 가격을 제공해야 합니다.**
   
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_08.png)
9. 다른 지역에 대한 가격을 수동으로 설정하거나 자동 가격 설정 마법사를 사용하여 기본 지역에 따라 다른 지역의 가격을 설정할 수 있습니다. 자동 가격 설정 마법사를 사용하려면 **미국의 가격에 따라 다른 마켓의 가격 자동 설정** 버튼을 클릭합니다. **참고:** 단추의 레이블은 선택한 지역에 따라 달라질 수 있습니다. 이 문서를 작성할 당시에, 미국을 선택했으므로 아래 스크린숏과 같이 "미국의 가격에 따라 다른 마켓의 가격 자동 설정"으로 버튼 레이블이 표시됩니다.
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_09.png)
10. 자동 가격 설정 마법사가 열립니다. 첫 번째 페이지에는 기본 마켓의 선택 옵션이 표시됩니다. 섹션을 만든 후 "->" 버튼을 클릭하여 다음 페이지로 이동합니다.
    
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_10.png)
11. 코어 및 계획을 선택하기 위한 옵션이 2페이지에 표시됩니다. 원하는 계획을 선택하고 "->" 버튼을 클릭합니다. **모두 설정/해제** 단추를 클릭하여 모든 **서비스 계획** 및 **미터**를 선택하거나 확인란을 수동으로 선택할 수 있습니다. **SKU에서 지원하지 않더라도 SKU의 모든 코어에 대해 가격을 제공해야 합니다.** 따라서 모든 코어 크기가 선택되어 있는지 확인합니다.
    
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_11.png)
12. 3페이지에 마켓/지역이 표시됩니다. **모두 설정/해제** 버튼을 클릭하여 모든 지역을 선택하거나 해당 지역의 확인란을 수동으로 선택합니다. "->" 버튼을 클릭하여 다음 페이지로 이동합니다. **참고:** Microsoft 세금 송금 국가에는 집 모양의 기호가 표시됩니다. 자세한 내용은 이 페이지의 SKU "구매처" 국가 섹션을 참조하세요.
    
    ![drawing](media/marketplace-publishing-push-to-staging/img2.1.1_12.png)
13. 4페이지에는 환율이 표시됩니다. 마침 버튼을 클릭하여 단계를 완료합니다.

### <a name="212-how-to-setup-byol-pricing-model-for-a-sku"></a>2.1.2 SKU에 대한 BYOL 가격 책정 모델을 설정하는 방법
아래의 단계를 수행하여 SKU에 대한 BYOL 가격 책정 모델을 설정하세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SKUS** 탭을 클릭합니다.
4. SKU가 "Bring your own license SKU"(사용자 라이선스 필요)로 표시되는지 확인합니다. 그렇지 않은 경우 편집 버튼을 클릭하여 요금 청구 모델을 되돌립니다. 창이 열립니다. '청구 및 라이선스를 Azure 외부에서 수행(사용자 라이선스 필요)' 확인란을 선택하고 변경 내용을 저장합니다.
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.2_04.png)
5. 왼쪽 메뉴에서 **가격 책정** 탭을 클릭합니다.
6. 기본 지역을 선택하고 외부 라이선스(BYOL) SKU 가용성 섹션에서 SKU에 대한 확인란을 선택하여 해당 지역에서 SKU를 사용할 수 있게 합니다(아래 스크린숏 참조).
   
   ![drawing](media/marketplace-publishing-push-to-staging/img2.1.2_06.png)
7. 수동으로 다른 지역에서 SKU를 사용할 수 있도록 하거나 이를 위해 자동 가격 설정 마법사를 사용할 수 있습니다. 이 페이지의 **"2.1.1 SKU에 대한 시간별 가격 책정 모델을 설정하는 방법"** 섹션에서 9번~13번 항목(자동 가격 설정 마법사의 사용 방법 설명)을 참조하세요.

### <a name="22-set-your-developer-service-prices"></a>2.2. 개발자 서비스 가격 설정
요금제는 기본 + 사용량 과금의 모든 조합이 가능합니다. 여기서 기본은 월별 가격이고 초과분은 사용량에 따른 가격입니다. 자세한 내용은 다음을 참조하세요.

**예:** Contoso 개발자 서비스 제공 사항

| 계획 | 가격 | 포함되는 내용 | 마이그레이션 경로 |
| --- | --- | --- | --- |
| 무료 |0달러/월 |기본 기능입니다. |다른 요금제로 마이그레이션 가능 |
| Bronze |10달러/월 |기본 기능 및 기능 X의 1,000 할당량입니다. |Bronze Plus, Silver 및 Gold Plans로 마이그레이션 가능 |
| Bronze Plus |무료 평가판 기간: 0달러/월 + 0달러/meter01 |기본 기능 및 기능 X의 10,000 할당량. 기능 X 할당량을 사용했으면 고객은 meter01을 통해 사용량에 따라 지불할 수 있습니다. |Silver Plus 및 Gold Plans로 마이그레이션 가능 |
| Bronze Plus |유료 기간(즉, 무료 평가판 만료): 10달러/월 + 0.05달러/meter01 |기본 기능 및 기능 X의 10,000 할당량. 기능 X 할당량을 사용했으면 고객은 meter01을 통해 사용량에 따라 지불할 수 있습니다. |Silver Plus 및 Gold Plans로 마이그레이션 가능 |
| Silver |0.15달러/meter01 |고객은 X 기능에 대해 meter01을 통해 사용량에 따라 지불할 수 있습니다. |Bronze 및 Gold Plans로 마이그레이션 가능 |
| Silver Plus |20달러/월 + 0.15달러/meter01 + 0.01달러/meter02 |기본 기능 및 기능 X의 10,000 할당량 및 기능 Y의 100 할당량. 기능 X 할당량을 사용했으면 고객은 meter01을 통해 사용량에 따라 지불할 수 있습니다.  기능 Y 할당량을 사용했으면 고객은 meter02를 통해 사용량에 따라 지불할 수 있습니다. |Bronze Plus 및 Gold Plans로 마이그레이션 가능 |
| Gold |1,000달러/월 |기능 X의 10,000 할당량, 기능 Y의 1,000 할당량 및 기능 Z 무제한입니다. |무료를 제외한 모든 요금제로 마이그레이션 가능 |

## <a name="step-3-provide-support-information"></a>3단계: 지원 정보 제공
연락처 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다. 지원 URL은 최종 고객에게 제공됩니다.

1. 게시 포털 왼쪽의 **지원** 머리글로 이동합니다.
2. **엔지니어링 연락처**아래에 정보를 입력합니다.
3. **고객 지원** 아래에 정보를 입력합니다. 메일 지원만 제공할 경우 더미 전화번호를 입력합니다. 그러면 제공된 메일이 대신 사용됩니다.
4. 지원 URL을 입력합니다.

## <a name="step-4-choose-azure-marketplace-categories"></a>4단계: Azure Marketplace 범주 선택
**범주** 탭에서는 선택 항목의 배열을 제공합니다. 개발자의 제품이 이러한 범주에 포함될 수 있으며, 최대 다섯 개의 범주를 선택할 수 있습니다.

## <a name="how-your-marketing-will-appear"></a>마케팅이 표시되는 방식
다음은 제품 마케팅 정보가 [Azure Marketplace 웹 사이트](https://azure.microsoft.com/marketplace/) 및 [Azure Portal](https://portal.azure.com)에서 어떻게 사용되는지를 자세히 보여 줍니다.

### <a name="azure-marketplace-website"></a>Azure Marketplace 웹 사이트
![drawing](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![drawing](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Azure Marketplace 웹 사이트에서 제품 목록*

![drawing](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Azure Marketplace 웹 사이트에서 제품 설명 정보*

![drawing](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Azure Marketplace 웹 사이트에서 제품 설명 가격 책정 정보*

### <a name="azure-portal"></a>Azure Portal
![drawing](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Azure Portal의 제품 목록*

![drawing](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Azure 포털의 제품 설명 정보*

## <a name="next-steps"></a>다음 단계
Marketplace 콘텐츠를 로드했으므로 스테이징에서 제품 테스트를 진행하겠습니다. 그러나 제품 유형별로 다양한 단계에 따라 아래 목록에서 적절한 제품 유형을 선택해야 합니다.

* [스테이징에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)
* [준비 단계에서 솔루션 템플릿 제품 테스트](marketplace-publishing-solution-template-test-in-staging.md)

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md
