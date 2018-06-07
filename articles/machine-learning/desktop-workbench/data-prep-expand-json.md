---
title: Azure Machine Learning Workbench를 사용하여 JSON 변환을 확장합니다
description: "'JSON 확장' 변환에 관한 참조 문서"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 21de94d2d0d3cc12aabcb8e9e8b0eec39b0a2710
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="expand-json-transformation"></a>JSON 확장 변환
사용자는 **JSON 확장** 변환을 사용해 유효한 JSON 텍스트가 들어 있는 기존 열을 여러 열로 확장할 수 있습니다.

## <a name="how-to-perform-this-transformation"></a>이 변환을 수행하는 방법

이 변환을 수행하려면 다음 단계를 따르세요.
1. JSON 텍스트를 포함하는 원본 열을 선택합니다.
2. **변환** 메뉴에서 **JSON 확장**을 선택합니다. 또는 소스 열의 헤더를 마우스 오른쪽 단추로 클릭하거나 상황에 맞는 메뉴에서 **JSON 확장**을 선택합니다. 
3. **확인**을 클릭합니다. 
 
새 열이 원본 열 옆에 추가됩니다. 이러한 열에는 JSON 텍스트에서 계층의 다음 수준에 있는 속성이 포함됩니다. 속성 키가 있는 경우 해당 열의 이름을 만드는 데 사용 됩니다. 중첩된 속성은 사용자 필요에 따라 반복적으로 확장하거나 삭제할 수 있는 JSON 텍스트로 유지됩니다.

## <a name="examples"></a>예

원본 열 *Custome*은 두 개의 열 *Customer.Name*과 *Customer.Phone*으로 확장됩니다.

| 고객                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

