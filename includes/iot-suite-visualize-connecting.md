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
ms.openlocfilehash: 5eca35141544e8ea451dfc7471a83e3e1e980360
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
## <a name="view-device-telemetry"></a>장치 원격 분석 보기

솔루션의 **장치** 페이지에서 장치에서 보낸 원격 분석을 볼 수 있습니다.

1. **장치** 페이지의 장치 목록에서 프로비전한 장치를 선택합니다. 패널은 장치 원격 분석의 그림을 포함한 장치에 대한 정보를 표시합니다.

    ![장치 세부 정보 보기](media/iot-suite-visualize-connecting/devicesdetail.png)

1. **압력**을 선택하여 원격 분석 표시를 변경합니다.

    ![압력 원격 분석 보기](media/iot-suite-visualize-connecting/devicespressure.png)

1. 장치에 대한 진단 정보를 보려면 **진단** 아래로 스크롤합니다.

    ![장치 진단 보기](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>장치에서 작동

장치에서 메서드를 호출하려면 원격 모니터링 솔루션의 **장치** 페이지를 사용합니다. 예를 들어, 원격 모니터링 솔루션에서 **냉각기** 장치는 **FirmwareUpdate** 메서드를 구현합니다.

1. **장치**를 선택하여 솔루션의 **장치** 페이지로 이동합니다.

1. **장치** 페이지의 장치 목록에서 프로비전한 장치를 선택합니다.

    ![물리적 장치 선택](media/iot-suite-visualize-connecting/devicesselect.png)

1. 장치에서 호출할 수 있는 메서드의 목록을 표시하려면 **작업**을 선택한 다음, **메서드 실행**을 선택합니다. 여러 장치에서 작업이 실행되도록 예약하려면 목록에서 여러 장치를 선택하면 됩니다. **작업** 패널에 선택한 모든 장치에 공통된 메서드 형식이 표시됩니다.

1. **FirmwareUpdate**를 선택하고 작업 이름을 **UpdatePhysicalChiller**라고 설정합니다. **펌웨어 버전**을 **2.0.0**으로 설정하고, **펌웨어 URI**를 **http://contoso.com/updates/firmware.bin**으로 설정한 다음, **적용**을 선택합니다.

    ![펌웨어 업데이트 예약](media/iot-suite-visualize-connecting/deviceschedule.png)

1. 시뮬레이션된 장치가 메서드를 처리하는 동안 장치 코드를 실행하는 콘솔에 메시지 시퀀스가 표시됩니다.

1. 업데이트가 완료되면 **장치** 페이지에 새 펌웨어 버전이 표시됩니다.

    ![업데이트 완료](media/iot-suite-visualize-connecting/complete.png)

> [!NOTE]
> 솔루션에서 작업의 상태를 추적하려면 **보기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

[원격 모니터링 솔루션 가속기 사용자 지정](../articles/iot-accelerators/iot-accelerators-remote-monitoring-customize.md) 문서에서는 솔루션 가속기를 사용자 지정하는 몇 가지 방법에 대해 설명합니다.