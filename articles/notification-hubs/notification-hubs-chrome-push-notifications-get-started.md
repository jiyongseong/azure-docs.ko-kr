---
title: Azure Notification Hubs를 사용하여 Chrome 앱에 알림 푸시 | Microsoft Docs
description: Azure Notification Hubs를 사용하여 Chrome 앱에 푸시 알림을 보내는 방법을 알아봅니다.
services: notification-hubs
keywords: 모바일 푸시 알림, 푸시 알림, 푸시 알림, Chrome 푸시 알림
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 75d4ff59-d04a-455f-bd44-0130a68e641f
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-chrome
ms.devlang: JavaScript
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 5754a537b8a0bf0a93d6d54ba0ba78e5957ac87f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>자습서: Azure Notification Hubs를 사용하여 Chrome 앱에 알림 푸시
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

이 자습서에서는 알림 허브를 만들고 [GCM(Google Cloud Messaging)](https://developers.google.com/cloud-messaging/)을 사용하여 샘플 Google Chrome 앱에 푸시 알림을 보내는 과정을 안내합니다. Chrome 앱은 Google Chrome 브라우저의 컨텍스트에서 실행되며 알림 허브에 등록됩니다. 

> [!NOTE]
> Chrome 앱 푸시 알림은 일반적인 브라우저 내 알림이 아닙니다. 브라우저 확장성 모델에 특정됩니다(자세한 내용은 [Chrome 앱 개요] 참조). 데스크톱 브라우저 외에도 Chrome 앱은 모바일(Android 및 iOS)에서 Apache Cordova를 통해 실행됩니다. 자세한 내용은 [모바일의 Chrome 앱]을 참조하세요.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * [Google Cloud Messaging 사용](#register)
> * [알림 허브 구성](#configure-hub)
> * [알림 허브에 Chrome 앱 연결](#connect-app)
> * [Chrome 앱에 푸시 알림 보내기](#send)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a id="register"></a>Google Cloud Messaging 사용
1. [Google 클라우드 콘솔](https://console.cloud.google.com/cloud-resource-manager) 웹 사이트로 이동하고 Google 계정 자격 증명으로 로그인합니다.
2. 도구 모음에서 **프로젝트 만들기**를 선택합니다. 

    ![프로젝트 만들기 단추](media/notification-hubs-chrome-get-started/create-project-button.png)
1. 적합한 **프로젝트 이름**을 입력하고 **만들기** 단추를 클릭합니다.
2. 도구 모음에서 알림 아이콘(종모양)을 선택하고 **프로젝트 만들기** 메시지를 선택합니다. 

    ![프로젝트 만들기 알림](media/notification-hubs-chrome-get-started/project-creation-notification.png)
1. 만든 프로젝트에 대한 **프로젝트** 페이지에서 **프로젝트 번호**를 적어 둡니다. 이 프로젝트 번호는 Chrome 앱에서 GCM에 등록하기 위해 **GCM 보낸 사람 ID**로 사용합니다.
   
    ![Google 클라우드 콘솔 - 프로젝트 번호](media/notification-hubs-chrome-get-started/gcm-project-number.png)
3. 대시보드에서 **API 개요로 이동**을 선택합니다. 

    ![API 개요로 이동 단추](media/notification-hubs-chrome-get-started/go-to-apis-overview-button.png)
1. API 및 서비스 페이지에서 **API 및 서비스 사용**을 선택합니다. 

    ![API 및 서비스 사용](media/notification-hubs-chrome-get-started/enable-apis-and-services.png)
1. **클라우드 메시징** 키워드를 사용하여 목록을 검색합니다. 필터링된 목록에서 **Google Cloud Messaging**을 선택합니다. 

    ![Google Cloud Messaging API](media/notification-hubs-chrome-get-started/google-cloud-messaging-api.png)
1. **Google Cloud Messaging** 페이지에서 **사용**을 선택합니다.

    ![GCM 사용](media/notification-hubs-chrome-get-started/enable-gcm.png)
1. **API 및 서비스** 페이지에서 **자격 증명** 탭으로 전환합니다. **자격 증명 만들기**를 선택한 후 **API 키**를 선택합니다. 

    ![API 키 만들기 단추](media/notification-hubs-chrome-get-started/create-api-key-button.png)
1. **API 키가 생성됨** 대화 상자에서 복사 단추를 선택하여 키를 클립보드에 복사합니다. 그런 후 아무 위치에나 저장합니다. 다음 섹션에서 이 값으로 알림 허브를 구성하여 GCM에 푸시 알림을 보낼 수 있습니다.

    ![API 페이지](media/notification-hubs-chrome-get-started/api-created-page.png)
12. **API 키** 목록에서 API 키를 선택합니다. API 키 페이지에서 **IP 주소(웹 서버, cron 작업 등)** 를 선택하고 IP 주소로 **0.0.0.0/0**을 입력한 후 저장을 클릭합니다. 

    ![IP 주소 사용](media/notification-hubs-chrome-get-started/enable-ip-addresses.png)

## <a id="configure-hub"></a>알림 허브 만들기
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

6. **알림 설정** 범주에서 **Google(GCM)** 을 선택하고 GCM 프로젝트에 대한 **API 키**를 입력한 후 **저장**을 클릭합니다.

      ![Azure Notification Hubs - Google(GCM)](media/notification-hubs-chrome-get-started/configure-gcm-api-key.png)

## <a id="connect-app"></a>알림 허브에 Chrome 앱 연결
이제 알림 허브가 GCM과 작동하도록 구성되었으며, 푸시 알림을 받고 보내도록 앱을 등록하기 위한 연결 문자열이 있습니다.

### <a name="create-a-new-chrome-app"></a>새 Chrome 앱 만들기
다음 샘플은 [Chrome 앱 GCM 샘플]을 기반으로 하며, 권장되는 방식을 사용하여 Chrome 앱을 만듭니다. 이 섹션에서는 Azure Notification Hubs에만 적용되는 단계를 중점적으로 설명합니다. 

> [!NOTE]
> [Chrome 앱 알림 허브 샘플]에서 이 Chrome 앱용 소스를 다운로드하는 것이 좋습니다. 

JavaScript를 사용하여 Chrome 앱을 만듭니다. 이때 원하는 단어 편집기를 사용하면 됩니다. 다음 이미지는 Chrome 앱의 모습입니다.

![Google Chrome 앱][15]

1. 폴더를 만들고 이름을 `ChromePushApp`로 지정합니다. 이름은 임의적입니다. 다른 이름을 지정하는 경우 필요한 코드 세그먼트에서 경로를 대체해야 합니다.
2. 두 번째 단계에서 만든 폴더에 [crypto-js 라이브러리] 를 다운로드합니다. 이 라이브러리 폴더에는 `components` 및 `rollups`라는 두 하위 폴더가 포함됩니다.
3. `manifest.json` 파일을 만듭니다. 모든 Chrome 앱은 앱 메타데이터 및 사용자가 설치하는 경우 앱에 부여된 권한을 모두 포함하는 매니페스트 파일에서 지원됩니다.
   
        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }
   
    이 Chrome 앱이 GCM에서 푸시 알림을 받을 수 있도록 지정하는 `permissions` 요소를 확인합니다. 또한 샘플 앱에서 원본 GCM 샘플로부터 다시 사용되는 원본에서 찾은 `gcm_128.png` 아이콘 파일도 사용합니다. [아이콘 조건](https://developer.chrome.com/apps/manifest/icons)에 맞는 이미지로 대체할 수 있습니다.
4. 다음 코드를 사용하여 `background.js` 라는 파일을 만듭니다.
   
        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }
   
        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.
   
          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);
   
          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }
   
        var registerWindowCreated = false;
   
        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {
   
            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }
   
        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);
   
        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);
   
    이 파일은 Chrome 앱 창 HTML(**register.html**) 팝업을 표시하며, 들어오는 푸시 알림을 처리하는 **messageReceived** 처리기도 정의합니다.
5. Chrome 앱의 UI를 정의하는 `register.html`이라는 파일을 만듭니다. 
   
   > [!NOTE]
   > 이 샘플은 **CryptoJS v3.1.2**를 사용합니다. 다른 버전의 라이브러리를 다운로드한 경우 `src` 경로에서 버전을 제대로 대체하도록 합니다.
   > 
   > 
   
        <html>
   
        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>
   
        <body>
   
        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>
   
        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>
   
        <br/>
   
        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>
   
        <br/>
        <br/>
   
        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>
   
        </body>
   
        </html>
6. 이 단계의 코드를 사용하여 `register.js`라는 파일을 만듭니다. 이 파일은 `register.html`의 스크립트를 지정합니다. Chrome 앱은 인라인 실행을 허용하지 않으므로 UI에 대해 별도의 지원 스크립트를 만들어야 합니다.
   
        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";
   
        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }
   
        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }
   
          document.getElementById("console").innerHTML = currentStatus  + status;
        }
   
        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);
   
          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }
   
        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;
   
          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }
   
          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;
   
          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }
   
        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }
   
        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";
   
          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });
   
          originalUri = endpoint + hubName;
        }
   
        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration
   
          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;
   
          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);
   
          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }
   
        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";
   
          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);
   
          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();
   
          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };
   
          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }
   
          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");
   
          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }
   
    스크립트에 있는 주요 매개 변수는 다음과 같습니다.
   
   * **window.onload** 는 UI에서 두 단추의 단추 클릭 이벤트를 정의합니다. 첫 번째 단추 클릭 이벤트 처리기가 GCM에 등록되고, GCM에 등록된 후에 반환되는 등록 ID를 사용하여 다른 하나의 처리기가 Azure Notification Hubs에 등록됩니다.
   * **updateLog**는 코드 로그 정보를 허용하는 함수입니다. 
   * **registerWithGCM**은 GCM에 대한 `chrome.gcm.register` 호출을 수행하여 현재 Chrome 앱 인스턴스를 등록하는 첫 번째 단추 클릭 처리기입니다.
   * **registerCallback** 은 GCM 등록 호출이 반환될 때 호출되는 콜백 함수입니다.
   * **registerWithNH** 는 Notification Hubs로 등록하는 두 번째 단추 클릭 처리기입니다. 사용자가 지정한 `hubName` 및 `connectionString`을 가져와 Notification Hubs 등록 REST API 호출을 만듭니다.
   * **splitConnectionString** 및 **generateSaSToken**은 모든 REST API 호출에서 사용되어야 하는 SaS 토큰 만들기 프로세스의 JavaScript 구현을 나타내는 도우미입니다. 자세한 내용은 [일반적인 개념](http://msdn.microsoft.com/library/dn495627.aspx)을 참조하세요.
   * **sendNHRegistrationRequest**는 Azure Notification Hubs에 대한 HTTP REST 호출을 수행하는 함수입니다.
   * **registrationPayload** 는 등록 XML 페이로드를 정의합니다. 자세한 내용은 [등록 NH REST API 만들기]를 참조하세요. GCM에서 받은 값을 사용하여 등록 ID를 업데이트합니다.
   * **client**는 응용 프로그램에서 HTTP POST 요청을 수행하는 데 사용하는 **XMLHttpRequest** 인스턴스입니다. `sasToken`을 사용하여 `Authorization` 헤더를 업데이트합니다. 이 호출이 성공적으로 완료되면 이 Chrome 앱 인스턴스가 Azure Notification Hubs에 등록됩니다.

        이 프로젝트에 대한 전체 폴더 구조는 ![Google Chrome 앱 - 폴더 구조][21]와 비슷합니다.

### <a name="set-up-and-test-your-chrome-app"></a>Chrome 앱 설치 및 테스트
1. Chrome 브라우저를 엽니다. **Chrome 확장**을 열고 **개발자 모드**를 사용하도록 설정합니다.
   
    ![Google Chrome - 개발자 모드 사용][16]
2. **압축 해제된 확장 로드** 를 클릭하고 파일을 만든 폴더로 이동합니다. 또한 선택적으로 **크롬 앱 및 확장 개발자 도구**를 사용할 수 있습니다. 이 도구는(Chrome 웹 스토어에서 설치된) Chrome 응용 프로그램 자체이며 크롬 앱 개발에 대한 고급 디버깅 기능을 제공합니다.
   
    ![Google Chrome - 압축 해제된 확장 로드][17]
3. Chrome 앱이 오류 없이 만들어지면 Chrome 앱이 표시됩니다.
   
    ![Google Chrome - Chrome 앱 표시][18]
4. 이전에 **Google 클라우드 콘솔**에서 확인한 **프로젝트 번호**를 보낸 사람 ID로 입력하고 **GCM에 등록**을 클릭합니다. **GCM로 등록 성공**
   
    ![Google Chrome - Chrome 앱 사용자 지정][19]
5. 앞에서 포털에서 확인한 **알림 허브 이름** 및 **DefaultListenSharedAccessSignature**를 입력하고 **Azure 알림 허브에 등록**을 클릭합니다. **성공적으로 알림 허브 등록!** 메시지가 표시되고 Azure Notification Hubs 등록 ID가 포함된 등록 응답 세부 정보도 함께 표시됩니다.
   
    ![Google Chrome - 알림 허브 세부 정보 지정][20]  

## <a name="send"></a>Chrome 앱에 알림 보내기
테스트를 위해 .NET 콘솔 응용 프로그램을 사용하여 Chrome 푸시 알림을 보냅니다. 

> [!NOTE]
> 공용 <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 인터페이스</a>를 통해 모든 백 엔드에서 Notification Hubs를 사용하여 푸시 알림을 보낼 수 있습니다. 더 많은 플랫폼 간 예제는 [설명서 포털](https://azure.microsoft.com/documentation/services/notification-hubs/)을 확인하세요.
> 
> 

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 선택합니다. **Visual C#** 에서 **Windows** 및 **콘솔 응용 프로그램**을 클릭하고 **확인**을 클릭합니다.  이 단계에서는 새 콘솔 응용 프로그램 프로젝트가 만들어집니다.
2. **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 아래쪽 창에 패키지 관리자 콘솔이 표시됩니다.
3. 콘솔 창에서 다음 명령을 실행합니다.
   
        Install-Package Microsoft.Azure.NotificationHubs
   
   <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지를 포함하는 Azure Service Bus SDK에 대한 참조는 프로젝트에 자동으로 추가됩니다.</a>
4. `Program.cs`을 열고 다음 `using` 문을 추가합니다.
   
        using Microsoft.Azure.NotificationHubs;
5. `Program` 클래스에 다음 메서드를 추가합니다.
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }
   
    `<hub name>` 자리 표시자를 알림 허브 페이지의 [포털](https://portal.azure.com)에 나타나는 알림 허브의 이름으로 바꿔야 합니다. 또한 연결 문자열 자리 표시자를 알림 허브 구성 섹션에서 가져온 `DefaultFullSharedAccessSignature` 이라는 연결 문자열로 바꿉니다.
   
    > [!NOTE]
    > **수신 대기** 권한이 아니라 **모든** 권한을 가진 연결 문자열을 사용해야 합니다. **수신** 액세스 연결 문자열은 푸시 알림을 보내는 권한을 부여하지 않습니다.
6. `Main` 메서드에 다음 호출을 추가합니다.
   
         SendNotificationAsync();
         Console.ReadLine();
7. Chrome을 실행하도록 하고 콘솔 응용 프로그램을 실행합니다.
8. 바탕 화면에 다음과 같은 알림 팝업이 표시됩니다.
   
    ![Google Chrome - 알림][13]
9. 또한 Chrome이 실행 중일 때 Window의 작업 표시줄에서 Chrome 알림 창을 사용하여 모든 알림을 확인할 수도 있습니다.
   
    ![Google Chrome - 알림 목록](./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png)

> [!NOTE]
> 크롬 앱을 실행하거나 브라우저에서 열 필요가 없습니다.(Chrome 브라우저 자체를 실행해야 함) 또한 Chrome 알림 창에서 모든 알림의 통합된 보기가 표시됩니다.
> 
> 

## <a name="next-steps"> </a>다음 단계
이 자습서에서는 백 엔드에 등록되어 있는 모든 클라이언트에 브로드캐스트 알림을 보냈습니다. 특정 장치로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다. 

> [!div class="nextstepaction"]
>[특정 장치에 알림 푸시](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Chrome 앱 알림 허브 샘플]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Notification Hubs Overview]: notification-hubs-push-notification-overview.md
[Chrome 앱 개요]: https://developer.chrome.com/apps/about_apps
[Chrome 앱 GCM 샘플]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[모바일의 Chrome 앱]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[등록 NH REST API 만들기]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[crypto-js 라이브러리]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud Messaging for Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Azure Notification Hubs Notify Users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Azure Notification Hubs breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
