---
title: IoT Hub 이벤트를 사용하여 Azure Logic Apps 트리거 | Microsoft Docs
description: IoT Hub 이벤트를 기반으로 Azure Logic Apps 작업을 수행하도록 Azure Event Grid의 이벤트 라우팅 서비스를 사용하여 자동화된 프로세스를 만듭니다.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.service: iot-hub
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: aab674f16fcc3fd4869f24f72f66878a8751d892
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301487"
---
# <a name="send-email-notifications-about-azure-iot-hub-events-using-logic-apps"></a>Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기

Azure Event Grid를 사용하면 다운스트림 비즈니스 응용 프로그램에서 작업을 트리거하여 IoT Hub에서 이벤트에 대응할 수 있습니다.

이 아티클은 IoT Hub 및 Event Grid를 사용하는 샘플 구성을 안내합니다. 결과적으로 장치가 IoT 허브에 추가될 때마다 Azure 논리 앱이 알림 이메일을 보내도록 설정합니다. 

## <a name="prerequisites"></a>필수 조건

* Azure Logic Apps에서 지원하는 이메일 공급자(예: Office 365 Outlook, Outlook.com 또는 Gmail)가 제공한 이메일 계정. 이 이메일 계정은 이벤트 알림을 보내는 데 사용됩니다. 지원되는 Logic App 커넥터의 전체 목록은 [커넥터 개요](https://docs.microsoft.com/connectors/)를 참조하세요.
* 활성 Azure 계정. 계정이 없는 경우 [무료 계정](http://azure.microsoft.com/pricing/free-trial/)에 만들 수 있습니다.
* Azure에 있는 IoT 허브입니다. 아직 만들지 않았다면, 연습으로 [IoT Hub 시작](../iot-hub/iot-hub-csharp-csharp-getstarted.md)을 참조하세요. 

## <a name="create-a-logic-app"></a>논리 앱 만들기

먼저 논리 앱을 만들고 가상 컴퓨터에 대한 리소스 그룹을 모니터링하는 Event Grid 트리거를 추가합니다. 

### <a name="create-a-logic-app-resource"></a>논리 앱 리소스 만들기


1. [Azure Portal](https://portal.azure.com)에서 **새로 만들기** > **엔터프라이즈 통합** > **논리 앱**을 선택합니다.

   ![논리 앱 만들기](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

2. 구독에서 논리 앱에 고유한 이름을 제공하고 동일한 구독과 리소스 그룹 및 위치를 IoT 허브로 선택합니다. 
3. 준비가 되면 **대시보드에 고정**을 선택하고 **만들기**를 선택합니다.

   이제 논리 앱에 대한 Azure 리소스를 만들었습니다. Azure에서 논리 앱을 배포하면 Logic Apps 디자이너에서 일반적인 패턴에 대한 템플릿을 표시하므로 더 빨리 시작할 수 있습니다.

   > [!NOTE] 
   > **대시보드에 고정**을 선택하면 Logic Apps 디자이너에서 해당 논리 앱이 자동으로 열립니다. 그렇지 않으면 논리 앱을 수동으로 찾아 열 수 있습니다.

4. **템플릿** 아래의 Logic App Designer에서 **비어 있는 논리 앱**을 선택하면 논리 앱을 처음부터 빌드할 수 있습니다.

## <a name="select-a-trigger"></a>트리거 선택

트리거는 논리 앱을 시작하는 특정 이벤트입니다. 이 자습서에서는 워크플로를 시작하는 트리거를 HTTP를 통해 요청을 받습니다.  

1. 커넥터 및 트리거 검색 표시줄에서 **HTTP**를 입력합니다.
2. **요청-HTTP 요청을 받은 경우** 트리거로 선택합니다. 

   ![HTTP 요청 트리거 선택](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

3. **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다. 

   ![HTTP 요청 트리거 선택](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

4. 다음 샘플 JSON 코드를 텍스트 상자에 붙여넣기한 다음 **완료**를 선택합니다.

   ```json
   [{
     "id": "56afc886-767b-d359-d59e-0da7877166b2",
     "topic": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<IoT hub name>",
     "subject": "devices/LogicAppTestDevice",
     "eventType": "Microsoft.Devices.DeviceCreated",
     "eventTime": "2018-01-02T19:17:44.4383997Z",
     "data": {
       "twin": {
         "deviceId": "LogicAppTestDevice",
         "etag": "AAAAAAAAAAE=",
         "status": "enabled",
         "statusUpdateTime": "0001-01-01T00:00:00",
         "connectionState": "Disconnected",
         "lastActivityTime": "0001-01-01T00:00:00",
         "cloudToDeviceMessageCount": 0,
         "authenticationType": "sas",
         "x509Thumbprint": {
           "primaryThumbprint": null,
           "secondaryThumbprint": null
         },
         "version": 2,
         "properties": {
           "desired": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           },
           "reported": {
             "$metadata": {
               "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
             },
             "$version": 1
           }
         }
       },
       "hubName": "egtesthub1",
       "deviceId": "LogicAppTestDevice",
       "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
       "opType": "DeviceCreated"
     },
     "dataVersion": "",
     "metadataVersion": "1"
   }]
   ```
5. **요청할 때 Content-Type 헤더 집합을 응용프로그램/json에 포함해야 합니다**라고 표시되는 팝업 알림을 받을 수도 있습니다. 이 제안을 안전하게 무시하고 다음 섹션으로 이동할 수 있습니다. 


### <a name="create-an-action"></a>작업 만들기

트리거가 논리 앱 워크플로를 시작한 후에 발생하는 모든 단계가 작업할 대상입니다. 이 자습서의 경우 작업은 메일 공급자의 이메일 알림을 보내는 것입니다. 

1. **새 단계** **작업 추가**를 차례로 선택합니다. 

   ![새 단계 후 작업 추가](./media/publish-iot-hub-events-to-logic-apps/new-step.png)

2. **이메일** 검색. 
3. 전자 메일 공급자에 따라 일치하는 커넥터를 찾아 선택합니다. 이 자습서에서는 **Office 365 Outlook**이 사용됩니다. 다른 이메일 공급자를 위한 단계들도 비슷합니다. 

   ![이메일 공급자 커넥터 선택](./media/publish-iot-hub-events-to-logic-apps/o365-outlook.png)

4. **이메일 보내기** 작업을 선택합니다. 
5. 메시지가 표시되면 이메일 계정에 로그인합니다. 
6. 이메일 템플릿을 작성합니다. 
   * **받는 사람**: 알림 이메일을 받을 이메일 주소를 입력합니다. 이 자습서의 경우 테스트를 위해 액세스할 수 있는 이메일 계정을 사용합니다. 
   * **제목** 및 **본문**: 이메일에 텍스트를 작성합니다. 이벤트 데이터를 기반으로 하는 동적 콘텐츠를 포함하도록 선택기 도구에서 JSON 속성을 선택합니다.  

   이메일 템플릿은 다음 예와 같습니다.

   ![이메일 정보 입력](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

7. 논리 앱을 저장합니다. 

### <a name="copy-the-http-url"></a>HTTP URL 복사

Logic Apps Designer를 나가기 전에 논리 앱이 트리거에 대해 수신 대기하는 URL을 복사합니다. 이 URL을 사용하여 Event Grid를 구성합니다. 

1. **HTTP 요청을 받을 경우** 트리거 구성 상자를 클릭하여 확장합니다. 
2. **HTTP POST URL**의 값을 그 옆에 있는 복사 단추를 선택하여 복사합니다. 

   ![HTTP POST URL 복사](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

3. 다음 섹션에서 참조할 수 있도록 이 URL을 저장합니다. 

## <a name="publish-an-event-from-iot-hub"></a>IoT Hub의 이벤트 게시

이 섹션에서는 일어나는 순서대로 이벤트를 게시하도록 Azure IoT Hub 를 구성합니다. 

1. Azure Portal에서 IoT Hub로 이동합니다. 
2. **이벤트**를 선택합니다.

   ![Event Grid 세부 정보 열기](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

3. **이벤트 구독**을 선택합니다. 

   ![새 이벤트 구독 만들기](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

4. 다음 값을 가진 이벤트 구독을 만듭니다. 
   * **이름**: 설명이 포함된 이름을 제공합니다.
   * **모든 이벤트 유형 구독**: 확인란을 선택 취소합니다.
   * **이벤트 유형**: **DeviceCreated**를 선택합니다.
   * **구독자 유형**: **Web Hook**를 선택합니다.
   * **구독자 끝점**: 논리 앱에서 복사한 URL을 붙여 넣습니다. 

   여기에 이벤트 구독을 저장하고 IoT 허브에서 생성된 모든 장치에 대한 알림을 받을 수 있습니다. 이 자습서의 경우 선택적 필드를 사용하여 특정 장치에 대해 필터링해 보겠습니다. 

   * **접두사 필터**: 1 작성에 장치 이벤트에 대한 필터링을 위해 `devices/Building1_`을 입력합니다.
   * **접미사를 필터**: 온도와 관련된 장치 이벤트에 대한 필터링을 위해 `_Temperature`를 입력합니다.

   작업을 완료했을 때 폼 모양은 다음 예제와 유사해야 합니다. 

   ![샘플 이벤트 구독 양식](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

5. **만들기**를 선택하여 이벤트 구독을 저장합니다.

## <a name="create-a-new-device"></a>새 장치 만들기

이벤트 알림 이메일을 트리거하는 새 장치를 만들어 논리 앱을 테스트합니다. 

1. IoT 허브에서 **IoT 장치**를 선택합니다. 
2. **추가**를 선택합니다.
3. **장치 ID**에 대해 `Building1_Floor1_Room1_Temperature`를 입력합니다.
4. **저장**을 선택합니다. 
5. 이벤트 구독 필터를 테스트하기 위해 다른 장치 ID를 사용하여 여러 장치를 추가할 수 있습니다. 이러한 예제를 시도해 보십시오. 
   * Building1_Floor1_Room1_Light
   * Building1_Floor2_Room2_Temperature
   * Building2_Floor1_Room1_Temperature
   * Building2_Floor1_Room1_Light

IoT 허브에 몇 개의 장치를 추가한 후 이메일을 확인하여 어떤 장치가 논리 앱을 트리거했는지 알아봅니다. 

## <a name="use-the-azure-cli"></a>Azure CLI 사용

Azure Portal을 사용하는 대신 Azure 명령줄 인터페이스를 사용하여 Azure IoT Hub 단계를 수행할 수 있습니다. 자세한 내용은 [이벤트 구독 만들기](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) 및 [IoT 장치 만들기](https://docs.microsoft.com/cli/azure/iot/device)에 대한 Azure 명령줄 인터페이스 페이지를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 Azure 구독에 요금을 부과하는 리소스를 사용했습니다. 자습서를 사용해보고 결과 테스트를 완료했다면 유지하지 않을 리소스는 비활성화하거나 삭제합니다. 

논리 앱에 대한 작업을 잃지 않으려면 삭제하는 대신에 비활성화합니다. 

1. 논리 앱으로 이동
2. **개요** 블레이드에서 **삭제** 또는 **사용 안 함**을 선택합니다. 

구독마다 하나의 무료 Azure IoT Hub를 가질 수 있습니다. 이 자습서에 대한 무료 허브를 만든 경우 요금이 부과되는 것을 막기 위해 삭제할 필요가 없습니다.

1. IoT Hub로 이동 
2. **개요** 블레이드에서 **삭제**를 선택합니다. 

IoT 허브를 유지하더라도 만든 이벤트 구독을 삭제하는 것이 좋습니다. 

1. IoT 허브에서 **Event Grid**를 선택합니다.
2. 제거하려는 이벤트 구독을 선택합니다. 
3. **삭제**를 선택합니다. 

## <a name="next-steps"></a>다음 단계

[작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md)에 대해 자세히 알아봅니다.

[Event Grid](overview.md)를 사용하여 다른 무엇을 할 수 있는지에 대해 알아봅니다.


