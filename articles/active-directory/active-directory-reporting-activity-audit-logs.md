---
title: Azure Active Directory 포털의 감사 작업 보고서 | Microsoft Docs
description: Azure Active Directory 포털의 감사 작업 보고서 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 5950b8bac89a8a6c0ed7979228b8296f34e43364
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory 포털의 감사 작업 보고서 

Azure Active Directory(Azure AD)에서 보고를 통해 사용자 환경의 작동 방법을 결정하는 데 필요한 모든 정보를 얻을 수 있습니다.

Azure AD의 보고 아키텍처는 다음 구성 요소로 구성됩니다.

- **활동** 
    - **로그인 활동** – 관리되는 응용 프로그램 및 사용자 로그인 활동의 사용량에 대한 정보
    - **감사 로그** - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할, 정책, 인증 등 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- **보안** 
    - **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 자세한 내용은 위험한 로그인을 참조하세요.
    - **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 자세한 내용은 위험 플래그가 지정된 사용자를 참조하세요.

이 항목에서는 감사 활동에 대한 개요를 제공합니다.
 
## <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?
* 보안 관리 또는 보안 판독기 역할의 사용자
* 전역 관리자
* 개별 사용자(비관리자)는 자신의 활동을 볼 수 있습니다.


## <a name="audit-logs"></a>감사 로그

Azure Active Directory의 감사 로그는 규정 준수를 위한 시스템 활동의 기록을 제공합니다.  
모든 감사 데이터에 대한 첫 번째 진입점은 **Azure Active Directory**의 **활동** 섹션에 있는 **감사 로그**입니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/61.png "감사 로그")

감사 로그에는 다음 항목을 보여 주는 기본 목록 보기가 있습니다.

- 발생 날짜와 시간
- 활동의 초기자/행위자(*누가*) 
- 활동(*무엇*) 
- 대상

![감사 로그](./media/active-directory-reporting-activity-audit-logs/18.png "감사 로그")

도구 모음에서 **열**을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/19.png "감사 로그")

그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/21.png "감사 로그")


목록 보기에서 항목을 클릭하면 사용할 수 있는 세부 정보를 모두 얻을 수 있습니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/22.png "감사 로그")


## <a name="filtering-audit-logs"></a>감사 로그 필터링

보고된 데이터를 자신에게 적합한 수준으로 좁히려면 다음 필드를 사용하여 감사 데이터를 필터링할 수 있습니다.

- 날짜 범위
- 초기자(작업자)
- Category
- 활동 리소스 종류
- 작업

![감사 로그](./media/active-directory-reporting-activity-audit-logs/23.png "감사 로그")


**날짜 범위** 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.  
가능한 값은 다음과 같습니다.

- 1개월
- 7 일
- 24시간
- 사용자 지정

사용자 지정 시간 범위를 선택하면 시작 시간과 종료 시간을 구성할 수 있습니다.

**초기자** 필터를 사용하면 작업자 이름이나 UPN(사용자 계정 이름)을 정의할 수 있습니다.

**범주** 필터를 사용하면 다음 필터 중 하나를 선택할 수 있습니다.

- 모두
- 핵심 범주
- 핵심 디렉터리
- 셀프 서비스 암호 관리
- 셀프 서비스 그룹 관리
- 계정 프로비전 - 자동화된 암호 롤오버
- 사용자 초대
- MIM 서비스
- ID 보호
- B2C

**활동 리소스 종류** 필터를 사용하면 다음 필터 중 하나를 선택할 수 있습니다.

- 모두 
- 그룹
- 디렉터리
- 사용자
- 응용 프로그램
- 정책
- 장치
- 기타

**그룹**을 **활동 리소스 종류**로 선택하면 다음과 같은 **원본**도 제공할 수 있는 추가 필터 범주가 생깁니다.

- Azure AD
- O365


**활동** 필터는 사용자가 선택한 범주와 활동 리소스 종류를 기반으로 합니다. 보려는 특정 활동을 선택하거나 모두 선택할 수 있습니다. 

Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta(여기서 $tenantdomain = 도메인 이름)를 사용하여 모든 감사 작업 목록을 가져오거나 [감사 보고서 이벤트](active-directory-reporting-audit-events.md) 문서를 참조할 수 있습니다.


## <a name="audit-logs-shortcuts"></a>감사 로그 바로 가기

**Azure Active Directory** 외에도 Azure Portal에서는 감사 데이터에 대한 다음 두 개의 추가 진입점을 제공합니다.

- 개요
- Enterprise 응용 프로그램

### <a name="users-and-groups-audit-logs"></a>사용자 및 그룹 감사 로그

사용자 및 그룹 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

- 어떤 종류의 업데이트가 사용자에게 적용되나요?

- 얼마나 많은 사용자가 변경되었나요?

- 얼마나 많은 암호가 변경되었나요?

- 관리자가 디렉터리에서 무엇을 수행했나요?

- 추가된 그룹은 무엇인가요?

- 멤버 자격이 변경된 그룹이 있나요?

- 그룹의 소유자가 변경되었나요?

- 어떤 라이선스가 그룹 또는 사용자에 할당되었나요?

사용자 및 그룹과 관련된 감사 데이터를 검토하려면 **사용자 및 그룹**의 **활동** 섹션에 있는 **감사 로그**에서 필터링된 보기를 찾을 수 있습니다. 이 진입점에는 미리 선택된 **활동 리소스 종류**로 **사용자 및 그룹**이 있습니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/93.png "감사 로그")

### <a name="enterprise-applications-audit-logs"></a>Enterprise 응용 프로그램 감사 로그

응용 프로그램 기반 감사 보고서를 사용하여 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

* 추가되거나 업데이트된 응용 프로그램은 무엇인가요?
* 삭제된 응용 프로그램은 무엇인가요?
* 응용 프로그램에 대한 서비스 원칙이 변경되었나요?
* 응용 프로그램의 이름이 변경되었나요?
* 누가 응용 프로그램에 동의했나요?

응용 프로그램과 관련된 감사 데이터를 검토하려면 **Enterprise 응용 프로그램** 블레이드의 **활동** 섹션에 있는 **감사 로그**에서 필터링된 보기를 찾을 수 있습니다. 이 진입점에는 미리 선택된 **활동 리소스 종류**인 **엔터프라이즈 응용 프로그램**이 있습니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/134.png "감사 로그")

이 보기는 **그룹** 또는 **사용자**로 더 자세히 필터링할 수 있습니다.

![감사 로그](./media/active-directory-reporting-activity-audit-logs/25.png "감사 로그")



## <a name="next-steps"></a>다음 단계

- 보고 개요는 [Azure Active Directory 보고](active-directory-reporting-azure-portal.md)를 참조하세요.

- 모든 감사 활동의 전체 목록은 [Azure AD 감사 활동 참조](active-directory-reporting-activity-audit-reference.md)를 참조하세요.

