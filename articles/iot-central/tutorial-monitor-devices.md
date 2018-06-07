---
title: Azure IoT Central에서 장치 모니터링 | Microsoft Docs
description: 운영자로서 Azure IoT Central 응용 프로그램을 사용하여 장치를 모니터링합니다.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a07c9e3c28fadaead8bfaaebe4d1ee06ac66a99e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201390"
---
# <a name="use-azure-iot-central-to-monitor-your-devices"></a>Azure IoT Central을 사용하여 장치 모니터링

이 자습서에서는 운영자로서 Microsoft Azure IoT Central 응용 프로그램을 사용하여 장치를 모니터링하고 설정을 변경하는 방법을 알려줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 알림 수신
> * 문제 조사
> * 문제 해결

## <a name="prerequisites"></a>필수 조건

시작하기 전에 작성기는 세 가지 작성기 자습서를 완료하여 Azure IoT Central 응용 프로그램을 만들어야 합니다.

* [새 장치 유형 정의](tutorial-define-device-type.md)
* [장치에 대한 규칙 및 작업 구성](tutorial-configure-rules.md)
* [연산자의 뷰 사용자 지정](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>알림 수신

Azure IoT Central은 장치에 대한 알림을 이메일 메시지로 보냅니다. 작성기는 연결된 공조 장치에서 온도가 임계값을 초과하는 경우 알림을 보내기 위해 규칙을 추가했습니다. 작성기가 알림을 수신하도록 선택한 계정에 전송되는 이메일을 확인합니다.

[장치에 대한 규칙 및 작업 구성](tutorial-configure-rules.md) 자습서의 끝에 수신한 이메일 메시지를 엽니다. 이메일에서 **장치를 열려면 여기를 클릭**을 선택합니다.

![응용 프로그램 작성기 규칙](media/tutorial-monitor-devices/email.png)

이전 자습서에서 만든 장치를 시뮬레이션한 **연결된 공조 장치-1**에 대한 **장치** 페이지가 브라우저에서 열립니다.

![알림 이메일 메시지를 트리거한 장치](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>문제 조사

운영자로서 **측정**, **설정**, **속성**, **규칙** 및  **대시보드** 페이지에서 장치에 대한 정보를 볼 수 있습니다. 작성기는 연결된 공조 장치에 대한 중요한 정보를 표시하기 위해 **대시보드**를 사용자 지정했습니다.

* 장치에 대한 정보를 보려면 **대시보드** 보기를 선택합니다.

    ![장치 대시보드](media/tutorial-monitor-devices/initial_screen.png)

    대시보드의 차트에는 장치 온도 플롯이 표시됩니다. **대상 온도 설정** 타일에서 장치에 대한 현재 대상 온도를 볼 수 있습니다. 대상 온도가 너무 높다고 판단합니다.

## <a name="remediate-an-issue"></a>문제 해결

장치의 대상 온도를 변경하려면 **설정** 페이지를 사용하세요.

1. **설정**을 선택합니다. **온도 설정**을 100으로 변경합니다. **업데이트**를 선택하여 새 대상 온도를 장치로 전송합니다. 장치가 설정 변경을 인식하는 경우 설정 값의 상태가 **동기화됨**으로 변경됩니다.

    ![업데이트 설정](media/tutorial-monitor-devices/change_settings.png)

1. **대시보드**를 선택하여 새 설정 값을 확인합니다.

    ![업데이트된 장치 대시보드](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> * 알림 수신
> * 문제 조사
> * 문제 해결

이제 장치를 모니터링했으므로 제안하는 다음 단계는 [장치 추가](tutorial-add-device.md)하는 것입니다.
