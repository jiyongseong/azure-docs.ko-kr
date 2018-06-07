---
title: Azure Portal을 사용하여 IoT Hub 만들기 | Microsoft 문서
description: Azure Portal을 통해 Azure IoT Hub를 만들고 관리하고 삭제하는 방법입니다. 가격 책정 계층, 보안, 배율 및 메시징 구성에 대한 정보가 포함됩니다.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0909cd2b-4c1e-49e0-b68a-75532caf0a6a
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: dobett
ms.openlocfilehash: 6ffde076caff6217bf6255c9294eca63d3e39b85
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>Azure Portal을 사용하여 IoT Hub 만들기

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

이 문서에서는 다음을 설명합니다.

* Azure Portal에서 IoT Hub 서비스를 찾는 방법.
* IoT Hub 만들기 및 관리 방법.

## <a name="where-to-find-the-iot-hub-service"></a>IoT Hub 서비스를 찾을 위치.

IoT Hub 서비스를 포털의 다음 위치에서 찾을 수 있습니다.

* **+새로 만들기**를 선택한 다음, **사물 인터넷**을 선택합니다.
* Marketplace에서 **사물 인터넷**을 선택합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

다음 메서드를 통해 IoT Hub를 만들 수 있습니다.

* **+ 새로 만들기** 옵션에서 다음 스크린 샷에 표시된 블레이드가 열립니다. 이 메서드는 Marketplace를 통해 IoT Hub를 만드는 단계와 동일합니다.
* Marketplace에서 **만들기**를 선택하여 다음 스크린 샷에 표시된 블레이드를 엽니다.

다음 섹션에서는 IoT Hub를 만드는 몇 가지 단계를 설명합니다.

### <a name="choose-the-name-of-the-iot-hub"></a>IoT Hub 이름 선택

IoT Hub를 만들려면 IoT Hub의 이름을 지정해야 합니다. 이 이름은 모든 IoT Hub에서 고유해야 합니다.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

### <a name="choose-the-pricing-tier"></a>가격 책정 계층 선택

원하는 기능 수 그리고 하루에 솔루션을 통해 보내는 메시지 수에 따라 여러 계층에서 선택할 수 있습니다. 무료 계층은 테스트와 평가를 위해 제공됩니다. IoT Hub에 500개 장치를 연결할 수 있으며 하루에 8,000개 메시지까지 허용합니다. 각 Azure 구독은 무료 계층에 하나의 IoT Hub를 만들 수 있습니다. 

다른 계층 옵션에 대한 자세한 내용은 [적절한 IoT Hub 계층 선택](iot-hub-scaling.md)을 참조하세요.

### <a name="iot-hub-units"></a>IoT Hub 단위

하루 단위당 허용되는 메시지의 수는 허브의 가격 책정 계층에 따라 다릅니다. 예를 들어 IoT Hub가 700,000개의 메시지 수신을 지원하려면 S1 계층 단위 2개를 선택합니다.

### <a name="device-to-cloud-partitions-and-resource-group"></a>클라우드 파티션 및 리소스 그룹에 대한 장치

IoT Hub에 대한 파티션 수를 변경할 수 있습니다. 기본 파티션 수는 4개로, 드롭다운 목록에서 다른 숫자를 선택할 수 있습니다.

빈 리소스 그룹을 명시적으로 만들지 않아도 됩니다. 리소스를 만들 때 기존 리소스 그룹을 사용하거나 새로 만들도록 선택할 수 있습니다.

![][5]

### <a name="choose-subscription"></a>구독 선택

Azure IoT Hub에 사용자 계정을 연결할 Azure 구독 목록이 자동으로 표시됩니다. Azure 구독을 선택하여 IoT Hub를 연결할 수 있습니다.

### <a name="choose-the-location"></a>위치 선택

위치 옵션은 IoT Hub가 제공되는 지역 목록을 제공합니다.

### <a name="create-the-iot-hub"></a>IoT Hub 만들기

이전 단계를 모두 완료하면 IoT Hub를 만들 수 있습니다. 백 엔드 프로세스를 시작하도록 **만들기**를 클릭하여 선택한 옵션으로 IoT Hub를 만들고 배포합니다.

적절한 위치 서버에서 백 엔드 배포가 실행되는 데 시간이 소요되므로 IoT Hub를 만드는 데 몇 분이 소요될 수 있습니다.

## <a name="change-the-settings-of-the-iot-hub"></a>IoT Hub의 설정 변경

IoT Hub 블레이드에서 IoT Hub를 만든 후에 기존 IoT Hub의 설정을 변경할 수 있습니다.

![][8]

**공유 액세스 정책**: IoT Hub에 연결할 장치 및 서비스에 대한 권한을 정의합니다. **일반**에서 **공유 액세스 정책**을 클릭하여 이러한 정책에 액세스할 수 있습니다. 이 블레이드에서 기존 정책을 수정하거나 새 정책을 추가할 수 있습니다.

### <a name="create-a-policy"></a>정책 만들기

* **추가**를 클릭하여 블레이드를 엽니다. 여기에서 다음 그림에 표시된 것처럼 새 정책 이름과 이 정책과 연결할 권한을 입력할 수 있습니다.

    이러한 공유 정책과 연결할 수 있는 몇 개의 권한이 있습니다. **레지스트리 읽기** 및 **레지스트리 쓰기** 정책은 ID 레지스트리에 대한 읽기 및 쓰기 액세스 권한을 부여합니다. 쓰기 옵션을 선택하면 읽기 옵션도 자동으로 선택됩니다.

    **서비스 연결** 정책은 **장치-클라우드 수신**과 같은 서비스 끝점에 액세스할 수 있는 권한을 부여합니다. **장치 연결** 정책은 IoT Hub 장치 쪽 끝점을 사용하여 메시지를 주고받기 위한 권한을 부여합니다.

