1. **앱** 프로젝트에서 `AndroidManifest.xml` 파일을 엽니다. 다음 코드를 `application` 시작 태그 뒤에 추가합니다.

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. `ToDoActivity.java` 파일을 열어 다음과 같이 변경합니다.

    - Import 문 추가:

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - `MobileServiceClient` 정의를 **private**에서 **private static**으로 변경하면 이제 다음과 같이 표시됩니다.

        ```java
        private static MobileServiceClient mClient;
        ```

    - `registerPush` 메서드 추가:

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - `ToDoActivity` 클래스의 **onCreate** 메서드를 업데이트합니다. `MobileServiceClient`가 인스턴스화된 후에 이 코드를 추가해야 합니다.

        ```java
        registerPush();
        ```

3. 알림을 처리하는 새 클래스를 추가합니다. 프로젝트 탐색기에서 **app** > **java** > **your-project-namespace** 노드를 열고 패키지 이름 노드를 마우스 오른쪽 단추로 클릭합니다. **새로 만들기**를 클릭한 후 **Java Class**를 클릭합니다. 이름에 `ToDoMessagingService`를 입력하고 [확인]을 클릭합니다. 그런 다음 클래스 선언을 다음으로 바꿉니다.

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. 토큰 업데이트를 처리할 다른 클래스를 추가합니다. `ToDoInstanceIdService` java 클래스를 만들고 클래스 선언을 다음으로 바꿉니다.

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.
