---
title: 장치 시뮬레이션 솔루션 시작 - Azure | Microsoft Docs
description: IoT 솔루션 가속기 시뮬레이션 솔루션은 IoT 솔루션의 개발 및 테스트를 지원하기 위해 사용할 수 있는 도구입니다. 시뮬레이션 서비스는 다른 솔루션 가속기와 함께 사용하거나 사용자 고유의 사용자 지정 솔루션에서 사용할 수 있는 독립 실행형 서비스입니다.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 742998dce07f6ceef0ad906831c60f11a7d08bd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="device-simulation-walkthrough"></a>장치 시뮬레이션 연습

Azure IoT 장치 시뮬레이션은 IoT 솔루션의 개발 및 테스트를 지원하기 위해 사용할 수 있는 도구입니다. 장치 시뮬레이션은 다른 솔루션 가속기와 함께 사용하거나 사용자 고유의 사용자 지정 솔루션에서 사용할 수 있는 독립 실행형 서비스입니다.

이 자습서에서는 장치 시뮬레이션의 기능 중 일부를 안내합니다. 작동 방식을 표시하며, 사용자 고유의 IoT 솔루션을 테스트하는 데 사용할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 시뮬레이션 구성
> * 시뮬레이션 시작 및 중지
> * 원격 분석 메트릭 보기

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure 구독에서 Azure IoT 장치 시뮬레이션의 배포된 인스턴스가 필요합니다.

장치 시뮬레이션을 아직 배포하지 않은 경우 [Azure IoT 장치 시뮬레이션](iot-accelerators-device-simulation-deploy.md) 자습서를 완료해야 합니다.

## <a name="configuring-device-simulation"></a>장치 시뮬레이션 구성

