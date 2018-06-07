
1. **App Services** 단추를 클릭하고, Mobile Apps 백 엔드를 선택하고, **빠른 시작**을 선택한 후 클라이언트 플랫폼(iOS, Android, Xamarin, Cordova)을 선택합니다.

    ![Mobile Apps 빠른 시작이 강조 표시된 Azure Portal][quickstart]

2. 데이터베이스 연결이 구성되지 않은 경우 다음을 수행하여 새로 만듭니다.

    ![Mobile Apps가 있는 Azure Portal 데이터베이스에 연결][connect]

    a. 새 SQL Database 및 서버를 만듭니다.

    ![Mobile Apps가 있는 Azure Portal 새 데이터베이스 및 서버 만들기][server]

    b. 데이터 연결을 성공적으로 만들 때까지 기다립니다.

    ![데이터 연결을 성공적으로 만들는 경우 Azure Portal 알림][notification]

    c. 데이터 연결이 성공해야 합니다.

    ![Azure Portal 알림, "이미 데이터가 연결되었습니다."][already-connection]

3. **2. 테이블 API 만들기** 아래에서 **백 엔드 언어**를 Node.js로 선택합니다. 
 
4. 승인을 허용하고 **TodoItem 테이블 만들기**를 선택합니다.  
    이 작업을 통해 데이터베이스의 테이블에 새 할 일 목록을 만듭니다. 

    >[!IMPORTANT]
    > 기존 백 엔드를 Node.js로 전환하면 모든 내용을 덮어씁니다. 대신 .NET 백 엔드를 만들려면 [Mobile Apps에 대한 .NET 백 엔드 서버 SDK 사용][instructions]을 참조하세요.

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
