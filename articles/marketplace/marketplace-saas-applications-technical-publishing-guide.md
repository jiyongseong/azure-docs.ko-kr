---
title: Azure Marketplace SaaS 응용 프로그램 기술 게시 가이드
description: Azure Marketplace에 SaaS 응용 프로그램을 게시하는 단계별 가이드 및 게시 검사 목록
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: BrentL-Collabera
manager: ''
editor: BrentL-Collabera
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 05/09/2018
ms.author: pabutler
ms.openlocfilehash: 48b0b4177dad6262105bf30be2b8714f6ea1228f
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076714"
---
# <a name="saas-applications-technical-publishing-guide"></a>SaaS 응용 프로그램 기술 게시 가이드

Azure Marketplace SaaS 응용 프로그램 기술 게시 가이드를 시작합니다. 이 가이드는 향후 게시자 및 기존 게시자가 SaaS 응용 프로그램 제품을 사용하여 Azure Marketplace에 응용 프로그램과 서비스를 나열하는 데 도움을 주기 위해 작성되었습니다. 

SaaS 제품을 게시하는 방법을 더 잘 이해하기 위해 이 가이드는 다음 섹션으로 구분됩니다.
* 제품 개요
* 비즈니스 요구 사항
* 기술적인 요구 사항
* 게시 프로세스
* Azure Active Directory를 사용하여 체험 지원
* Marketplace에 대한 Azure AD 통합 인증

## <a name="offer-overview"></a>제품 개요  

SaaS 응용 프로그램은 모두 Azure 상점에서 사용할 수 있습니다. 다음 표에서는 현재 사용할 수 있는 옵션을 설명합니다.

| 상점 옵션 | 나열 | 평가판/거래 |  
| --- | --- | --- |  
| AppSource | 예(연락처) | 예(PowerBI/Dynamics) |
| Azure 마켓플레이스 | 아니오 | 예(SaaS 앱) |   

**목록:** 나열 게시 옵션은 연락처 제품 형식으로 이루어지고 평가판 또는 거래 수준의 참여가 불가능할 때 사용됩니다. 이 방식은 마켓 내 솔루션의 게시자가 비즈니스 수익을 창출하는 거래를 할 수 있는 잠재 고객을 바로 받을 수 있다는 장점이 있습니다.  
**평가판/거래:** 고객은 솔루션에 대한 평가판을 직접 구입하거나 요청할 수 있습니다. 평가판 환경을 제공하면 고객에게 제공되는 참여 수준이 높아지고 고객이 솔루션을 구입하기 전에 탐색할 수 있습니다. 평가판 환경을 사용할 경우 상점에서 홍보할 기회를 갖고 고객 참여를 통해 보다 풍부하고 많은 잠재 고객을 창출할 수 있습니다. 체험의 경우 적어도 체험 기간 동안 무료 지원이 포함되어야 합니다.  

| SaaS 앱 제품 | 비즈니스 요구 사항 | 기술적인 요구 사항 |  
| --- | --- | --- |  
| **문의처** | 예 | 아니오 |  
| **PowerBI/Dynamics** | 예 | 예(Azure AD 통합) |  
| **SaaS 앱**| 예 | 예(Azure AD 통합) |     

