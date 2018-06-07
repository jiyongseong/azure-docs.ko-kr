---
title: C를 사용하여 Azure IoT Hub에 시뮬레이션된 X.509 장치 프로비전 | Microsoft Docs
description: Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service용 C 장치 SDK를 사용하여 시뮬레이션된 X.509 장치 만들기 및 프로비전
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/16/2018
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: e5fe9282dd10bd6bdc41c63718a884a92da4d7c6
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-provision-an-x509-simulated-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service용 C 장치 SDK를 사용하여 시뮬레이션된 X.509 장치 만들기 및 프로비전
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

다음 단계에서는 Windows OS를 실행 중인 개발 컴퓨터에서 X.509 장치를 시뮬레이션하고, 코드 샘플을 사용하여 이 시뮬레이션된 장치를 Device Provisioning Service 및 IoT Hub와 연결하는 방법을 보여 줍니다. 

자동 프로비전 프로세스에 익숙하지 않은 경우 [자동 프로비전 개념](concepts-auto-provisioning.md)도 검토하세요. 계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>개발 환경 준비 

1. 컴퓨터에 Visual Studio 2015 또는 [Visual Studio 2017](https://www.visualstudio.com/vs/)이 설치되어 있는지 확인합니다. Visual Studio 설치에서 ['C++를 사용한 데스크톱 개발'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드를 사용하도록 설정해야 합니다.

2. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드하여 설치합니다. `cmake`를 설치하기 **전에** 'C++를 사용한 데스크톱 개발' 워크로드가 있는 Visual Studio를 컴퓨터에 설치해야 합니다. 

3. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 **Git Bash**가 포함됩니다. 

4. 명령 프롬프트 또는 Git Bash를 엽니다. 장치 시뮬레이션 샘플 코드에 대한 GitHub 리포지토리를 복제합니다.
    
    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. CMake 빌드 프로세스에 대한 이 GitHub 리포지토리의 로컬 복사본에 폴더를 만듭니다. 

    ```cmd
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. 코드 샘플에서는 X.509 인증서를 사용하여 X.509 인증을 통해 증명을 제공합니다. 다음 명령을 실행하여 개발 클라이언트 플랫폼 및 [증명 메커니즘](concepts-security.md#attestation-mechanism)(X.509 인증서)과 관련된 SDK 버전을 빌드합니다. 또한 시뮬레이션된 장치에 대해 Visual Studio 솔루션을 생성합니다. 

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```
    
    `cmake`에서 C++ 컴파일러를 찾지 못하면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 


<a id="portalenroll"></a>

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>자체 서명된 X.509 장치 인증서 및 개별 등록 항목 만들기

1. *cmake* 폴더에 생성된 `azure_iot_sdks.sln`이라는 솔루션을 열고 Visual Studio에서 빌드합니다.

2. **Provision\_Samples** 폴더 아래에서 **dice\_device\_enrollment** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 솔루션을 실행합니다. 메시지가 표시되면 출력 창에서 개별 등록에 대해 **i**를 입력합니다. 출력 창에는 시뮬레이션된 장치에 대해 로컬로 생성된 X.509 인증서가 표시됩니다. *-----BEGIN CERTIFICATE-----* 에서 시작하여 첫 번째 *-----END CERTIFICATE-----* 에서 끝나는 출력(이 두 줄 모두 포함)을 클립보드에 복사합니다. 출력 창의 첫 번째 인증서만 필요하기 때문입니다.
 
3. Windows 컴퓨터에 **_X509testcert.pem_** 이라는 파일을 만들어 원하는 편집기에서 연 다음, 클립보드의 내용을 이 파일에 복사합니다. 파일을 저장합니다. 

4. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, 프로비전 서비스를 엽니다.

5. Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭을 선택하고 맨 위에서 **추가** 단추를 클릭합니다. 

6. **등록 추가** 패널 아래에 다음 정보를 입력합니다.
    - ID 증명 *메커니즘*으로 **X.509**를 선택합니다.
    - *기본 인증서 .pem 또는 .cer 파일* 아래에서 *파일 선택*을 클릭하여 이전 단계에서 만든 **X509testcert.pem** 인증서 파일을 선택합니다.
    - 필요에 따라 다음 정보를 입력합니다.
      - 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
      - 고유한 장치 ID를 입력합니다. 장치 이름을 지정할 때 중요한 데이터가 포함되지 않도록 합니다. 
      - 장치에 대해 원하는 초기 구성으로 **초기 장치 쌍 상태**를 업데이트합니다.
    - 완료되면 **저장** 단추를 클릭합니다. 

    [![포털에서 X.509 증명에 대한 개별 등록 추가](./media/quick-create-simulated-device-x509/individual-enrollment.png)](./media/quick-create-simulated-device-x509/individual-enrollment.png#lightbox)

   등록에 성공하면 X.509 장치가 *개별 등록* 탭의 *등록 ID* 열에 **riot-device-cert**로 표시됩니다. 



<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>장치에 대한 첫 번째 부팅 시퀀스 시뮬레이션

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 블레이드를 선택하고 **_ID 범위_** 값을 기록해 둡니다.

    ![포털 블레이드에서 DPS 끝점 정보 추출](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 컴퓨터의 Visual Studio에서, **Provision\_Samples** 폴더 아래에 있는 **prov\_dev\_client\_sample**이라는 샘플 프로젝트로 이동하여 **prov\_dev\_client\_sample.c** 파일을 엽니다.

3. _ID 범위_ 값을 `id_scope` 변수에 할당합니다. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. 동일한 파일의 **main()** 함수에서 **SECURE_DEVICE_TYPE**이 X.509로 설정되어 있는지 확인합니다.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

   존재할 수 있는 `hsm_type = SECURE_DEVICE_TYPE_TPM;` 문을 주석 처리하거나 삭제합니다. 

5. **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 샘플을 실행합니다. 장치를 부팅하고 IoT Hub 정보를 얻기 위해 Device Provisioning Service에 연결하는 과정을 시뮬레이션하는 메시지를 확인합니다. 허브가 성공적으로 등록되었다는 *서비스에서 등록 정보 수신: yourhuburl!* 메시지를 살펴봅니다. 메시지가 표시되면 창을 닫습니다.

6. 포털에서 프로비전 서비스와 연결된 IoT Hub로 이동하여 **IoT 장치** 블레이드를 엽니다. 시뮬레이션된 X.509 장치가 허브에 성공적으로 프로비전되면 *상태*가 **사용**인 장치 ID가 **IoT 장치** 블레이드에 표시됩니다. 샘플 장치 응용 프로그램을 실행하기 전에 블레이드를 이미 열어 놓은 경우 화면 상단의 **새로 고침** 단추를 클릭해야 합니다. 

    ![장치가 IoT Hub에 등록됨](./media/quick-create-simulated-device/hub-registration.png) 

    장치에 대한 등록 항목의 기본값으로부터 *초기 장치 쌍 상태*를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 장치 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.


> [!IMPORTANT]
> 이 빠른 시작의 단계를 다음과 같이 수정하여 X.509 장치의 *그룹 등록*을 수행할 수도 있습니다.
>    1. [Windows 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#windows) 가이드의 **WebSockets** 섹션에 따라 기본 **SChannel** 대신 **OpenSSL** 라이브러리를 사용하도록 Windows 시스템을 구성합니다. Linux 컴퓨터는 기본적으로 OpenSSL을 사용합니다. 
>    2. 위에 나오는 [Device Provisioning Service에서 장치 등록 항목 만들기](#portalenroll) 섹션의 2단계에서 그룹 등록에 대해 **g**를 입력합니다.
>    3. [동일한 섹션](#portalenroll)의 4단계 및 5단계에서 **등록 그룹**을 선택하고, 그룹 항목에 필요한 정보를 입력합니다.  
>

## <a name="clean-up-resources"></a>리소스 정리

장치 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 장치 클라이언트 샘플 출력 창을 닫습니다.
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 Device Provisioning Service를 선택합니다. 서비스에 대한 **등록 관리** 블레이드를 연 다음, **개별 등록** 탭을 클릭합니다. 이 빠른 시작에서 등록한 장치의 *등록 ID*를 선택하고, 위쪽의 **삭제** 단추를 클릭합니다. 
1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. 허브에 대한 **IoT 장치** 블레이드를 열고, 이 빠른 시작에서 등록한 장치의 *장치 ID*를 선택한 다음, 위쪽의 **삭제** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 시뮬레이션된 X.509 장치를 Windows 컴퓨터에 만들고, 포털에서 Azure IoT Hub Device Provisioning Service를 사용하여 IoT Hub에 이 장치를 프로비전했습니다. 프로그래밍 방식으로 X.509 장치를 등록하는 방법을 알아보려면 프로그래밍 방식으로 X.509 장치를 등록하는 빠른 시작으로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service에 X.509 장치 등록](quick-enroll-device-x509-java.md)
