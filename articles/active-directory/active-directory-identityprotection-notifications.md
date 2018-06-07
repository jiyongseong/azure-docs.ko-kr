---
title: "Azure Active Directory ID 보호 알림 | Microsoft Docs"
description: "알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다."
services: active-directory
keywords: "Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 65ca79b9-4da1-4d5b-bebd-eda776cc32c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: bea21439afef4fda453732edffc84c62667dfe38
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD ID 보호는 두 가지 유형의 자동화된 알림 전자 메일을 보내어 사용자 위험과 위험 이벤트를 관리합니다.

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.


## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨**이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](active-directory-reporting-security-user-at-risk.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/active-directory-identityprotection-notifications/01.png)


### <a name="configuration"></a>구성

관리자로서 다음을 설정할 수 있습니다.

- **이 전자 메일의 생성을 트리거하는 위험 수준** - 기본적으로 위험 수준은 “높음” 위험으로 설정됩니다.
- **이 전자 메일의 받는 사람** - 기본적으로 받는 사람에는 모든 전역 관리자가 포함됩니다. 전역 관리자는 다른 전역 관리자, 보안 관리자, 보안 판독기를 받는 사람으로 추가할 수도 있습니다.  


관련된 대화 상자를 열려면 **ID 보호** 페이지에서 **설정** 섹션의 **경고**를 클릭합니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/active-directory-identityprotection-notifications/05.png)


## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

주 단위 요약 메일은 새로운 위험 이벤트의 요약을 포함합니다.  
다음을 포함합니다.

- 위험에 노출된 사용자

- 의심스러운 활동

- 감지된 취약점

- ID 보호에서 관련된 보고서에 대한 링크

    ![수정](./media/active-directory-identityprotection-notifications/400.png "수정")

### <a name="configuration"></a>구성

관리자로서, 주 단위 요약 메일 보내기를 끌 수 있습니다.

![사용자 위험](./media/active-directory-identityprotection-notifications/62.png "사용자 위험")

관련 대화 상자를 열려면 **ID 보호** 페이지에서 **설정** 섹션의 **주 단위 요약**을 클릭합니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/active-directory-identityprotection-notifications/04.png)


## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](active-directory-identityprotection.md)
