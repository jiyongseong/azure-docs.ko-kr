---
title: 예약된 알림을 보내는 방법 | Microsoft Docs
description: 이 항목에서는 Azure Notification Hubs로 예약된 알림 사용에 대해 설명합니다.
services: notification-hubs
documentationcenter: .net
keywords: 푸시 알림, 푸시 알림, 푸시 알림 예약
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 0f4055a11d22604c0936685a7a2be3d56b259a5b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-send-scheduled-notifications"></a>방법: 예약된 알림 보내기
## <a name="overview"></a>개요
이후 특정 시점에 알림을 보내려는 시나리오가 있지만 알림을 보내는 백 엔드 코드를 다시 시작하는 간편한 방법이 없습니다. 표준 계층 알림 허브는 이후 최대 7일까지 알림을 예약할 수 있는 기능을 지원합니다.

알림을 보낼 때는 다음 예제에 표시된 대로 Notification Hubs SDK에서 [ScheduledNotification](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) 클래스를 사용합니다.

    Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
    var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));

또한 해당 notificationId를 사용하여 이전에 예약된 알림을 취소할 수 있습니다.

    await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);

보낼 수 있는 예약된 알림 수에는 제한이 없습니다.

