---
title: "Windows에서 C를 사용하여 장치 연결 | Microsoft Docs"
description: "Windows에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 56c58a31c6b20bd8da7d1442ae75425cb3e20f3d
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>미리 구성된 원격 모니터링 솔루션에 장치 연결(Windows)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Windows에서 C 샘플 솔루션 만들기
다음 단계에서는 미리 구성된 원격 모니터링 솔루션과 통신하는 클라이언트 응용 프로그램을 만드는 방법을 보여 줍니다. 이 응용 프로그램은 C로 작성되었으며 Windows에서 작성 및 실행됩니다.

Visual Studio 2015 또는 Visual Studio 2017에서 시작 프로젝트를 만들고 IoT Hub 장치 클라이언트 NuGet 패키지를 추가합니다.

1. Visual Studio에서 Visual C++ **Win32 콘솔 응용 프로그램** 템플릿을 사용하여 C 콘솔 응용 프로그램을 만듭니다. 프로젝트 이름을 **RMDevice**로 지정합니다.
2. **Win32 응용 프로그램 마법사**의 **응용 프로그램 설정** 페이지에서 **콘솔 응용 프로그램**이 선택되어 있는지 확인하고 **미리 컴파일된 헤더** 및 **SDL(Security Development Lifecycle) 검사**의 선택을 취소합니다.
3. **솔루션 탐색기**에서 stdafx.h, targetver.h 및 stdafx.cpp 파일을 삭제합니다.
4. **솔루션 탐색기**에서 RMDevice.cpp 파일의 이름을 RMDevice.c로 바꿉니다.
5. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. **찾아보기**를 클릭하고 다음 NuGet 패키지를 검색하여 설치합니다.
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 클릭하여 프로젝트의 **속성 페이지** 대화 상자를 엽니다. 자세한 내용은 [Visual C++ 프로젝트 속성 설정][lnk-c-project-properties]을 참조하세요. 
7. **Linker** 폴더를 클릭한 다음 **입력** 속성 페이지를 클릭합니다.
8. **crypt32.lib**를 **추가 종속성** 속성에 추가합니다. **확인**을 한 번 클릭한 다음 다시 **확인**을 클릭하여 프로젝트 속성 값을 저장합니다.

Parson JSON 라이브러리를 **RMDevice** 프로젝트에 추가하고 필수 `#include` 문을 추가합니다.

1. 컴퓨터의 적합한 폴더에서 다음 명령을 사용하여 Parson GitHub 리포지토리를 복제합니다.

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. Parson 리포지토리의 로컬 복사본에서 parson.h 및 parson.c 파일을 **RMDevice** 프로젝트 폴더로 복사합니다.

1. Visual Studio에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **추가**를 클릭하고 **기존 항목**을 클릭합니다.

1. **기존 항목 추가** 대화 상자에서 **RMDevice** 프로젝트 폴더의 parson.h 및 parson.c 파일을 선택합니다. 그런 다음 **추가**를 클릭하여 프로젝트에 이러한 두 파일을 추가합니다.

1. Visual Studio에서 RMDevice.c 파일을 엽니다. 기존 `#include` 문을 다음 코드로 바꿉니다.
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > 이제 이를 빌드하여 프로젝트에 올바른 종속성 설정이 있는지 확인할 수 있습니다.

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

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

1. **빌드**를 클릭한 다음 **솔루션 빌드**를 클릭하여 장치 응용 프로그램을 빌드합니다.

1. **솔루션 탐색기**에서 **RMDevice** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 클릭한 다음 **새 인스턴스 시작**을 클릭하여 샘플을 실행합니다. 콘솔은 응용 프로그램이 미리 구성된 솔루션에 샘플 원격 분석을 보내고 솔루션 대시보드에 설정된 desired 속성 값을 수신하고 솔루션 대시보드에서 호출된 메서드에 응답함에 따라 메시지를 표시합니다.

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx
