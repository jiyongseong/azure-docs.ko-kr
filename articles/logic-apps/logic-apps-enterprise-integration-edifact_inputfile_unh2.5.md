---
title: "Logic Apps B2B EDIFACT 디코딩 확인 UNH2.5 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps B2B EDIFACT 디코딩 확인 UNH2.5"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 62ad8183cc6e9f56255b2729a04ee7710d00a21a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>UNH2.5 세그먼트를 포함하는 EDIFACT 문서를 처리하는 방법
EDIFACT 문서에 UNH2.5가 표시되면 스키마 조회에 사용되고 있는 것입니다. 

예제: UNH 필드는 EDIFACT 메시지에서 **EAN008**입니다.  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

메시지를 처리하기 위해 수행하는 단계 
1. 스키마 업데이트
2. 규약 설정 확인  

## <a name="update-the-schema"></a>스키마 업데이트
메시지를 처리하려면 UNH2.5 루트 노드 이름을 갖는 스키마를 배포해야 합니다.  예제에서 스키마 루트 이름은 **EFACT_D03B_ORDERS_EAN008**입니다.  

다른 UNH2.5 세그먼트를 갖는 각 D03B_ORDERS에 대해 개별 스키마를 배포해야 합니다.  

## <a name="add-schema-to-the-edifact-agreement"></a>EDIFACT 규약에 스키마 추가
### <a name="edifact-decode"></a>EDIFACT 디코딩
들어오는 메시지를 디코딩하려면 EDIFACT 규약 수신 설정에서 스키마를 구성합니다.
1. 통합 계정에 스키마를 추가합니다.    
2. EDIFACT 규약 수신 설정에서 스키마를 구성합니다. 
3. EDIFACT 규약을 선택하고 **JSON으로 편집**을 클릭합니다.  수신 규약 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)에 UNH2.5 값을 추가합니다.

### <a name="edifact-encode"></a>EDIFACT 인코딩
들어오는 메시지를 인코딩하려면 EDIFACT 규약 송신 설정에서 스키마를 구성합니다.
1. 통합 계정에 스키마를 추가합니다.    
2. EDIFACT 규약 송신 설정에서 스키마를 구성합니다. 
3. EDIFACT 규약을 선택하고 **JSON으로 편집**을 클릭합니다.  송신 규약 **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)에 UNH2.5 값을 추가합니다.

## <a name="next-steps"></a>다음 단계
* [통합 계정 규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  