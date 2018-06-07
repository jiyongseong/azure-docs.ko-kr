---
title: Azure Portal에서 Azure Active Directory 사용자 작업 보고서 찾기 | Microsoft Docs
description: Azure Portal에서 Azure Active Directory 사용자 작업 보고서가 어디에 위치하는지 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: dhanyahk
ms.openlocfilehash: 985bdc3c00549844958c23a83496a019d7a31944
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal에서 작업 보고서 찾기

이 문서에서는 Azure Portal에서 Azure Active Directory 사용자 작업 보고서를 찾는 방법을 설명합니다.

## <a name="whats-new"></a>새로운 기능

Azure 클래식 포털의 보고서는 다음과 같은 범주로 구분됩니다.
* 보안 보고서
* 작업 보고서
* 통합 앱 보고서

### <a name="activity-and-integrated-app-reports"></a>활동 및 통합 앱 보고서

Azure Portal의 컨텍스트 기반 보고서의 경우 기존 보고서가 단일 보기에 병합되었습니다. 단일 기본 API는 보기에 데이터를 제공합니다.

이 보기를 보려면 **Azure Active Directory** 블레이드의 **작업**에서 **감사 로그**를 선택합니다.

![감사 로그](./media/active-directory-reporting-migration/482.png "감사 로그")

다음과 같은 보고서가 이 보기에 통합되었습니다.

* 감사 보고서
* 암호 재설정 활동
* 암호 재설정 등록 활동
* 셀프 서비스 그룹 작업
* Office 365 그룹 이름 변경
* 계정 프로비전 활동
* 암호 롤오버 상태
* 계정 프로비전 오류


개선된 응용 프로그램 사용 현황 보고서가 **로그인** 보기에 포함되었습니다. 이 보기를 보려면 **Azure Active Directory** 블레이드의 **작업**에서 **로그인**을 선택합니다.

![로그인 보기](./media/active-directory-reporting-migration/483.png "로그인 보기")

**로그인** 보기에는 모든 사용자 로그인이 포함되어 있습니다. 이 정보를 사용하여 응용 프로그램 사용 정보를 얻을 수 있습니다. **관리** 섹션의 **Enterprise 응용 프로그램** 개요에서도 응용 프로그램 사용 정보를 볼 수 있습니다.

![Enterprise 응용 프로그램](./media/active-directory-reporting-migration/484.png "Enterprise 응용 프로그램")

## <a name="access-a-specific-report"></a>특정 보고서에 액세스

Azure Portal에서 단일 보기를 제공하지만 사용자는 특정 보고서를 살펴볼 수도 있습니다.

### <a name="audit-logs"></a>감사 로그

고객 피드백에 대한 응답으로 Azure Portal에서 고급 필터링을 사용하여 원하는 데이터에 액세스할 수 있습니다. 사용할 수 있는 필터 중 하나는 Azure AD의 다양한 작업 로그 유형을 나열하는 *작업 범주*입니다. 원하는 내용만 표시되도록 결과 범위를 좁히려면 범주를 선택합니다.

예를 들어 셀프 서비스 암호 재설정과 관련된 작업만 보려면 **셀프 서비스 암호 관리** 범주를 선택하면 됩니다. 사용자에게 표시되는 범주는 사용자가 현재 작업 중인 리소스를 기반으로 합니다.  

![필터 감사 로그 페이지의 범주 옵션](./media/active-directory-reporting-migration/06.png "필터 감사 로그 페이지의 범주 옵션")

작업 범주에는 다음과 같은 항목이 포함됩니다.

- 핵심 디렉터리
- 셀프 서비스 암호 관리
- 셀프 서비스 그룹 관리
- 계정 프로비전

### <a name="application-usage"></a>응용 프로그램 사용 현황

모든 앱 또는 단일 앱의 응용 프로그램 사용 현황에 대한 세부 정보를 보려면 **작업**에서 **로그인**을 선택합니다. 결과의 범위를 좁히려면 사용자 이름 또는 응용 프로그램 이름을 필터링하면 됩니다.

![로그인 이벤트 필터링 페이지](./media/active-directory-reporting-migration/07.png "로그인 이벤트 필터링 페이지")

### <a name="security-reports"></a>보안 보고서

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 비정상 작업 보고서

하나의 중앙 보기를 제공하도록 Azure 클래식 포털의 Azure AD 비정상 작업 보안 보고서가 통합되었습니다. 이 보기에는 Azure AD가 감지하여 보고할 수 있는 모든 보안 관련 위험 이벤트가 표시됩니다.

