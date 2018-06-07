---
title: 응용 프로그램 프록시 응용 프로그램을 만들 때 문제 발생 | Microsoft 문서
description: Azure AD 관리 포털에서 응용 프로그램 프록시 응용 프로그램을 만들 때 발생하는 문제를 해결하는 방법
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 376925715e954904cfdadccb060d0ca242bbec4a
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="problem-creating-an-application-proxy-application"></a>응용 프로그램 프록시 응용 프로그램을 만들 때 문제 발생 

다음은 새로운 응용 프로그램 프록시 응용 프로그램을 만들 때 발생하는 몇 가지 일반적인 문제입니다.

## <a name="recommended-documents"></a>권장되는 문서 

관리 포털을 통해 응용 프로그램 프록시 응용 프로그램을 만드는 방법에 대한 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](manage-apps/application-proxy-publish-azure-portal.md)를 참조하세요.

해당 문서의 단계에 따라 응용 프로그램을 만드는 중 오류가 발생하면 오류 세부 정보에서 응용 프로그램 문제 해결 방법에 대한 정보 및 제안 사항을 참조하세요. 대부분의 오류 메시지에는 제안 수정이 포함되어 있습니다. 

## <a name="specific-things-to-check"></a>확인할 항목

일반적인 오류를 방지하려면 다음을 확인합니다.

-   응용 프로그램 프록시 응용 프로그램을 만들 수 있는 권한을 가진 관리자여야 합니다.

-   내부 URL이 고유해야 합니다.

-   외부 URL이 고유해야 합니다.

-   URL이 http 또는 https로 시작하고 "/"로 끝나야 합니다.

-   URL은 IP 주소가 아닌 도메인 이름이어야 합니다.

응용 프로그램을 만들 때 오류 메시지가 오른쪽 상단 모서리에 표시됩니다. 알림 아이콘을 선택하여 오류 메시지를 볼 수도 있습니다.

   ![알림 프롬프트](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>다음 단계
[Azure Portal에서 응용 프로그램 프록시 사용](manage-apps/application-proxy-enable.md)
