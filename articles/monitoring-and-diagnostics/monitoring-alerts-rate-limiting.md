---
title: SMS, 메일, Azure 앱 푸시 알림 및 웹후크에 대한 속도 제한 | Microsoft Docs
description: 작업 그룹에서 가능한 SMS, 메일, Azure 앱 푸시 또는 웹후크 알림의 수를 Azure에서 제한하는 방법을 알아봅니다.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/12/2018
ms.author: dukek
ms.openlocfilehash: 9216a64dbd8201ff09ea5c9283b4db465682a3bd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>음성, SMS, 이메일, Azure 앱 푸시 알림 및 웹후크 게시물에 대한 속도 제한
속도 제한은 특정 전화 번호, 이메일 주소 또는 장치로 너무 많이 전송될 때 발생하는 알림의 일시 중단입니다. 속도를 제한하면 경고를 관리하고 실행할 수 있게 됩니다.

속도 제한 임계값은 다음과 같습니다.

 - **SMS**: 5분마다 SMS 1개 이하
 - **음성**: 5분마다 음성 1개 이하
 - **Email**: 시간 당 이메일 100개 이하.
 
 다른 작업은 속도 제한이 없습니다.

## <a name="rate-limit-rules"></a>속도 제한 규칙
- 허용된 임계값보다 더 많은 메시지를 수신하는 경우 특정 전화 번호 또는 전자 메일의 속도가 제한됩니다.
- 전화 번호 또는 전자 메일은 많은 구독에서 작업 그룹에 속할 수 있습니다. 속도 제한은 모든 구독에 걸쳐 적용됩니다. 여러 구독에서 메시지가 전송되는 경우에도 임계값에 도달하면 즉시 적용됩니다.
- 메일 주소의 속도가 제한되면 속도 제한을 알리기 위한 추가 알림이 전송됩니다. 이메일은 속도 제한이 만료되는 시점을 알려 줍니다.

## <a name="next-steps"></a>다음 단계 ##
* [SMS 경고 동작](monitoring-sms-alert-behavior.md)에 대해 자세히 알아보세요.
* [활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [서비스 상태 알림이 게시될 때마다 경고를 구성](monitoring-activity-log-alerts-on-service-notifications.md)하는 방법을 알아보세요.
