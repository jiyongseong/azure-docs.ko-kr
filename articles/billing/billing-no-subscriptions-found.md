---
title: Azure Portal 또는 Azure 계정 센터에 로그인을 시도할 때 구독을 찾을 수 없음 오류 발생 | Microsoft Docs
description: Azure Portal 또는 Azure 계정 센터에 로그인할 때 구독을 찾을 수 없음 오류가 발생하는 문제에 대한 해결 방법을 제공합니다.
services: ''
documentationcenter: ''
author: genlin
manager: jlian
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 475a4ad72a1c2fc2ebf99387e193713797cc2586
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Azure Portal 또는 Azure 계정 센터에 로그인을 시도할 때 구독을 찾을 수 없음 오류 발생

[Azure Portal](https://portal.azure.com/) 또는 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인을 시도할 때 “구독을 찾을 수 없음” 오류 메시지가 표시될 수 있습니다. 본 문서는 이 문제에 대한 해결 방법을 제공합니다.

## <a name="symptom"></a>증상

[Azure Portal](https://portal.azure.com/) 또는 [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인을 시도할 때 “구독을 찾을 수 없음” 오류 메시지가 표시됩니다.

## <a name="cause"></a>원인

잘못된 디렉터리에서 선택했거나 사용자 계정에 충분한 권한이 없는 경우 이 문제가 발생합니다. 

## <a name="solution"></a>해결 방법

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>시나리오 1: [Azure Portal](https://portal.azure.com)에서 오류 메시지가 표시됨

이 문제를 해결하려면

* 오른쪽 위에 있는 계정을 클릭하여 올바른 Azure 디렉터리를 선택했는지 확인합니다.

  ![Azure Portal 오른쪽 위에 있는 디렉터리를 선택합니다.](./media/billing-no-subscriptions-found/directory-switch.png)
* 올바른 Azure 디렉터리를 선택했으나 여전히 오류 메시지가 표시되면 [계정을 소유자로 추가](billing-add-change-azure-subscription-administrator.md)합니다.

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>시나리오 2: [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에서 오류 메시지가 표시됨

사용한 계정이 계정 관리자인지 확인합니다. 계정 관리자가 누구인지 확인하려면 다음 단계를 따르세요.

1. [Azure Portal의 구독 보기](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 로그인합니다.
1. 확인하려는 구독을 선택한 다음 **설정**에서 확인합니다.
1. **속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.  

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)하여 문제를 신속하게 해결하세요. 