다음 테이블에는 Azure Portal의 Azure AD 비정상 작업 보안 보고서 및 해당하는 위험 이벤트 형식이 나열되어 있습니다.

| Azure AD 비정상 작업 보고서 |  ID 보호 위험 이벤트 유형|
| :--- | :--- |
| 자격 증명이 손실된 사용자 | 유출된 자격 증명 |
| 비정상적인 로그인 작업 | 비정상적 위치로 불가능한 이동 |
| 감염 가능성이 있는 장치에서 로그인 | 감염된 장치에서 로그인|
| 알 수 없는 원본에서 로그인 | 익명 IP 주소에서 로그인 |
| 의심스러운 작업이 있는 IP 주소에서 로그인 | 의심스러운 작업이 있는 IP 주소에서 로그인 |
| - | 알 수 없는 위치에서 로그인 |

다음 Azure AD 비정상 작업 보안 보고서는 Azure Portal에 위험 이벤트로 포함되지 않습니다.

* 여러 번의 실패 후 로그인
* 여러 지역에서의 로그인

이러한 보고서는 아직은 Azure 클래식 포털에 제공되지만 어느 시점부터는 더 이상 제공되지 것입니다.

자세한 내용은 [Azure Active Directory 위험 이벤트](active-directory-identity-protection-risk-events.md)를 참조하세요.  


#### <a name="detected-risk-events"></a>검색된 위험 이벤트

Azure Portal의 **Azure Active Directory** 블레이드 **보안** 섹션에서는 검색된 위험 이벤트에 대한 보고서에 액세스할 수 있습니다. 검색된 위험 이벤트는 다음과 같은 보고서에서 추적됩니다.   

- 위험에 노출된 사용자
- 위험한 로그인

![보안 보고서](./media/active-directory-reporting-migration/04.png "보안 보고서")

보안 보고서에 대한 자세한 내용은 다음 항목을 참조하세요.

- [Azure Active Directory 포털의 위험에 노출된 사용자 보안 보고서](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 포털의 위험한 로그인 보고서](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Azure 클래식 포털 및 Azure Portal의 작업 보고서

이 섹션의 테이블에는 Azure 클래식 포털의 기존 보고서가 나열되어 있습니다. 또한 Azure Portal에서 동일한 정보를 얻는 방법이 설명되어 있습니다.

모든 감사 데이터를 보려면 **Azure Active Directory** 블레이드의 **작업**에서 **감사 로그**로 이동합니다.

![감사 로그](./media/active-directory-reporting-migration/61.png "감사 로그")

| Azure 클래식 포털                 | Azure Portal에서 찾으려면                                                         |
| ---                                  | ---                                                                        |
| 감사 로그                           | **작업 범주**로 **핵심 디렉터리**를 선택합니다.                       |
| 암호 재설정 활동              | **작업 범주**로 **셀프 서비스 암호 관리**를 선택합니다. |
| 암호 재설정 등록 활동 | **작업 범주**로 **셀프 서비스 암호 관리**를 선택합니다.     |
| 셀프 서비스 그룹 작업         | **작업 범주**로 **셀프 서비스 그룹 관리**를 선택합니다.        |
| 계정 프로비전 활동        | **작업 범주**로 **계정 사용자 프로비전**을 선택합니다.         |
| 암호 롤오버 상태             | **작업 범주**로 **자동 앱 암호 롤오버**를 선택합니다.      |
| 계정 프로비전 오류          | **작업 범주**로 **계정 사용자 프로비전**을 선택합니다.        |
| Office 365 그룹 이름 변경         | **작업 범주**로 **셀프 서비스 암호 관리**를 선택합니다. **작업 리소스 유형**으로 **그룹**을 선택합니다. **작업 원본**으로 **O365 그룹**을 선택합니다.|

**응용 프로그램 사용 현황** 보고서를 보려면 **Azure Active Directory** 블레이드의 **관리**에서 **Enterprise 응용 프로그램**을 선택한 다음 **로그인**을 선택합니다.


![Enterprise 응용 프로그램 로그인 보고서](./media/active-directory-reporting-migration/199.png "Enterprise 응용 프로그램 로그인 보고서")

## <a name="next-steps"></a>다음 단계

보고 개요는 [Azure Active Directory 보고](active-directory-reporting-azure-portal.md)를 참조하세요.
