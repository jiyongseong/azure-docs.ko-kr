---
title: Microsoft Power BI Embedded 시작 | Microsoft Docs
description: Power BI Embedded를 비즈니스 인텔리전스 응용 프로그램에 추가
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 01/11/2018
ms.author: maghan
ms.openlocfilehash: 692edc9a685ce7d1906f13f08c763909a2513b03
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Microsoft Power BI Embedded 시작

**Power BI Embedded**는 ISV(독립 소프트웨어 공급 업체) 및 개발자가 용량 기반 시간당 요금 모델을 통해 뛰어난 시각적 개체, 보고서 및 대시보드를 응용 프로그램에 신속하게 추가하는 방법을 제공합니다.

![포함 흐름의 다이어그램](media/get-started/introduction.png)

Power BI Embedded는 ISV, 개발자 및 고객을 위한 이점을 제공합니다. 예를 들어 ISV는 Power BI Desktop을 무료로 이용하여 시각적 개체를 만들 수 있습니다. ISV는 시각적 분석 개발 작업을 최소화하여 더 빠르게 시장에 대응하고, 차별화된 데이터 환경에서 경쟁사보다 두각을 나타낼 수 있습니다. 또한 ISV는 포함된 분석을 사용하여 만든 추가 값에 대한 프리미엄 요금을 청구하도록 선택할 수 있습니다.

개발자는 시각적 개체 및 분석 개발에 시간을 쓰는 것이 아니라 응용 프로그램의 핵심 역량을 구축하는 데 집중하여 시간을 할애할 수 있습니다. 개발자는 고객 보고서 및 대시보드 요구를 신속하게 충족하고, 완전히 문서화된 API 및 SDK로 쉽게 포함할 수 있습니다. 마지막으로, ISV는 앱에서 탐색하기 쉬운 데이터 탐색을 사용할 수 있도록 함으로써 고객이 모든 장치에서 확신을 가지고 빠르게 컨텍스트에서 데이터 기반 결정을 내릴 수 있도록 합니다.

## <a name="register-an-application-within-azure-active-directory"></a>Azure Active Directory 내 응용 프로그램 등록

AAD(Azure Active Directory) 내 등록된 응용 프로그램은 사용자 지정 응용 프로그램에 포함하기 위해 필요합니다. 등록된 응용 프로그램은 테넌트가 Power BI 테넌트여야 합니다. Power BI 테넌트는 조직에서 최소 한 명의 사용자가 Power BI에 등록했음을 의미합니다. Power BI에 등록한 사용자가 있으면 Power BI API가 등록된 응용 프로그램 내에 표시됩니다.

AAD에서 응용 프로그램을 등록하는 방법에 대한 자세한 내용은 [Power BI 콘텐츠를 포함하기 위한 Azure AD 앱 등록](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)을 참조하세요.

## <a name="embed-content-in-your-application"></a>응용 프로그램에 콘텐츠 포함

AAD에 응용 프로그램을 등록한 후에 응용 프로그램 내에서 Power BI 콘텐츠를 포함합니다. JavaScript API와 함께 REST API를 사용하여 콘텐츠를 포함합니다.

시작하는 데 유용한 샘플이 준비되어 있습니다. 샘플의 연습은 [응용 프로그램에 대시보드, 타일 또는 보고서 통합](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)을 참조하세요.

## <a name="get-capacity-and-move-to-production"></a>용량 얻기 및 프로덕션으로 이동

Microsoft Azure 내에서 Power BI Embedded 용량을 만들어 프로덕션으로 응용 프로그램을 이동합니다. 용량을 만드는 방법에 대한 자세한 내용은 [Azure Portal에서 Power BI Embedded 용량 만들기](create-capacity.md)를 참조하세요.

> [!IMPORTANT]
> embed 토큰은 개발 테스트용으로만 사용되므로 Power BI 마스터 계정에서 생성할 수 있는 embed 토큰의 수는 제한됩니다. 프로덕션 포함 시나리오의 경우 [용량을 구매해야 합니다](https://docs.microsoft.com/power-bi/developer/embedded-faq#technical). 용량 구매 시 embed 토큰 생성은 제한되지 않습니다. [사용 가능한 기능 가져오기](https://msdn.microsoft.com/en-us/library/mt846473.aspx)로 이동하여 현재 포함된 사용량을 백분율로 확인합니다.

Power BI 관리 포털 내에서 용량을 관리합니다. 앱 작업 영역을 지원하도록 작업 영역 할당자를 할당합니다. 자세한 내용은 [Power BI Premium 및 Power BI Embedded 내 용량 관리](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Power BI Embedded 용량을 만들 준비가 된 경우 [Azure Portal에서 Power BI Embedded 용량 만들기](create-capacity.md)를 참조하세요.

샘플 연습을 찾는 경우 [응용 프로그램에 대시보드, 타일 또는 보고서 통합](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)을 참조하세요.

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](http://community.powerbi.com/)
