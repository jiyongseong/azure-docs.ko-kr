---
title: "문제 해결: Azure Active Directory 활동 로그의 누락된 데이터 | Microsoft Docs"
description: "Azure Active Directory 보고에 사용할 수 있는 다양한 보고서 나열"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 55587fb4ff6d8a2130eb838782a65788fb2dd2b3
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>Azure Active Directory 활동 로그에서 수행한 일부 작업을 찾을 수 없습니다.


## <a name="symptoms"></a>증상

Azure Portal에서 일부 작업을 수행했고 `Activity logs > Audit Logs` 블레이드에서 해당 작업에 대한 감사 로그가 표시될 것을 예상했지만 찾을 수 없습니다.

 ![보고](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>원인

작업이 활동 감사 로그에 즉시 나타나지 않습니다. 포털에서 감사 로그가 표시되는 데 작업이 수행된 시간부터 15분에서 1시간이 걸릴 수 있습니다.

## <a name="resolution"></a>해결 방법

15분에서 1시간 동안 기다렸다가 로그에 작업이 표시되는지 확인합니다. 여전히 표시되지 않으면 지원 티켓을 제기해 주세요. 살펴보도록 하겠습니다.


## <a name="next-steps"></a>다음 단계
[Azure Active Directory 보고 FAQ](active-directory-reporting-faq.md)를 참조하세요.

