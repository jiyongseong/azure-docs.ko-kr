---
title: Azure App Service IP 제한 | Microsoft Docs
description: Azure App Service에 IP 제한을 사용하는 방법
author: btardif
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/23/2017
ms.author: byvinyal
ms.openlocfilehash: 72416cfcd05767b223cc92ac28bd0e736516ddf6
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-app-service-static-ip-restrictions"></a>Azure App Service 고정 IP 제한 #

IP 제한을 사용하여 앱 액세스가 허용되는 IP 주소 목록을 정의할 수 있습니다. 허용 목록에는 서브넷 마스크에 의해 정의되는 개별 IP 주소 또는 IP 주소 범위를 포함할 수 있습니다.

클라이언트에서 앱에 대한 요청이 생성되면 허용 목록에 대해 IP 주소가 평가됩니다. IP 주소가 목록에 없는 경우 앱은 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 응답합니다.

IP 제한은 런타임 시 앱이 사용하는 web.config에 정의됩니다.(즉, 제한은 applicationHost.config 파일의 허용된 IP 주소 집합에 삽입됩니다. 또한 web.config 파일에서 허용된 IP 주소 집합을 추가하는 경우 우선 순위가 됩니다.) 특정 상황에서 일부 모듈은 HTTP 파이프라인에서 IP 제한 논리 전에 실행될 수 있습니다. 이 경우 요청은 다른 HTTP 오류 코드로 실패합니다.

IP 제한은 앱에 할당된 것과 동일한 App Service 계획 인스턴스에서 평가됩니다.

앱에 IP 제한 규칙을 추가하려면 메뉴를 사용하여 **네트워크**>**IP 제한**을 열고 **IP 제한 구성**을 클릭합니다.

![IP 제한](media/app-service-ip-restrictions/ip-restrictions.png)  

여기서 앱에 대해 정의된 IP 제한 규칙 목록을 검토할 수 있습니다.

![IP 제한 나열](media/app-service-ip-restrictions/browse-ip-restrictions.png)

**[+] 추가**를 클릭하여 새 IP 제한 규칙을 추가할 수 있습니다.

![IP 제한 추가](media/app-service-ip-restrictions/add-ip-restrictions.png)
