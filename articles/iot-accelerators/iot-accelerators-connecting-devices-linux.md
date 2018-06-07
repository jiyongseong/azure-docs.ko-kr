---
title: C로 Linux 장치를 원격 모니터링에 프로비전 - Azure | Microsoft Docs
description: C로 작성한 응용 프로그램을 Linux에서 실행하여 원격 모니터링 솔루션 가속기에 장치를 연결하는 방법을 설명합니다.
services: iot-suite
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 9ccf2b185c5aa67b41aab650989ad0326aba0215
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>장치를 원격 모니터링 솔루션 가속기에 연결(Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

이 자습서는 원격 모니터링 솔루션 가속기에 물리적 장치를 연결하는 방법을 보여줍니다.

## <a name="create-a-c-client-project-on-linux"></a>Linux에서 C 클라이언트 프로젝트 만들기

제한된 장치에서 실행되는 대부분의 임베디드 응용 프로그램과 마찬가지로, 장치 응용 프로그램의 클라이언트 코드는 C로 작성됩니다. 이 자습서에서는 Ubuntu(Linux)를 실행하는 컴퓨터에서 응용 프로그램을 빌드합니다.

이러한 단계를 완료하려면 Ubuntu 버전 15.04 이상을 실행하는 장치가 필요합니다. 계속하기 전에 다음 명령을 사용하여 Ubuntu 장치에서 필수 구성 요소 패키지를 설치합니다.

```sh
sudo apt-get install cmake gcc g++
```

### <a name="install-the-client-libraries-on-your-device"></a>장치에 클라이언트 라이브러리 설치

Azure IoT Hub 클라이언트 라이브러리를 **apt-get** 명령을 사용하여 Ubuntu 장치에 설치할 수 있는 패키지로 사용할 수 있습니다. Ubuntu 컴퓨터에서 IoT Hub 클라이언트 라이브러리와 헤더 파일을 포함하는 패키지를 설치하려면 다음 단계를 완료합니다.

1. 셸에서 AzureIoT 리포지토리를 컴퓨터에 추가합니다.

    ```sh
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

1. azure-iot-sdk-c-dev 패키지 설치

    ```sh
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

### <a name="install-the-parson-json-parser"></a>Parson JSON 파서 설치

IoT Hub 클라이언트 라이브러리는 Parson JSON 파서를 사용하여 메시지 페이로드를 구문 분석합니다. 컴퓨터의 적합한 폴더에서 다음 명령을 사용하여 Parson GitHub 리포지토리를 복제합니다.

```sh
git clone https://github.com/kgabis/parson.git
```

### <a name="prepare-your-project"></a>프로젝트 준비

Ubuntu 컴퓨터에서 `remote_monitoring`이라는 폴더를 만듭니다. `remote_monitoring` 폴더에:

- 4개의 `main.c`, `remote_monitoring.c`, `remote_monitoring.h` 및 `CMakeLists.txt` 파일을 만듭니다.
- `parson`이라는 폴더를 만듭니다.

Parson 리포지토리의 로컬 복사본에서 `parson.c` 및 `parson.h` 파일을 `remote_monitoring/parson` 폴더로 복사합니다.

텍스트 편집기에서 `remote_monitoring.c` 파일을 엽니다. 다음 `#include` 문을 추가합니다.

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

## <a name="add-code-to-run-the-app"></a>코드를 추가하여 앱 실행

텍스트 편집기에서 `remote_monitoring.h` 파일을 엽니다. 다음 코드를 추가합니다.

```c
void remote_monitoring_run(void);
```

텍스트 편집기에서 `main.c` 파일을 엽니다. 다음 코드를 추가합니다.

```c
#include "remote_monitoring.h"

int main(void)
{
  remote_monitoring_run();

  return 0;
}
```

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

    set(AZUREIOT_INC_FOLDER "${CMAKE_SOURCE_DIR}" "${CMAKE_SOURCE_DIR}/parson" "/usr/include/azureiot" "/usr/include/azureiot/inc")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./parson/parson.c
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./parson/parson.h
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_mqtt_transport
        aziotsharedutil
        umqtt
        pthread
        curl
        ssl
        crypto
        m
    )
    ```

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
