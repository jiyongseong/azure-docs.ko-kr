---
title: "B2B 통신 규약 - Azure Logic Apps | Microsoft Docs"
description: "파트너가 Azure Logic Apps 및 엔터프라이즈 통합 팩에 대한 B2B 시나리오에서 통신할 수 있도록 계약 만들기"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: LADocs
ms.openlocfilehash: 1f939c626be11a358b0f8bece8840992a4362e1f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2018
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩과의 B2B 통신에 대한 파트너 규약

규약은 산업 표준 프로토콜을 사용하여 비즈니스 엔터티가 원활하게 통신할 수 있도록 하며 B2B(기업 간) 통신의 토대입니다. 엔터프라이즈 통합 팩을 사용하는 Logic Apps에 대한 B2B 시나리오를 설정할 때 규약은 B2B 거래 파트너 간의 통신 배열에 해당합니다. 이 규약은 파트너가 설정하려고 하는 통신을 기반으로 하며 프로토콜 또는 전송별로 다릅니다.

엔터프라이즈 통합은 다음과 같은 세 가지 프로토콜 또는 전송 표준을 지원합니다.

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>규약을 사용하는 이유

규칙을 사용할 때는 몇 가지 일반적인 이점은 다음과 같습니다.

* 서로 다른 조직 및 비즈니스가 잘 알려진 형식으로 정보를 교환할 수 있습니다.
* B2B 트랜잭션을 수행할 경우 효율성을 향상시킵니다.
* 엔터프라이즈 통합 앱을 생성할 경우 쉽게 작성, 관리 및 사용할 수 있습니다.

## <a name="how-to-create-agreements"></a>규약 생성 방법

* [AS2 규약 만들기](logic-apps-enterprise-integration-as2.md)
* [X12 규약 만들기](logic-apps-enterprise-integration-x12.md)
* [EDIFACT 규약 만들기](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>규약 사용 방법

만든 규약을 사용하여 B2B 기능으로 [Logic Apps](logic-apps-overview.md "논리 앱에 대해 알아보기")를 만들 수 있습니다.

## <a name="how-to-edit-an-agreement"></a>규약 편집 방법

다음 단계를 수행하여 모든 규약을 편집할 수 있습니다.

1. 업데이트하려는 규약을 포함하는 통합 계정을 선택합니다.

2. **규약** 타일을 선택합니다.

3. **규약** 블레이드에서 규약을 선택합니다.

4. **편집**을 선택합니다. 변경을 수행합니다.

5. 변경 내용을 저장하려면 **확인**을 선택합니다.

## <a name="how-to-delete-an-agreement"></a>규약 삭제 방법

다음 단계를 수행하여 규약을 삭제할 수 있습니다.

1. 삭제하려는 규약을 포함하는 통합 계정을 선택합니다.
2. **규약** 타일을 선택합니다.
3. **규약** 블레이드에서 규약을 선택합니다.
4. **삭제**를 선택합니다.
5. 선택한 규약을 삭제할지 확인합니다.

    규약 블레이드에 삭제된 규약이 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계
* [AS2 규약 만들기](logic-apps-enterprise-integration-as2.md)
