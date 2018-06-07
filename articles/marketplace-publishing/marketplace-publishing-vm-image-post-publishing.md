---
title: Azure Marketplace에서 가상 머신 이미지 관리 | Microsoft Docs
description: 초기 게시 후 Azure Marketplace에서 가상 머신 이미지를 관리하는 방법에 대한 자세한 가이드입니다.
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: mbaldwin;
ms.openlocfilehash: 19f60de4a6c651885da7ff1fb7612a77f3d0c8f1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Azure Marketplace의 가상 머신 제품에 대한 프로덕션 이후 가이드
이 문서에서는 Azure Marketplace의 라이브 가상 머신 제품을 업데이트하는 방법을 설명합니다. 기존 제품에 하나 이상의 새로운 SKU를 추가하는 과정을 설명합니다. 또한 Marketplace에서 라이브 가상 머신 제품 또는 SKU를 제거하는 과정을 설명합니다.

제품/SKU가 [Azure Portal](http://portal.azure.com)에서 준비된 후에는 다음과 같은 텍스트 상자를 변경할 수 없습니다.

* **제품 식별자:** [게시 포털]에서 **가상 머신**로 이동하여 제품을 선택합니다. **VM 이미지** > **제품 식별자**를 클릭합니다.
* **SKU 식별자:** [게시 포털]에서 **가상 머신**으로 이동하여 제품을 선택합니다. **SKU** > **SKU 추가**를 클릭합니다.
* **게시자 네임스페이스:** [게시 포털]에서 **가상 머신** > **연습** > **회사에 대한 정보 제공**("2단계 회사 등록" 참조) > **게시자 네임스페이스** > **네임스페이스**로 이동합니다.

제품/SKU가 [Marketplace](http://azure.microsoft.com/marketplace)에서 나열된 후에는 다음과 같은 텍스트 상자를 변경할 수 없습니다.

* **제품 식별자:** [게시 포털]에서 **가상 머신**로 이동하여 제품을 선택합니다. **VM 이미지** > **제품 식별자**를 클릭합니다.
* **SKU 식별자:** [게시 포털]에서 **가상 머신**으로 이동하여 제품을 선택합니다. **SKU** > **SKU 추가**를 클릭합니다.
* **게시자 네임스페이스:** [게시 포털]에서 **가상 머신** > **연습 탭** > **회사에 대한 정보 제공**("2단계 등록" 참조) &amp;gt; **게시자 네임스페이스** > **네임스페이스**로 이동합니다.
* **포트:** [게시 포털]에서 **가상 머신**로 이동하여 제품을 선택합니다. **VM 이미지** > **포트 열기**를 클릭합니다.
* **나열된 SKU의 가격 책정 변경**
* **나열된 SKU의 청구 모델 변경**
* **나열된 SKU의 청구 지역 제거**
* **나열된 SKU의 데이터 디스크 수 변경**

## <a name="update-the-technical-details-of-a-sku"></a>SKU의 기술 세부 정보 업데이트
나열된 SKU에 새 버전을 추가하고 제품을 다시 게시하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **VM 이미지** 탭을 클릭합니다.
4. **SKU** 섹션에서 업데이트하려는 SKU를 찾습니다.
5. SKU의 새 버전 번호를 추가하고 **+** 버튼을 클릭합니다. 새 버전은 X.Y.Z 형식이어야 하며 여기서 X, Y, Z는 정수입니다. 버전 변경은 증분되어야만 합니다.
6. **OS VHD URL** 상자에 운영 체제 VHD에 대해 만들어진 공유 액세스 서명 URI를 입력하고 변경 내용을 저장합니다.

   > [!IMPORTANT]
   > 나열된 SKU의 데이터 디스크 수를 늘리거나 줄일 수는 없습니다. 이 경우 새로운 SKU를 만들어야 합니다. 자세한 지침은 [나열된 제품에 새 SKU 추가](#add-a-new-sku-under-a-listed-offer) 섹션을 참조하세요.
   >
   >
7. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
8. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![VM 이미지](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>제품 또는 SKU의 비기술적인 세부 정보 업데이트
Marketplace에서 라이브 제품 또는 SKU의 비기술적인(마케팅, 법률, 지원, 범주) 세부 정보를 업데이트할 수 있습니다.

### <a name="update-the-offer-description-and-logos"></a>제품 설명 및 로고 업데이트
세부 정보를 업데이트하고 제품을 다시 게시하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **마케팅** 탭을 클릭합니다.
4. **영어(미국)**을 클릭합니다.
5. **세부 정보** 탭을 클릭합니다. **설명** 섹션에서 제품 **제목**, **요약** 및 **긴 요약**을 업데이트하고 변경 내용을 저장합니다.

   > [!NOTE]
   > SKU 세부 정보를 업데이트할 때 이러한 제한 사항을 고려해야 합니다. 
   * 제품 설명 및 SKU 설명에 중복 텍스트를 입력하지 않습니다.
   * SKU 제목 및 제품 긴 요약에 중복 텍스트를 입력하지 않습니다. 
   * SKU 제목 및 제품 요약에 중복 텍스트를 입력하지 않습니다.
   >
   >

    ![세부 정보](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. **세부 정보** 탭의 **로고** 섹션에서 로고를 업데이트할 수 있습니다. 로고가 [Azure Marketplace 지침](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)에 부합하는지 확인합니다.

   > [!NOTE]
   > 대표 아이콘은 선택 사항입니다. 대표 아이콘을 업로드하지 않아도 됩니다. 그러나 대표 아이콘을 업로드하면 게시 포털에서 삭제할 프로비전이 없습니다. [대표 아이콘 지침](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)에 따릅니다.
   >
   >
7. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
8. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![로고](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>SKU 설명 업데이트
SKU 세부 정보를 업데이트하고 제품을 다시 게시하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **마케팅** 탭을 클릭합니다.
4. **영어(미국)**을 클릭합니다.
5. **계획** 탭을 클릭합니다. **SKU** 섹션에서 SKU **제목**, **요약** 및 **설명**을 업데이트하고 변경 내용을 저장합니다.

   > [!NOTE]
   > SKU 세부 정보를 업데이트할 때 이러한 제한 사항을 고려해야 합니다. 
   * 제품 설명 및 SKU 설명에 중복 텍스트를 입력하지 않습니다. 
   * SKU 제목 및 제품 긴 요약에 중복 텍스트를 입력하지 않습니다. 
   * SKU 제목 및 제품 요약에 중복 텍스트를 입력하지 않습니다.
   >
   >
6. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
7. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![요금제](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>기존 링크 변경 또는 새 링크 추가
기존 링크를 변경하거나 새 링크를 추가한 다음 제품을 다시 게시하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **마케팅** 탭을 클릭합니다.
4. **영어(미국)**을 클릭합니다.
5. **링크** 탭을 클릭합니다.
6. 새 링크를 추가하려면 **링크** 섹션에서 **+ 링크 추가**를 클릭합니다. **링크 추가** 대화 상자에서 **제목** 및 **URL** 링크를 입력하고 변경 내용을 저장합니다. 고객에게 도움이 될 수 있는 정보를 포함하는 모든 링크를 입력할 수 있습니다.
7. 기존 링크를 업데이트 또는 삭제하려면 링크를 선택하고 **편집** 단추 또는 **삭제** 단추를 클릭합니다.

   > [!NOTE]
   > 이러한 링크는 프로덕션 요청 프로세스 동안 유효성 검사를 받으므로 이 섹션에서 입력한 링크가 제대로 작동하는지 확인합니다.
   >
   >
8. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
9. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![링크](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![링크 추가](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>기존 샘플 이미지 변경 또는 새 샘플 이미지 추가
기존 샘플 이미지를 변경하거나 새 샘플 이미지를 추가한 다음 제품을 다시 게시하려면 다음 단계를 따르세요.

> [!NOTE]
> 하나의 샘플 이미지만 [Azure Portal](https://portal.azure.com)에 표시됩니다.
>
>

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **마케팅** 탭을 클릭합니다.
4. **영어(미국)**을 클릭합니다.
5. **샘플 이미지** 탭을 클릭합니다.
6. 새 샘플 이미지를 추가하려면 **샘플 이미지** 섹션에서 **새로운 이미지 업로드**를 클릭하고 변경 내용을 저장합니다.

   > [!NOTE]
   > 샘플 이미지를 포함하는 것은 선택적입니다.
   >
7. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
8. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![샘플 이미지](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>법적 콘텐츠 업데이트
법적 콘텐츠를 업데이트하고 제품을 다시 게시하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **마케팅** 탭을 클릭합니다.
4. **영어(미국)**을 클릭합니다.
5. **법적** 탭을 클릭합니다. **법적** 섹션 아래에서 정책/사용 약관을 업데이트합니다. **사용 약관** 텍스트 상자에 정책/용어를 입력하거나 붙여 넣고 변경 내용을 저장합니다.
6. 사용 약관의 문자 제한은 1백만자입니다.
7. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
8. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![법적 정보](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>지원 정보 업데이트
지원 정보를 업데이트하고 제품을 다시 게시하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **지원** 탭을 클릭합니다.
4. **엔지니어링 문의** 섹션에서 엔지니어링 연락처 세부 정보를 업데이트합니다. 세부 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다.
5. **고객 지원** 섹션에서 지원 연락처 세부 정보 및 **지원 URL**을 업데이트합니다. 세부 정보는 파트너와 Microsoft 간의 내부 통신에만 사용됩니다.

   > [!NOTE]
   > 이메일 지원만 제공하려는 경우 **고객 지원** 섹션에서 더미 전화번호를 입력합니다. 이 경우에 사용자가 제공한 이메일을 대신 사용합니다.
   >
   >
6. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
7. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![지원](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>범주 업데이트
제품에 대한 범주 섹션을 업데이트하고 제품을 다시 게시하려면 다음과 같은 단계에 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **범주** 탭을 클릭합니다.
4. **범주** 섹션에서 제품에 대한 범주를 업데이트하고 변경 내용을 저장합니다. Azure Marketplace 갤러리에 대해 최대 5개의 범주를 선택할 수 있습니다.
5. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
6. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![범주](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>나열된 제품에 새 SKU 추가
라이브 제품에 새로운 SKU를 추가하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SKU** 탭을 클릭합니다. 그런 다음 **SKU 추가**를 클릭합니다. 
4. 대화 상자에서 **SKU 식별자**를 소문자로 입력합니다. BYOL 청구 모델로 새 SKU를 게시하려는 경우 **BYOL(사용자 라이선스 필요) 청구 모델** 확인란을 선택합니다. 그렇지 않으면 확인란의 선택을 취소합니다. 눈금 표시를 클릭하여 새로운 SKU를 만듭니다. BYOL 청구 모델을 선택하지 않은 경우 청구 모델은 매시간으로 자동 설정됩니다. 30일 체험 평가판을 매시간 요금 청구 모델로 사용하려면 **체험 평가판을 사용할 수 있나요?**에 **한 달**을 선택합니다. 그렇지 않은 경우 **평가판 없음**을 선택합니다. (새로운 SKU를 만드는 동안 BYOL을 선택하지 않은 경우에만 **체험 평가판을 사용할 수 있나요?**가 나타납니다.)

   > [!IMPORTANT]
   > 솔루션 템플릿을 게시하도록 승인한 경우*에만* **솔루션 템플릿을 통해 항상 구입되어야 하기 때문에 Marketplace에서 이 SKU를 숨깁니다**는 **예**로 표시되어야 합니다. 그렇지 않은 경우 이 옵션은 항상 **아니요**로 표시되어야 합니다.
   >
   >
4. 왼쪽 메뉴에서 **VM 이미지** 탭을 클릭하고 사용자가 만든 새 SKU를 찾습니다.
5. 새로운 SKU를 설정하려면 지침으로 [VM 이미지에 대한 인증 가져오기](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image)를 참조하세요.
6. 새로운 SKU에 대한 마케팅 자료를 추가하려면 [Marketplace 마케팅 콘텐츠 제공](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content)을 참조하세요.
7. 새로운 SKU에 대한 가격 책정 정보를 추가하려면 [가격 설정](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)을 참조하세요.
8. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
9. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

    ![SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![SKU 추가](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>나열된 SKU에 대한 데이터 디스크 수 변경
나열된 SKU의 데이터 디스크 수를 늘리거나 줄일 수는 없습니다. 이 경우 새로운 SKU를 만들어야 합니다. 자세한 지침은 [나열된 제품에 새 SKU 추가](#add-a-new-sku-under-a-listed-offer) 섹션을 참조하세요.

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Marketplace에서 나열된 제품 삭제
라이브 제품을 제거하는 요청이 있는 경우에 다양한 측면을 해결해야 합니다. Azure Marketplace에서 나열된 제품을 제거하기 위해 지원 팀의 지침을 가져오려면 다음 단계를 수행하세요.

1. [인시던트 만들기](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) 페이지에서 지원 티켓을 발행합니다.

2. **제품 관리**로 **문제 형식**을 선택하고 **프로덕션 환경에서 제품 및/또는 SKU 수정**으로 **범주**를 선택합니다.
3. 요청을 제출합니다.

지원 팀은 제품/SKU 삭제 프로세스를 안내합니다.

> [!NOTE]
> [초안] 상태([스테이징] 또는 [프로덕션] 상태가 아님)인 동안에는 제품을 언제든지 삭제할 수 있습니다. **기록** 탭에서 **초안 버리기**를 클릭합니다.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Marketplace에서 나열된 SKU 삭제
Marketplace에서 나열된 SKU를 삭제하려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.

2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 창에서 **SKU** 탭을 클릭합니다.
4. 삭제할 SKU를 선택하고 **삭제** 단추를 클릭합니다.
5. [게시 포털]에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.
6. Marketplace에 제품이 다시 게시되면 SKU가 Marketplace 및 Azure Portal에서 삭제됩니다.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Marketplace에서 나열된 현재 버전의 SKU 삭제
Marketplace에서 나열된 현재 버전의 SKU를 삭제하려면 다음 단계를 따르세요. 

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.

2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **VM 이미지** 탭을 클릭합니다.
4. 현재 버전을 삭제할 SKU를 선택하고 **삭제** 버튼을 클릭합니다.
5. [게시 포털]에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.
6. Marketplace에 제품이 다시 게시되면 나열된 SKU의 현재 버전이 Marketplace 및 Azure Portal에서 삭제됩니다. SKU는 이전 버전으로 롤백됩니다.

## <a name="revert-the-listing-price-to-production-values"></a>나열 가격을 프로덕션 값으로 되돌리기
나열 가격을 프로덕션 값으로 되돌리려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.
2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **가격 책정** 탭을 클릭합니다.
4. 가격 책정을 재설정할 지역을 선택합니다.

    ![가격 책정 지역](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. 시간당 청구 모델인 SKU의 경우 선택한 지역에 대한 프로덕션에 있으므로 모든 코어에 대한 가격을 재설정합니다. BYOL 청구 모델인 SKU의 경우 **외부 라이선스(BYOL) SKU 가용성Y** 섹션에서 SKU에 대한 확인란을 선택하여 해당 지역에서 SKU를 사용할 수 있게 합니다.

    ![가격 책정 모델](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. **미국의 가격에 따라 다른 마켓의 가격 자동 설정**을 클릭합니다.

   > [!NOTE]
   > 버튼의 레이블은 선택한 지역에 따라 달라질 수 있습니다. 미국을 선택했기 때문에 단추는 **미국의 가격에 따라 다른 마켓의 가격 자동 설정**으로 표시됩니다.
   >
   >

    ![가격 자동 설정](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. [가격 자동 설정] 마법사의 1페이지에서 기본 시장을 선택하고 **화살표** 단추를 클릭합니다.

    ![기본 시장](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. 2페이지에서 서비스 계획 및 미터(코어)를 선택하고 **화살표** 단추를 클릭합니다.

    ![서비스 계획 및 미터](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. 3페이지에서 **모두 토글**을 클릭하여 모든 지역을 선택합니다. 또는 특정 지역에 대한 확인란을 수동으로 선택할 수 있습니다. **화살표** 단추를 클릭합니다.

    ![시장 선택](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. 4페이지에서 환율을 검토하고 **마침**을 클릭합니다. 마법사는 선택 항목에 따라 가격 책정을 재설정합니다.

11. **가격 책정** 탭에서 **변경 내용 및 요약 보기**를 클릭합니다.
    **버전 보기**에서 **초안**을 선택하고 **비교 대상**에서 **프로덕션**을 선택합니다. 가격 책정에 차이점이 없으면 가격이 프로덕션 값으로 되돌려집니다.

    ![요약 및 변경 내용 보기](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
13. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

## <a name="revert-the-billing-model-to-production-values"></a>청구 모델을 프로덕션 값으로 되돌리기
청구 모델을 프로덕션 값으로 되돌리려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.

2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SKU** 탭을 클릭합니다.
4. **편집** 버튼을 클릭하여 요금 청구 모델을 되돌립니다. 열린 창에서 **청구 및 라이선스를 Azure 외부에서 수행(사용자 라이선스 필요)** 확인란을 선택하거나 선택을 취소합니다.

    ![청구 편집](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. 이 문서에서 "나열 가격을 프로덕션 값으로 되돌리기"의 단계를 따르세요.
6. **게시** 탭으로 이동하고 **스테이징으로 푸시**를 클릭합니다. 스테이징 환경에서 제품을 테스트하는 방법에 대한 자세한 지침은 [Marketplace에서 VM 제품 테스트](marketplace-publishing-vm-image-test-in-staging.md)를 참조하세요.
7. 준비 단계에서 제품을 테스트한 후에 게시 포털에서 **게시** 탭으로 이동합니다. **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>나열된 SKU의 표시 유형 설정을 프로덕션 값으로 되돌리기
나열된 SKU의 표시 유형 설정을 프로덕션 값으로 되돌리려면 다음 단계를 따르세요.

1. [게시 포털](https://publish.windowsazure.com)에 로그인합니다.

2. **가상 머신** 탭으로 이동하고 제품을 선택합니다.
3. 왼쪽 메뉴에서 **SKU** 탭을 클릭합니다.
4. SKU를 선택하고 SKU의 표시 여부 설정을 프로덕션 값으로 되돌립니다.

    ![표시 유형](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. 변경 내용을 수행한 후에 **프로덕션에 푸시하도록 요청 승인**을 클릭하여 Marketplace에서 제품을 다시 게시합니다.

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품 게시](marketplace-publishing-getting-started.md)
* [지급 보고 이해](marketplace-publishing-report-payout.md)
* [클라우드 솔루션 공급자 재판매인 인센티브 변경](marketplace-publishing-csp-incentive.md)
* [Marketplace에서 일반적인 게시 문제 해결](marketplace-publishing-support-common-issues.md)
* [게시자로 지원 받기](marketplace-publishing-get-publisher-support.md)
* [온-프레미스 VM 이미지 만들기](marketplace-publishing-vm-image-creation-on-premise.md)
* [Azure Preview 포털에서 Windows를 실행하는 가상 머신 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
