---
title: Marketplace용 솔루션 템플릿 만들기 가이드 | Microsoft Docs
description: Azure Marketplace에서 구입하기 위한 여러 VM 이미지 솔루션 템플릿을 만들고 인증하고 배포하는 방법에 대한 자세한 지침입니다.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 83e69701aa5e8c11354e7551d66f3fdd18289000
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Azure Marketplace용 솔루션 템플릿 만들기 가이드
1단계 [계정 만들기 및 등록][link-acct-creation]을 완료한 후 [솔루션 템플릿을 만들기 위한 기술 필수 조건](marketplace-publishing-solution-template-creation-prerequisites.md)에서 Azure 호환 솔루션 템플릿 만들기에 대해 안내했습니다. 이제 Azure Marketplace의 [게시 포털][link-pubportal]에서 여러 VM에 대한 솔루션 템플릿을 만드는 단계를 안내합니다.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>게시 포털에서 솔루션 템플릿 제품 만들기
[https://publish.windowsazure.com](http://publish.windowsazure.com)으로 이동합니다. 처음으로 [게시 포털](https://publish.windowsazure.com/)에 로그인할 때는 회사의 판매자 프로필이 등록된 계정을 동일하게 사용하세요. 나중에 게시 포털에서 회사의 직원을 공동 관리자로 추가할 수 있습니다.

### <a name="1-select-solution-templates"></a>1. "솔루션 템플릿" 선택
  ![drawing][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. 새 솔루션 템플릿 만들기
  ![drawing][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. 토폴로지로 시작
솔루션 템플릿은 해당하는 모든 토폴로지의 "부모"입니다. 하나의 제품/솔루션 템플릿에서 여러 토폴로지를 정의할 수 있습니다. 제품이 스테이징으로 푸시될 때 해당 토폴로지도 모두 함께 푸시됩니다. 아래 단계를 따라 제품을 정의합니다.     

* 토폴로지 만들기: "토폴로지 식별자"는 일반적으로 솔루션 템플릿의 토폴로지 이름입니다. 토폴로지 식별자는 아래와 같은 URL에서 사용됩니다.

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure Portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* 새 버전을 추가합니다.

### <a name="4-get-your-topology-versions-certified"></a>4. 토폴로지 버전 인증받기
해당하는 특정 토폴로지 버전을 프로비전하는 데 필요한 모든 파일이 포함된 zip 파일을 업로드합니다. 이 zip 파일에는 다음이 포함되어 있어야 합니다.

* 루트 디렉터리의 *mainTemplate.json* 및 *createUiDefinition.json* 파일
* 연결된 모든 템플릿 및 모든 필수 스크립트

  > [!TIP]
  > 개발자가 솔루션 템플릿 토폴로지를 만들고 인증을 받는 동안 회사의 비즈니스, 마케팅 및/또는 법무 부서는 마케팅 및 법률 콘텐츠를 작업할 수 있습니다.
  >
  >

## <a name="next-steps"></a>다음 단계
지금까지 솔루션 템플릿을 만들고 zip 파일을 업로드했으므로 제품을 스테이징으로 푸시하기 전에 [Marketplace 마케팅 콘텐츠 가이드](marketplace-publishing-push-to-staging.md) 의 지침을 따르세요. [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)을 참조하여 전체 마켓플레이스 게시 문서를 확인할 수 있습니다.

다음 관련 문서를 참조할 수도 있습니다.

* VM 이미지: [Azure의 Virtual Machine 이미지 정보](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* VM 확장: [VM 에이전트 및 VM 확장 개요](https://msdn.microsoft.com/library/azure/dn832621.aspx) 및 [Azure VM 확장 및 기능](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md) 및 [간단한 템플릿 예제](https://github.com/rjmax/ArmExamples)
* Storage 계정 제한: [Storage 계정 제한을 모니터링하는 방법](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) 및 [Premium storage](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
