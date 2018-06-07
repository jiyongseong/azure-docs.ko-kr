---
title: "Azure Active Directory 보고 대기 시간 | Microsoft Docs"
description: "Azure Portal에 보고 이벤트를 표시하는 데 걸리는 시간에 대해 알아보기"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: markvi;dhanyahk
ms.reviewer: dhanyahk
ms.openlocfilehash: 5ec41817fede495b8262e28d2d614a480d98ff3b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory 보고 대기 시간

Azure Active Directory에서 [보고](active-directory-preview-explainer.md)를 통해 사용자 환경의 작동 방법을 결정하는 데 필요한 모든 정보를 얻을 수 있습니다. 보고 데이터가 Azure Porta에 표시되는 데 걸리는 시간을 대기 시간이라고도 합니다. 

이 항목에서는 Azure Portal의 모든 보고 범주에 대한 대기 시간 정보를 나열합니다. 


## <a name="activity-reports"></a>작업 보고서

활동 보고에는 다음 두 가지 영역이 있습니다.

- **로그인 활동** – 관리되는 응용 프로그램 및 사용자 로그인 활동의 사용량에 대한 정보
- **감사 로그** - 사용자 및 그룹 관리, 관리되는 응용 프로그램 및 디렉터리 활동에 대한 시스템 활동 정보

다음 표에는 활동 보고서에 대한 대기 시간 정보가 나와 있습니다.

| 보고서 | 최소 | 평균 | 설명 |
| :-- | --- | --- | :-- |
| 감사 로그 | 30분  | 1시간  |일부 인스턴스에서 감사 활동 데이터가 표시되는 데 최대 2시간이 걸릴 수 있습니다.|
| 로그인 | 15분  | 2시간 |일부 인스턴스에서 로그인 활동 데이터가 표시되는 데 최대 24시간이 걸릴 수 있습니다. 여기에는 레거시 Office 응용 프로그램에서 들어오는 로그인 활동 데이터가 포함됩니다. |







## <a name="security-reports"></a>보안 보고서

보안 보고에는 다음 두 가지 영역이 있습니다.

- **위험한 로그인** - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다. 
- **위험 플래그가 지정된 사용자** - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다. 

다음 표에는 보안 보고서에 대한 대기 시간 정보가 나와 있습니다.

| 보고서 | 최소 | 평균 | 최대 |
| :-- | --- | --- | --- |
| 위험에 노출된 사용자          | 5분   | 15분  | 2시간  |
| 위험한 로그인         | 5분   | 15분  | 2시간  |

## <a name="risk-events"></a>위험 이벤트

Azure Active Directory는 적응 기계 학습 알고리즘 및 추론을 사용하여 사용자의 계정과 관련된 의심스러운 작업을 검색합니다. 검색된 각 의심스러운 동작은 위험 이벤트라는 레코드에 저장됩니다.

다음 표에는 위험 이벤트에 대한 대기 시간 정보가 나와 있습니다.

| 보고서 | 최소 | 평균 | 최대 |
| :-- | --- | --- | --- |
| 익명 IP 주소에서 로그인 |5분 |15분 |2시간 |
| 알 수 없는 위치에서 로그인 |5분 |15분 |2시간 |
| 자격 증명이 손실된 사용자 |2시간 |4시간 |8시간 |
| 비정상적 위치로 불가능한 이동 |5분 |1시간 |8시간  |
| 감염된 장치에서 로그인 |2시간 |4시간 |8시간  |
| 의심스러운 작업이 있는 IP 주소에서 로그인 |2시간 |4시간 |8시간  |



## <a name="next-steps"></a>다음 단계

Azure Portal의 활동 보고서에 자세히 알아보려면 다음을 참조하세요.

- [Azure Active Directory 포털의 로그인 활동 보고서](active-directory-reporting-activity-sign-ins.md)
- [Azure Active Directory 포털의 감사 활동 보고서](active-directory-reporting-activity-audit-logs.md)

Azure Portal의 보안 보고서에 자세히 알아보려면 다음을 참조하세요.

- [Azure Active Directory 포털에서 제공하는 위험에 노출된 사용자 보안 보고서](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 포털에서 제공하는 위험한 로그인 보고서](active-directory-reporting-security-risky-sign-ins.md)

위험 이벤트에 대한 자세한 내용은 [Azure Active Directory 위험 이벤트](active-directory-reporting-risk-events.md)를 참조하세요.
