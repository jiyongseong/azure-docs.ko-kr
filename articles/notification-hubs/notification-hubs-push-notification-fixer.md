---
title: Azure Notification Hubs 삭제된 알림 진단
description: Azure Notification Hubs의 삭제된 알림과 관련된 일반적인 문제를 진단하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: Mobile
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: bc9ef70560f0485da81c1f54aa955cee76d280ab
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Notification Hubs에서 삭제된 알림 진단

Azure Notification Hubs 고객이 가장 자주 하는 질문 중 하나는 응용 프로그램에서 보낸 알림이 클라이언트 장치에 나타나지 않는 문제를 해결하는 방법입니다. 고객은 알림이 삭제되는 위치와 이유 그리고 문제 해결 방법을 알고 싶어합니다. 이 문서에서는 알림이 삭제되거나 장치에서 알림을 받지 못하는 이유를 알아봅니다. 근본 원인을 분석하고 확인하는 방법을 알아봅니다. 

먼저 Notification Hubs 서비스가 장치에 알림을 푸시하는 방법을 이해해야 합니다.

![Notification Hubs 아키텍처][0]

일반적인 알림 보내기 흐름에서는 메시지가 *응용 프로그램 백 엔드*에서 Notification Hubs로 전송됩니다. Notification Hubs는 모든 등록에서 일부 처리를 수행합니다. 처리 시 구성된 태그 및 태그 식을 고려하여 "대상"이 결정됩니다. 푸시 알림을 받아야 하는 모든 등록이 대상입니다. 이러한 등록은 지원되는 모든 플랫폼(iOS, Google, Windows, Windows Phone, Kindle 및 중국 Android용 Baidu)에 적용될 수 있습니다.

대상이 설정되면 Notification Hubs 서비스는 장치 플랫폼에 대한 *푸시 알림 서비스*에 알림을 푸시합니다. APNs(Apple Push Notification service) for Apple 및 FCM(Firebase Cloud Messaging) for Google을 예로 들 수 있습니다. Notification Hubs는 여러 등록 일괄 처리에 걸쳐 분할된 알림을 푸시합니다. Notification Hubs는 Azure Portal의 **알림 허브 구성**에 설정된 자격 증명을 기반으로 각 푸시 알림 서비스를 인증합니다. 그러면 푸시 알림 서비스가 각 *클라이언트 장치*에 알림을 전달합니다. 

최종 알림 배달 레그는 플랫폼 푸시 알림 서비스와 장치 사이에서 발생합니다. 푸시 알림 프로세스의 네 가지 주요 구성 요소(클라이언트, 응용 프로그램 백 엔드, Notification Hubs 및 플랫폼 푸시 알림 서비스) 때문에 알림이 삭제될 수 있습니다. Notification Hubs 아키텍처에 대한 자세한 내용은 [Notification Hubs 개요]를 참조하세요.

초기 테스트/준비 단계에서 알림 배달이 실패할 수 있습니다. 이 단계에서 알림이 삭제될 경우 구성이 잘못된 것일 수 있습니다. 프로덕션 환경에서 알림 배달이 실패할 경우 알림의 일부 또는 전체가 삭제될 수 있습니다. 이 경우 보다 근본적인 응용 프로그램 또는 메시지 패턴 문제가 있는 것입니다. 

다음 섹션에서는 일반적인 문제부터 희귀한 문제까지 알림이 삭제될 수 있는 시나리오를 살펴보겠습니다.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs 구성 오류
각 푸시 알림 서비스에 알림을 성공적으로 보내려면 Notification Hubs 서비스는 개발자의 응용 프로그램 컨텍스트에서 자신을 인증해야 합니다. 그러기 위해 개발자는 각 플랫폼(Google, Apple, Windows 등)으로 개발자 계정을 만듭니다. 그런 다음 자격 증명을 받게 되는 플랫폼에 응용 프로그램을 등록합니다. 

Azure Portal에 플랫폼 자격 증명을 추가해야 합니다. 장치에 도달하는 알림이 하나도 없을 때 가장 먼저 할 일은 Notification Hubs에서 올바른 자격 증명이 구성되었는지 확인하는 것입니다. 자격 증명이 플랫폼별 개발자 계정에서 만든 응용 프로그램과 일치해야 합니다. 

