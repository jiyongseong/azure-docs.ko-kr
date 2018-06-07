---
title: C로 Windows 장치를 원격 모니터링에 프로비전 - Azure | Microsoft Docs
description: Windows에서 실행 중인 C로 작성한 응용 프로그램을 Linux에서 실행하여 원격 모니터링 솔루션 가속기에 장치를 연결하는 방법을 설명합니다.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 51ed87fb0f0c6d526249adaae95d87afaafd8812
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>장치를 원격 모니터링 솔루션 가속기에 연결(Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 물리적 장치를 연결하는 방법을 보여줍니다.

## <a name="create-a-c-client-solution-on-windows"></a>Windows에서 C 클라이언트 솔루션 만들기

제한된 장치에서 실행되는 대부분의 임베디드 응용 프로그램과 마찬가지로, 장치 응용 프로그램의 클라이언트 코드는 C로 작성됩니다. 이 자습서에서는 Windows를 실행하는 컴퓨터에서 응용 프로그램을 빌드합니다.

### <a name="create-the-starter-project"></a>시작 프로젝트 만들기

Visual Studio 2017에서 시작 프로젝트를 만들고 IoT Hub 장치 클라이언트 NuGet 패키지를 추가합니다.

1. Visual Studio에서 Visual C++ **Windows 콘솔 응용 프로그램** 템플릿을 사용하여 C 콘솔 응용 프로그램을 만듭니다. 프로젝트 이름을 **RMDevice**로 지정합니다.

    ![Visual C++ Windows 콘솔 응용 프로그램 만들기](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. **솔루션 탐색기**에서 `stdafx.h`, `targetver.h` 및 `stdafx.cpp` 파일을 삭제합니다.

1. **솔루션 탐색기**에서 `RMDevice.cpp` 파일의 이름을 `RMDevice.c`로 바꿉니다.

    ![이름이 바뀐 RMDevice.c 파일을 보여주는 솔루션 탐색기](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **찾아보기**를 선택한 다음 NuGet 패키지를 검색하여 설치합니다.

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![설치된 Microsoft.Azure.IoTHub 패키지를 보여주는 NuGet 패키지 관리자](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택하여 프로젝트의 **속성 페이지** 대화 상자를 엽니다. 자세한 내용은 [Visual C++ 프로젝트 속성 설정](https://docs.microsoft.com/cpp/ide/working-with-project-properties)을 참조하세요.

1. **C/C++** 폴더를 선택한 다음 **미리 컴파일된 헤더** 속성 페이지를 선택합니다.

1. **미리 컴파일된 헤더**를 **미리 컴파일된 헤더 사용 안 함**으로 설정합니다. 그런 다음 **적용**을 선택합니다.

    ![미리 컴파일된 헤더를 사용하지 않는 프로젝트를 보여주는 프로젝트 속성](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. **Linker** 폴더를 선택한 다음 **입력** 속성 페이지를 선택합니다.

1. `crypt32.lib`를 **추가 종속성** 속성에 추가합니다. 프로젝트 속성 값을 저장하려면 **확인**을 클릭한 다음 **확인**을 다시 클릭합니다.

    ![crypt32.lib가 포함된 Linker를 보여주는 프로젝트 속성](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Parson JSON 라이브러리 추가

Parson JSON 라이브러리를 **RMDevice** 프로젝트에 추가하고 필수 `#include` 문을 추가합니다.

1. 컴퓨터의 적합한 폴더에서 다음 명령을 사용하여 Parson GitHub 리포지토리를 복제합니다.

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Parson 리포지토리의 로컬 복사본에서 `parson.h`와 `parson.c` 파일을 **RMDevice** 프로젝트 폴더로 복사합니다.

1. Visual Studio에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가**를 선택하고 **기존 항목**을 선택합니다.

1. **기존 항목 추가** 대화 상자에서 **RMDevice** 프로젝트 폴더의 `parson.h` 및 `parson.c` 파일을 선택합니다. 이러한 두 파일을 프로젝트에 추가하려면 **추가**를 클릭합니다.

    ![parson.h와 parson.c 파일을 보여주는 솔루션 탐색기](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. Visual Studio에서 `RMDevice.c` 파일을 엽니다. 기존 `#include` 문을 다음 코드로 바꿉니다.

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > 이제 솔루션을 빌드하여 프로젝트에 올바른 종속성이 설정되어 있는지 확인할 수 있습니다.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

코드를 추가하여 **remote\_monitoring\_run** 함수를 호출한 다음 장치 응용 프로그램을 빌드하고 실행합니다.

1. **remote\_monitoring\_run** 함수를 호출하려면 **main** 함수를 다음 코드로 바꿉니다.

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. **빌드** 다음 **솔루션 빌드**를 선택하여 장치 응용 프로그램을 빌드합니다.

1. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 다음 **새 인스턴스 시작**을 선택하여 샘플을 실행합니다. 다음과 같은 경우 콘솔에 메시지가 표시됩니다.

    * 응용 프로그램이 샘플 원격 분석 데이터를 솔루션 가속기에 보낼 때.
    * 솔루션 대시보드에 설정된 원하는 속성 값을 수신할 때.
    * 솔루션 대시보드에서 호출된 메서드에 응답할 때.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
