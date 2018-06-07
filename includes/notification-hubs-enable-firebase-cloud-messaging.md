---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
1. [Firebase 콘솔](https://firebase.google.com/console/)에 로그인합니다. 아직 없는 경우 새 Firebase 프로젝트를 만듭니다.
2. 프로젝트를 만든 후 **Android 앱에 Firebase 추가**를 선택합니다. 그런 후 제공되는 지침을 따릅니다. **google-services.json** 파일을 다운로드합니다. 

    ![Android 앱에 Firebase 추가](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Firebase 콘솔에서 프로젝트의 톱니바퀴를 선택합니다. 그런 후 **프로젝트 설정**을 선택합니다.

    ![프로젝트 설정 선택](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. 프로젝트 설정에서 **일반** 탭을 선택합니다. 그런 후 서버 API 키와 클라이언트 ID가 포함된 **google-services.json** 파일을 다운로드합니다.
5. 프로젝트 설정에서 **클라우드 메시지** 탭을 선택한 다음, **레거시 서버 키**의 값을 복사합니다. 이 값은 알림 허브 액세스 정책을 구성하는 데 사용됩니다.
