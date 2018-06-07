---
title: "Azure Active Directory ID 보호 FAQ | Microsoft Docs"
description: "Azure AD ID 보호에 대한 질문과 대답입니다."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 102cf0108aeefa255d40bd932e41f1e7b5536205
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-identity-protection-faq"></a>Azure Active Directory ID 보호 FAQ

이 문서에는 Azure AD(Azure Active Directory) Identity Protection에 대한 질문과 대답이 포함되어 있습니다. 자세한 내용은 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)을 참조하세요. 


## <a name="why-do-some-risk-events-have-closed-system-status"></a>일부 위험 이벤트에 "닫힘(시스템)" 상태가 있는 이유는 무엇인가요?

**A:** 이러한 이벤트는 Azure Active Directory ID 보호에서 검색했지만 나중에 해당 이벤트가 더 이상 위험하다고 간주되지 않아 닫힌 이벤트이며, 사용자의 위험 수준으로 계산되지 않습니다. 

---

## <a name="do-i-need-to-be-a-global-admin-to-use-identity-protection-in-the-azure-portal"></a>Azure Portal에서 ID 보호를 사용하려면 전역 관리자여야 하나요?
**A:** 아니요. 보안 읽기 권한자, 보안 관리자 또는 전역 관리자는 ID 보호를 사용할 수 있습니다.

---

## <a name="how-do-i-get-identity-protection"></a>ID 보호를 얻으려면 어떻게 해야 하나요?

**A:** 이 질문에 대한 답변은 [Azure Active Directory Premium 시작하기](active-directory-get-started-premium.md)를 참조하세요.

---

## <a name="how-can-i-sort-users-in-users-flagged-for-risk"></a>'위험에 대한 플래그가 지정된 사용자'의 사용자를 어떻게 정렬할 수 있나요?

**A:** **위험에 대한 플래그가 지정된 사용자** 페이지 위쪽에 있는 **다운로드**를 클릭하여 위험에 대한 플래그가 지정된 사용자 보고서를 다운로드합니다. 그런 다음 마지막 업데이트(UTC)를 포함하여 사용 가능한 필드를 기반으로 다운로드된 데이터를 정렬할 수 있습니다.

---