* **만들기** 를 클릭하여 새로 만들어진 이 정책을 기존 목록에 추가합니다.

![][10]

## <a name="endpoints"></a>Endpoints

**끝점**을 클릭하여 수정하는 IoT Hub에 대한 끝점 목록을 표시합니다. 끝점의 두 가지 유형은 IoT Hub에 기본 제공된 끝점 및 IoT Hub를 만든 후 여기에 추가된 끝점입니다.

![][11]

### <a name="built-in-endpoints"></a>기본 제공 끝점

두 가지 기본 제공 끝점인 **클라우드-장치 피드백** 및 **이벤트**가 있습니다.

* **클라우드-장치 피드백** 설정: 이 설정에는 메시지에 대한 **클라우드-장치 TTL**(time-to-live) 및 **보존 시간**(시간 단위)의 두 가지 하위 설정이 포함됩니다. IoT Hub를 처음 만들 때 이 두 가지 설정 모두 1시간이 기본값 입니다. 이러한 설정을 조정하려면 슬라이더를 사용하거나 값을 입력합니다.
* **이벤트** 설정: 이 설정에는 몇 가지 하위 설정이 있고 일부는 읽기 전용입니다. 다음 목록은 이러한 설정에 대해 설명합니다.

  * **파티션**: IoT Hub가 만들어질 때 기본 값이 설정됩니다. 이 설정을 통해 파티션 수를 변경할 수 있습니다.

  * **이벤트 허브 호환 이름 및 끝점**: IoT Hub가 만들어질 때 특정 상황에서 사용자가 액세스해야 하는 이벤트 허브가 내부적으로 만들어집니다. Event Hub 호환 이름 및 끝점 값을 사용자 지정할 수는 없지만 **복사**를 클릭하여 복사할 수 있습니다.

  * **보존 시간**: 기본적으로 1일로 설정되지만 드롭다운 목록을 사용하여 변경할 수 있습니다. 이 값은 장치-클라우드 설정에 대한 일 단위입니다.

  * **소비자 그룹**: 소비자 그룹은 여러 판독기에서 IoT Hub의 메시지를 독립적으로 읽을 수 있도록 합니다. 모든 IoT Hub가 기본 소비자 그룹으로 만들어집니다. 그러나 이 설정을 사용하여 소비자 그룹을 IoT Hub에 추가 또는 삭제할 수 있습니다.

  > [!NOTE]
  > 기본 소비자 그룹을 편집하거나 삭제할 수 없습니다.

### <a name="custom-endpoints"></a>사용자 지정 끝점

포털을 사용하여 IoT Hub에 사용자 지정 끝점을 추가할 수 있습니다. **끝점** 블레이드에서 맨 위의 **추가**를 클릭하여 **끝점 추가** 블레이드를 엽니다. 필요한 정보를 입력한 다음 **확인**을 클릭합니다. 사용자 지정 끝점이 기본 **끝점** 블레이드에 표시됩니다.

![][13]

[참조 - IoT Hub 끝점][lnk-devguide-endpoints]에서 사용자 지정 끝점에 대해 자세히 알아볼 수 있습니다.

## <a name="routes"></a>경로

**경로**를 클릭하여 IoT Hub가 장치-클라우드 메시지를 발송하는 방법을 관리합니다.

![][14]

**경로** * 블레이드에서 맨 위의 **추가**를 클릭하여 필요한 정보를 입력하고 **확인** 을 클릭하여 IoT Hub에 경로를 추가할 수 있습니다. 그러면 기본 **경로** 블레이드에 경로가 나열됩니다. 경로 목록에서 경로를 클릭하면 편집할 수 있습니다. 경로를 사용하려면 경로 목록에서 경로를 클릭하고 **사용** 토글을 **해제**로 설정합니다. 변경 내용을 저장하려면 블레이드의 맨 아래에서 **확인**을 클릭합니다.

![][15]

## <a name="delete-the-iot-hub"></a>IoT Hub 삭제

**찾아보기**를 클릭한 후 삭제할 해당 허브를 선택하여 삭제할 IoT Hub를 찾아볼 수 있습니다. IoT Hub를 삭제하려면 IoT Hub 이름 아래의 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 알아보려면 다음 링크를 따라가세요.

* [IoT 장치 대량 관리][lnk-bulk]
* [IoT Hub 메트릭][lnk-metrics]
* [작업 모니터링][lnk-monitor]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드][lnk-devguide]
* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포][lnk-iotedge]
* [처음부터 IoT 솔루션 보안 유지][lnk-securing]

[4]: ./media/iot-hub-create-through-portal/create-iothub.png
[5]: ./media/iot-hub-create-through-portal/location1.png
[8]: ./media/iot-hub-create-through-portal/portal-settings.png
[10]: ./media/iot-hub-create-through-portal/shared-access-policies.png
[11]: ./media/iot-hub-create-through-portal/messaging-settings.png
[12]: ./media/iot-hub-create-through-portal/pricing-error.png
[13]: ./media/iot-hub-create-through-portal/endpoint-creation.png
[14]: ./media/iot-hub-create-through-portal/routes-list.png
[15]: ./media/iot-hub-create-through-portal/route-edit.png

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
