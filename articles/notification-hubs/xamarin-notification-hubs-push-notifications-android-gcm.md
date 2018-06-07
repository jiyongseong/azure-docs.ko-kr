---
title: Azure Notification Hubs를 사용하여 Xamarin.Android 앱에 알림 푸시 | Microsoft Docs
description: 이 자습서에서 Azure Notification Hubs를 사용하여 Xamarin.Android 응용 프로그램에 푸시 알림을 보내는 방법을 알아봅니다.
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: f75671e2e5511054f3db550a8c24e62d031492c3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-xamarinandroid-apps-using-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 Xamarin.Android 앱에 알림 푸시
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Azure Notification Hubs를 사용하여 Xamarin.Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. FCM(Firebase Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Xamarin.Android 앱을 만듭니다. 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트합니다. 완성된 코드는 [NotificationHubs 앱][GitHub] 샘플에서 사용할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Firebase 프로젝트 생성 및 Firebase Cloud Messaging 사용
> * 알림 허브 만들기
> * Xamarin.Android 앱을 생성한 후 알림 허브에 연결
> * Azure Portal에서 테스트 알림 전송

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Windows의 경우 [Xamarin이 포함된 Visual Studio] 또는 OS X의 경우 [Mac용 Visual Studio]
- 활성 Google 계정

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Firebase 프로젝트 생성 및 Firebase Cloud Messaging 사용
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>알림 허브 만들기
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-settings-for-the-notification-hub"></a>알림 허브에 대한 GCM 설정 구성

1. **알림 설정** 섹션에서 **Google(GCM)** 을 선택합니다. 
2. Google Firebase 콘솔에서 적어둔 **레거시 서버 키**를 입력합니다. 
3. 도구 모음에서 **저장**을 선택합니다. 

    ![](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

알림 허브가 FCM과 작동하도록 구성되었으며, 알림을 받고 푸시 알림을 보내기 위해 앱을 등록하는 연결 문자열이 있습니다.

## <a name="create-xamainandroid-app-and-connect-it-to-notification-hub"></a>Xamarin.Android 앱을 생성한 후 알림 허브에 연결

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio 프로젝트 생성 및 NuGet 패키지 설치
1. Visual Studio에서 **파일**을 가리킨 후 **새로 만들기**를 선택하고 **프로젝트**를 선택합니다. 
   
      ![Visual Studio - 새 Android 프로젝트 만들기](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog.png)
2. **솔루션 탐색기** 창에서 **속성**을 확장하고 **AndroidManifest.xml**을 클릭합니다. Google Firebase 콘솔에서 Firebase Cloud Messaging을 프로젝트에 추가할 때 입력한 패키지 이름과 일치하도록 패키지 이름을 업데이트합니다. 

      ![GCM의 패키지 이름](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
3. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 
4. **찾아보기** 탭을 선택합니다. **Xamarin.GooglePlayServices.Base**를 검색합니다. 결과 목록에서 **Xamarin.GooglePlayServices.Base**를 선택합니다. 그런 후 **설치**를 선택합니다. 

      ![Google Play 서비스 NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
5. **NuGet 패키지 관리자** 창에서 **Xamarin.Firebase.Messaging**을 검색합니다. 결과 목록에서 **Xamarin.Firebase.Messaging**을 선택합니다. 그런 후 **설치**를 선택합니다. 
6. 이제 **Xamarin.Azure.NotificationHubs.Android**를 검색합니다. 결과 목록에서 **Xamarin.Azure.NotificationHubs.Android**를 선택합니다. 그런 후 **설치**를 선택합니다. 

### <a name="add-the-google-services-json-file"></a>Google Services JSON 파일 추가

1. Google Firebase 콘솔에서 다운로드한 **google-services.json**을 프로젝트 폴더로 복사합니다.
2. **google-services.json**을 프로젝트에 추가합니다.
3. **솔루션 탐색기** 창에서 **google-services.json**을 선택합니다.
4. **속성** 창에서 빌드 작업을 **GoogleServicesJson**으로 설정합니다. **GoogleServicesJson**이 표시되지 않으면 Visual Studio를 닫았다가 다시 시작한 후 프로젝트를 다시 열고 다시 시도하세요. 

      ![GoogleServicesJson 빌드 작업](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>프로젝트에서 알림 허브 설정

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging 등록

**AndroidManifest.xml** 파일을 열고 다음 `<receiver>` 요소를 `<application>` 요소로 삽입합니다.

        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
        <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
          <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
          </intent-filter>
        </receiver>

1. Android 앱 및 알림 허브에 대해 다음 정보를 수집합니다.
   
   * **연결 문자열 수신 대기**: [Azure Portal]의 대시보드에서 **연결 문자열 보기**를 선택합니다. 이 값에 대해 *DefaultListenSharedAccessSignature* 연결 문자열을 복사합니다.
   * **허브 이름**: [Azure Portal]의 허브 이름입니다. 예를 들어 *mynotificationhub2*입니다.
     
2. **솔루션 탐색기** 창에서 **프로젝트**를 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 후 **클래스**를 선택합니다. 
4. Xamarin 프로젝트에 대해 **Constants.cs** 클래스를 만들고 클래스에 다음 상수 값을 정의합니다. 자리 표시자는 해당 값으로 바꿉니다.
    
    ```csharp
        public static class Constants
        {
           public const string ListenConnectionString = "<Listen connection string>";
           public const string NotificationHubName = "<hub name>";
        }
    ```
3. **MainActivity.cs**에 다음 using 문을 추가합니다.
   
    ```csharp
        using Android.Util;
    ```
4. 앱이 실행되고 있을 때 경고 대화 상자를 표시하는 데 사용할 인스턴스 변수를 **MainActivity.cs**에 추가합니다.
   
    ```csharp
        public const string TAG = "MainActivity";
    ```
5. **MainActivity.cs**의 `OnCreate`에서 `base.OnCreate(savedInstanceState)` 다음에 다음 코드를 추가합니다.

    ```csharp   
        if (Intent.Extras != null)
        {
            foreach (var key in Intent.Extras.KeySet())
            {
                if(key!=null)
                {
                    var value = Intent.Extras.GetString(key);
                    Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
                }
            }
        }
    ```
7. **Constants** 클래스를 만들 때처럼 새 클래스 **MyFirebaseIIDService**를 만듭니다. 
8. **MyFirebaseIIDService.cs**에 다음 using 문을 추가합니다.
   
    ```csharp
    using Android.Util;
    using WindowsAzure.Messaging;
    using Firebase.Iid;
    ```

9. **MyFirebaseIIDService.cs**에서 다음 **class** 선언을 추가하고, **FirebaseInstanceIdService**에서 클래스를 상속받도록 설정합니다.
   
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
        public class MyFirebaseIIDService : FirebaseInstanceIdService
    ```
10. **MyFirebaseIIDService.cs**에 다음 코드를 추가합니다.
   
    ```csharp
        const string TAG = "MyFirebaseIIDService";
        NotificationHub hub;

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "FCM token: " + refreshedToken);
            SendRegistrationToServer(refreshedToken);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                      Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
11. 프로젝트에 대한 또 다른 클래스를 새로 만들고, 이름을 **MyFirebaseMessagingService**로 지정합니다.
12. **MyFirebaseMessagingService.cs**에 다음 using 문을 추가합니다.
    
    ```csharp
        using Android.Util;
        using Firebase.Messaging;
    ```
13. class 선언 위에 다음을 추가하고, **FirebaseMessagingService**에서 클래스를 상속받도록 설정합니다.
    
    ```csharp
        [Service]
        [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
        public class MyFirebaseMessagingService : FirebaseMessagingService
    ```    
14. **MyFirebaseMessagingService.cs**에 다음 코드를 추가합니다.
    
    ```csharp
        const string TAG = "MyFirebaseMsgService";
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if(message.GetNotification()!= null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else 
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());

            }

        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new Notification.Builder(this)
                        .SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetContentIntent(pendingIntent);

            var notificationManager = NotificationManager.FromContext(this);

            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```
15. 프로젝트를 **빌드**합니다. 
16. 장치 또는 로드된 에뮬레이터에서 앱을 **실행**합니다.

## <a name="send-test-notification-from-the-azure-portal"></a>Azure Portal에서 테스트 알림 전송
[Azure Portal]에서 *테스트 보내기* 옵션을 사용하여 앱에서 알림 수신을 테스트할 수 있습니다. 이렇게 하면 테스트 푸시 알림이 장치로 전송됩니다.

![Azure Portal - 보내기 테스트](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

푸시 알림은 일반적으로 Mobile Services 또는 ASP.NET과 같은 백 엔드 서비스에서 호환되는 라이브러리를 통해 보내집니다. 백 엔드에서 라이브러리를 사용할 수 없는 경우 REST API를 직접 사용하여 알림 메시지를 보낼 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 백 엔드에 등록된 모든 Android 장치로 브로드캐스트 알림을 보냈습니다. 특정 Android 장치로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
>[특정 장치에 알림 푸시](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)


<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Xamarin이 포함된 Visual Studio]: https://docs.microsoft.com/visualstudio/install/install-visual-studio
[Mac용 Visual Studio]: https://www.visualstudio.com/vs/visual-studio-mac/

[Azure Portal]: https://portal.azure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /manage/services/notification-hubs/breaking-news-dotnet
[GitHub]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid
