---
title: Azure Cosmos DB에서 액세스 제어 설정 | Microsoft Docs
description: Azure Cosmos DB에서 액세스 제어를 설정하는 방법을 알아봅니다.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: fae3af3f-4d6e-46d8-9d9b-b80a4218add9
ms.service: cosmos-db
ms.topic: article
ms.date: 02/06/2018
ms.author: sngun
ms.openlocfilehash: 740f1ca560207ada95dd03fbecc7f9940ee7b2a0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="access-control-in-azure-cosmos-db"></a>Azure Cosmos DB에서 액세스 제어

사용자 계정에 Azure Cosmos DB 계정 독자 액세스를 추가하려면 구독 소유자가 Azure Portal에서 다음 단계를 수행하도록 합니다.

1. Azure Portal을 열고, Azure Cosmos DB 계정을 선택합니다.
2. **Access Control(IAM)** 탭을 클릭한 다음, **+ 추가**를 클릭합니다.
3. **사용 권한 추가** 창의 **역할** 상자에서 **Cosmos DB 계정 독자 역할**을 선택합니다.
4. **다음에 대한 액세스 할당** 상자에서 **Azure AD 사용자, 그룹 또는 응용 프로그램**을 선택합니다.
5. 액세스 권한을 부여하려는 디렉터리에서 사용자, 그룹 또는 응용 프로그램을 선택합니다.  표시 이름, 이메일 주소 또는 개체 식별자로 디렉터리를 검색할 수 있습니다.
    선택한 사용자, 그룹 또는 응용 프로그램이 선택한 멤버 목록에 나타납니다.
6. **저장**을 클릭합니다.

이제 엔터티는 Azure Cosmos DB 리소스를 읽을 수 있습니다.
