---
title: Java에서 Notification Hubs를 사용하는 방법
description: Java 백 엔드에서 Azure Notification Hubs를 사용하는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 88e3ab3cc03cc1e760672120bc5c484af1ba4722
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-use-notification-hubs-from-java"></a>Java에서 Notification Hubs를 사용하는 방법
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

이 항목에서는 완전히 지원되는 새 공식 Azure 알림 허브 Java SDK의 주요 기능에 대해 설명합니다. 이 프로젝트는 오픈 소스 프로젝트이며 [Java SDK]에서 전체 SDK 코드를 확인할 수 있습니다. 

일반적으로는 MSDN 항목 [Notification Hubs REST API](http://msdn.microsoft.com/library/dn223264.aspx)에서 설명하는 것처럼 Notification Hubs REST 인터페이스를 사용하여 Java/PHP/Python/Ruby 백 엔드에서 모든 Notification Hubs 기능에 액세스할 수 있습니다. 이 Java SDK는 Java에서 이러한 REST 인터페이스에 대한 씬 래퍼를 제공합니다. 

현재 SDK에 포함되어 있는 기능은 다음과 같습니다.

* Notification Hubs에 대한 CRUD 
* 등록에 대한 CRUD
* 설치 관리
* 등록 Import/Export
* 일반 보내기
* 예약된 보내기
* Java NIO를 통한 비동기 작업
* 지원되는 플랫폼: APNS(iOS), GCM(Android), WNS(Windows 스토어 앱), MPNS(Windows Phone), ADM(Amazon Kindle Fire), Baidu(Google 서비스가 포함되지 않은 Android) 

## <a name="sdk-usage"></a>SDK 사용
### <a name="compile-and-build"></a>컴파일 및 빌드
[Maven]

빌드하려면 다음 코드를 사용합니다.

    mvn package

## <a name="code"></a>코드
### <a name="notification-hub-cruds"></a>알림 허브 CRUD
**NamespaceManager 만들기:**

    NamespaceManager namespaceManager = new NamespaceManager("connection string")

**알림 허브 만들기:**

    NotificationHubDescription hub = new NotificationHubDescription("hubname");
    hub.setWindowsCredential(new WindowsCredential("sid","key"));
    hub = namespaceManager.createNotificationHub(hub);

 또는

    hub = new NotificationHub("connection string", "hubname");

**알림 허브 가져오기:**

    hub = namespaceManager.getNotificationHub("hubname");

**알림 허브 업데이트:**

    hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
    hub = namespaceManager.updateNotificationHub(hub);

**알림 허브 삭제:**

    namespaceManager.deleteNotificationHub("hubname");

### <a name="registration-cruds"></a>등록 CRUD
**알림 허브 클라이언트 만들기:**

    hub = new NotificationHub("connection string", "hubname");

**Windows 등록 만들기:**

    WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");    
    hub.createRegistration(reg);

**iOS 등록 만들기:**

    AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
    reg.getTags().add("myTag");
    reg.getTags().add("myOtherTag");
    hub.createRegistration(reg);

마찬가지로 Android(GCM), Windows Phone(MPNS) 및 Kindle Fire(ADM)에 대한 등록도 만들 수 있습니다.

**템플릿 등록 만들기:**

    WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
    reg.getHeaders().put("X-WNS-Type", "wns/toast");
    hub.createRegistration(reg);

**registration ID + upsert 패턴을 사용하여 등록 만들기:**

장치에 등록 ID를 저장하는 경우 응답 손실로 인한 중복 항목을 제거합니다.

    String id = hub.createRegistrationId();
    WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
    hub.upsertRegistration(reg);

**등록 업데이트:**

    hub.updateRegistration(reg);

**등록 삭제:**

    hub.deleteRegistration(regid);

**등록 쿼리:**

* **단일 등록 가져오기:**
  
        hub.getRegistration(regid);

* **허브에서 모든 등록 가져오기:**
  
        hub.getRegistrations();

* **태그가 포함된 등록 가져오기:**
  
        hub.getRegistrationsByTag("myTag");

* **채널별 등록 가져오기:**
  
        hub.getRegistrationsByChannel("devicetoken");


모든 컬렉션 쿼리는 $top 및 연속 토큰을 지원합니다.

### <a name="installation-api-usage"></a>설치 API 사용
설치 API는 등록 관리 대신 사용할 수 있는 메커니즘입니다. 올바르지 않거나 비효율적인 방식으로 수행하기 쉬운 중요한 등록을 여러 개 유지 관리하는 대신 이제는 단일 설치 개체를 사용할 수 있습니다. 이 경우 설치에는 푸시 채널(장치 토큰), 태그, 템플릿, 보조 타일(WNS 및 APNS의 경우) 등 필요한 모든 항목이 포함됩니다. 더 이상 ID를 가져오기 위해 서비스를 호출할 필요가 없으며 GUID나 다른 식별자를 생성하여 장치에 저장한 다음 푸시 채널(장치 토큰)과 함께 백 엔드로 전송하기만 하면 됩니다. 백 엔드에서는 CreateOrUpdateInstallation만 단일 호출하면 됩니다. 이 호출은 완전 멱등 방식이므로 필요한 경우 다시 시도해도 됩니다.

Amazon Kindle Fire의 경우 예제:

    Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
    hub.createOrUpdateInstallation(installation);

이 호출을 다음과 같이 업데이트할 수도 있습니다. 

    installation.addTag("foo");
    installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
    installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
    hub.createOrUpdateInstallation(installation);

고급 시나리오용으로 설치 개체의 특정 속성만 수정할 수 있는 부분 업데이트 기능을 사용합니다. 부분 업데이트는 설치 개체에 대해 실행할 수 있는 JSON 패치 작업의 하위 집합입니다.

    PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
    PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
    PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
    hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

설치 삭제:

    hub.deleteInstallation(installation.getInstallationId());

CreateOrUpdate, Patch 및 Delete의 최종 결과는 Get과 동일합니다. 요청한 작업은 호출 중에 시스템 큐로 전송되어 백그라운드에서 실행됩니다. Get은 기본 런타임 시나리오용이 아니며 디버그 및 문제 해결 전용이므로 서비스에서 엄격하게 제한됩니다.

설치의 전송 흐름은 등록에서도 동일합니다. 특정 설치에만 알림을 보내려는 경우 "InstallationId:{desired-id}" 태그를 사용하면 됩니다. 이 경우 코드는 다음과 같습니다.

    Notification n = Notification.createWindowsNotification("WNS body");
    hub.sendNotification(n, "InstallationId:{installation-id}");

여러 템플릿 중에서 하나 선택:

    Map<String, String> prop =  new HashMap<String, String>();
    prop.put("value3", "some value");
    Notification n = Notification.createTemplateNotification(prop);
    hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### <a name="schedule-notifications-available-for-standard-tier"></a>알림 예약(표준 계층에 사용 가능)
일반 전송과 같지만 알림을 배달해야 하는 시간을 지정하는 scheduledTime 매개 변수가 추가로 사용됩니다. 서비스에서는 현재 시간 + 5분 및 현재 날짜 + 7일 사이의 모든 지정 시간을 사용할 수 있습니다.

**Windows 기본 알림 예약:**

    Calendar c = Calendar.getInstance();
    c.add(Calendar.DATE, 1);    
    Notification n = Notification.createWindowsNotification("WNS body");
    hub.scheduleNotification(n, c.getTime());

### <a name="importexport-available-for-standard-tier"></a>Import/Export(표준 계층에 사용 가능)
등록에 대해 대량 작업을 수행해야 하는 경우가 있습니다. 일반적으로는 다른 시스템과 통합할 때나 태그 업데이트 등의 대량 수정을 수행하는 경우가 여기에 해당합니다. 등록 수가 매우 많은 경우에는 Get/Update 흐름을 사용하지 않는 것이 좋습니다. 이러한 시나리오에서는 Import/Export 기능을 사용합니다. 기본적으로는 들어오는 데이터의 원본과 출력의 위치로 사용할 저장소 계정의 특정 Blob 컨테이너에 대한 액세스를 제공합니다.

**내보내기 작업 제출:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ExportRegistrations);
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);


