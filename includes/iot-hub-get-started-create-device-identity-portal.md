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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "34371204"
---
## <a name="create-a-device-identity"></a>장치 ID 만들기

이 섹션에서는 [Azure Portal][lnk-azure-portal]을 사용하여 IoT hub의 ID 레지스트리에 장치 ID를 만듭니다. ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 "ID 레지스트리" 섹션을 참조하세요. 포털에서 **IoT 장치** 패널을 사용하여 장치에 대한 고유한 장치 ID 및 키를 생성하여 IoT Hub에서 장치를 식별하는 데 사용합니다. 장치 ID는 대/소문자를 구분합니다.

1. [Azure Portal][lnk-azure-portal]에 로그인합니다.

1. **모든 리소스**를 선택하고 IoT hub 리소스를 찾습니다.

1. IoT Hub 리소스가 열리면 **IoT 장치** 도구를 클릭한 다음, 위쪽에서 **추가**를 클릭합니다. 

    ![포털에서 장치 ID 만들기][img-add-device]

1. **myDeviceId**와 같은 새 장치의 이름을 입력하고 **저장**을 클릭합니다. 이 작업은 IoT Hub에 대한 새 장치 ID를 만듭니다.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![새 장치 추가][img-create-device]

1. 나중에 사용하기 위해 장치 목록에서 새로 만든 장치를 클릭하고 **연결 문자열---기본 키**를 복사합니다.

    ![장치 연결 문자열][img-connection-string]

> [!NOTE]
> IoT Hub ID 레지스트리는 장치 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 할 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

