---
title: 제품을 만들고 Marketplace에 배포하는 방법 개요 | Microsoft Docs
description: 승인된 Microsoft 개발자가 되고, 가상 머신 이미지, 템플릿, 데이터 서비스 또는 개발자 서비스를 만들어서 Azure Marketplace에 배포하는 데 필요한 단계를 이해합니다.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: 02df78de48c8466f3488ef5f8346e1852b4e3ded
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
> [!NOTE]
> 이 설명서는 최신 정보가 아니며 정확하지 않습니다. Azure Marketplace에 제품을 게시하는 방법에 대한 지침을 보려면 Azure Marketplace [판매자 가이드](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide)로 이동하세요.

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Azure Marketplace에 제품 게시 및 관리
다른 Azure 고객 및 파트너가 구매하여 사용하도록 개발자가 Azure Marketplace에 나열된 솔루션을 만들고, 배포하고, 관리하는 데 도움을 주기 위해 이 문서를 제공합니다.

## <a name="marketplace-publishing"></a>Marketplace 게시
Azure 게시자인 사용자는 Marketplace에서 다른 개발자, ISV 및 IT 전문가에게 혁신적인 솔루션 또는 서비스를 배포하고 판매할 수 있습니다. Marketplace를 통해 클라우드 기반 응용 프로그램 및 모바일 솔루션을 신속하게 개발하려는 고객에게 접근할 수 있습니다. 솔루션이 비즈니스 사용자를 대상으로 하는 경우 [AppSource](http://appsource.microsoft.com) Marketplace를 고려할 수 있습니다.


## <a name="supported-types-of-solutions"></a>지원되는 솔루션 형식
게시자로서 자신의 회사에서 제공할 솔루션의 종류를 정의하는 작업을 우선 수행하려고 합니다. Marketplace에서는 다음과 같은 종류의 제품을 지원합니다.

|솔루션 형식|가상 머신|솔루션 템플릿|
|---|---|---|
|**정의**|완전히 설치된 운영 체제 및 하나 이상의 응용 프로그램이 포함된 미리 구성된 이미지입니다. Virtual Machines 이미지는 Azure Virtual Machines 서비스에서 Virtual Machines를 만들고 배포하는 데 필요한 정보를 제공합니다.|하나 이상의 서로 다른 Azure 서비스를 참조할 수 있는 데이터 구조는 다른 판매자가 게시한 서비스를 포함합니다. Azure 구독자는 단일 조정된 방식으로 하나 이상의 제품을 배포하는 데 사용할 수 있습니다.|
|**예제**|Azure 게시자인 사용자는 혁신적인 데이터베이스 서비스를 사용하여 VM을 만들고 유효성을 검사했습니다. 다른 Azure 구독자는 해당 클라우드 서비스 환경에 이 VM을 확보하고 배포하려고 합니다.|Azure 게시자로 부하 분산, 강화된 보안 및 고가용성 기능을 갖춘 클라우드 서비스를 빠르게 배포할 수 있도록 하는 Azure의 서비스 집합을 포함했습니다. 다른 Azure 구독자는 해당 목표를 충족하는 솔루션 템플릿을 확보하여 시간을 절약할 수 있습니다. 동일하거나 유사한 Azure 서비스를 수동으로 찾고, 확보하고, 배포하고 구성하지 않아도 됩니다.|

> [!NOTE]
> 일부 단계는 여러 형식의 솔루션에서 공유되고 다른 단계는 각 솔루션 형식에 고유합니다. 이 문서에서는 모든 유형의 솔루션에 대해 완료해야 하는 단계에 대한 간략한 개요를 제공합니다.

## <a name="publish-a-solution"></a>솔루션 게시
![추천, 등록, 게시](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>사전 승인을 위한 솔루션 추천
가상 머신 [솔루션](https://createopportunity.azurewebsites.net)을 Marketplace에 게시하려면 Microsoft Azure Certified **솔루션 추천 양식**을 완료합니다.

>[!NOTE]
> 파트너 계정 관리자나 DX 파트너 관리자와 함께 작업하는 경우에는 Azure Certified 프로그램에 솔루션을 추천해달라고 요청하세요. 또는 [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) 웹 페이지로 이동하여 응용 프로그램 양식을 작성하면 됩니다. **Microsoft 스폰서 연락처** 상자에서 파트너 계정 관리자 또는 DX 파트너 관리자의 이메일을 입력합니다.

[Azure Marketplace 참여 정책](http://go.microsoft.com/fwlink/?LinkID=526833)의 자격 조건을 충족하고 응용 프로그램이 승인되면 Marketplace에 솔루션을 등록하기 위한 작업을 시작합니다.

### <a name="register-your-account-as-a-microsoft-seller"></a>Microsoft 판매자로 계정 등록
[Microsoft 개발자 계정](marketplace-publishing-accounts-creation-registration.md)으로 Microsoft 계정을 등록합니다.

### <a name="publish-your-solution"></a>솔루션 게시
솔루션을 Marketplace에 게시하려면 다음과 같은 단계를 따르세요.
1. 비기술 요구 사항 충족

    a. [비기술 필수 구성 요소](marketplace-publishing-pre-requisites.md) 충족

    나. [VM 기술 필수 구성 요소](marketplace-publishing-vm-image-creation-prerequisites.md) 충족

    다. [솔루션 템플릿 기술 필수 구성 요소](marketplace-publishing-solution-template-creation-prerequisites.md) 충족

2. 제품 만들기

    a. [가상 머신](marketplace-publishing-vm-image-creation.md) 제품 만들기

    나. [솔루션 템플릿](marketplace-publishing-solution-template-creation.md) 제품 만들기

3. 제품 [마케팅 콘텐츠](marketplace-publishing-push-to-staging.md) 만들기

4. 준비 단계에서 제품 테스트

    a. [준비 단계](marketplace-publishing-vm-image-test-in-staging.md)에서 VM 제품 테스트

    나. [준비 단계](marketplace-publishing-solution-template-test-in-staging.md)에서 솔루션 템플릿 제품 테스트

5. [Marketplace](marketplace-publishing-push-to-production.md)로 제품 배포


### <a name="create-and-manage-a-virtual-machine-image"></a>가상 머신 이미지 만들기 및 관리
다음과 같은 리소스를 사용하여 VM 이미지를 만들고 관리합니다.
* VM 이미지 [온-프레미스](marketplace-publishing-vm-image-creation-on-premise.md) 만들기
* [Azure Portal에서 Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 실행하는 가상 컴퓨터 만들기
* [Azure Portal에서 Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 실행하는 가상 머신 만들기
* [VHD를 만드는](marketplace-publishing-vm-image-creation-troubleshooting.md) 동안 발생하는 일반적인 문제 해결

## <a name="manage-your-solution"></a>솔루션 관리
다음 리소스에서 도움말을 사용하여 솔루션을 관리합니다.
* [가상 컴퓨터 제품에 대한 사후 생성 가이드 참고](marketplace-publishing-vm-image-post-publishing.md)
* [제품 또는 SKU의 비기술적인 세부 정보 업데이트](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [제품 또는 SKU의 기술적인 세부 정보 업데이트](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [나열된 제품에 새 SKU 추가](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [나열된 SKU에 대한 데이터 디스크 수 변경](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Marketplace에서 나열된 제품 삭제](marketplace-publishing-vm-image-post-publishing.md)
* [Marketplace에서 나열된 SKU 삭제](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Marketplace에서 나열된 현재 버전의 SKU 삭제](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [나열 가격을 프로덕션 값으로 되돌리기](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [청구 모델을 프로덕션 값으로 되돌리기](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [나열된 SKU의 표시 유형 설정을 프로덕션 값으로 되돌리기](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>추가 리소스
[Azure PowerShell 설정](marketplace-publishing-powershell-setup.md)