Marketplace 상점 및 각 게시 옵션의 설명에 대한 자세한 내용은 [Marketplace 게시자 가이드](https://aka.ms/sellerguide) 및 [게시 옵션](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#select-a-publishing-option)을 참조하세요.

## <a name="business-requirements"></a>비즈니스 요구 사항
SaaS 제품 비즈니스 요구 사항은 기술 요구 사항과 병렬로 완료될 수 있습니다. 대부분의 비즈니스 요구 사항 및 정보는 Cloud 파트너 포털에 있는 SaaS 제품을 빌드할 때 수집됩니다. 비즈니스 요구 사항은 다음과 같습니다. 
* 참여 정책에 동의
* Microsoft와의 통합 
* 제품의 대상 식별
* 사용할 잠재 고객 관리 정의 및 결정
* 개인 정보 취급 방침 및 사용 약관 설정
* 고객 지원팀 연락처 정의  

자세한 내용은 [마켓플레이스 게시에 대한 필수 구성 요소](https://docs.microsoft.com/en-us/azure/marketplace/marketplace-publishers-guide#prerequisites-for-marketplace-publishing) 항목에서 찾을 수 있습니다.

## <a name="technical-requirements"></a>기술적인 요구 사항

SaaS 응용 프로그램에 대한 기술 요구 사항은 간단합니다. 게시할 Azure AD(Azure Active Directory)와 게시자가 통합되기만 하면 됩니다. Azure AD와 응용 프로그램의 통합은 문서화가 잘 되어있으며 이를 위해 Microsoft는 다수의 SDK와 리소스가 제공됩니다.  

시작하려면 다른 이니셔티브와 작업을 격리할 수 있도록 Azure Marketplace 게시 전용 구독을 마련하는 것이 좋습니다. 이 작업이 완료되면 이 구독에서 SaaS 응용 프로그램을 배포하기 시작하여 개발 작업을 시작할 수 있습니다.  

최상의 Azure Active Directory 설명서, 샘플 및 지침은 다음 사이트에 위치합니다. 

* [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide)

* [Azure Active Directory와 통합](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-how-to-integrate)

* [Azure Active Directory와 응용 프로그램 통합](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure 로드맵 - 보안 및 ID](https://azure.microsoft.com/roadmap/?category=security-identity)

비디오 자습서는 다음 정보를 검토합니다.

* [Vittorio Bertocci가 소개하는 Azure Active Directory 인증](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 기술 브리핑 - 1/2부](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Azure Active Directory ID 기술 브리핑 - 2/2부](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Microsoft Azure Active Directory로 앱 구축](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Active Directory에 중점을 둔 Microsoft Azure 비디오](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

무료 Azure Active Directory 교육은 다음에서 지원됩니다.  
* [IT 전문가용 Microsoft Azure 콘텐츠 시리즈: Azure Active Directory](https://mva.microsoft.com/en-US/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

또한 Azure Active Directory에서는 서비스 업데이트를 확인하는 사이트를 제공합니다.   
* [Azure AD 서비스 업데이트](https://azure.microsoft.com/updates/?product=active-directory)

지원이 필요하면 다음 리소스를 사용하세요.
* [MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="publishing-process"></a>게시 프로세스

SaaS 게시 프로세스에는 기술 및 비즈니스 단계가 있습니다.  Azure Active Directory를 개발하고 통합하는 대부분의 작업은 제품의 비즈니스 요구 사항을 충족하는 데 필요한 작업과 병렬로 수행될 수 있습니다. 대량의 비즈니스 요구 사항은 SaaS 앱 제품 구성 Cloud 파트너 포털의 일부입니다.  
다음 다이어그램에서는 평가판/거래 제품에 대한 주요 게시 단계를 보여줍니다.  

![SaaS 게시 단계](./media/marketplace-saas-applications-technical-publishing-guide/saaspublishingsteps.png)  

다음 표에서는 각 주요 게시 단계를 설명합니다.  

| 게시 단계 | 설명 |   
| --- | --- |  
| **SaaS 응용 프로그램 만들기** | Cloud 파트너 포털에 로그인하고, **새로 만들기**를 선택한 다음, **SaaS 앱** 제품을 선택합니다. |  
| **Azure AD와 통합 만들기** | 이전 섹션에 설명된 기술 요구를 따라 SaaS 제품을 Azure AD와 통합합니다. |  
| **제품 설정 지정**| 모든 SaaS 제품 초기 정보를 입력합니다. 사용하려는 제품 ID 및 제품 이름입니다. |     
| **기술 정보 설정** | 이 제품에 대한 기술 정보를 입력합니다. SaaS 응용 프로그램의 경우 솔루션의 URI 및 제품의 취득 형식 단추(무료, 평가판 또는 연락처)가 필요합니다. |  
| **시험 사용(선택 사항)** | 대개 다른 형식의 Marketplace 제품에 필요한 평가판의 선택적 형식입니다. 게시자의 구독 및 최종 사용자에게 배포된 평가판이 있을 수 있습니다. |  
| **제품 상점 자료 설정**| 이 섹션에서 게시자는 로고, 마케팅 자료, 법률 문서를 연결하고 업로드하며, 잠재 고객 관리 시스템을 구성합니다. |
| **제품 연락처 설정** | SaaS 제품에 대한 엔지니어링 팀 연락처 및 고객 지원팀 연락처 정보를 모두 입력합니다. |  
| **SaaS 앱 Azure AD 통합 확인** | 게시하기 위해 SaaS 앱을 제출하기 전에 앱이 Azure AD와 통합되었는지 확인해야 합니다. |  
| **제품 게시**| 제품 및 기술 자산을 완료한 후에 제품을 제출할 수 있습니다. 그러면 게시를 위해 솔루션 템플릿을 테스트하고, 유효성을 검사하고, 인증하고, 승인하는 게시 프로세스를 시작합니다. |

## <a name="using-azure-active-directory-to-enable-trials"></a>Azure Active Directory를 사용하여 체험 지원  

Microsoft에서는 Azure AD를 사용하여 모든 Marketplace를 인증하므로, 인증된 사용자가 Marketplace에서 평가판 항목을 클릭하고 평가판 환경으로 리디렉션되면, 추가 로그인 단계를 요구하지 않으면서 사용자를 평가판 단계로 직접 프로비전할 수 있습니다. 인증 중에 Azure AD에서 앱이 수신하는 토큰에는 앱에서 사용자 계정을 만드는 데 사용할 수 있는 귀중한 사용자 정보가 포함되어 있으므로, 프로비전 환경을 자동화하고 변환 가능성을 높일 수 있습니다. 토큰에 대한 자세한 내용은 [샘플 토큰](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)을 참조하세요.

Azure AD를 사용하면 다음과 같이 앱 또는 평가판에 대한 원클릭 인증을 사용할 수 있습니다.  
* 고객이 Marketplace의 환경에서 평가판으로 간단히 전환할 수 있습니다.  
* 사용자가 Marketplace에서 도메인 또는 평가판 환경으로 리디렉션되더라도 '제품 내 환경'을 사용한다는 느낌을 유지할 수 있습니다.  
* 추가 로그인 단계가 없으므로 리디렉션 중에 중단될 가능성이 줄어듭니다.  
* 대규모 Azure AD 사용자에 대한 배포 장애물이 줄어듭니다.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Marketplace에 대한 Azure AD 통합 인증  

응용 프로그램이 단일 테넌트인지 아니면 다중 테넌트인지, Azure AD 페더레이션된 SSO(Single Sign-On)을 처음 사용하는지 아니면 이미 지원하는지에 따라 몇 가지 다른 방법으로 Azure AD 통합을 인증할 수 있습니다.  

**다중 테넌트 응용 프로그램의 경우:**  

이미 Azure AD를 지원하는 경우 다음을 수행합니다.
1.  Azure Portal에서 응용 프로그램을 등록합니다.
2.  Azure AD의 다중 테넌트 지원 기능으로 원클릭 평가판 환경을 사용할 수 있습니다. [여기](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)에서 자세한 내용을 찾을 수 있습니다.  

Azure AD 페더레이션 SSO를 처음 사용하는 경우 다음을 수행합니다. 
1.  Azure Portal에서 응용 프로그램을 등록합니다.
2.  [OpenID Connect](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 또는 [OAuth 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-protocols-oauth-code)을 사용하여 Azure AD에서 SSO를 개발합니다.
3.  AAD의 다중 테넌트 지원 기능으로 '원클릭'만으로 평가판 환경을 사용할 수 있습니다. [여기](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-devhowto-appsource-certified)에서 자세한 내용을 찾을 수 있습니다.  

**단일 테넌트 응용 프로그램의 경우 다음 옵션 중 하나를 사용합니다.**  
* [Azure B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)를 사용하여 게스트 사용자로 디렉터리에 사용자 추가
* '연락처'를 사용하여 고객에게 평가판을 수동으로 프로비전
* 고객별로 '시험 사용' 개발
* SSO를 통해 다중 테넌트 샘플 데모 앱을 빌드합니다.

