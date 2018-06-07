---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 19352df7abff23ed44521a11e7907c84c8c0327f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33835851"
---
이 섹션에서는 .NET 콘솔 앱에서 태그가 지정된 템플릿 알림으로 속보를 보냅니다. 

1. Visual Studio에서 다음과 같이 새로운 Visual C# 콘솔 응용 프로그램을 만듭니다.
   
      ![콘솔 응용 프로그램 링크][13]

2. Visual Studio 주 메뉴에서 **도구** > **라이브러리 패키지 관리자** > **패키지 관리자 콘솔**을 차례로 선택한 후 콘솔 창에서 다음 문자열을 입력합니다.
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. **Enter** 키를 선택합니다.  
    이 작업은 [Microsoft.Azure.Notification Hubs NuGet 패키지]를 사용하는 Azure Notification Hubs SDK에 대한 참조를 추가합니다.

4. Program.cs 파일을 열고 다음 `using` 문을 추가합니다.
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. `Program` 클래스에서 다음 메서드를 추가하거나 이미 있으면 바꿉니다.
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    이 코드는 문자열 배열에 있는 6개의 각 태그에 대한 템플릿 알림을 보냅니다. 태그를 사용하면 등록된 범주의 알림만 장치에서 받습니다.

5. 위의 코드에서 `<hub name>` 및 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 알림 허브의 대시보드에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다.

6. **Main** 메서드에 다음 줄을 추가합니다.
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. 콘솔 앱을 시작합니다.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet 패키지]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