**가져오기 작업 제출:**

    NotificationHubJob job = new NotificationHubJob();
    job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
    job.setImportFileUri("input file uri with SAS signature");
    job.setOutputContainerUri("container uri with SAS signature");
    job = hub.submitNotificationHubJob(job);

**작업 완료 시까지 대기:**

    while(true){
        Thread.sleep(1000);
        job = hub.getNotificationHubJob(job.getJobId());
        if(job.getJobStatus() == NotificationHubJobStatus.Completed)
            break;
    }       

**모든 작업 가져오기:**

    List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**SAS 서명이 포함된 URI:** 이 URL은 특정 Blob 파일이나 Blob 컨테이너의 URL에 사용 권한, 만료 시간 등의 매개 변수 집합과 계정 SAS 키를 사용하여 작성된 이러한 모든 항목의 서명이 결합된 URL입니다. Azure Storage Java SDK에는 이러한 종류의 URI 만들기를 비롯하여 다양한 기능이 포함되어 있습니다. 작업을 간단하게 수행하려는 경우 github 위치에서 기본적이고 간단한 서명 알고리즘 구현을 포함하는 ImportExportE2E 테스트 클래스를 살펴보세요.

### <a name="send-notifications"></a>알림 보내기
알림 개체는 헤더, 네이티브 코드 작성 시 사용되는 몇 가지 유틸리티 메서드 및 템플릿 알림 개체를 포함하는 본문입니다.

