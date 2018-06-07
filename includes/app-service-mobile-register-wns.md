
1. Visual Studio 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭합니다. 그런 다음, **스토어** > **앱을 스토어에 연결**을 선택합니다.

    ![Windows 스토어에 응용 프로그램 연결](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. 마법사에서 **다음**을 선택합니다. 그런 다음, Microsoft 계정으로 로그인합니다. **앱 이름 예약**에서 앱 이름을 입력한 다음, **예약**을 선택합니다.
3. 앱을 성공적으로 등록한 후에 새로운 앱 이름을 선택합니다. **다음**을 선택한 후 **연결**을 선택합니다. 이 프로세스는 필수 Windows Store 등록 정보를 응용 프로그램 매니페스트에 추가합니다.
4. Windows 스토어 앱에서 이전에 만든 동일한 등록을 사용하여 Windows Phone 스토어 앱 프로젝트에서 1 및 3단계를 반복합니다.  
5. [Windows 개발자 센터](https://dev.windows.com/en-us/overview)로 이동한 다음, Microsoft 계정으로 로그인합니다. **내 앱**에서 새 앱 등록을 선택합니다. 그런 다음, **서비스** > **푸시 알림**을 확장합니다.
6. **푸시 알림** 페이지에서 **WNS(Windows 푸시 알림 서비스) 및 Microsoft Azure Mobile Apps** 아래의 **Live 서비스 사이트**를 선택합니다.  **응용 프로그램 암호**에서 **패키지 SID** 및 *현재* 값의 값을 기록합니다. 

    ![개발자 센터의 응용 프로그램 설정](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > 응용 프로그램 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 이러한 값은 다른 사람과 공유하지 말고 앱과 함께 분산하지 마세요.
   >
   >
