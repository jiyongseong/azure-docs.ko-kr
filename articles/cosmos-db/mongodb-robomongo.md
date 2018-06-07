---
title: Azure Cosmos DB에 Robomongo 사용 | Microsoft Docs
description: 'Azure Cosmos DB: MongoDB API 계정으로 Robomongo를 사용하는 방법을 알아봅니다.'
keywords: robomongo
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 7d318880b7b0078e4c03acb66885f4aed5534ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Azure Cosmos DB: MongoDB API 계정으로 Robomongo 사용
Robomongo를 사용하여 Azure Cosmos DB: MongoDB API 계정에 연결하려면 다음을 수행해야 합니다.

* [Robomongo](https://robomongo.org/) 다운로드 및 설치
* Azure Cosmos DB: MongoDB API 계정의 [연결 문자열](connect-mongodb-account.md) 정보가 필요합니다.

## <a name="connect-using-robomongo"></a>Robomongo를 사용하여 연결
Azure Cosmos DB: MongoDB API 계정을 Robomongo MongoDB 연결에 추가하려면 다음 단계를 수행합니다.

1. [여기](connect-mongodb-account.md)에 있는 지침을 사용하여 Azure Cosmos DB: MongoDB API 계정 연결 정보를 검색합니다.

    ![연결 문자열 블레이드의 스크린샷](./media/mongodb-robomongo/connectionstringblade.png)
2. *Robomongo.exe* 실행

3. 연결을 관리하려면 **파일**에서 연결 단추를 클릭합니다. 그런 다음 **MongoDB 연결** 창에서 **만들기**를 클릭하면 **연결 설정** 창이 열립니다.

4. **연결 설정** 창에서 이름을 선택합니다. 그런 다음 1단계의 연결 정보에서 **호스트** 및 **포트**를 찾아 **주소** 및 **포트**에 각각 입력합니다.

    ![Robomongo 연결 관리의 스크린샷](./media/mongodb-robomongo/manageconnections.png)
5. **인증** 탭에서 **인증 수행**을 클릭합니다. 그런 다음 데이터베이스(기본값은 *관리자*), **사용자 이름** 및 **암호**를 입력합니다.
**사용자 이름** 및 **암호**는 모두 1단계의 연결 정보에서 찾을 수 있습니다.

    ![Robomongo 인증 탭의 스크린샷](./media/mongodb-robomongo/authentication.png)
6. **SSL** 탭에서 **SSL 프로토콜 사용**을 선택한 다음 **인증 방법**을 **자체 서명된 인증서**로 변경합니다.

    ![Robomongo SSL 탭의 스크린샷](./media/mongodb-robomongo/SSL.png)
7. 마지막으로 **테스트**를 클릭하여 연결할 수 있는지 확인한 다음 **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB: MongoDB API [샘플](mongodb-samples.md)을 살펴봅니다.
