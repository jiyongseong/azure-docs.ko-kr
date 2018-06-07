---
title: Azure Marketplace용 제품을 만들기 위한 비기술적인 필수 조건 | Microsoft Docs
description: 제품을 만들고 다른 사람이 구입할 수 있도록 Azure Marketplace에 배포하기 위한 요구 사항을 이해합니다.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: mbaldwin
ms.openlocfilehash: 5c30e62bf345843fe83b3f17b728e1a937d19ce3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Azure Marketplace용 제품을 만들기 위한 일반적인 필수 조건
제품 만들기 프로세스를 진행하는 데 필요한 일반적인 비즈니스 프로세스 중심 필수 조건을 이해합니다.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Microsoft에 판매자로 등록되어 있는지 확인
Microsoft에 판매자 계정을 등록하는 방법에 대한 자세한 지침은 [계정 만들기 및 등록](marketplace-publishing-accounts-creation-registration.md)을 참조하세요.

* **회사가 이미 개발자 센터에 판매자도 등록되어 있으며 새 제품을 만들려는 경우** 개발자 센터 등록을 수행할 때 사용한 동일한 전자 메일 ID를 사용하여 게시 포털에 로그인합니다. 개발자 센터 및 게시 포털이 상호 연결될 수 있도록 하기 위해 이 단계가 필요합니다.
* **회사가 이미 개발자 센터에 판매자로 등록되어 있으며 기존 제품을 편집하려는 경우** 관리자 계정 또는 게시 포털에서 공동 관리자로 추가한 계정을 사용하여 게시 포털에 로그인합니다. 공동 관리자 계정을 추가하는 단계는 아래에 나와 있습니다.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>게시 포털에 공동 관리자를 추가하는 단계
게시 포털의 관리자는 응용 프로그램에 대해 작업 중인 회사의 다른 멤버를 게시 포털에 공동 관리자로 추가할 수 있습니다. **관리자인 경우** 아래에서 공동 관리자 추가 단계를 참조하세요.

> [!NOTE]
> 새 사용자인 경우 게시에 포털에 공동 관리자를 추가하기 전에 게시 포털에서 하나 이상의 응용 프로그램을 만들었는지 확인합니다. **게시자** 탭은 게시 포털에서 하나 이상의 응용 프로그램을 만든 이후에만 나타나기 때문입니다.
> 
> 

1. 공동 관리자 전자 메일 ID가 MSA(Microsoft 계정)인지 확인합니다. 그렇지 않은 경우 이 [링크](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)를 사용하여 MSA로 등록합니다.
2. 공동 관리자를 추가하려고 하기 전에 관리자 계정 아래에 있는지 하나 이상의 응용 프로그램이 있는지 확인합니다.
3. 위 단계를 수행했으면 공동 관리자 전자 메일 ID를 사용하여 게시 포털에 로그인한 후 다시 로그아웃합니다.
4. 이제 관리자 전자 메일 ID를 사용하여 게시 포털에 로그인합니다.
5. 게시자->계정 선택->관리자->공동 관리자 추가로 이동합니다(아래 스크린샷 참조).
   
    ![drawing](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. 다양한 게시 프로세스 단계(예: 개발자 센터, 게시 포털)에 제공되는 전자 메일 ID는 Microsoft의 모든 통신에 대해 모니터링됩니다.
7. 개발자 센터 등록을 위해서는 단일 사용자와 연결된 계정을 사용하지 않도록 합니다. 한 개인의 종속성을 제거하기 위해 이러한 방식이 제안됩니다.
8. 개발자 센터 등록과 관련된 문제가 발생하면 이 [링크](https://developer.microsoft.com/en-us/windows/support)를 사용하여 티켓을 제기하세요.

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>게시 포털에서 공동 관리자를 삭제하는 단계
**관리자인 경우** 아래에서 공동 관리자 삭제 단계를 참조하세요.

1. 관리자 전자 메일 ID를 사용하여 게시 포털에 로그인합니다.
2. **게시자**로 이동하여 계정을 선택하고 **관리자** -> **공동 관리자**로 이동합니다.
3. 삭제하려는 공동 관리자 옆의 **X** 단추를 클릭합니다(아래에서 스크린샷 제공).
   
    ![drawing](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> 무료(또는 사용자 라이선스 필요) 제품만 게시하려는 경우에는 회사 세금 및 은행 정보를 작성하지 않아도 됩니다.
> 
> 판매자로 활동하려면 회사 등록을 완료해야 합니다. 하지만, 회사가 Microsoft 개발자 계정의 세금 및 은행 정보를 준비하는 동안 개발자는 [게시 포털](https://publish.windowsazure.com)에서 가상 컴퓨터 이미지를 만들어서 인증을 받고 Azure 스테이징 환경에서 테스트할 수 있습니다. Azure Marketplace에 제품을 게시하는 마지막 단계에서만 판매자 계정 승인을 완료하면 됩니다.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Azure "종량제" 구독 구입
이 구독은 VM 이미지를 만들고 [Azure Marketplace](https://azure.microsoft.com/marketplace/)에 이미지를 넘겨주는 데 사용됩니다. 기존 구독이 없는 경우 https://account.windowsazure.com/signup?offer=ms-azr-0003p에서 등록하세요.

## <a name="sell-from-countries"></a>"판매" 국가
> [!WARNING]
> Microsoft Azure Marketplace에서 서비스를 판매하려면, 승인된 “판매" 국가 중 한 곳에 사용자의 등록 법인이 속해 있는지 확인해야 합니다. 이 제한은 지급액 및 세금 때문에 있습니다. Microsoft는 가까운 장래에 이 국가 목록을 확장하기 위해 적극 노력하고 있으니 기대해 주세요. 전체 목록은 [Azure Marketplace 참가 정책](http://go.microsoft.com/fwlink/?LinkID=526833)의 섹션 1b를 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
비기술 필수 구성 요소가 충족되면 제품별 기술 구성 요소를 충족해야 합니다. Azure Marketplace용으로 만들 제품 유형에 해당하는 문서로 연결되는 링크를 클릭하세요.

* [VM 기술 필수 구성 요소](marketplace-publishing-vm-image-creation-prerequisites.md)
* [솔루션 템플릿 기술 필수 구성 요소](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>참고 항목
* [시작: Azure Marketplace에 제품을 게시하는 방법](marketplace-publishing-getting-started.md)

