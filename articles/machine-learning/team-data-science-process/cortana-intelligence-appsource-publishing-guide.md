---
title: Cortana Intelligence AppSource 게시 가이드 | Microsoft Docs
description: 여기서는 Microsoft 파트너가 Cortana Intelligence 솔루션을 AppSource에 게시하기 위해 수행해야 하는 모든 단계를 설명합니다.
services: machine-learning
documentationcenter: ''
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: anupams
ms.openlocfilehash: 3817d58cd61fb349d7815984420d0deb1ae0edd9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="cortana-intelligence-appsource-publishing-guide"></a>Cortana Intelligence AppSource 게시 가이드

## <a name="overview"></a>개요
AppSource는 BDM(비즈니스 의사 결정자)이 파트너가 빌드하고 Microsoft에서 평가한 비즈니스 솔루션/앱을 검색하고 매끄럽게 시도하기 위한 단일 대상입니다. AppSource의 작동 방식을 알아보려면 [이 동영상](https://youtu.be/hpq_Y9LuIB8)을 시청하세요. 

다음과 같은 경우 Microsoft 파트너는 AppSource에 게시하여 실제로 혜택을 얻을 수 있습니다.
- [Cortana Intelligence Suite](https://azure.microsoft.com/suites/cortana-intelligence-suite/?cdn=disable)를 사용하여 지능형 솔루션/앱을 빌드한 경우
- 솔루션 또는 앱이 특정 비즈니스 문제를 다루는 경우
- 고객이 예측 가능한 방식으로 비교적 신속하게 다시 사용할 수 있는 모듈 또는 지적 재산을 빌드한 경우

AppSource에 이미 게시된 [Cortana Intelligence 솔루션](https://appsource.microsoft.com/en-us/marketplace/apps?product=cortana-intelligence&page=1)을 살펴보세요. 

이 문서에서는 파트너의 Cortana Intelligence 솔루션이 AppSource에 게시되도록 하는 단계를 안내합니다.

## <a name="getting-started"></a>시작
1. [Partner Community Benefits Guide](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Partner%20Community%20Benefits%20Guide%20-%20Cloud%20and%20Enterprise.pdf)(파트너 커뮤니티 혜택 가이드)(PDF)에서 9페이지를 참조하여 Advanced Analytics 파트너로 등록합니다.
1. [앱 제출](https://appsource.microsoft.com/en-us/partners/list-an-app) 양식을 작성합니다.

    *“앱의 기반 제품 선택*” 질문에서 **기타** 확인란을 선택하고 편집 컨트롤에 “Cortana Intelligence”를 나열합니다.
1. Cortana Intelligence 앱이 AppSource에 등록되려면 먼저 Cortana Intelligence의 파트너 솔루션 기술 팀에서 인증을 받아야 합니다. 프로세스를 시작하려면 [AppSource 게시를 위한 Cortana Intelligence 솔루션 평가](https://aka.ms/cisappsrceval)에서 설문 조사 양식을 작성하여 앱에 대한 세부 정보를 알려주세요. 이 사이트는 암호로 보호되며, 사이트에 암호를 받는 방법에 대한 지침이 나와 있습니다.

## <a name="solution-evaluation-criteria"></a>솔루션 평가 기준
다음은 앱이 충족해야 하는 기준 목록입니다.
1. 앱은 짧은 기간 내에 구현할 수 있는 미리 정의된 가치 제안을 위한 최소한의 구성으로, 지정된 기능 영역 내의 특정 비즈니스 사용 사례 문제를 반복 가능한 방식으로 해결해야 합니다.
1. 솔루션은 다음 구성 요소 중 하나 이상을 사용해야 합니다.

    - HDInsight
    - Machine Learning
    - Data Lake Analytics
    - Stream Analytics
    - Cognitive Services
    - Bot Framework
    - Analysis Services
    - Microsoft R Server 독립 실행형
    - SQL 2016 또는 HDInsight Premium의 R Services
1. 솔루션은 DPOR/CSP를 사용하는 고객당 매달 $1000 이상을 창출해야 합니다.
1. 솔루션은 사용자 인증 및 리소스 액세스 제어에 대해 동의를 활성화하고 AAD 페더레이션된 SSO(Azure Active Directory 페더레이션된 Single Sign-On)을 사용해야 합니다. 솔루션이 AAD 페더레이션된 SSO를 사용할 수 있음을 평가 팀에 입증해야 AppSource에 앱을 등록할 수 있습니다.

     AAD 페더레이션된 SSO를 사용할 수 있다는 것의 의미를 확인하려면 [AppSource 평가판 체험 안내](https://aka.ms/trialexperienceforwebapps) 동영상에서 02:35 위치를 검색하세요. 앱에서 AAD 페더레이션된 SSO를 아직 사용할 수 없는 경우 관련된 몇 가지 설명서는 다음과 같습니다.
   1. [한 번 클릭 로그인](https://identity.microsoft.com/Landing?ru=https://identity.microsoft.com/)
   1. [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application)
     
1. 솔루션에 Power BI 사용: 선택 사항이지만 잠재 고객을 더 많이 발생시키는 것으로 입증되었으므로 권장됩니다.

## <a name="devcenter-account-setup"></a>DevCenter 계정 설정
Microsoft의 게시자가 되기 위해 회사를 등록하는 프로세스입니다. 기존 DevCenter 계정으로 등록된 게시자인 경우 DevCenter 계정과 연결된 메일 ID를 알려주세요. 응용 프로그램이 게시용으로 승인되면 [클라우드 포털 관리 게시자 프로필](https://cloudpartner.azure.com/#documentation/manage-publisher-profile)에 대한 전체 설명서에 액세스할 수 있습니다.

DevCenter 계정이 없는 경우 DevCenter 계정을 설정하는 주요 단계는 다음과 같습니다.
1. [여기](https://signup.live.com/signup.aspx)서 Microsoft 계정을 만듭니다.
1. Microsoft DevCenter [등록 페이지](http://go.microsoft.com/fwlink/?LinkId=615100)로 이동한 다음 "등록"을 클릭합니다.
1. 결제를 요청하는 메시지가 표시되면 제공된 코드를 사용합니다. 코드가 없는 경우 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20for%20promotion-code%20for%20DevCenter%20account%20setup)에 문의하세요.
1. 거주하거나 비즈니스가 있는 [국가/지역](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)을 선택합니다. **이 정보는 나중에 변경할 수 없습니다.**
1. [개발자 계정 유형](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees)을 선택합니다. AppSource의 경우 **회사**를 선택합니다. 회사 계정의 경우 이 [지침](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account)을 검토해야 합니다.
1. 개발자 계정에 사용하려는 연락처 정보를 입력합니다.
DevCenter 계정을 설정하는 방법에 대한 단계별 지침은 [여기](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-accounts-creation-registration)에 제공된 지침을 참조하세요.

## <a name="provide-info-for-microsoft-sellers"></a>Microsoft 판매자에 대한 정보 제공
파트너에 대한 AppSource의 핵심 가치 제안 중 하나는 잠재 고객 앞에 파트너 앱을 배치할 때 Microsoft 판매자와 협력할 수 있다는 것입니다.

[Microsoft 판매자용 파트너 솔루션 정보](https://aka.ms/aapartnerappinfo)를 작성하여 [appsourcecissupport@microsoft.com](mailto:appsourcecissupport@microsoft.com?subject=Request%20publisher%20account%20creation%20for%20%3cPartner%20Name%3e%20and%20whitelist%20owner/contributer%20AAD/MSA%20email%20IDs)으로 보내주세요. 이는 Cortana Intelligence 앱이 게시에 대한 승인을 받기 위한 필수 단계입니다.

## <a name="build-a-compelling-customer-walkthrough-on-appsource"></a>AppSource에서 매력적인 고객 연습 작성
먼저 AppSource에서 [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)(Neal Analytics 인벤토리 최적화)를 살펴보세요. AppSource의 모든 앱 항목에는 평가판 체험 진입점 외에도 제목, 요약(최대 100자), 설명(최대 1300자), 이미지, 동영상(선택 사항), pdf 문서가 있습니다. 파트너는 이러한 모든 정보를 활용하여 매력적인 고객 경험을 구축해야 합니다.

파트너는 AppSource에 배치한 콘텐츠를 종단 간 영업 오케스트레이션으로 간주해야 합니다. 고객은 제목과 설명을 통해 가치 제안을 파악한 다음 이미지 및 동영상을 통해 솔루션의 용도를 이해합니다. 잠재 고객은 사례 연구를 통해 기존 고객이 어떻게 가치를 얻는지를 확인할 수 있습니다. 

이러한 모든 정보는 고객이 흥미를 갖고 더 많이 알고 싶도록 만들어야 합니다. 이를 뛰어난 기술 영업 담당자가 새로운 고객에게 안내하는 피치 데크 기반 프레젠테이션으로 간주하면 됩니다. 제안된 설명 형식은 텍스트를 가치 제안에 따라 하위 섹션으로 나누고, 각 하위 섹션을 부제로 강조 표시하는 것입니다. 

일반적으로 방문자는 "제품 요약" 필드와 부제를 통해 앱이 다루는 내용과 앱을 고려해야 하는 이유를 한눈에 살펴봅니다. 따라서 사용자의 주의를 끌고 구체적인 정보를 계속 살펴볼 이유를 제공하는 것이 중요합니다.

다음 파트너의 성과를 참조하세요.
- [Neal Analytics Inventory Optimization](https://appsource.microsoft.com/en-us/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)(Neal Analytics Inventory 최적화)
- [Plexure Retail Personalization](https://appsource.microsoft.com/en-us/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)(Plexure Retail 개인 설정)
- [AvePoint Citizen Services](https://appsource.microsoft.com/en-us/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)(AvePoint 대국민 서비스)

영업의 최종 작업은 가치 제안이 어떻게 전달되는지를 보여 주는 앱/솔루션 데모를 제공하는 것입니다. 이것이 AppSource의 고객 평가판 체험에 바라는 것입니다. 좋은 데모는 다음을 수행합니다.
- 앱의 가치 제안을 간략하게 다시 요약하고 솔루션을 조작하는 고객 회사 내의 가상 사용자 열거
- 스토리를 말하고 특정 문제를 다루는 샘플 고객에 대한 컨텍스트 설정
- 일반적으로 상황이 어떻게 진행되고 고객에게 영향을 미치는지를 솔루션을 사용할 경우의 상황과 비교해서 설명합니다. 이 정보는 PowerBI 대시보드 등을 사용하여 표시할 수 있습니다.
- 특정 Machine Learning 알고리즘 등을 사용하여 솔루션을 통해 어떻게 발생하는지를 요약합니다.

AppSource에서 추가하는 콘텐츠는 고품질이어야 하며 다음이 가능하도록 구성되어야 합니다.
- 파트너의 기술 영업 담당자는 이 콘텐츠를 영업 오케스트레이션에 사용해야 합니다. 영업 팀이 이 콘텐츠를 사용하는 것은 MS 영업 담당자도 동일한 작업을 수행할 수 있음을 예상할 수 있는 좋은 징후입니다. 이렇게 하면 고객 연락 지점이 동일한 스토리를 팀 동료와 상사에게 일관되게 릴레이하여 구매 거래가 완료되기 전에 예산과 승인을 받을 수 있습니다.
- 사이트를 방문하는 고객은 유기적으로 모든 콘텐츠를 스스로 탐색하고, 파트너 통신에 응답하여 다음 단계로 넘어갈 수 있습니다.

파트너가 AppSource에 배치한 콘텐츠를 종단 간 영업 오케스트레이션으로 간주해야 하는 것은 바로 이런 이유 때문입니다. 평가판 체험에 표시되는 스토리 라인 및 기능에 따라 제품 유형이 결정될 수 있습니다.

## <a name="publish-your-app-on-the-publishing-portal"></a>게시 포털에 앱 게시
응용 프로그램에 대해 위 단계의 평가를 완료했으므로, 게시 포털에 액세스할 수 있으며 [클라우드 파트너 포털을 통해 Cortana Intelligence 제품을 게시하는 방법](https://cloudpartner.azure.com/#documentation/cloud-partner-portal-publish-cortana-intelligence-app)에서 다음 단계에 대한 자세한 지침을 확인할 수 있습니다.

필드에 대한 정보가 필요하면 <appsourcecissupport@microsoft.com>으로 이메일을 보내주세요.
## <a name="my-app-is-published-on-appsource---now-what"></a>내 앱이 AppSource에 게시되었습니다. 이제 어떻게 해야 하나요?
먼저 앱이 게시된 것을 축하 드립니다.
AppSource에 앱을 게시하여 얻게 되는 수익 수준은 대상 사용자에게 미치는 영향에 따라 달라집니다. 수익을 극대화하는 방법에 대한 자세한 내용은 [AppSource에서 Cortana Intelligence 앱 해킹 증가](http://aka.ms/aagrowthhackguide)를 참조하세요.

의문 사항이나 제안 내용이 있으면 <appsourcecissupport@microsoft.com>으로 연락하시기 바랍니다.

