---
title: 포함 파일
description: 포함 파일
services: iot-suite
author: dominicbetts
ms.service: iot-suite
ms.topic: include
ms.date: 04/24/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 500e335d0b2eddc56cdfb9828236bc4676d9b6aa
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
> [!div class="op_single_selector"]
> * [Windows에서 C](../articles/iot-accelerators/iot-accelerators-connecting-devices.md)
> * [Linux에서 C](../articles/iot-accelerators/iot-accelerators-connecting-devices-linux.md)
> * [Node.js(일반)](../articles/iot-accelerators/iot-accelerators-connecting-devices-node.md)
> * [Raspberry Pi의 Node.js](../articles/iot-accelerators/iot-accelerators-connecting-pi-node.md)
> * [Raspberry Pi의 C](../articles/iot-accelerators/iot-accelerators-connecting-pi-c.md)

이 자습서에서는 원격 모니터링 [솔루션 가속기](../articles/iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)에 다음과 같은 원격 분석을 보내는 **냉각기** 장치를 구현합니다.

* 온도
* 압력
* 습도

간단히 하기 위해 코드는 **냉각기**에 대한 샘플 원격 분석 값을 생성합니다. 실제 센서를 장치에 연결하고 실제 원격 분석을 보내 샘플을 확장할 수 있습니다.

샘플 장치는 또한:

* 솔루션에 메타데이터를 보내 해당 기능을 설명합니다.
* 솔루션의 **장치** 페이지에서 트리거된 작업에 응답합니다.
* 솔루션의 **장치** 페이지에서 보내는 구성 변경 내용에 응답합니다.

이 자습서를 완료하려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

장치에 대한 코드를 작성하기 전에, 원격 모니터링 솔루션 가속기를 배포하고 이 솔루션에 새로운 물리적 장치를 추가합니다.

### <a name="deploy-your-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기 배포

이 자습서에서 만드는 **냉각기** 장치는 [원격 모니터링](../articles/iot-suite/iot-suite-remote-monitoring-explore.md) 솔루션 가속기의 인스턴스에 데이터를 전송합니다. Azure 계정에서 원격 모니터링 솔루션 가속기를 미리 프로비전하지 않은 경우 [원격 모니터링 솔루션 가속기 배포](../articles/iot-accelerators/iot-accelerators-remote-monitoring-deploy.md)를 참조하세요.

원격 모니터링 솔루션의 배포 프로세스가 완료되면 **시작** 을 클릭하여 브라우저에서 솔루션 대시보드를 엽니다.

![솔루션 대시보드](media/iot-suite-selector-connecting/dashboard.png)

### <a name="add-your-device-to-the-remote-monitoring-solution"></a>장치를 원격 모니터링 솔루션에 추가

> [!NOTE]
> 솔루션에 장치가 이미 추가되어 있으면 이 단계를 건너뜁니다. 그러나 다음 단계에서는 장치 연결 문자열이 필요합니다. [Azure Portal](https://portal.azure.com)에서 검색하거나 [az iot](https://docs.microsoft.com/cli/azure/iot?view=azure-cli-latest) CLI 도구를 사용하여 장치 연결 문자열을 검색할 수 있습니다.

솔루션 가속기에 연결하는 장치는 유효한 자격 증명을 사용하여 IoT Hub에 자신을 식별할 수 있어야 합니다. 솔루션에 장치를 추가할 때 자격 증명이 포함된 장치 연결 문자열을 저장할 기회가 있습니다. 이 자습서의 뒷부분에서는 클라이언트 응용 프로그램에 장치 연결 문자열을 포함시킵니다.

원격 모니터링 솔루션에 장치를 추가하려면 솔루션의 **장치** 페이지에서 다음 단계를 완료합니다.

1. **+ 새 장치**를 선택한 다음, **장치 유형**으로 **물리적**을 선택합니다.

    ![물리적 장치 추가](media/iot-suite-selector-connecting/devicesprovision.png)

1. 장치 ID로 **물리적 냉각기**를 입력합니다. **대칭 키** 및 **자동 생성 키** 옵션을 선택합니다.

    ![장치 옵션 선택](media/iot-suite-selector-connecting/devicesoptions.png)

1. **적용**을 선택합니다. 그런 다음, **장치 ID**, **기본 키**, **연결 문자열 기본 키** 값을 메모합니다.

    ![자격 증명 검색](media/iot-suite-selector-connecting/credentials.png)

이제 물리적 장치를 원격 모니터링 솔루션 가속기에 추가했고 장치 연결 문자열을 확인했습니다. 다음 섹션에서는 장치 연결 문자열을 사용하여 솔루션에 연결하는 클라이언트 응용 프로그램을 구현합니다.

클라이언트 응용 프로그램은 기본 제공 **냉각기** 장치 모델을 구현합니다. 솔루션 가속기 장치 모델은 장치에 대해 다음을 지정합니다.

* 장치가 솔루션에 보고하는 속성 예를 들어, **냉각기** 장치는 해당 펌웨어 및 위치에 대한 정보를 보고합니다.
* 장치가 솔루션에 보내는 원격 분석의 유형 예를 들어, **냉각기** 장치는 온도, 습도 및 압력 값을 보냅니다.
* 장치에서 실행되도록 솔루션에서 예약할 수 있는 메서드 예를 들어, **냉각기** 장치는 **Reboot**, **FirmwareUpdate**, **EmergencyValveRelease** 및 **IncreasePressure** 메서드를 구현해야 합니다.