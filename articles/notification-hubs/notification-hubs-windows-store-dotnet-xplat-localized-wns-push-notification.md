---
title: Azure Notification Hubs를 사용하여 Windows 앱에 지역화된 알림 보내기 | Microsoft Docs
description: Azure Notification Hubs를 사용하여 지역화된 최신 뉴스 알림을 보내는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 517e7ae3871a1ed816ea407ad47c9033a1bb5a0e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-localized-notifications-to-windows-apps-by-using-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 Windows 앱에 지역화된 알림 푸시
> [!div class="op_single_selector"]
> * [Windows 스토어 C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>개요
이 자습서에서는 Notification Hubs 서비스에 등록된 모바일 장치에 지역화된 알림을 푸시하는 방법을 보여줍니다. 이 자습서에서는 [자습서: 특정 장치에 알림 보내기(유니버설 Windows 플랫폼)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)에서 만든 응용 프로그램을 업데이트하여 다음 시나리오를 지원합니다. 

- Windows 스토어 앱을 사용하면 클라이언트 장치에서 언어를 지정하고, 다른 속보 범주를 구독할 수 있습니다.
- 백 엔드 앱에서 Azure Notification Hubs의 **태그** 및 **템플릿** 기능을 사용하여 알림을 브로드캐스트합니다.

이 자습서를 완료하면 모바일 응용 프로그램을 사용하여 관심 있는 범주에 등록하고, 알림을 수신할 언어를 지정할 수도 있습니다. 백 엔드 응용 프로그램은 언어 및 장치별로 지역화된 알림을 보냅니다. 

이 자습서에서는 다음 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * 로캘 정보를 지원하도록 Windows 앱 업데이트
> * 지역화된 알림을 보내도록 백 엔드 앱 업데이트
> * 앱 테스트

## <a name="prerequisites"></a>필수 조건
[자습서: 특정 장치로 알림 보내기(유니버설 Windows 플랫폼)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)를 완료합니다. 

[자습서: 특정 장치로 알림 보내기(유니버설 Windows 플랫폼)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)에서는 **태그**를 사용하여 다른 뉴스 **범주**에 대한 알림을 구독하는 앱을 빌드했습니다. 이 자습서에서는 Notification Hubs의 **템플릿** 기능을 사용하여 **지역화된** 속보 알림을 쉽게 제공할 수 있습니다.

개괄적으로 볼 때 템플릿을 사용하면 특정 장치에서 알림을 받는 형식을 지정할 수 있습니다. 템플릿은 앱에서 백 엔드로 보낸 메시지에 포함된 속성을 참조하여 정확한 페이로드 형식을 지정합니다. 이 자습서에서는 백 엔드 응용 프로그램은 지원되는 모든 언어를 포함하는 로캘 독립적 메시지를 보냅니다.

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

장치는 올바른 속성을 참조하는 템플릿에 등록됩니다. 예를 들어, 영얼 알림 메시지를 받으려는 Windows 스토어 앱에 해당 태그가 있는 다음 템플릿을 등록합니다.

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

템플릿에 대한 자세한 내용은 [템플릿](notification-hubs-templates-cross-platform-push-messages.md) 문서를 참조하세요. 

## <a name="update-windows-app-to-support-locale-information"></a>로캘 정보를 지원하도록 Windows 앱 업데이트

1. [자습서: 특정 장치로 알림 보내기(유니버설 Windows 플랫폼)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)를 위해 만든 Visual Studio 솔루션을 엽니다. 
2. 로캘 콤보 상자를 포함하도록 **MainPage.xaml**을 업데이트합니다.

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. **Notifications** 클래스에서 **StoreCategoriesAndSubscribe** 및 **SubscribeToCateories** 메서드에 로캘 매개 변수를 추가합니다.

    ```csharp   
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    *RegisterNativeAsync* 메서드를 호출하는 대신, *RegisterTemplateAsync*를 호출합니다. 템플릿이 로캘에 종속되는 특정 알림 형식을 등록합니다. 또한 알림 메시지용 템플릿, 타일 알림용 템플릿 등 여러 템플릿을 등록할 수도 있으므로 템플릿의 이름("localizedWNSTemplateExample")을 제공합니다. 또한 템플릿을 업데이트하거나 삭제하려면 해당 이름을 지정해야 합니다.
   
    장치에서 동일한 태그로 여러 템플릿을 등록한 경우 해당 태그를 대상으로 하는 수신 메시지는 템플릿별로 하나씩 여러 알림을 장치에 제공합니다. 이 동작은 동일한 논리 메시지로 여러 시각적 알림을 나타내야 하는 경우(예: Windows 스토어 응용 프로그램에 배지와 알림을 모두 표시해야 하는 경우)에 유용합니다.
3. 다음 메서드를 추가하여 저장된 로캘을 검색합니다.
   
    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

4. **MainPage.xaml.cs**에서 아래와 같이 로캘 콤보 상자의 현재 값을 검색한 후 Notifications 클래스에 대한 호출에 값을 제공하여 단추 클릭 처리기를 업데이트합니다.
   
    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
4. 마지막으로 App.xaml.cs 파일에서 로캘을 검색하고 구독할 때 사용하도록 `InitNotificationsAsync` 메서드를 업데이트해야 합니다.

    ```csharp   
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

## <a name="send-localized-notifications-from-your-back-end"></a>백 엔드에서 지역화된 알림 보내기
템플릿 알림을 보낼 때 속성 집합만 제공하면 되지만 이 자습서에서는 백 엔드 응용 프로그램이 최신 뉴스의 지역화된 버전을 포함하는 속성 집합을 보냅니다. 예를 들면 다음과 같습니다.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

이 섹션에서는 솔루션에서 콘솔 응용 프로그램 프로젝트를 업데이트합니다. 이전에 다음 코드를 사용하여 만든 콘솔 앱에서 `SendTemplateNotificationAsync` 메서드를 수정합니다. 

> [!IMPORTANT]
> 코드에서 알림 허브에 대한 모든 권한이 있는 이름 및 연결 문자열을 지정합니다. 


```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

이 간단한 호출은 알림 허브에서 네이티브 페이로드를 생성하여 특정 태그에 가입된 모든 장치에 전달할 때 플랫폼과 상관없이 올바르게 지역화된 뉴스를 **모든** 장치에 전달합니다.

## <a name="test-the-app"></a>앱 테스트
1. 유니버설 Windows 스토어 응용 프로그램을 실행합니다. **등록 성공** 메시지가 표시될 때까지 기다립니다.

    ![모바일 응용 프로그램 및 등록](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
1. **범주** 및 **로캘**을 선택하고 **구독**을 클릭합니다. 앱은 선택한 범주를 태그로 변환하고 알림 허브에서 선택한 태그에 대한 새로운 장치 등록을 요청합니다.

    ![모바일 응용 프로그램](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
2.  **구독**에 대한 **확인** 메시지가 표시됩니다. 

    ![구독 메시지](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)
1. 확인 메시지를 받은 후에 **콘솔 앱**을 실행하여 각 범주에 대한 알림을 보내도록 지원되는 각 언어로 보냅니다. 구독한 범주에 대한 알림만 수신되는지와 메시지가 선택한 로캘에 대한 것인지 확인합니다. 

    ![알림 메시지](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)
 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 등록에 태그가 연결된 특정 장치에 지역화된 알림을 푸시하는 방법을 배웠습니다. 둘 이상의 장치를 사용할 수 있는 특정 사용자에게 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
>[특정 사용자에게 알림 푸시](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)


<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notify users with Notification Hubs: Mobile Services]: /manage/services/notification-hubs/notify-users
[Use Notification Hubs to send breaking news]: /notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
