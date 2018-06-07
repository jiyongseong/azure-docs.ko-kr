---
title: "Azure Security Center 검색 | Microsoft Docs"
description: "Azure Security Center에서 Log Analytics 검색을 사용하여 보안 데이터를 검색하고 분석하는 방법에 대해 알아봅니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 513c98237a322dabd6b2bf13443e8998ca843b1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-search"></a>Azure Security Center 검색
Azure Security Center에서는 [Log Analytics 검색](../log-analytics/log-analytics-log-searches.md)을 사용하여 보안 데이터를 검색하고 분석합니다. Log Analytics는 데이터를 신속하게 검색하고 통합할 수 있는 쿼리 언어를 포함합니다. Security Center에서 Log Analytics 검색을 활용하여 쿼리를 생성하고 수집된 데이터를 분석할 수 있습니다.

검색은 Security Center의 무료 계층과 표준 계층에서 모두 사용 가능합니다.  로그 검색에서 사용 가능한 데이터는 작업 영역에 적용되는 계층 수준에 따라 달라집니다.  자세한 내용은 Security Center [가격 책정 페이지](../security-center/security-center-pricing.md)를 참조하세요.


> [!NOTE]
> Security Center는 무료 계층이 적용되는 작업 영역의 보안 데이터를 저장하지 않습니다. 무료 계층이 적용되는 작업 영역으로 다양한 로그를 전송한 다음 해당 데이터를 검색할 수는 있지만, 이 경우 Security Center의 데이터는 검색 결과에 포함되지 않습니다. Security Center는 표준 계층이 적용되는 작업 영역에만 데이터를 저장합니다.
>
>

## <a name="access-search"></a>검색 액세스
1. Security Center 주 메뉴에서 **검색**을 선택합니다.

  ![로그 검색 선택][1]

2. Security Center에 Azure 구독에 포함된 모든 작업 영역이 나열됩니다. 작업 영역을 선택합니다. 작업 영역이 하나뿐인 경우 이 작업 영역 선택기가 표시되지 않습니다.

  ![작업 영역 선택][2]

3. **로그 검색**이 열립니다. 선택한 작업 영역에서 더 많은 데이터를 쿼리하려면 다음 예제 쿼리를 입력합니다.

  SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

  결과에는 로그온에 실패한 모든 계정(이벤트 4625)이 표시됩니다.

  ![검색 결과][3]

선택한 작업 영역에서 데이터를 쿼리하는 방법에 대한 자세한 내용은 [Log Analytics 쿼리 언어](../log-analytics/log-analytics-search-reference.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 검색에 액세스하는 방법을 알아보았습니다. Security Center에서는 Log Analytics 검색을 사용합니다. Log Analytics 검색에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Log Analytics란?](../log-analytics/log-analytics-overview.md) - Log Analytics의 개요를 제공합니다.
- [Log Analytics의 로그 검색 이해](../log-analytics/log-analytics-log-search-new.md) - Log Analytics에서 로그 검색이 사용되는 방식에 대해 설명하고, 로그 검색을 만들기 전에 이해해야 하는 개념을 제시합니다.
- [Log Analytics에서 로그 검색을 사용하여 데이터 찾기](../log-analytics/log-analytics-log-searches.md) – 로그 검색 사용 방법을 설명하는 자습서입니다.
- [Log Analytics 검색 참조](../log-analytics/log-analytics-search-reference.md) – Log Analytics의 쿼리 언어에 대해 설명합니다.

Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Security Center 개요](security-center-intro.md) – Security Center의 주요 기능에 대해 설명합니다.

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
