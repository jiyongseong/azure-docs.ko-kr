---
title: C를 사용하여 원격 모니터링으로 Raspberry Pi 프로비전 - Azure | Microsoft Docs
description: C에 작성된 응용 프로그램을 사용하여 원격 모니터링 솔루션 가속기에 Raspberry Pi 장치를 연결하는 방법을 설명합니다.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: fc50a33f-9fb9-42d7-b1b8-eb5cff19335e
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9de7616ec7174f6c55888a659e9a12bca1e07f94
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>원격 모니터링 솔루션 가속기에 Raspberry Pi 장치 연결(C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 물리적 장치를 연결하는 방법을 보여줍니다. 제한된 장치에서 실행되는 포함된 응용 프로그램과 마찬가지로 Raspberry Pi 장치 응용 프로그램에 대한 클라이언트 코드는 C에서 작성되었습니다. 이 자습서에서는 Raspbian OS를 실행 중인 Raspberry Pi에서 응용 프로그램을 빌드합니다.

### <a name="required-hardware"></a>필수 하드웨어

Raspberry Pi의 명령줄에 원격으로 연결할 수 있는 데스크톱 컴퓨터이며

[Raspberry Pi 3용 Microsoft IoT 시작 키트](https://azure.microsoft.com/develop/iot/starter-kits/) 또는 동등한 구성 요소입니다. 이 자습서에서는 키트에서 다음 항목을 사용합니다.

- Raspberry Pi 3
- MicroSD 카드(NOOBS 포함)
- USB 미니 케이블
- 이더넷 케이블

### <a name="required-desktop-software"></a>필수 데스크톱 소프트웨어

Raspberry Pi의 명령줄에 원격으로 액세스할 수 있도록 데스크톱 컴퓨터에 SSH 클라이언트가 필요합니다.

- Windows에서는 SSH 클라이언트를 포함하지 않습니다. [PuTTY](http://www.putty.org/)를 사용하는 것이 좋습니다.
- 대부분의 Linux 배포판 및 Mac OS는 명령줄 SSH 유틸리티를 포함합니다. 자세한 내용은 [Linux 또는 Mac OS를 사용하는 SSH](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)를 참조하세요.

### <a name="required-raspberry-pi-software"></a>필수 Raspberry Pi 소프트웨어

이 문서에서는 [Raspberry Pi에 Raspbian OS](https://www.raspberrypi.org/learning/software-guide/quickstart/)의 최신 버전을 설치했다고 가정합니다.

다음 단계는 솔루션 가속기에 연결하는 C 응용 프로그램을 빌드하기 위해 Raspberry Pi를 준비하는 방법을 보여줍니다.

1. **ssh**를 사용하여 Raspberry Pi에 연결합니다. 자세한 내용은 [Raspberry Pi 웹 사이트](https://www.raspberrypi.org/)에서 [SSH(Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)를 참조하세요.

1. 다음 명령을 사용하여 Raspberry Pi를 업데이트합니다.

    ```sh
    sudo apt-get update
    ```

1. 다음 명령을 사용하여 Raspberry Pi에 필요한 개발 도구 및 라이브러리를 추가합니다.

    ```sh
    sudo apt-get purge libssl-dev
    sudo apt-get install g++ make cmake gcc git libssl1.0-dev build-essential curl libcurl4-openssl-dev uuid-dev
    ```

1. 다음 명령을 사용하여 Raspberry Pi에서 IoT Hub 클라이언트 라이브러리를 다운로드, 빌드 및 설치합니다.

    ```sh
    cd ~
    git clone --recursive https://github.com/azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c/build_all/linux
    ./build.sh --no-make
    cd ../../cmake/iotsdk_linux
    make
    sudo make install
    ```

## <a name="create-a-project"></a>프로젝트 만들기

Raspberry Pi에 **ssh** 연결을 사용하여 다음 단계를 완료합니다.

1. Raspberry Pi의 홈 폴더에 `remote_monitoring`이라는 폴더를 만듭니다. 셸에서 이 폴더로 이동합니다.

    ```sh
    cd ~
    mkdir remote_monitoring
    cd remote_monitoring
    ```

1. `remote_monitoring` 폴더에 **main.c**, **remote_monitoring.c**, **remote_monitoring.h**, **CMakeLists.txt**의 4개 파일을 만듭니다.

1. 텍스트 편집기에서 **remote_monitoring.c** 파일을 엽니다. Raspberry Pi에서 **nano** 또는 **vi** 텍스트 편집기를 사용할 수 있습니다. 다음 `#include` 문을 추가합니다.

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

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

**remote_monitoring.c** 파일을 저장하고 편집기를 종료합니다.

## <a name="add-code-to-run-the-app"></a>코드를 추가하여 앱 실행

텍스트 편집기에서 **remote_monitoring.h** 파일을 엽니다. 다음 코드를 추가합니다.

```c
void remote_monitoring_run(void);
```

**remote_monitoring.h** 파일을 저장하고 편집기를 종료합니다.

텍스트 편집기에서 **main.c** 파일을 엽니다. 다음 코드를 추가합니다.

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

**main.c** 파일을 저장하고 편집기를 종료합니다.

## <a name="build-and-run-the-application"></a>응용 프로그램 빌드 및 실행

다음 단계에서는 *CMake* 를 사용하여 클라이언트 응용 프로그램을 빌드하는 방법을 설명합니다.

1. 텍스트 편집기에서 `remote_monitoring` 폴더의 **CMakeLists.txt** 파일을 엽니다.

1. 클라이언트 응용 프로그램을 작성하는 방법을 정의하려면 다음 지침을 추가합니다.

    ```cmake
    macro(compileAsC99)
      if (CMAKE_VERSION VERSION_LESS "3.1")
        if (CMAKE_C_COMPILER_ID STREQUAL "GNU")
          set (CMAKE_C_FLAGS "--std=c99 ${CMAKE_C_FLAGS}")
          set (CMAKE_CXX_FLAGS "--std=c++11 ${CMAKE_CXX_FLAGS}")
        endif()
      else()
        set (CMAKE_C_STANDARD 99)
        set (CMAKE_CXX_STANDARD 11)
      endif()
    endmacro(compileAsC99)

    cmake_minimum_required(VERSION 2.8.11)
    compileAsC99()

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "/usr/local/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
      serializer
      iothub_client_mqtt_transport
      umqtt
      iothub_client
      aziotsharedutil
      parson
      pthread
      curl
      ssl
      crypto
      m
    )
    ```

1. **CMakeLists.txt** 파일을 저장하고 편집기를 종료합니다.

1. `remote_monitoring` 폴더에 CMake에서 생성하는 *make* 파일을 저장할 폴더를 만듭니다. 그리고 다음과 같이 **cmake** 및 **make** 명령을 실행합니다.

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 클라이언트 응용 프로그램을 실행하고 IoT Hub에 원격 분석을 전송합니다.

    ```sh
    ./sample_app
    ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
