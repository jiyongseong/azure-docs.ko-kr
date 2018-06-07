---
title: "Azure Active Directory 보고 | Microsoft Docs"
description: "Azure Active Directory 보고에 대한 일반적 개요를 제공합니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 66c53b84e1e820178083b3ae703e0a1feada187f
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-reporting"></a>Azure Active Directory 보고

Azure Active Directory 보고 기능을 사용하면 환경이 작동하는 방법에 대한 정보를 얻을 수 있습니다.  
제공된 데이터를 통해 다음을 수행할 수 있습니다.

- 사용자가 앱과 서비스를 활용하는 방식을 결정합니다.
- 환경의 상태에 영향을 줄 수 있는 잠재적인 위험을 검색합니다.
- 사용자가 자신의 작업을 완료하지 못하게 하는 문제를 해결합니다.  

보고 아키텍처는 다음 두 가지 주요 요소로 구성됩니다.

- 보안 보고서
- 작업 보고서

![보고](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>보안 보고서

Azure Active Directory의 보안 보고서는 조직의 ID를 보호하는 데 도움이 됩니다.  
Azure Active Directory에는 두 가지 유형의 보안 보고서가 있습니다.

- **위험 플래그가 지정된 사용자** - [위험 플래그가 지정된 사용자 보안 보고서](active-directory-reporting-security-user-at-risk.md)에서 손상되었을 수 있는 사용자 계정에 대한 개요를 얻을 수 있습니다.

- **위험한 로그인** - [위험한 로그인 보안 보고서](active-directory-reporting-security-risky-sign-ins.md)를 사용하면 사용자 계정의 정당한 소유자가 아닌 사람이 수행한 로그인 시도에 대한 지표를 얻을 수 있습니다. 

**보안 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?**  
모든 Azure Active Directory 버전에서 위험 플래그가 지정된 사용자 보고서 및 위험한 로그인 보고서를 제공합니다.  
그러나 보고서의 세분성 수준은 다음과 같이 버전에 따라 다릅니다. 

- **Azure Active Directory Free 및 Basic 버전**에는 위험 플래그가 지정된 사용자 및 위험한 로그인 목록이 이미 있습니다. 

- **Azure Active Directory Premium 1** 버전은 각 보고서에서 검색된 기본 위험 이벤트 중 일부를 검사할 수 있게 함으로써 이 모델을 확장합니다. 

- **Azure Active Directory Premium 2** 버전은 기본 위험 이벤트에 대한 가장 자세한 정보를 제공하며, 구성된 위험 수준에 자동으로 응답하는 보안 정책을 구성할 수도 있습니다.


## <a name="activity-reports"></a>작업 보고서

Azure Active Directory에는 두 가지 유형의 활동 보고서가 있습니다.

- **감사 로그** - [감사 로그 활동 보고서](active-directory-reporting-activity-audit-logs.md)는 테넌트에서 수행된 모든 작업 기록에 대한 액세스를 제공합니다.

- **로그인** - [로그인 활동 보고서](active-directory-reporting-activity-sign-ins.md)를 사용하면 감사 로그 보고서에서 보고한 작업을 수행한 사람을 확인할 수 있습니다.



**감사 로그 보고서**는 규정 준수에 대한 시스템 활동 기록을 제공합니다.
제공된 데이터를 사용하면 다음과 같은 일반적인 시나리오를 처리할 수 있습니다.

- 내 테넌트의 누군가가 관리 그룹에 액세스할 수 있었습니다. 이들에게 액세스 권한을 부여한 사람을 알고 싶습니다. 

- 최근에 앱을 등록한 이후 특정 앱에 로그인하고 있는 사용자의 목록과 앱이 잘 작동하는지의 여부를 알고 싶습니다.

- 내 테넌트에서 발생하고 있는 암호 재설정 횟수를 알고 싶습니다.


**감사 로그 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?**  
감사 로그 보고서는 라이선스가 있는 기능에 대해 사용할 수 있습니다. 특정 기능에 대한 라이선스가 있으면 해당 기능에 대한 감사 로그 정보에 액세스할 수도 있습니다.

자세한 내용은 [Azure Active Directory 기능](https://www.microsoft.com/cloud-platform/azure-active-directory-features)의 **Free, Basic 및 Premium 버전의 일반 공급 기능 비교**를 참조하세요.   



**로그인 활동 보고서**는 다음 질문에 대한 대답을 찾을 수 있습니다.

- 사용자의 로그인 패턴이란?
- 한 주 동안 얼마나 많은 사용자가 로그인했나요?
- 이러한 로그인의 상태란?


**로그인 활동 보고서에 액세스하는 데 필요한 Azure AD 라이선스는 무엇인가요?**  
로그인 활동 보고서에 액세스하려면 이와 연결된 Azure AD Premium 라이선스가 테넌트에 있어야 합니다.


## <a name="programmatic-access"></a>프로그래밍 방식 액세스

Azure Active Directory 보고에서는 사용자 인터페이스 외에도 [프로그래밍 방식 액세스](active-directory-reporting-api-getting-started-azure-portal.md)를 보고 데이터에 제공합니다. 이러한 보고서의 데이터는 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 응용 프로그램에 매우 유용할 수 있습니다. Azure AD Reporting API는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

Azure Active Directory의 다양한 보고서 유형에 대한 자세한 내용은 다음을 참조하세요.

- [위험 플래그가 지정된 사용자 보고서](active-directory-reporting-security-user-at-risk.md)
- [위험한 로그인 보고서](active-directory-reporting-security-risky-sign-ins.md)
- [감사 로그 보고서](active-directory-reporting-activity-audit-logs.md)
- [로그인 로그 보고서](active-directory-reporting-activity-sign-ins.md)

보고 API를 사용하여 보고 데이터에 액세스하는 방법에 대한 자세한 내용은 다음을 참조하세요. 

- [Azure Active Directory 보고 API 시작](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png