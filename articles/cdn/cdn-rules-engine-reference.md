---
title: "Azure CDN 규칙 엔진 참조 | Microsoft Docs"
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
ms.openlocfilehash: 602b4303dd1940791c11b8b71ac6a27f0474a6d5
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2018
---
# <a name="azure-cdn-rules-engine-reference"></a>Azure CDN 규칙 엔진 참조
이 아티클에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-rules-engine.md)에 제공되는 일치 조건 및 기능에 대해 자세히 설명합니다.

규칙 엔진은 특정 형식의 요청이 CDN에서 처리되는 방식을 최종적으로 결정하도록 설계되었습니다.

**일반적인 사용**:

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 중요한 콘텐츠에 대한 요청에 대해 보안을 유지하거나 거부합니다.
- 요청을 리디렉션합니다.
- 사용자 지정 로그 데이터를 저장합니다.

## <a name="terminology"></a>용어
규칙은 [**조건식**](cdn-rules-engine-reference-conditional-expressions.md), [**일치 조건**](cdn-rules-engine-reference-match-conditions.md) 및 [**기능**](cdn-rules-engine-reference-features.md)을 통해 정의됩니다. 이러한 요소는 다음 그림에 강조 표시되어 있습니다.

 ![CDN 일치 조건](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>구문

특수 문자가 처리되는 방식은 일치 조건 또는 기능이 텍스트 값을 처리하는 방식에 따라 다릅니다. 일치 조건 또는 기능은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

1. [**리터럴 값**](#literal-values) 
2. [**와일드카드 값**](#wildcard-values)
3. [**정규식**](#regular-expressions)

### <a name="literal-values"></a>리터럴 값
리터럴 값으로 해석되는 텍스트는 % 기호를 제외한 모든 특수 문자를 일치되어야 하는 값의 일부로 취급합니다. 즉, `\'*'\`로 설정된 리터럴 일치 조건은 정확한 값(예: `\'*'\`)을 찾은 경우에만 충족됩니다.
 
백분율 기호는 URL 인코딩을 나타내는 데 사용됩니다(예: `%20`).

### <a name="wildcard-values"></a>와일드카드 값
와일드카드 값으로 해석되는 텍스트는 특수 문자에 추가적인 의미를 할당합니다. 다음 표에서는 다음 문자 집합이 해석되는 방식을 설명합니다.

문자 | 설명
----------|------------
\ | 백슬래시는 이 테이블에 지정된 문자를 이스케이프하는 데 사용됩니다. 백슬래시는 이스케이프해야 하는 특수 문자 바로 앞에 지정되어야 합니다.<br/>예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호는 URL 인코딩을 나타내는 데 사용됩니다(예: `%20`).
* | 별표는 하나 이상의 문자를 나타내는 와일드카드입니다.
공백 | 공백 문자는 지정된 값 또는 패턴에 의해 일치 조건이 충족될 수 있음을 나타냅니다.
'value' | 작은따옴표는 특별한 의미가 없습니다. 그러나 값을 리터럴 값으로 취급해야 함을 나타내기 위해 작은따옴표 쌍을 사용합니다. 다음과 같은 방법으로 사용할 수 있습니다.<br><br/>- 지정된 값이 비교 값의 일부와 일치할 때마다 일치 조건이 충족될 수 있습니다.  예를 들어 `'ma'`는 다음 문자열 중 하나와 일치합니다. <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 특수 문자를 리터럴 문자로 지정할 수 있습니다. 예를 들어 공백 문자를 작은따옴표 쌍으로 묶어 리터럴 공백 문자를 지정할 수 있습니다(즉, `' '` 또는 `'sample value'`).<br/>- 빈 값을 지정할 수 있습니다. 작은따옴표 쌍(예: '')을 지정하여 빈 값을 지정합니다.<br /><br/>**중요:**<br/>- 지정된 값이 와일드 카드를 포함하지 않으면 자동으로 리터럴 값으로 간주됩니다. 즉, 작은따옴표 집합을 지정할 필요가 없습니다.<br/>- 백슬래시가 이 표의 다른 문자를 이스케이프하지 않으면 작은따옴표 집합으로 묶어서 지정할 때 무시됩니다.<br/>- 특수 문자를 리터럴 문자로 지정하는 또 다른 방법은 백슬래시를 사용하여 이스케이프하는 것입니다(즉, `\`).

### <a name="regular-expressions"></a>정규식

정규식은 텍스트 값 내에서 검색될 패턴을 정의합니다. 정규식 표기법은 다양한 기호에 대한 특정 의미를 정의합니다. 다음 표에서는 특수 문자가 정규식을 지원하는 일치 조건 및 기능에 의해 처리되는 방식을 나타냅니다.

특수 문자 | 설명
------------------|------------
\ | 백슬래시는 뒤에 오는 문자를 이스케이프합니다. 그러면 해당 문자가 정규식 의미를 갖지 않고 리터럴 값으로 처리됩니다. 예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호의 의미는 사용법에 따라 달라집니다.<br/><br/> `%{HTTPVariable}`: 이 구문은 HTTP 변수를 식별합니다.<br/>`%{HTTPVariable%Pattern}`: 이 구문은 백분율 기호를 사용하여 HTTP 변수를 식별하고 구분 기호로 지정합니다.<br />`\%`: 백분율 기호를 이스케이프하면 리터럴 값으로 사용되거나 URL 인코딩을 나타낼 수 있습니다(예: `\%20`).
* | 별표를 사용하면 앞에 오는 문자의 일치 여부가 0번 이상 확인될 수 있습니다. 
공백 | 공백 문자는 일반적으로 리터럴 문자로 취급됩니다. 
'value' | 작은따옴표는 리터럴 문자로 처리됩니다. 작은따옴표 쌍은 특별한 의미가 없습니다.


## <a name="next-steps"></a>다음 단계
* [규칙 엔진 일치 조건](cdn-rules-engine-reference-match-conditions.md)
* [규칙 엔진 조건식](cdn-rules-engine-reference-conditional-expressions.md)
* [규칙 엔진 기능](cdn-rules-engine-reference-features.md)
* [규칙 엔진을 사용하여 HTTP 동작 재정의](cdn-rules-engine.md)
* [Azure CDN 개요](cdn-overview.md)
