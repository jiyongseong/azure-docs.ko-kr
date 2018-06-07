---
title: "Azure CDN 규칙 엔진 조건식 | Microsoft Docs"
description: "Azure CDN 규칙 엔진 일치 조건 및 기능에 대한 참조 설명서"
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Azure CDN 규칙 엔진 조건식
이 항목에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-rules-engine.md)에 대한 조건식에 대해 자세히 설명합니다.

규칙의 첫 번째 부분은 조건식입니다.

조건식 | 설명
-----------------------|-------------
IF | IF 식은 항상 규칙의 첫 번째 문의 일부입니다. 다른 모든 조건식과 마찬가지로 이 IF 문은 일치와 관련이 있어야 합니다. 추가 조건식이 정의되지 않은 경우 이 일치는 기능 집합이 요청에 적용되기 전에 충족되어야 하는 기준을 결정합니다.
AND IF | AND IF 식은 IF, AND IF 형식의 조건식 뒤에만 추가할 수 있습니다. 초기 IF 문에 대해 충족되어야 하는 다른 조건이 있음을 나타냅니다.
ELSE IF| ELSE IF 식은 이 ELSE IF 문과 관련된 기능 집합이 수행되기 전에 충족되어야 하는 대체 조건을 지정합니다. ELSE IF 문이 있으면 이전 문의 끝을 나타냅니다. ELSE IF 문 다음에 올 수 있는 유일한 조건식은 다른 ELSE IF 문입니다. 즉, ELSE IF 문은 충족해야 하는 단일 추가 조건을 지정하는 데에만 사용할 수 있습니다.

**예**: ![CDN 일치 조건](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 후속 규칙은 이전 규칙에서 지정된 동작을 재정의할 수 있습니다. 예: catch-all 규칙은 토큰 기반 인증을 통해 모든 요청을 보호합니다. 특정 요청 형식에 대한 예외를 만들기 위해 다른 규칙을 바로 아래에 만들 수 있습니다.

### <a name="next-steps"></a>다음 단계
* [Azure CDN 개요](cdn-overview.md)
* [규칙 엔진 참조](cdn-rules-engine-reference.md)
* [규칙 엔진 일치 조건](cdn-rules-engine-reference-match-conditions.md)
* [규칙 엔진 기능](cdn-rules-engine-reference-features.md)
* [규칙 엔진을 사용하여 기본 HTTP 동작 재정의](cdn-rules-engine.md)
