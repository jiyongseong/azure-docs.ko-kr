---
title: "Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서 | Microsoft Docs"
description: "Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서에 대해 알아보기"
services: active-directory
author: MarkusVi
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: dca87eef3026ef43c0548a73f1b071b56e410e90
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 위험에 대한 플래그가 지정된 사용자 보안 보고서

Azure AD(Azure Active Directory)에서 보안 보고를 통해 사용자 환경에서 손상된 사용자 계정의 확률에 대한 정보를 얻을 수 있습니다. 

Azure Active Directory는 사용자 계정에 관련된 의심스러운 작업을 감지합니다. 작업이 감지된 경우 *위험 이벤트*라는 레코드가 만들어집니다. 자세한 내용은 [Azure Active Directory 위험 이벤트](active-directory-identity-protection-risk-events.md)를 참조하세요. 

감지된 위험 이벤트를 계산하는 데 사용합니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 자세한 내용은 [위험한 로그인](active-directory-identityprotection.md#risky-sign-ins)을 참조하세요. 

- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 자세한 내용은 [위험 플래그가 지정된 사용자](active-directory-identityprotection.md#users-flagged-for-risk)를 참조하세요.  

Azure Portal의 **보안** 섹션에서 **Azure Active Directory** 블레이드에 대한 보안 보고서를 찾을 수 있습니다.  

![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>보안 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?  

모든 Azure Active Directory 버전에서 위험 플래그가 지정된 사용자 보고서를 제공합니다.  
그러나 보고서의 세분성 수준은 다음과 같이 버전에 따라 다릅니다. 

- **Azure Active Directory Free 및 Basic 버전**에는 위험 플래그가 지정된 사용자 목록이 이미 있습니다. 

- **Azure Active Directory Premium 1** 버전은 각 보고서에서 검색된 기본 위험 이벤트 중 일부를 검사할 수 있게 함으로써 이 모델을 확장합니다. 

- **Azure Active Directory Premium 2** 버전은 모든 기본 위험 이벤트에 대한 가장 자세한 정보를 제공하며, 구성된 위험 수준에 자동으로 응답하는 보안 정책을 구성할 수 있습니다.



## <a name="azure-active-directory-free-and-basic-edition"></a>Azure Active Directory 무료 및 기본 버전

Azure Active Directory 평가판 및 기본 버전의 위험에 대한 플래그가 지정된 사용자 보고서에는 손상되었을 수 있는 사용자 계정 목록이 제공됩니다. 


![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/03.png)

사용자를 선택하면 관련된 사용자 데이터 블레이드가 열립니다.
위험에 노출된 사용자에 대해 사용자의 로그인 기록을 검토하고 필요한 경우 암호를 다시 설정할 수 있습니다.

![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/46.png)


이 대화 상자는 다음과 같은 옵션을 제공합니다.

- 보고서 다운로드

- 사용자 검색

![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Azure Active Directory Premium Edition

Azure Active Directory Premium Edition의 위험에 대한 플래그가 지정된 사용자 보고서에 제공되는 내용:

- 손상되었을 수 있는 [사용자 계정 목록](active-directory-identityprotection.md#users-flagged-for-risk) 

- 감지된 [위험 이벤트 유형](active-directory-identity-protection-risk-events.md)에 대해 집계된 정보

- 보고서를 다운로드할 수 있는 옵션

- [사용자 위험 수정 정책](active-directory-identityprotection.md#user-risk-security-policy)을 구성할 수 있는 옵션  


![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/71.png)

사용자를 선택하면 사용자에 대한 자세한 보고서 보기가 제공되고 다음과 같은 작업이 가능합니다.

- 모든 로그인 보기 열기

- 사용자 암호 재설정

- 모든 이벤트 해제

- 사용자에 대해 보고된 위험 이벤트 조사. 


![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/324.png)


위험 이벤트를 조사하려면 목록에서 이벤트를 하나 선택하고 이 위험 이벤트에 대한 **세부 정보** 블레이드를 엽니다. **세부 정보** 블레이드에 [위험 이벤트를 수동으로 닫거나](active-directory-identityprotection.md#closing-risk-events-manually) 수동으로 닫은 위험 이벤트를 다시 활성화할 수 있는 옵션이 있습니다. 


![위험한 로그인](./media/active-directory-reporting-security-user-at-risk/325.png)



## <a name="next-steps"></a>다음 단계

- Azure Active Directory ID 보호에 대한 자세한 내용은 [Azure Active Directory ID 보호](active-directory-identityprotection.md)를 참조하세요.

