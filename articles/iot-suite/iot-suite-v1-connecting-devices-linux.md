---
title: "Linux에서 C를 사용하여 장치 연결 | Microsoft Docs"
description: "Linux에서 실행되는 C로 작성된 응용 프로그램을 사용하여 미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 장치를 연결하는 방법을 설명합니다."
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0c7c8039-0bbf-4bb5-9e79-ed8cff433629
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a5768041a13d5ddc355c054dc85ba651b0752aba
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-linux"></a>미리 구성된 원격 모니터링 솔루션에 장치 연결(Linux)
[!INCLUDE [iot-suite-v1-selector-connecting](../../includes/iot-suite-v1-selector-connecting.md)]

## <a name="build-and-run-a-sample-c-client-linux"></a>샘플 C 클라이언트 Linux 빌드 및 실행
다음 단계에서는 미리 구성된 원격 모니터링 솔루션과 통신하는 클라이언트 응용 프로그램을 만드는 방법을 보여 줍니다. 이 응용 프로그램은 C로 작성되었으며 Ubuntu Linux에서 작성 및 실행됩니다.

이러한 단계를 완료하려면 Ubuntu 버전 15.04 또는 15.10을 실행하는 장치가 필요합니다. 계속하기 전에 다음 명령을 사용하여 Ubuntu 장치에서 필수 구성 요소 패키지를 설치합니다.

```
sudo apt-get install cmake gcc g++
```

## <a name="install-the-client-libraries-on-your-device"></a>장치에 클라이언트 라이브러리 설치
Azure IoT Hub 클라이언트 라이브러리를 **apt-get** 명령을 사용하여 Ubuntu 장치에 설치할 수 있는 패키지로 사용할 수 있습니다. Ubuntu 컴퓨터에서 IoT Hub 클라이언트 라이브러리와 헤더 파일을 포함하는 패키지를 설치하려면 다음 단계를 완료합니다.

1. 셸에서 AzureIoT 리포지토리를 컴퓨터에 추가합니다.
   
    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```
2. azure-iot-sdk-c-dev 패키지 설치
   
    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## <a name="install-the-parson-json-parser"></a>Parson JSON 파서 설치
IoT Hub 클라이언트 라이브러리는 Parson JSON 파서를 사용하여 메시지 페이로드를 구문 분석합니다. 컴퓨터의 적합한 폴더에서 다음 명령을 사용하여 Parson GitHub 리포지토리를 복제합니다.

```
git clone https://github.com/kgabis/parson.git
```

## <a name="prepare-your-project"></a>프로젝트 준비
Ubuntu 컴퓨터에서 **remote\_monitoring**이라는 폴더를 만듭니다. **remote\_monitoring** 폴더에서:

- **main.c**, **remote\_monitoring.c**, **remote\_monitoring.h**, **CMakeLists.txt**의 4개 파일을 만듭니다.
- **parson**이라는 폴더를 만듭니다.

Parson 리포지토리의 로컬 복사본에서 파일 **parson.c** 및 **parson.h**를 **remote\_monitoring/parson** 폴더로 복사합니다.

텍스트 편집기에서 **remote\_monitoring.c** 파일을 엽니다. 다음 `#include` 문을 추가합니다.
   
```
#include "iothubtransportmqtt.h"
#include "schemalib.h"
#include "iothub_client.h"
#include "serializer_devicetwin.h"
#include "schemaserializer.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "parson.h"
```

[!INCLUDE [iot-suite-v1-connecting-code](../../includes/iot-suite-v1-connecting-code.md)]

## <a name="call-the-remotemonitoringrun-function"></a>remote\_monitoring\_run 함수 실행
텍스트 편집기에서 **remote_monitoring.h** 파일을 엽니다. 다음 코드를 추가합니다.

```
void remote_monitoring_run(void);
```

텍스트 편집기에서 **main.c** 파일을 엽니다. 다음 코드를 추가합니다.

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## <a name="build-and-run-the-application"></a>응용 프로그램 빌드 및 실행
다음 단계에서는 *CMake* 를 사용하여 클라이언트 응용 프로그램을 빌드하는 방법을 설명합니다.

1. 텍스트 편집기에서 **remote_monitoring** 폴더의 **CMakeLists.txt** 파일을 엽니다.

1. 클라이언트 응용 프로그램을 작성하는 방법을 정의하려면 다음 지침을 추가합니다.
   
    ```
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
1. **remote_monitoring** 폴더에서 CMake가 생성하는 *make* 파일을 저장할 폴더를 만든 후 다음과 같이 **cmake** 및 **make** 명령을 실행합니다.
   
    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 클라이언트 응용 프로그램을 실행하고 IoT Hub에 원격 분석을 전송합니다.
   
    ```
    ./sample_app
    ```

[!INCLUDE [iot-suite-v1-visualize-connecting](../../includes/iot-suite-v1-visualize-connecting.md)]

