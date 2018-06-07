---
title: Azure Active Directory 보고서 보존 정책 | Microsoft Docs
description: Azure Active Directory에서 보고서 데이터 보존 정책
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/10/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9101b3877f8a011878baeed0d5c23d29fddaeaad
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055174"
---
# <a name="azure-active-directory-report-retention-policies"></a>Azure Active Directory 보고서 보존 정책


이 문서에서는 Azure Active Directory의 여러 작업 보고서에 대한 데이터 보존과 함께 가장 일반적인 질문에 대한 대답을 제공합니다. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Q: 작업 데이터 수집을 시작하려면 어떻게 해야 하나요?

**A:**

| Azure AD 버전 | 수집 시작 |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | 구독 등록 시 |
| Azure AD Free | 처음으로 [Azure Active Directory 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)를 열거나 [보고 API](https://aka.ms/aadreports)를 사용할 때  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Q: Azure Portal에서 작업 데이터를 사용할 수 있는 시기는 언제인가요?

**A:**

- **즉시** - 이미 Azure Portal에서 보고서로 작업한 적이 있는 경우
- **2시간 이내** - Azure Portal에서 보고 기능을 설정하지 않은 경우

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Q: 보안 신호 수집을 시작하려면 어떻게 해야 하나요?  

**A:** 보안 신호의 경우 ID 보호 센터를 사용하도록 옵트인할 때 수집 프로세스가 시작됩니다. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Q: 수집된 데이터의 저장 기간은 얼마나 되나요?

**A:**

**작업 보고서**    

| 보고서                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| 디렉터리 감사        | 7 일        | 30일             | 30일             |
| 로그인 작업       | 해당 없음           | 30일             | 30일             |
| Azure MFA 사용        | 30일       | 30일             | 30일             |

**보안 신호**

| 보고서         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| 위험에 노출된 사용자  | 7 일        | 30일             | 90일             |
| 위험한 로그인 | 7 일        | 30일             | 90일             |

---
