---
title: Azure ID 개발자를 위한 지원 및 도움말 옵션 | Microsoft Docs
description: Microsoft Azure ID(Azure Active Directory 및 MSA)와 통합되는 응용 프로그램을 만들 때 개발과 관련된 질문 및 문제에 대한 지원 및 도움말을 얻는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: ae682830a8d0e2c5c27211a3396d04c881c41585
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="support-and-help-options-for-developers"></a>개발자를 위한 지원 및 도움말 옵션 

Azure Active Directory, Microsoft ID 또는 Microsoft Graph API와 통합하기 시작하는지, 아니면 응용 프로그램에 새 기능을 구현하는지에 관계없이 커뮤니티에서 도움을 얻거나 개발자로서 사용하는 지원 옵션을 이해해야 할 때가 있습니다. 이 문서는 아래에 요약된 옵션을 이해하는 데 도움이 됩니다.

> [!div class="checklist"]
> * 커뮤니티에서 문제 관련 질문에 답변하지 않았는지 또는 구현하려는 기능에 대한 기존 설명서가 이미 존재하는지를 확인하기 위해 검색합니다
> * 경우에 따라 지원 도구를 사용하여 특정 문제를 디버그하는 데 도움이 되는 경우도 있습니다.
> * 필요한 답변을 찾을 수 없으면 *Stack Overflow*에서 질문할 수 있습니다.
> * 인증 라이브러리 중 하나에서 문제가 발생하면 *GitHub* 문제를 제기합니다.
> * 마지막으로 누군가와 대화할 필요가 있으면 지원 요청을 열 수 있습니다.


## <a name="search"></a>검색

개발 관련 질문이 있으면 설명서, [github 샘플](https://github.com/azure-samples) 또는 [Stack Overflow](https://www.stackoverflow.com) FAQ에서 필요한 답변을 찾을 수 있습니다.

### <a name="scoped-search"></a>범위 지정 검색
빠른 결과를 얻으려면 [즐겨찾는 검색 엔진](https://bing.com)에서 다음을 사용하여 Stack Overflow, 설명서 및 코드 샘플에 대한 검색 범위를 지정합니다.
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
여기서 *{Your Search Terms}* 는 검색 키워드입니다.
<br/>

## <a name="use-our-development-support-tools"></a>개발 지원 도구 사용

|도구  |설명  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| ID 또는 액세스 토큰을 붙여넣어 클레임 이름 및 값을 디코딩합니다. |
|[오류 코드 분석기](https://apps.dev.microsoft.com/portal/tools/errors)| 로그인 또는 동의 페이지에서 받은 오류 코드를 붙여넣어 가능한 원인 및 수정 작업을 확인합니다. |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| 요청을 하고 Microsoft Graph API에 대한 응답을 확인할 수 있는 도구입니다.|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Stack Overflow에 질문을 게시합니다.

Stack Overflow는 개발 관련 질문에 대한 기본 설정 채널이며, Microsoft 팀 멤버인 커뮤니티 구성원 모두가 직접 관련되어 있어 문제를 해결하는 데 도움이 됩니다.

검색을 통해 문제에 대한 답변을 찾을 수 없으면 Stack Overflow에 새 질문을 제출합니다. 다음 태그 중 하나를 사용하여 커뮤니티에서 식별하는 데 도움이 되는 질문을 하면 적시에 답변할 수 있습니다.

|구성 요소/영역  |태그들  |
|---------|---------|
|ADAL 라이브러리 |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|MSAL 라이브러리     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|OWIN 미들웨어  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft Graph API](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|인증 또는 권한 부여 주제와 관련된 다른 모든 영역 |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> 다음 Stack Overflow 게시물에는 질문을 작성하는 방법 및 소스 코드를 추가하는 방법에 대한 팁이 있습니다. 이러한 지침을 따르면 커뮤니티 구성원이 신속하게 질문을 평가하고 응답할 수 있는 기회를 늘릴 수 있습니다.  
> - [좋은 질문을 하는 방법](https://stackoverflow.com/help/how-to-ask)
> - [최소한의 완전하고 검증 가능한 예제를 만드는 방법](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>GitHub 문제 만들기

 라이브러리와 관련된 버그 또는 문제가 있으면 GitHub 리포지토리에서 문제를 제기합니다. 라이브러리는 오픈 소스이므로 끌어오기 요청을 자유롭게 제출할 수도 있습니다. 다음 문서에는 라이브러리 및 해당 GitHub 리포지토리의 목록이 포함되어 있습니다.

- [ADAL, MSAL 및 Owin 미들웨어](active-directory-authentication-libraries.md) 라이브러리 및 GitHub 리포지토리

<br/>

## <a name="open-a-support-request"></a>지원 요청 열기

누군가와 대화할 필요가 있으면 지원 요청을 열 수 있습니다. Azure 고객인 경우 몇 가지 지원 옵션을 사용할 수 있습니다. 계획을 비교하려면 [이 페이지](https://azure.microsoft.com/support/plans/)를 참조하세요. 개발자 지원은 Azure 고객에게도 제공됩니다. 개발자 지원 계획을 구매하는 방법에 대한 자세한 내용은 [이 페이지](https://azure.microsoft.com/support/plans/developer/)를 참조하세요.

- Azure 지원 계획이 이미 있는 경우 [여기서 지원 요청을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Azure 고객이 아닌 경우 [상용 지원](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)을 통해 Microsoft 지원 요청을 열 수도 있습니다.

[가상 에이전트](https://support.microsoft.com/contactus/?ws=support)를 사용하여 지원받거나 질문할 수도 있습니다.

### <a name="free-chat-support-for-a-limited-time"></a>제한된 시간 동안 체험 채팅 지원

제한된 시간 동안 Microsoft 파트너에 대한 체험 채팅 지원을 사용할 수도 있습니다. Microsoft 파트너가 아닌 경우 [여기](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx)로 이동하여 추가 비용 없이 등록하고 다른 혜택을 얻을 수 있습니다.

등록한 후에는 [여기서](https://aka.ms/devchat) 채팅 요청을 시작할 수 있습니다.