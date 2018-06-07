---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34371374"
---
## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

IoT 허브를 만들었으므로 IoT 허브에 장치 및 응용 프로그램을 연결하는 데 사용하는 중요한 정보를 찾습니다. 

IoT Hub 탐색 메뉴에서 **공유 액세스 정책**을 엽니다.
**iothubowner** 정책을 선택하고, IoT Hub의 **연결 문자열---기본 키**를 복사합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](../articles/iot-hub/iot-hub-devguide-security.md)를 참조하세요.

   > [!NOTE] 
   > 이 설치 자습서에는 이 iothubowner 연결 문자열이 필요 없습니다. 그러나 이 설치를 완료한 후 다른 IoT 시나리오의 일부 자습서에는 필요할 수 있습니다.

   ![IoT Hub 연결 문자열 가져오기](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>장치의 IoT Hub에서 장치 등록

1. IoT Hub 탐색 메뉴에서 **IoT 장치**를 열고 **추가**를 클릭하여 IoT Hub에 장치를 등록합니다.

   ![IoT 허브의 IoT 장치에 장치 추가](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. 새 장치의 **장치 ID**를 입력합니다. 장치 ID는 대/소문자를 구분합니다.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. **저장**을 클릭합니다.
5. 장치가 만들어진 후 **IoT 장치** 창의 목록에서 장치를 엽니다.
6. 나중에 사용하기 위해 **연결 문자열---기본 키**를 복사합니다.

   ![장치 연결 문자열 가져오기](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
