---
title: Azure Notification Hubs를 사용하여 iOS 앱에 알림 푸시 | Microsoft Docs
description: 이 자습서에서는 Azure Notification Hubs를 사용하여 iOS 응용 프로그램으로 푸시 알림을 보내는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: ios
keywords: 푸시 알림, 푸시알림,ios 푸시 알림
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 083b0c956055ab5b54a4af2eec57f096613cbe65
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 iOS 앱에 알림 푸시
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

이 자습서에서는 Azure Notification Hubs를 사용하여 iOS 응용 프로그램으로 알림을 푸시합니다. [APNS(Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 인증서 서명 요청 파일 생성
> * 푸시 알림에 대해 앱 요청
> * 앱용 프로비저닝 프로필 만들기
> * iOS 푸시 알림에 대해 알림 허브 구성
> * 알림 허브에 iOS 앱 연결
> * 테스트 푸시 알림 보내기
> * 앱에 알림을 수신되는지 확인

이 자습서에 대해 완료된 코드는 [GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted)에서 찾을 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

- 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [평가판 계정](https://azure.microsoft.com/free)을 만들 수 있습니다. 
- [Windows Azure Messaging 프레임워크]
- [Xcode]
- iOS 10 이상 지원 장치
- [Apple 개발자 프로그램](https://developer.apple.com/programs/) 멤버 자격
  
  > [!NOTE]
  > 푸시 알림에 대한 구성 요구 사항 때문에 iOS 시뮬레이터 대신 실제 iOS 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.
  
이 자습서를 완료해야 다른 모든 iOS 앱용 Notification Hubs 자습서를 진행할 수 있습니다.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>iOS 푸시 알림에 대해 알림 허브 구성
이 섹션에서는 알림 허브를 만들고, APNS와 함께 이전에 만든 **.p12** 푸시 인증서를 사용하여 인증을 구성합니다. 이미 만든 알림 허브를 사용하려는 경우 5단계로 건너뛸 수 있습니다.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-your-notification-hub-with-apns-information"></a>APNS 정보로 알림 허브 구성

1. **Notification Services** 아래에서 **Apple(APNS)** 을 선택합니다. 
2. **인증서**를 선택합니다.
3. **파일 아이콘**을 선택합니다.
4. 이전에 내보낸 **.p12** 파일을 선택합니다.
5. 올바른 **암호**를 지정합니다.
6. **샌드박스** 모드를 선택합니다. 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우에만 **프로덕션**을 사용합니다.

    ![Azure Portal에서 APNS 인증 구성][7]

이제 APNS를 사용하여 알림 허브를 구성했으며, 앱을 등록하고 푸시 알림을 보내기 위한 연결 문자열이 있습니다.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Notification Hubs에 iOS 앱 연결
1. Xcode에서 새 iOS 프로젝트를 만들고 **응용 프로그램 단일 보기** 템플릿을 선택합니다.
   
    ![Xcode - 단일 보기 응용 프로그램][8]
    
2. 새 프로젝트에 대한 옵션을 설정하는 경우 Apple Developer 포털에서 번들 식별자를 설정할 때 사용한 것과 동일한 **제품 이름** 및 **조직 식별자**를 사용해야 합니다.
   
    ![Xcode - 프로젝트 옵션][11]
    
3. [Project Navigator(프로젝트 탐색기)]에서 프로젝트 이름, **일반** 탭을 차례로 클릭한 다음 **서명**을 찾습니다. Apple Developer 계정에 적절한 팀이 선택되었는지 확인합니다. XCode는 번들 식별자에 따라 이전에 만든 프로비전 프로필을 자동으로 끌어옵니다.
   
    Xcode에서 만든 새 프로비전 프로필이 보이지 않으면 서명 ID에 대한 프로필을 새로 고칩니다. 메뉴 모음에서 **Xcode**, **기본 설정**, **계정** 탭, **세부 정보 보기** 단추, 서명 ID를 차례로 클릭한 다음 오른쪽 아래 모서리에 있는 새로 고침 단추를 클릭합니다.

    ![Xcode - 프로비전 프로필][9]

4. **기능** 탭을 선택하고 푸시 알림을 사용하도록 설정합니다.

    ![Xcode - 푸시 기능][12]
   
5. [Windows Azure Messaging 프레임워크]를 다운로드하고 파일의 압축을 풉니다. Xcode에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **파일 추가** 옵션을 클릭하여 **WindowsAzureMessaging.framework** 폴더를 Xcode 프로젝트에 추가합니다. **옵션**을 선택하고, **필요한 경우 항목 복사**가 선택되었는지 확인한 다음, **추가**를 클릭합니다.

    ![Azure SDK 압축 해제][10]

6. **HubInfo.h**라는 프로젝트에 새 헤더 파일을 추가합니다. 이 파일에는 알림 허브에 대한 상수가 들어 있습니다. 다음 정의를 추가하고 문자열 리터럴 자리 표시자를 *허브 이름* 및 앞에서 언급한 *DefaultListenSharedAccessSignature*로 바꿉니다.

    ```obj-c
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
    ```
    
7. **AppDelegate.h** 파일을 열고 다음 import 지시문을 추가합니다.

    ```obj-c
        #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
        #import <UserNotifications/UserNotifications.h> 
        #import "HubInfo.h"
    ```
8. **AppDelegate.m 파일**에서 iOS 버전에 따라 **didFinishLaunchingWithOptions** 메서드에 다음 코드를 추가합니다. 이 코드는 APNS로 장치 핸들을 등록합니다.

    ```obj-c
        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
            UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```
   
9. 동일한 파일에 다음 메서드를 추가합니다.

    ```obj-c
         - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
           SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
               if (error != nil) {
                   NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                   [self MessageBox:@"Registration Status" message:@"Registered"];
              }
          }];
         }
   
        -(void)MessageBox:(NSString *) title message:(NSString *)messageText
        {
         UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
    ```

    이 코드는 사용자가 HubInfo.h에서 지정한 연결 정보를 사용하여 알림 허브에 연결합니다. 그런 다음 알림 허브에서 알림을 보낼 수 있도록 해당 알림 허브에 장치 토큰을 제공합니다.

10. 같은 파일에서 앱이 활성 상태일 때 알림이 수신되는 경우 **UIAlert** 를 표시하려면 다음 메서드를 추가합니다.

    ```obj-c
            - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
               NSLog(@"%@", userInfo);
               [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
           }
    ```

11. 오류가 없는지 확인하려면 장치에서 앱을 빌드하고 실행합니다.

## <a name="send-test-push-notifications"></a>테스트 푸시 알림 보내기
[Azure Portal]에서 *테스트 보내기* 옵션을 사용하여 앱에서 알림 수신을 테스트할 수 있습니다. 이렇게 하면 테스트 푸시 알림이 장치로 전송됩니다.

![Azure Portal - 보내기 테스트][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## <a name="verify-that-your-app-receives-push-notifications"></a>앱에 푸시 알림이 수신되는지 확인
iOS에서 푸시 알림을 테스트하려면 실제 iOS 장치에 앱을 배포해야 합니다. iOS 시뮬레이터를 사용하여 Apple 푸시 알림을 보낼 수 없습니다.

1. 앱을 실행하고 등록이 성공했는지 확인한 다음 **확인**을 누릅니다.
   
    ![iOS 앱 푸시 알림 등록 테스트][33]
2. 다음으로, 이전 섹션에 설명한 대로 [Azure Portal]에서 테스트 푸시 알림을 보냅니다. 

3. 특정 알림 허브에서 보내는 알림을 수신하도록 등록된 모든 장치에 푸시 알림이 전송됩니다.
   
    ![iOS 앱 푸시 알림 수신 테스트][35]

## <a name="next-steps"></a>다음 단계
이 간단한 예제에서는 등록된 모든 iOS 장치로 포시 알림을 브로드캐스트합니다. 특정 iOS 장치로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
>[특정 장치에 알림 푸시](notification-hubs-ios-xplat-segmented-apns-push-notification.md)


<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Windows Azure Messaging 프레임워크]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com
