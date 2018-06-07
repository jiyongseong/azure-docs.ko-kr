---
title: "Azure IoT Hub Device Provisioning Service를 사용하여 부하가 분산된 IoT Hub 간 장치 프로비전 | Microsoft Docs"
description: "Azure Portal에서 부하가 분산된 IoT Hub 간 DPS 자동 장치 프로비전"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>부하가 분산된 IoT Hub 간 장치 프로비전

이 자습서에서는 DPS(Device Provisioning Service)를 사용하여 부하가 분산된 여러 IoT Hub에 대해 장치를 프로비전하는 방법을 보여 줍니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 두 번째 IoT Hub에 두 번째 장치를 프로비전 
> * 두 번째 장치에 등록 목록 항목 추가
> * DPS 할당 정책을 **균등하게 배포**하도록 설정
> * DPS에 새 IoT Hub 연결

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 이전 [허브에 장치 프로비전](tutorial-provision-device-to-hub.md) 자습서를 토대로 작성되었습니다.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Azure Portal을 사용하여 두 번째 IoT Hub에 두 번째 장치를 프로비전

[허브에 장치 프로비전](tutorial-provision-device-to-hub.md) 자습서의 단계를 수행하여 다른 IoT Hub에 두 번째 장치를 프로비전합니다.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>두 번째 장치에 등록 목록 항목 추가

등록 목록은 장치와 함께 사용 중인 증명 메서드(장치 ID 확인을 위한 메서드)가 무엇인지를 DPS에 알려 줍니다. 다음 단계는 두 번째 장치에 대한 등록 목록 항목을 추가하는 것입니다. 

1. DPS에 대한 페이지에서 **등록 관리**를 클릭합니다. **등록 목록 항목 추가** 페이지가 나타납니다. 
2. 페이지 위쪽에서 **저장**을 클릭합니다.
2. 필드를 완료한 다음 **저장**을 클릭합니다.

## <a name="set-the-dps-allocation-policy"></a>DPS 할당 정책 설정

할당 정책은 IoT Hub에 장치를 할당하는 방법을 결정하는 DPS 설정입니다. 지원되는 세 가지 할당 정책은 다음과 같습니다. 

1. **최소 대기 시간**: 장치가 대기 시간이 가장 낮은 허브를 기준으로 IoT Hub에 프로비전됩니다.
2. **균등 가중치 배포**(기본값): 연결된 IoT Hub들은 동일하게 해당 허브에 프로비전된 장치를 갖게 됩니다. 기본 설정입니다. 장치를 단 하나의 IoT Hub에 프로비전하려는 경우 이 설정을 유지할 수 있습니다. 
3. **등록 목록을 통한 고정 구성**: 등록 목록에서 원하는 IoT Hub의 사양을 DPS 수준 할당 정책보다 우선합니다.

할당 정책을 설정하려면 다음 단계를 수행합니다.

1. 할당 정책을 설정하려면 DPS 페이지에서 **할당 정책 관리**를 클릭합니다.
2. 할당 정책을 **균등 가중치 배포**로 설정합니다.
3. **Save**를 클릭합니다.

## <a name="link-the-new-iot-hub-to-dps"></a>DPS에 새 IoT Hub 연결

DPS가 해당 허브에 장치를 등록할 수 있도록 DPS와 IoT Hub를 연결합니다.

1. **모든 리소스** 페이지에서 이전에 만든 DPS를 클릭합니다.
2. DPS 페이지에서 **연결된 IoT Hub**를 클릭합니다.
3. **추가**를 클릭합니다.
4. **IoT Hub에 링크 추가** 페이지에서 라디오 단추를 사용하여 연결된 IoT Hub가 현재 구독 또는 다른 구독 중 어디에 있는지를 지정합니다. 그런 다음 IoT Hub 이름을 **IoT Hub** 상자에서 선택합니다.
5. **Save**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 두 번째 IoT Hub에 두 번째 장치를 프로비전 
> * 두 번째 장치에 등록 목록 항목 추가
> * DPS 할당 정책을 **균등하게 배포**하도록 설정
> * DPS에 새 IoT Hub 연결

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
