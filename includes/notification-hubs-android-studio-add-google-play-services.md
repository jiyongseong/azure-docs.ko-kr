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
ms.openlocfilehash: 540c9775ae56798ce2d2d87fed4924244c31412f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
1. Android Studio의 도구 모음에서 아이콘을 클릭하거나 메뉴에서 **도구** > **Android** > **SDK Manager**를 클릭하여 Android SDK Manager를 엽니다. 프로젝트에서 사용되는 Android SDK의 대상 버전을 찾고, **패키지 정보 표시**를 클릭하여 열고, 아직 설치되지 않은 경우 **Google API**를 선택합니다.
2. **SDK 도구** 탭을 클릭합니다. Google Play Service를 아직 설치하지 않은 경우 아래와 같이 **Google Play Services** 를 클릭합니다. **적용** 을 클릭하여 설치합니다. 
   
    이후 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다. 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 앱 디렉터리에서 **build.gradle** 파일을 엽니다.
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. *종속성*아래에 이 줄 추가: 
    
    ```java
        compile 'com.google.android.gms:play-services-gcm:12.0.0'
    ```
5. 도구 모음에서 **Gradle 파일과 프로젝트 동기화** 아이콘을 클릭합니다.
6. **AndroidManifest.xml** 을 열고 이 태그를 *응용프로그램* 태그에 추가합니다.
   
    ```java
    <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
    ```

