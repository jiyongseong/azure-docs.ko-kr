---
title: "Azure의 하이브리드 ID 디자인 - 디렉터리 동기화 요구 사항 | Microsoft Docs"
description: "기업에 대한 온-프레미스와 클라우드 사이의 모든 사용자를 동기화하는 데 필요한 요구 사항을 식별합니다."
documentationcenter: 
services: active-directory
author: billmath
manager: mtillman
editor: 
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 475b35c767fcc9c42b828dd2219a14890e05f779
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="determine-directory-synchronization-requirements"></a>디렉터리 동기화 요구 사항 결정
동기화는 해당 온-프레미스 ID에 기반하여 클라우드에서 사용자에게 ID를 제공하는 모든 작업입니다. 인증에 대해 동기화된 계정 또는 페더레이션된 인증을 사용하는지와 무관하게 사용자는 클라우드에서 ID가 필요합니다.  이 ID는 정기적으로 유지 관리하고 업데이트해야 합니다.  업데이트는 제목 변경에서 암호 변경까지 다양한 형태일 수 있습니다.  

조직 온-프레미스 ID 솔루션 및 사용자 요구 사항을 평가하여 시작합니다. 이 평가판은 사용자 ID가 클라우드에서 생성되고 유지 관리되는 방법에 대한 기술 요구 사항을 정의하는 데 중요합니다.  대부분의 조직의 경우 Active Directory는 온-프레미스이며 사용자가 동기화하는 온-프레미스 디렉터리이지만 경우에 따라 다를 수 있습니다.  

다음 질문에 답변하세요.

* AD 포리스트가 하나 또는 다수가 있거나 없습니까?
  
  * Azure AD 디렉터리를 몇 개나 동기화합니까?
    
    1. 필터링을 사용하십니까?
    2. 여러 Azure AD Connect 서버를 계획했습니까?
* 현재 동기화 도구 온-프레미스가 있습니까?
  
  * 그렇다면 사용자에게 ID의 가상 디렉터리/통합이 있습니까?
* 동기화하려는 다른 디렉터리 온-프레미스(예: LDAP 디렉터리, HR 데이터베이스 등)가 있습니까?
  * GALSync를 수행할 것입니까?
  * 조직에서 UPN의 현재 상태는 무엇입니까? 
  * 사용자가 인증하는 다른 디렉터리가 있습니까?
  * 회사는 Microsoft Exchange를 사용합니까?
    * 하이브리드 Exchange를 배포할 계획입니까?

이제 동기화 요구 사항을 이해했으므로 이러한 요구 사항을 만족하기에 알맞은 도구를 결정해야 합니다.  Microsoft는 디렉터리 통합 및 동기화를 수행하기 위한 여러 도구를 제공합니다.  자세한 내용은 [하이브리드 ID 디렉터리 통합 도구 비교표](active-directory-hybrid-identity-design-considerations-tools-comparison.md) 를 참조하세요. 

이제 동기화 요구 사항을 확인하고 회사에 대해 이를 수행하는 도구를 가지고 있으므로 이러한 디렉터리 서비스를 사용하는 응용 프로그램을 평가해야 합니다. 이 평가판은 클라우드에 이러한 응용 프로그램을 통합하는 기술 요구 사항을 정의하는 데 중요합니다. 다음 질문에 답변하세요.

* 이러한 응용 프로그램이 클라우드로 이동되고 디렉터리를 사용합니까?
* 클라우드로 동기화되어야 하는 특수한 특성 사항이 있으므로 이러한 응용 프로그램이 특성을 성공적으로 사용할 수 있습니까?
* 이러한 응용 프로그램은 클라우드 인증을 활용하기 위해 다시 작성해야 합니까?
* 사용자가 클라우드 ID를 사용하여 액세스하는 동안 이러한 응용 프로그램은 계속 온-프레미스에서 작동합니까?

또한 보안 요구 사항 및 제약 조건 디렉터리 동기화를 결정해야 합니다. 이 평가판은 클라우드에서 사용자의 ID를 만들고 유지하기 위해 필요한 요구 사항 목록을 가져오는 데 중요합니다. 다음 질문에 답변하세요.

* 동기화 서버는 어디에 배치됩니까?
* 가입된 도메인입니까?
* DMZ와 같은 방화벽 뒤에 제한된 네트워크에 서버가 위치합니까?
  * 동기화를 지원하기 위해 필요한 방화벽 포트를 열 수 있습니까?
* 동기화 서버에 대한 재해 복구 계획이 있습니까?
* 동기화하려는 모든 포리스트에 대한 올바른 사용 권한이 있는 계정이 있습니까?
  * 회사가 이 질문에 대한 대답을 모르는 경우 [Azure Active Directory 동기화 서비스 설치](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) 문서에서 "암호 동기화에 대한 사용 권한" 섹션을 검토하고 이러한 사용 권한이 있는 계정이 이미 있는지 혹은 하나를 만들어야 할지를 결정합니다.
* 다중 포리스트 동기화가 있는 경우 동기화 서버가 각 포리스트에 도달할 수 있습니까?

> [!NOTE]
> 각 답변을 주목하고 답변 이유를 이해해야 합니다. [사고 대응 요구 사항 결정](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) 은 사용할 수 있는 옵션을 검토합니다. 질문에 답변함으로써 비즈니스 요구 사항에 가장 적합한 옵션을 선택할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
[Multi-Factor Authentication 요구 사항 결정](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>참고 항목
[디자인 고려 사항 개요](active-directory-hybrid-identity-design-considerations-overview.md)

