---
title: "스키마 버전 2015-08-01-미리 보기에 논리 앱을 마이그레이션하는 방법 | Microsoft 문서"
description: "논리 앱을 쉽게 최신 스키마 버전에 마이그레이션할 수 있습니다. 다음 단계를 수행합니다."
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
ms.openlocfilehash: a5a73a9f124e5339b61dbc49021444a208a471f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>스키마 버전 2015-08-01-미리 보기에 논리 앱을 마이그레이션하는 방법
기존 논리 앱을 새 스키마로 이동하려면 다음을 수행합니다.  

1. Azure 포털에서 논리 앱을 엽니다.  
2. 스키마 업데이트를 클릭합니다.
   
   ![API 아이콘][step1]   
   스키마 업데이트 페이지에서 새 스키마의 개선 사항에 대한 세부 정보를 제공하는 문서에 대한 링크를 표시하여 제공합니다. ![API 아이콘][step2]

> [!NOTE]
> **스키마 업데이트**를 선택하면 마이그레이션 단계를 자동으로 실행하고 코드 출력을 제공합니다. 정의를 업데이트하기 위해 이 기능을 사용할 수 있지만 아래의 **모범 사례** 섹션에 설명된 것처럼 좋은 코딩 방법을 따르도록 합니다.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>논리 앱을 최신 스키마 버전에 마이그레이션할 경우 모범 사례는 다음과 같습니다.
* 새 논리 앱에 마이그레이션된 스크립트 복사 - 테스트를 완성하고 예상대로 마이그레이션된 앱이 작동하는지 확인될 때까지 이전 앱을 덮어쓰지 마십시오.
* 프로덕션에 배치하기 **전에** 논리 앱을 테스트합니다.
* 마이그레이션이 완료되면 논리 앱을 업데이트하기 시작하여 가능한 경우 [관리된 API](apis-list.md) 를 사용합니다. 예를 들어 DropBox v1을 사용하는 경우 Dropbox v2를 사용하기 시작할 수 있습니다.

## <a name="whats-next"></a>다음 단계
* [논리 앱을 수동으로 마이그레이션하는 방법을 알아봅니다.](../logic-apps/logic-apps-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