대시보드 내에서 장치 시뮬레이션을 완전히 구성하고 실행할 수 있습니다. IoT 솔루션 가속기 [프로비전된 솔루션](https://www.azureiotsuite.com/) 페이지에서 대시보드를 엽니다. 새 장치 시뮬레이션 배포에서 **시작**을 클릭합니다.

### <a name="target-iot-hub"></a>대상 IoT Hub

미리 프로비전된 IoT Hub 또는 다른 IoT Hub와 함께 장치 시뮬레이션을 사용할 수 있습니다.

![대상 IoT Hub](./media/iot-accelerators-device-simulation-explore/targethub.png)

> [!NOTE]
> 미리 프로비전된 IoT Hub를 사용하는 옵션은 장치 시뮬레이션을 배포할 때 새 IoT Hub를 만들도록 선택한 경우에만 사용할 수 있습니다. IoT Hub가 없는 경우에는 [Azure Portal](https://portal.azure.com)에서 항상 새로 만들 수 있습니다.

특정 IoT Hub를 대상으로 지정하려면 **iothubowner** 연결 문자열을 제공합니다. 이 연결 문자열은 [Azure Portal](https://portal.azure.com)에서 가져올 수 있습니다.

1. Azure Portal의 IoT Hub 구성 페이지에서 **공유 액세스 정책**을 클릭합니다.
1. **iothubowner**를 클릭합니다.
1. 기본 또는 보조 키를 복사합니다.
1. 대상 IoT Hub 아래의 텍스트 상자에 이 값을 붙여 넣습니다.

![대상 IoT Hub](./media/iot-accelerators-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>장치 모델

장치 모델을 사용하여 시뮬레이트할 장치 유형을 선택할 수 있습니다. 미리 구성된 장치 모델 중 하나를 선택하거나 사용자 지정 장치 모델에 대한 센서 목록을 정의할 수 있습니다.

![장치 모델](./media/iot-accelerators-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>미리 구성된 장치 모델

장치 시뮬레이션은 세 가지 미리 구성된 장치 모델을 제공합니다. 냉각기, 엘리베이터 및 트럭에 대한 장치 모델을 사용할 수 있습니다.

미리 구성된 장치 모델에는 JavaScript 파일에 정의된 고급 동작의 여러 센서가 포함됩니다. 이러한 사용자 지정 동작은 웹 UI에서 지원되지 않습니다. 

다음 표에서는 미리 구성된 각 장치 모델에 대한 구성 목록을 보여 줍니다.

| 장치 모델 | 센서 | 단위 | 
| -------------| ------ | -----| 
| 냉각기 | humidity | % |
| | pressure | psig | 
| | 온도 | F | 
| 엘리베이터 | Floor | 
| | Vibration | MM | 
| | 온도 | F | 
| 트럭 | 위도 | |
| | 경도 | | 
| | speed | mph | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>사용자 지정 장치 모델

사용자 지정 장치 모델을 사용하여 장치를 보다 자세히 나타내는 센서를 모델링할 수 있습니다. 사용자 지정 장치에는 최대 10개의 사용자 지정 센서가 있을 수 있습니다.

사용자 지정 장치 모델 종류를 선택하는 경우 **+ 센서 추가**를 클릭하여 새 센서를 추가할 수 있습니다.

![센서 추가](./media/iot-accelerators-device-simulation-explore/customsensors.png)

사용자 지정 센서에는 다음과 같은 속성이 있습니다.

| 필드 | 설명 |
| ----- | ----------- |
| 센서 이름 | 센서의 이름(예: **temperature** 또는 **speed**)입니다. |
| 동작 | 동작을 사용하여 원격 분석 데이터를 메시지마다 다르게 하여, 실제 데이터를 시뮬레이트할 수 있습니다. **증분**은 전송된 각 메시지의 값을 최소값부터 시작해서 1씩 늘립니다. 최대값이 충족되면 최소값에서 다시 시작됩니다. **감소**는 **증분**과 같은 방식으로 동작하지만 카운트다운합니다. **무작위** 동작은 최소값과 최대값 사이에 임의 값을 생성합니다. |
| 최소값 | 허용되는 범위를 나타내는 가장 낮은 숫자입니다. |
| 최대값 | 허용되는 범위를 나타내는 가장 큰 숫자입니다. |
| 단위 | 센서의 측정 단위(예: °F 또는 MPH)입니다. |

### <a name="number-of-devices"></a>장치 수

현재, 장치 시뮬레이션을 사용하면 최대 20,000개의 장치를 시뮬레이트할 수 있습니다.

![장치 수](./media/iot-accelerators-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>원격 분석 빈도

원격 분석 빈도를 사용하여 시뮬레이트된 장치가 IoT Hub에 데이터를 전송해야 하는 빈도를 지정할 수 있습니다. 10초 간격으로 자주 데이터를 전송할 수도 있고, 99시간, 59분, 59초 간격으로 덜 자주 데이터를 전송할 수도 있습니다.

![원격 분석 빈도](./media/iot-accelerators-device-simulation-explore/frequency.png)

> [!NOTE]
> 미리 프로비전된 IoT Hub 이외의 IoT Hub를 사용하는 경우 대상 IoT Hub에 대한 메시지 제한을 고려해야 합니다. 예를 들어, 10초마다 S1 허브에 원격 분석을 전송하는 1,000개의 시뮬레이트된 장치가 있는 경우 1시간만에 허브에 대한 원격 분석 제한에 도달하게 됩니다.

### <a name="simulation-duration"></a>시뮬레이션 기간

특정 기간 동안 시뮬레이션을 실행하거나, 명시적으로 중지할 때까지 실행되도록 선택할 수 있습니다. 특정 기간을 선택하는 경우 10분부터, 99시간, 59분, 59초 사이의 어떤 기간도 선택할 수 있습니다.

![시뮬레이션 기간](./media/iot-accelerators-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>시뮬레이션 시작 및 중지

필요한 모든 구성 데이터를 양식에 추가했으면 **시뮬레이션 시작** 단추가 사용되도록 설정됩니다. 시뮬레이션을 시작하려면 이 단추를 클릭합니다.

![시뮬레이션 시작](./media/iot-accelerators-device-simulation-explore/start.png)

시뮬레이션에 대해 특정 기간을 지정하는 경우, 해당 시간에 도달하면 자동으로 중지됩니다. 항상 **시뮬레이션 중지**를 클릭하여 시뮬레이션을 일찍 중지할 수 있습니다.

시뮬레이션을 무기한으로 실행하도록 선택한 경우 **시뮬레이션 중지**를 클릭할 때까지 실행됩니다. 언제든지 브라우저를 닫고 장치 시뮬레이션 페이지로 돌아가 시뮬레이션을 중지할 수 있습니다.

![시뮬레이션 중지](./media/iot-accelerators-device-simulation-explore/stop.png)

> [!NOTE]
> 한 번에 하나의 시뮬레이션만 실행할 수 있습니다. 새 시뮬레이션을 시작하기 전에 현재 실행 중인 시뮬레이션을 중지해야 합니다.