이 프로세스를 완료하는 단계별 지침은 [Azure Notification Hubs 시작]을 참조하세요.

다음과 같은 구성 오류를 확인해야 합니다.

* **일반**
   
    * 다음 위치에서 알림 허브 이름(오타 없이)이 동일한지 확인합니다.

        * 클라이언트에서 등록하는 위치.
        * 백 엔드에서 알림을 보내는 위치.
        * 푸시 알림 서비스 자격 증명을 구성한 위치.
    
    * 클라이언트 및 응용 프로그램 백 엔드에서 올바른 공유 액세스 서명 구성 문자열을 사용해야 합니다. 일반적으로 클라이언트에서는 **DefaultListenSharedAccessSignature**를 사용해야 하고 응용 프로그램 백 엔드에서는 **DefaultFullSharedAccessSignature**를 사용해야 합니다(Notification Hubs에 알림을 보낼 수 있는 권한 부여).

* **APNs 구성**
   
    프로덕션에 사용되는 허브와 테스트에 사용되는 허브, 총 2개의 허브를 유지해야 합니다. 즉, 샌드박스 환경에서 사용할 인증서를 별도의 허브에 업로드한 다음 프로덕션에 사용할 인증서를 별도의 허브에 업로드해야 합니다. 같은 허브에 다른 종류의 인증서를 업로드하지 마세요. 알림이 실패할 수 있습니다. 
    
    실수로 같은 허브에 다른 종류의 인증서를 업로드한 경우 허브를 삭제하고 새 허브로 새로 시작하는 것이 좋습니다. 어떤 이유로 허브를 삭제할 수 없는 경우 적어도 허브에서 기존 등록을 모두 삭제해야 합니다. 

* **FCM 구성** 
   
    1. Firebase에서 얻은 *서버 키*가 Azure Portal에서 등록한 서버 키와 일치하는지 확인합니다.
   
    ![Firebase 서버 키][3]
   
    2. 클라이언트에서 **프로젝트 ID**를 구성했는지 확인합니다. **프로젝트 ID** 값은 Firebase 대시보드에서 얻을 수 있습니다.
   
    ![Firebase 프로젝트 ID][1]

## <a name="application-issues"></a>응용 프로그램 문제
* **태그 및 태그 식**

    태그 또는 태그 식을 사용하여 청중을 분할하는 경우 알림을 보낼 때 송신 호출에서 지정하는 태그 또는 태그 식을 기반으로 찾을 수 있는 대상이 없을 수도 있습니다. 
    
    알림을 보낼 때 일치하는 태그가 있도록 등록을 검토합니다. 그런 다음 해당 등록을 사용하는 클라이언트의 알림 수신만 확인합니다. 
    
    예를 들어 Notification Hubs를 사용한 모든 등록에 "정치" 태그를 지정하고 "스포츠" 태그가 지정된 알림을 보내면 어떤 장치에도 알림이 전송되지 않습니다. "태그 A" 또는 "태그 B"를 사용하여 등록했지만 알림을 보낼 때 "태그 A && 태그 B"를 대상으로 알림을 보내는 복잡한 태그 식도 있을 수 있습니다. 본 문서 뒷부분의 자체 진단 팁 섹션에서 등록 및 태그를 검토하는 방법을 보여드리겠습니다. 

* **템플릿 문제**

    템플릿을 사용하는 경우 [템플릿]에 설명된 지침을 따릅니다. 

