---
title: Logic Apps에 Facebook 커넥터 추가 | Microsoft Docs
description: REST API 매개 변수를 사용하는 Facebook 커넥터 개요
services: ''
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: estfan; ladocs
ms.openlocfilehash: b0a1e2f04b6a4c7992db582f1238be4bcc3c6174
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-facebook-connector"></a>Facebook 커넥터 시작
Facebook에 연결하여 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 수행합니다. Facebook을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* Facebook에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 새 게시를 수신할 때 트리거를 사용합니다.
* 타임라인에 게시, 페이지 피드 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 타임라인에 새 게시물이 있는 경우 해당 게시물을 가져와서 Twitter 피드에 푸시할 수 있습니다. 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-facebook"></a>Facebook에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Facebook에 연결할 권한을 논리 앱에 부여해야 합니다.

1. Facebook 계정으로 로그인
2. **권한 부여**를 선택하고 논리 앱에서 Facebook에 연결하고 사용할 수 있도록 허용합니다. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 


## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/facebook/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.