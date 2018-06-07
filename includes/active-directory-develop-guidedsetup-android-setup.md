---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 0ed42e4ace17db1e681152589cc46d82c26dddff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
## <a name="set-up-your-project"></a>프로젝트 설정

대신, 이 샘플의 Android Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)하면 코드 샘플을 실행하기 전에 구성하는 [구성 단계](#register-your-application)를 건너뛸 수 있습니다.

### <a name="create-a-new-project"></a>새 프로젝트 만들기 
1.  Android Studio를 연 후 **파일** > **새로 만들기** > **새 프로젝트**를 선택합니다.
2.  응용 프로그램 이름을 지정하고 **다음**을 선택합니다.
3.  **API 21 이상(Android 5.0)** 을 선택한 후 **다음**을 선택합니다.
4.  **빈 활동**을 그대로 유지하고 **다음**을 선택한 후 **마침**을 선택합니다.


### <a name="add-msal-to-your-project"></a>프로젝트에 MSAL 추가
1.  Android Studio에서 **Gradle Scripts** > **build.gradle(Module: app)** 을 선택합니다.
2.  **종속성** 아래에 다음 코드를 붙여넣습니다.

    ```ruby  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>이 패키지 정보

위의 코드에 있는 패키지는 Microsoft 인증 라이브러리를 설치합니다. MSAL은 Azure Active Directory v2 끝점에서 보호하는 API에 액세스하는 데 사용되는 사용자 토큰의 획득, 캐싱 및 새로 고침을 처리합니다.
<!--end-collapse-->

## <a name="create-the-application-ui"></a>응용 프로그램 UI 만들기

1. **res** > **레이아웃**으로 이동한 후 **activity_main.xml**을 엽니다. 
2. `android.support.constraint.ConstraintLayout` 등에서 `LinearLayout`으로 작업 레이아웃을 변경합니다.
3. `android:orientation="vertical"` 속성을 `LinearLayout` 노드에 추가합니다.
4. 다음 코드를 `LinearLayout` 노드에 붙여넣어 현재 내용을 바꿉니다.

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