* **Windows 스토어 및 Windows Phone 8.1(비 Silverlight)**
  
        String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
        Notification n = Notification.createWindowsNotification(toast);
        hub.sendNotification(n);
* **iOS**
  
        String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
        Notification n = Notification.createAppleNotification(alert);
        hub.sendNotification(n);
* **Android**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createGcmNotification(message);
        hub.sendNotification(n);
* **Windows Phone 8.0 및 8.1 Silverlight**
  
        String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>Hello from Java!</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
        Notification n = Notification.createMpnsNotification(toast);
        hub.sendNotification(n);
* **Kindle Fire**
  
        String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
        Notification n = Notification.createAdmNotification(message);
        hub.sendNotification(n);
* **태그로 보내기**
  
        Set<String> tags = new HashSet<String>();
        tags.add("boo");
        tags.add("foo");
        hub.sendNotification(n, tags);
* **태그 식으로 보내기**       
  
        hub.sendNotification(n, "foo && ! bar");
* **템플릿 알림 보내기**
  
        Map<String, String> prop =  new HashMap<String, String>();
        prop.put("prop1", "v1");
        prop.put("prop2", "v2");
        Notification n = Notification.createTemplateNotification(prop);
        hub.sendNotification(n);

이제 Java 코드를 실행하면 대상 장치에 나타나는 알림이 생성되어야 합니다.

## <a name="next-steps"></a>다음 단계
이 항목에서는 Notification Hubs에 대한 단순한 Java REST 클라이언트를 만드는 방법을 알아보았습니다. 여기에서 다음을 할 수 있습니다.

* 전체 SDK 코드가 포함된 전체 [Java SDK]를 다운로드합니다. 
* 다음 샘플을 사용해 봅니다.
  * [Notification Hubs 시작]
  * [속보 보내기]
  * [지역화된 속보 보내기]
  * [인증된 사용자에게 알림 보내기]
  * [인증된 사용자에게 플랫폼 간 알림 보내기]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Notification Hubs 시작]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[속보 보내기]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[지역화된 속보 보내기]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[인증된 사용자에게 알림 보내기]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[인증된 사용자에게 플랫폼 간 알림 보내기]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

