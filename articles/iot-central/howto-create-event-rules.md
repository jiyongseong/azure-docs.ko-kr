---
title: Azure IoT Central 응용 프로그램에서 이벤트 규칙 만들기 및 관리 | Microsoft Docs
description: Azure IoT Central 이벤트 규칙을 사용하여 장치를 거의 실시간으로 모니터링하고, 규칙이 트리거되면 이메일 보내기 등의 작업을 자동으로 호출합니다.
services: iot-central
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932b1906b767ee7676f46ffd7242ad3d478d41c2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201594"
---
# <a name="create-an-event-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Azure IoT Central 응용 프로그램에서 이벤트 규칙을 만들고 알림 설정

Microsoft Azure IoT Central을 사용하여 원격으로 연결된 장치를 모니터링할 수 있습니다. Azure IoT Central 규칙을 사용하여 장치를 거의 실시간으로 모니터링하고, 규칙이 트리거되면 이메일 보내기 등의 작업을 자동으로 호출합니다. 클릭 몇 번으로 장치 데이터를 모니터링하는 조건을 구성하고 호출할 작업을 구성할 수 있습니다. 이 문서에서는 이벤트 모니터링 규칙을 자세히 설명합니다.

Azure IoT Central은 [이벤트 측정값](howto-set-up-template.md)을 사용하여 장치 데이터를 캡처합니다. 측정 유형마다 측정을 정의하는 키 특성이 있습니다. 각 장치 측정 유형을 모니터링하다가 규칙이 트리거되면 경고를 생성하는 규칙을 만들 수 있습니다. 선택한 장치 이벤트를 장치에서 보고하면 이벤트 규칙이 트리거됩니다.

## <a name="create-an-event-rule"></a>이벤트 규칙 만들기

이 섹션에서는 이벤트 규칙을 만드는 방법을 보여줍니다. 이 예제에서는 팬 모터 오류 이벤트를 보고하는 냉장 자동 판매기 장치를 사용합니다. 이 규칙은 장치에서 보고한 이벤트를 모니터링하다가 이벤트가 보고될 때마다 이메일을 보냅니다.

1. 규칙을 추가하려는 장치에 대한 장치 세부 정보 페이지로 이동합니다.

1. 아직 규칙을 하나도 만들지 않은 경우 다음 화면이 표시됩니다.

    ![규칙 없음](media\howto-create-event-rules\image1.png)

1. **규칙** 탭에서 **+ 새 규칙**을 선택하면 만들 수 있는 규칙 유형이 표시됩니다.

    ![규칙 유형](media\howto-create-event-rules\image2.png)

1. **이벤트**를 클릭하여 규칙 작성 양식을 엽니다.

    ![이벤트 규칙](media\howto-create-event-rules\image3.png)

1. 이 장치 템플릿에서 규칙을 식별하는 데 도움이 되는 이름을 선택합니다.

1. 이 템플릿에서 만든 모든 장치에 즉시 규칙을 사용하려면 **규칙 사용**으로 전환합니다.

### <a name="configure-the-rule-condition"></a>규칙 조건 구성

이 섹션에서는 팬 모터 오류 이벤트 측정값을 모니터링하는 조건을 추가하는 방법을 보여줍니다.

1. **조건** 옆에 있는 **+** 기호를 선택합니다.

1. 드롭다운 목록에서 모니터링할 이벤트 측정값을 선택합니다. 이 예에서는 **팬 모터 오류** 이벤트를 선택했습니다.

1. 필요에 따라 장치에서 보고하는 이벤트의 특정 값을 모니터링하려는 경우 값을 제공할 수도 있습니다. 예를 들어 장치에서 오류 코드가 다른 동일한 이벤트를 보고하는 경우 규칙 조건의 값으로 오류 코드를 제공하면 장치에서 해당 특정 값을 이벤트 페이로드로 보내는 경우에만 규칙이 트리거됩니다. 이 값을 비워 두면 이벤트 값에 관계 없이 장치에서 이벤트를 보낼 때마다 규칙이 트리거됩니다.

    ![이벤트 조건 추가](media\howto-create-event-rules\image4.png)

    > [!NOTE]
    > 이벤트 규칙 조건을 정의할 때 이벤트 측정값을 하나 이상 선택해야 합니다.

### <a name="configure-the-action"></a>작업 구성

이 섹션에서는 작업을 추가하여 조건이 일치할 때 규칙이 하는 일을 지정하는 방법을 보여줍니다.

1. **작업** 옆에 있는 **+** 기호를 선택합니다. 사용 가능한 작업 목록을 표시됩니다. 공개 미리 보기 기간에는 **이메일** 작업만 지원됩니다.

    ![작업 추가](media\howto-create-event-rules\image5.png)

1. **이메일** 작업을 선택하고, **받는 사람** 필드에 유효한 이메일 주소를 입력하고, 규칙이 트리거될 때 이메일 본문에 표시할 메모를 입력합니다.

    > [!NOTE]
    > 응용 프로그램에 추가되어 한 번 이상 로그인한 사용자에게만 이메일이 발송됩니다. Azure IoT Central에서 [사용자 관리](howto-administer.md)에 대해 자세히 알아보세요.

   ![작업 구성](media\howto-create-event-rules\image6.png)

1. 규칙을 저장하려면 **저장**을 선택합니다. 몇 분 이내에 규칙이 적용되어 응용 프로그램으로 전송되는 이벤트의 모니터링이 시작됩니다. 규칙에 지정된 조건이 일치하는 경우 규칙이 구성된 이메일 작업을 트리거합니다.

## <a name="parameterize-the-rule"></a>규칙 매개 변수화

**장치 속성**을 매개 변수로 사용하여 작업을 구성할 수도 있습니다. 이메일 주소가 장치 속성으로 저장되는 경우 **받는 사람** 주소를 정의할 때 사용할 수 있습니다.

## <a name="delete-a-rule"></a>규칙 삭제

규칙이 더 이상 필요 없으면 해당 규칙을 열고 **삭제**를 선택하여 삭제할 수 있습니다. 규칙을 삭제하면 장치 템플릿 및 모든 관련 장치에서 해당 규칙이 제거됩니다.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>장치 템플릿에 규칙을 사용하도록 또는 사용하지 않도록 설정

해당 장치로 이동하여 사용하도록 또는 사용하지 않도록 설정할 규칙을 선택합니다. 규칙에서 **이 템플릿의 모든 장치에 규칙 사용** 단추를 누르면 장치 템플릿과 연결된 모든 장치에 규칙을 사용하도록 또는 사용하지 않도록 설정됩니다.

## <a name="enable-or-disable-a-rule-for-a-device"></a>장치에 규칙을 사용하도록 또는 사용하지 않도록 설정

해당 장치로 이동하여 사용하도록 또는 사용하지 않도록 설정할 규칙을 선택합니다. **이 장치에 규칙 사용** 단추를 눌러 해당 장치에 규칙을 사용하도록 또는 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 응용 프로그램에서 규칙을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

> [!div class="nextstepaction"]
> [장치를 관리하는 방법](howto-manage-devices.md).