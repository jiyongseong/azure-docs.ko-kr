---
title: B2B(기업 간) 메시지에 대해 파트너 만들기 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩 및 Logic Apps와 통합 계정에 파트너를 추가하는 방법 알아보기
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 17f15c49e0f8137d5f11c57fa600588cda791c28
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>워크플로에서 기업 간 규약에 파트너 추가 또는 업데이트

파트너는 B2B(기업 간) 트랜잭션에 참여하고 서로 메시지를 교환하는 주체입니다. 사용자와 이러한 트랜잭션의 다른 조직을 나타내는 파트너를 만들려면 서로 전송한 메시지를 식별하고 유효성을 확인하는 정보를 공유해야 합니다. 이러한 세부 정보를 확인하고 비즈니스 관계를 시작할 준비가 되면 통합 계정에 쌍방을 나타내는 파트너를 만들 수 있습니다.

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>파트너는 통합 계정에서 어떤 역할을 하나요?

파트너 간에 교환되는 메시지에 대한 세부 정보를 정의하려면 해당 파트너 간의 규약을 만듭니다. 그러나 규약을 만들기 전에 통합 계정에 둘 이상의 파트너를 추가해야 합니다. 조직은 **호스트 파트너**로서 규약에 속해야 합니다. 다른 파트너 또는 **게스트 파트너**는 사용자 조직과 메시지를 교환하는 조직을 나타냅니다. 게스트 파트너는 다른 회사 또는 조직 내 다른 부서일 수 있습니다.

이러한 파트너를 추가한 후에 규약을 만들 수 있습니다.

수신 및 송신 설정은 호스트 파트너의 관점에서 시작됩니다. 예를 들어 규약의 수신 설정은 호스트 파트너가 게스트 파트너로부터 받은 메시지를 수신하는 방법을 결정합니다. 마찬가지로, 규약의 송신 설정은 호스트 파트너가 게스트 파트너에게 메시지를 보내는 방법을 나타냅니다.

## <a name="create-partner"></a>파트너 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Azure 주 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 “통합”을 입력한 다음, **통합 계정**을 선택합니다.

   ![통합 계정 찾기](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. **통합 계정**에서 파트너를 추가하려는 통합 계정을 선택합니다.

   ![통합 계정 선택](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. **파트너** 타일을 선택합니다.

   ![“파트너” 선택](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. **파트너**에서 **추가**를 선택합니다.

   !["추가" 선택](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. 파트너의 이름을 입력하고 **한정자**를 선택합니다. **값**을 입력하여 앱이 수신하는 문서를 식별합니다. 완료하면 **확인**을 선택합니다.

   ![파트너 세부 정보 추가](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. **파트너** 타일을 다시 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   새 파트너가 나타납니다. 

   ![새 파트너 보기](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>파트너 편집

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/edit.png)

2. **파트너**에서 편집할 파트너를 선택합니다.

   ![삭제할 파트너 선택](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. **파트너 업데이트**에서 필요한 내용을 변경합니다.
완료하면 **저장**을 선택합니다. 

   ![변경 후 저장](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   변경 내용을 취소하려면 **취소**를 선택합니다.

## <a name="delete-partner"></a>파트너 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 찾고 선택합니다. **파트너** 타일을 선택합니다.

   ![“파트너” 타일 선택](./media/logic-apps-enterprise-integration-partners/delete.png)

2. **파트너**에서 삭제할 파트너를 선택합니다.
**삭제**를 선택합니다.

   ![파트너 삭제](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>다음 단계

* [규약에 대해 자세히 알아보기](../logic-apps/logic-apps-enterprise-integration-agreements.md "엔터프라이즈 통합 규약에 대해 알아보기")  

