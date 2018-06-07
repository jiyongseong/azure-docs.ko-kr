---
title: Azure AD reporting API 시작 | Microsoft Docs
description: Azure Active Directory Reporting API를 시작하는 방법
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a818c9b0e2c9097f45d2fdd39676ef6807d06ca5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929089"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory reporting API 시작하기

Azure Active Directory는 다양한 [보고서](active-directory-reporting-azure-portal.md)를 제공합니다. 이러한 보고서의 데이터는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 응용 프로그램에 매우 유용할 수 있습니다. 

Azure AD reporting API를 사용하면 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스할 수 있습니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

이 문서에서는 관련 API를 사용하여 보고 데이터에 액세스할 수 있는 로드맵을 제공합니다.

문제가 발생하면 [Azure Active Directory에 대한 지원을 받는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)을 참조하세요.


## <a name="prerequisites"></a>필수 조건

reporting API에 액세스하려면, 스크립트를 사용하여 API에 액세스하려는 경우에도, 다음과 같은 작업이 필요합니다.

1. 역할(보안 읽기 권한자, 보안 관리자, 전역 관리자) 할당
2. 응용 프로그램 등록
3. 권한 부여
4. 구성 설정 수집


 
자세한 지침은 [Azure Active Directory reporting API에 액세스하기 위한 전제 조건](active-directory-reporting-api-prerequisites-azure-portal.md)을 참조하세요.


## <a name="recommendation"></a>권장 사항 

사용자 개입 없이 보고 데이터를 검색하려는 경우에는 Azure AD Reporting API를 인증서와 함께 사용하는 것이 좋습니다.

자세한 지침은 [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](active-directory-reporting-api-with-certificates.md)를 참조하세요.


## <a name="explore"></a>탐색

reporting API의 첫 인상 살펴보기:
   
   - [감사 API에 대한 샘플 사용](active-directory-reporting-api-audit-samples.md) 
 
   - [로그인 활동 보고서 API에 대한 샘플 사용](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>사용자 지정  

고유의 솔루션 만들기: 
   
   - [감사 API 참조 사용](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [로그인 활동 보고서 API 참조 사용](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