* **잘못된 등록**

    알림 허브가 올바르게 구성되고 태그 또는 태그 식이 올바르게 사용된다면 유효한 대상이 발견됩니다. 이러한 대상에 알림을 보내야 합니다. 그런 다음, Notification Hubs 서비스가 여러 일괄 처리 프로세스를 병렬로 시작합니다. 각 일괄 처리는 등록 집합에 메시지를 보냅니다. 

    > [!NOTE]
    > 처리가 병렬로 수행되므로 알림이 배달되는 순서는 보장되지 않습니다. 

    Notification Hubs는 "최대 한 번의" 메시지 배달 모델에 최적화되었습니다. 우리는 장치에 알림이 두 번 이상 전달되지 않도록 중복 제거를 시도합니다. 이렇게 하기 위해 푸시 알림 서비스에 메시지를 보내기 전에 등록을 확인하고 장치 식별자당 하나의 메시지만 보냈는지 확인합니다. 

    각 일괄 처리가 푸시 알림 서비스에 전송되어 등록을 수락하고 유효성을 검사한 후에는 푸시 알림 서비스가 일괄 처리에서 등록이 하나 이상인 오류를 검색할 수 있습니다. 이 경우 푸시 알림 서비스가 Notification Hubs에 오류를 반환하고 프로세스가 중지됩니다. 푸시 알림 서비스는 해당 일괄 처리를 완전히 삭제합니다. TCP 스트림 프로토콜을 사용하는 APNS의 경우 특히 이렇게 진행됩니다. 

    우리 솔루션은 최대 한 번 배달에 최적화되었습니다. 하지만 이 경우 오류가 있는 등록이 데이터베이스에서 제거됩니다. 그런 다음 해당 일괄 처리의 나머지 장치에 알림을 다시 배달합니다.

    등록 시 실패한 배달 시도에 대한 더 많은 오류 정보를 얻으려면 Notification Hubs REST API [메시지별 원격 분석: 알림 메시지 원격 분석 가져오기](https://msdn.microsoft.com/library/azure/mt608135.aspx) 및 [PNS 피드백](https://msdn.microsoft.com/library/azure/mt705560.aspx)을 사용하세요. 샘플 코드는 [REST 보내기 예제](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample)를 참조하세요.

## <a name="push-notification-service-issues"></a>푸시 알림 서비스 문제
플랫폼 푸시 알림 서비스가 알림 메시지를 받은 후에는 푸시 알림 서비스가 장치에 알림을 보내야 합니다. Notification Hubs 서비스는 여기에 관여하지 않으며 알림이 장치로 전달되는지 여부 또는 그 시기를 제어하지 않습니다. 

플랫폼 알림 서비스가 매우 견고하기 때문에 알림은 푸시 알림 서비스로부터 몇 초 이내에 장치에 도달하게 됩니다. 푸시 알림 서비스가 제한하는 경우 Notification Hubs는 지수 백오프 전략을 적용합니다. 30분 동안 푸시 알림 서비스에 도달할 수 없는 경우 해당 메시지를 만료하고 영구적으로 삭제하는 정책이 있습니다. 

푸시 알림 서비스가 알림을 전달하려고 하지만 장치가 오프라인인 경우 푸시 알림 서비스는 일정 시간 동안 알림을 저장합니다. 장치가 다시 온라인이 되면 장치에 알림이 배달됩니다. 

앱마다 최신 알림 하나만 저장됩니다. 장치가 오프라인일 때 여러 알림이 전송되면 각 새 알림이 이전 알림을 삭제합니다. 가장 최신 알림만 유지하는 이 동작을 APNs에서는 *결합 알림*이라고 하고 FCM에서는 *축소 알림*(축소 키 사용)이라고 합니다. 장치가 오랫동안 오프라인 상태일 경우 해당 장치에 대해 저장된 모든 알림이 삭제됩니다. 자세한 내용은 [APNs 개요] 및 [FCM 메시지 정보]를 참조하세요.

Azure Notification Hubs를 사용하면 제네릭 SendNotification API를 사용하여 HTTP 헤더를 통해 결합 키를 전달할 수 있습니다. 예를 들어 .NET SDK의 경우 **SendNotificationAsync**를 사용합니다. 또한 SendNotification API는 각 푸시 알림 서비스에 있는 그대로 전달되는 HTTP 헤더를 사용합니다. 

## <a name="self-diagnosis-tips"></a>자체 진단 팁
다음은 Notification Hubs에서 알림이 삭제되는 근본 원인을 진단하는 경로입니다.

### <a name="verify-credentials"></a>자격 증명 확인
* **푸시 알림 서비스 개발자 포털**
   
    각 푸시 알림 서비스 개발자 포털(APNs, FCM, Windows Notification Service 등)에서 자격 증명을 확인합니다. 자세한 내용은 [Azure Notification Hubs 시작]을 참조하세요.

* **Azure Portal**
   
    자격 증명을 검토하고 푸시 알림 서비스 개발자 포털에서 얻은 자격 증명과 일치시키려면 Azure Portal에서 **액세스 정책** 탭으로 이동합니다. 
   
    ![Azure Portal 액세스 정책][4]

### <a name="verify-registrations"></a>등록 확인
* **Visual Studio**
   
    개발에 Visual Studio를 사용하는 경우 서버 탐색기를 통해 Azure에 연결하여 Notification Hubs를 비롯한 여러 Azure 서비스를 살펴보고 관리할 수 있습니다. 개발/테스트 환경에서 주로 유용합니다. 
   
    ![Visual Studio 서버 Explorer][9]
   
    허브에서 플랫폼, 네이티브 또는 템플릿 등록, 태그, 푸시 알림 서비스 식별자, 등록 ID, 만료 날짜별로 분류된 모든 등록을 보고 관리할 수 있습니다. 또한 이 페이지에서 등록을 편집할 수 있습니다. 태그를 편집할 때 특히 유용합니다. 
   
    ![Visual Studio 장치 등록][8]
   
   > [!NOTE]
   > 개발/테스트 중에만 제한된 수의 등록에 한해 Visual Studio를 사용하여 등록을 편집할 수 있습니다. 등록을 대량으로 편집해야 할 경우 [대량으로 등록 내보내기 및 수정](https://msdn.microsoft.com/library/dn790624.aspx)에 설명된 등록 내보내기 및 가져오기 기능을 사용하는 방안을 고려해 봅니다.
   > 
   > 
* **Service Bus 탐색기**
   
    많은 고객이 [Service Bus 탐색기]를 사용하여 알림 허브를 살펴보고 관리합니다. Service Bus 탐색기는 오픈 소스 프로젝트입니다. 샘플을 보려면 [Service Bus 탐색기 코드]를 참조하세요.

### <a name="verify-message-notifications"></a>알림 메시지 확인
* **Azure Portal**
   
    서비스 백 엔드를 실행하지 않고 클라이언트에 테스트 알림을 보내려면 **지원 + 문제 해결**에서 **테스트 보내기**를 선택합니다. 
   
    ![Azure의 테스트 보내기 기능][7]
* **Visual Studio**
   
    Visual Studio에서 테스트 알림을 보낼 수도 있습니다.
   
    ![Visual Studio의 테스트 보내기 기능][10]
   
    Visual Studio 서버 탐색기에서 Notification Hubs를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요. 
   
   * [알림 허브에 대한 장치 등록 보기]
   * [심층 분석: Visual Studio 2013 업데이트 2 RC 및 Azure SDK 2.3]
   * [Visual Studio 2013 업데이트 3 및 Azure SDK 2.4 릴리스 발표]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>실패한 알림 디버그 및 알림 결과 검토
**EnableTestSend 속성**

Notification Hubs를 통해 처음으로 알림을 보내면 Notification Hubs에서 처리할 수 있도록 알림이 대기열에 추가됩니다. Notification Hubs는 올바른 대상을 결정한 후 푸시 알림 서비스에 알림을 보냅니다. REST API 또는 클라이언트 SDK를 사용하는 경우 송신 호출이 성공적으로 반환된다는 것은 메시지가 Notification Hubs에 성공적으로 대기한다는 것만을 의미합니다. Notification Hubs가 결국 푸시 알림 서비스에 메시지를 보냈을 때 어떤 일이 일어났는지는 알지 못합니다. 

알림이 클라이언트 장치에 도착하지 않는 경우 Notification Hubs가 푸시 알림 서비스에 메시지를 배달하려고 할 때 오류가 발생한 것일 수 있습니다. 예를 들어 페이로드 크기가 푸시 알림 서비스에서 허용되는 최대값을 초과하거나 Notification Hubs에서 구성된 자격 증명이 잘못된 것일 수 있습니다. 

푸시 알림 서비스 오류에 대한 정보를 얻으려면 [EnableTestSend] 속성을 사용합니다. 이 속성은 포털 또는 Visual Studio 클라이언트에서 테스트 메시지를 보낼 때 자동으로 활성화됩니다. 이 속성을 사용하여 자세한 디버깅 정보를 볼 수 있습니다. 또한 API를 통해 이 속성을 사용할 수 있습니다. 현재 이 속성은 .NET SDK에서 사용할 수 있습니다. 결국 이 속성은 모든 클라이언트 SDK에 추가됩니다. 

REST 호출에 **EnableTestSend** 속성을 사용하려면 송신 호출 끝에 *test*라는 쿼리 문자열 매개 변수를 추가합니다. 예:  

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**예(.NET SDK)**

다음은 .NET SDK를 사용하여 네이티브 팝업(토스트 알림) 알림을 보내는 예제입니다.

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

실행이 끝나면 **result.State**는 간단히 **큐에 넣음**으로 표시됩니다. 푸시 알림에서 발생한 일에 대한 정보는 결과에 제공되지 않습니다. 

다음으로 **EnableTestSend** 부울 속성을 사용할 수 있습니다. **NotificationHubClient**를 초기화할 때 **EnableTestSend** 속성을 사용하여 알림을 보낼 때 발생하는 푸시 알림 서비스 오류에 대한 자세한 상태를 가져올 수 있습니다. 송신 호출은 반환에 더 긴 시간이 걸립니다. Notification Hubs가 푸시 알림 서비스에 알림을 배달하고 결과를 확인한 후에야 반환되기 때문입니다. 

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**샘플 출력**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

이 메시지는 Notification Hubs에서 잘못된 자격 증명이 구성되었는지 아니면 허브 등록에 문제가 있는지를 나타냅니다. 이 등록을 삭제하고 클라이언트에서 등록을 다시 만든 후 메시지를 보내게 하는 것이 좋습니다. 

> [!NOTE]
> **EnableTestSend** 속성 사용은 철저하게 제한됩니다. 이 옵션은 개발/테스트 환경에서만 제한된 등록에 사용해야 합니다. 10개의 장치에만 디버그 알림을 보냅니다. 또한 분당 10개의 디버그 보내기만 처리할 수 있습니다. 
> 
> 

### <a name="review-telemetry"></a>원격 분석 검토
* **Azure Portal 사용**
   
    포털에서 알림 허브의 모든 활동에 대한 간략한 개요를 확인할 수 있습니다. 
   
    1. **개요** 탭에서 등록, 알림 및 오류 집계를 플랫폼별로 볼 수 있습니다. 
   
        ![Notification Hubs 개요 대시보드][5]
   
    2. **모니터** 탭에서 세부 분석을 위한 다른 여러 플랫폼별 메트릭을 추가할 수 있습니다. Notification Hubs 서비스가 푸시 알림 서비스에 알림을 보내려고 시도할 때 반환되는 푸시 알림 서비스와 관련된 오류를 자세히 살펴볼 수 있습니다. 
   
        ![Azure Portal 활동 로그][6]
   
    3. 먼저 **들어오는 메시지**, **등록 작업** 및 **성공한 알림**을 검토합니다. 그런 다음 플랫폼별 탭으로 이동하여 푸시 알림 서비스와 관련된 오류를 검토합니다. 
   
    4. 알림 허브에 대한 인증 설정이 올바르지 않으면 **PNS 인증 오류**가 나타납니다. 이것은 푸시 알림 서비스 자격 증명을 확인해야 한다는 좋은 지표입니다. 

* **프로그래밍 방식 액세스**

    프로그래밍 방식 액세스에 대한 자세한 내용은 다음 문서를 참조하세요. 

    * [프로그래밍 방식 원격 분석 액세스]  
    * [API 샘플을 통한 원격 분석 액세스] 

    > [!NOTE]
    > API를 통해 등록 및 원격 분석 액세스 내보내기 및 가져오기 같은 여러 원격 분석 관련 기능은 표준 서비스 계층에서만 사용할 수 있습니다. 무료 또는 기본 서비스 계층에서 이러한 기능을 사용하려고 시도하면 SDK를 사용하는 경우에는 예외 메시지가 표시되고 REST API에서 직접 이 기능을 사용하는 경우에는 HTTP 403(사용할 수 없음) 오류가 표시됩니다. 
    >
    >원격 분석 관련 기능을 사용하려면 먼저 Azure Portal에서 표준 서비스 계층을 사용하고 있는지 확인합니다.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs 개요]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs 시작]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[템플릿]: https://msdn.microsoft.com/library/dn530748.aspx 
[APNs 개요]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM 메시지 정보]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus 탐색기]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Service Bus 탐색기 코드]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[알림 허브에 대한 장치 등록 보기]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[심층 분석: Visual Studio 2013 업데이트 2 RC 및 Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Visual Studio 2013 업데이트 3 및 Azure SDK 2.4 릴리스 발표]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[프로그래밍 방식 원격 분석 액세스]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[API 샘플을 통한 원격 분석 액세스]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

