---
title: Azure IoT Hub Device Provisioning Service를 위한 장치 설정
description: 장치 제조 프로세스 중 IoT Hub Device Provisioning Service를 통해 프로비전할 장치 설정
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c885e4d5d747d913eaf0b7137b240950e920e7ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service를 사용하여 장치 프로비전

이전 자습서에서 장치를 IoT Hub에 자동으로 프로비전하도록 Azure IoT Hub Device Provisioning Service를 설정하는 방법을 배웠습니다. 이 자습서에서는 IoT Hub가 자동으로 프로비전되도록 제조 과정에서 장치를 설정하는 방법을 보여줍니다. 장치를 처음으로 부팅하고 프로비전 서비스에 연결할 때 장치의 [증명 메커니즘](concepts-device.md#attestation-mechanism)에 따라 장치가 프로비전됩니다. 이 자습서에서는 다음과 같은 프로세스를 설명합니다.

> [!div class="checklist"]
> * 플랫폼별 장치 프로비전 서비스 클라이언트 SDK 빌드
> * 보안 아티팩트 추출
> * 장치 등록 소프트웨어 만들기

## <a name="prerequisites"></a>필수 조건

계속하려면 이전 자습서 [1 - 클라우드 설정](./tutorial-set-up-cloud.md)의 지침에 따라 Device Provisioning Service 인스턴스 및 IoT Hub를 만들어야 합니다.

이 자습서에서는 C용 Device Provisioning Service 클라이언트 SDK를 포함하고 있는 [Azure IoT SDK 및 C 리포지토리용 라이브러리](https://github.com/Azure/azure-iot-sdk-c)를 사용합니다. 이 SDK는 현재 Windows 또는 Ubuntu 구현에서 실행 중인 장치에 TPM 및 X.509 지원을 제공합니다. 이 자습서는 Windows 개발 클라이언트 사용을 기반으로 하며, 마찬가지로 사용자가 Visual Studio 2017의 기본적인 내용을 알고 있다고 가정합니다. 

자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](concepts-auto-provisioning.md)을 검토하세요. 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>플랫폼별 SDK 버전 빌드

Device Provisioning Service 클라이언트 SDK는 장치 등록 소프트웨어를 구현하는 데 도움을 줍니다. 하지만 사용하려면 개발 클라이언트 플랫폼 및 증명 메커니즘과 관련된 SDK의 버전을 빌드해야 합니다. 이 자습서에서는 Windows 개발 플랫폼에서 지원되는 증명 형식에 Visual Studio 2017을 사용하는 SDK를 빌드합니다.

1. 필요한 도구를 설치하고 C용 프로비전 서비스 클라이언트 SDK를 포함하는 GitHub 리포지토리를 복제합니다.

   a. 컴퓨터에 Visual Studio 2015 또는 [Visual Studio 2017](https://www.visualstudio.com/vs/)이 설치되어 있는지 확인합니다. Visual Studio 설치에서 ['C++를 사용한 데스크톱 개발'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) 워크로드를 사용하도록 설정해야 합니다.

   나. [CMake 빌드 시스템](https://cmake.org/download/)을 다운로드하여 설치합니다. CMake를 설치하기 **전에** 'C++를 사용한 데스크톱 개발' 워크로드가 있는 Visual Studio를 컴퓨터에 설치해야 합니다.

   다. 컴퓨터에 `git`이 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 로컬 Git 리포지토리와 상호 작용하기 위한 명령줄 Bash 셸인 **Git Bash**를 포함하여 최신 `git` 도구를 위한 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 

   d. Git Bash를 열고, "C용 Azure IoT SDK 및 라이브러리" 리포지토리를 복제합니다. 여러 종속 하위 모듈까지 다운로드해야 하므로 복제 명령이 완료될 때까지 몇 분 정도 걸릴 수 있습니다.
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. 새로 만든 리포지토리 하위 디렉터리 내부에 새 `cmake` 하위 디렉터리를 만듭니다.

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. Git Bash 명령 프롬프트에서 azure iot-sdk c 리포지토리의 `cmake` 하위 디렉터리로 변경합니다.

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. 다음 명령 중 하나를 사용하여(두 개의 후행 마침표에 주의) 개발 플랫폼에 대한 SDK와 지원되는 증명 메커니즘 중 하나를 빌드합니다. 완료되면 CMake가 사용자의 장치 관련 콘텐츠를 포함하는 `/cmake` 하위 디렉터리를 빌드합니다.
    - 증명에 실제 TPM/HSM 또는 시뮬레이션 된 X.509 인증서를 사용하는 장치의 경우:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - 증명에 TPM 시뮬레이터를 사용하는 장치의 경우:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

이제 SDK를 사용하여 장치 등록 코드를 빌드할 준비가 완료되었습니다. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>보안 아티팩트 추출 

다음 단계는 장치에서 사용하는 증명 메커니즘에 대한 보안 아티팩트를 추출하는 것입니다. 

### <a name="physical-device"></a>물리적 장치 

실제 TPM/HSM의 증명을 사용하는 SDK를 빌드한 경우:

- TPM 장치의 경우 TPM 칩 제조업체로부터 해당 장치에 연결되어 있는 **인증 키**를 확인해야 합니다. 등록 키를 해시하여 사용자 TPM 장치에 대한 고유한 **등록 ID**를 얻을 수 있습니다.  

- X.509 장치의 경우 장치에 발급된 인증서를 얻어야 합니다. 개별 장치 등록의 경우 최종 엔터티 인증서이며 장치의 그룹 등록의 경우 루트 인증서입니다. 

### <a name="simulated-device"></a>시뮬레이션된 장치

시뮬레이션된 TPM 또는 X.509 인증서의 증명을 사용하는 SDK를 빌드한 경우:

- 시뮬레이션된 TPM 장치:
   1. 별도의/새 명령 프롬프트에서 `azure-iot-sdk-c` 하위 디렉터리로 이동하여 TPM 시뮬레이터를 실행합니다. 포트 2321 및 2322에서 소켓을 수신 대기합니다. 이 명령 창을 닫지 마세요. 다음 빠른 시작 가이드가 끝날 때까지 이 시뮬레이터가 계속 실행되어야 합니다. 

      `azure-iot-sdk-c` 하위 디렉터리에서 다음 명령을 실행하여 시뮬레이터를 시작합니다.

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. Visual Studio를 사용하여 *cmake* 폴더에 생성된 `azure_iot_sdks.sln` 솔루션을 열고, "빌드" 메뉴에서 "솔루션 빌드" 명령을 사용하여 빌드합니다.

   3. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Tools** 폴더로 이동합니다. **tpm_device_provision** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 선택합니다. 

   4. "디버그" 메뉴에서 "시작" 명령 중 하나를 사용하여 솔루션을 실행합니다. 장치 등록에 필요한 TPM 시뮬레이터의 **_등록 ID_**와 **_인증 키_**가 출력 창에 표시됩니다. 나중에 사용할 수 있도록 이러한 값을 복사합니다. 이 창을 닫아도 되지만(등록 Id 및 인증 키를 얻은 후) 1단계에서 시작한 TPM 시뮬레이터 창은 계속 실행되도록 두세요.

- 시뮬레이션된 X.509 장치:
  1. Visual Studio를 사용하여 *cmake* 폴더에 생성된 `azure_iot_sdks.sln` 솔루션을 열고, "빌드" 메뉴에서 "솔루션 빌드" 명령을 사용하여 빌드합니다.

  2. Visual Studio의 *솔루션 탐색기* 창에서 **Provision\_Tools** 폴더로 이동합니다. **dice\_device\_enrollment** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 
  
  3. "디버그" 메뉴에서 "시작" 명령 중 하나를 사용하여 솔루션을 실행합니다. 메시지가 표시되면 출력 창에서 개별 등록에 대해 **i**를 입력합니다. 출력 창에는 시뮬레이션된 장치에 대해 로컬로 생성된 X.509 인증서가 표시됩니다. *-----BEGIN CERTIFICATE-----*에서 시작하여 첫 번째 *-----END CERTIFICATE-----*에서 끝나는 출력(이 두 줄 모두 포함)을 클립보드에 복사합니다. 출력 창의 첫 번째 인증서만 필요하기 때문입니다.
 
  4. **_X509testcert.pem_**이라는 파일을 만들어 원하는 텍스트 편집기에서 연 다음, 클립보드의 내용을 이 파일에 복사합니다. 나중에 장치를 등록할 때 사용해야 하므로 파일을 저장해 둡니다. 등록 소프트웨어를 실행하면 자동 프로비전과 동일한 인증서를 사용합니다.    

이러한 보안 아티팩트는 장치를 Device Provisioning Service에 등록할 때 꼭 필요합니다. 프로비전 서비스는 향후 특정한 시점에 이러한 장치가 부팅 및 연결될 때까지 대기합니다. 장치가 처음으로 부팅되면 클라이언트 SDK 논리가 칩(또는 시뮬레이터)과 상호 작용하여 장치에서 보안 아티팩트를 추출하고, Device Provisioning Service에 등록을 확인합니다. 

## <a name="create-the-device-registration-software"></a>장치 등록 소프트웨어 만들기

마지막 단계는 장치를 IoT Hub 서비스에 등록하도록 Device Provisioning Service 클라이언트 SDK를 사용하는 등록 응용 프로그램을 작성하는 것입니다. 

> [!NOTE]
> 이 단계에서는 워크스테이션에서 SDK 샘플 등록 응용 프로그램을 실행하여 얻은 시뮬레이션된 장치를 사용하는 것으로 가정합니다. 하지만 물리적 장치에 배포할 등록 응용 프로그램을 작성하는 경우에도 동일한 개념이 적용됩니다. 

1. Azure Portal에서 Device Provisioning 서비스에 대한 **개요** 블레이드를 선택하고 **_ID 범위_** 값을 복사합니다. *ID 범위*는 서비스에 의해 생성되고 고유성이 보장됩니다. 이는 변경할 수 없으며 등록 ID를 고유하게 식별하는 데 사용됩니다.

    ![포털 블레이드에서 DPS 끝점 정보 추출](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. 컴퓨터의 Visual Studio *솔루션 탐색기*에서 **Provision\_Samples** 폴더로 이동합니다. **prov\_dev\_client\_sample**이라는 샘플 프로젝트를 선택하고, **prov\_dev\_client\_sample.c** 원본 파일을 엽니다.

3. 1단계에서 얻은 _ID 범위_ 값을 `id_scope` 변수에 할당합니다(왼쪽/`[` 및 오른쪽/`]` 대괄호 제거). 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    참고로, `global_prov_uri` 변수는 IoT Hub 클라이언트 등록 API `IoTHubClient_LL_CreateFromDeviceAuth`가 지정된 Device Provisioning Service 인스턴스에 연결하는 것을 허용합니다.

4. 동일한 파일의 **main()** 함수에서, 장치의 등록 소프트웨어에서 사용 중인 증명 메커니즘(TPM 또는 X.509)과 일치하는 `hsm_type` 변수에 대한 주석을 달거나 주석 처리 제거합니다. 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. 변경 내용을 저장하고 "빌드" 메뉴에서 "솔루션 빌드"를 선택하여 **prov\_dev\_client\_sample** 샘플을 다시 빌드합니다. 

6. **Provision\_Samples** 폴더 아래에서 **prov\_dev\_client\_sample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다. 아직 응용 프로그램 예제를 실행하지 마세요.

> [!IMPORTANT]
> 아직 장치를 실행/시작하지 마세요! 장치를 시작하기 전에 먼저 Device Provisioning Service에 장치를 등록하여 프로세스를 마쳐야 합니다. 아래의 다음 단계에서는 다음 문서를 안내합니다.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>등록 시 사용되는 SDK API(참조용으로만 사용)

참고로, SDK는 등록 시 사용할 응용 프로그램에 다음과 같은 API를 제공합니다. 이러한 API는 장치가 부팅되면 Device Provisioning Service에 연결 및 등록하도록 도와줍니다. 그러면 장치가 IoT Hub 인스턴스와 연결을 설정하는 데 필요한 정보를 수신합니다.

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

처음에는 시뮬레이션된 장치를 사용하고, 그 다음에는 테스트 서비스 설정을 사용하여 Device Provisioning Service 클라이언트 등록 응용 프로그램을 구체화해야 할 수도 있습니다. 응용 프로그램이 테스트 환경에서 작동되면 특정 장치에 대해 빌드하고, 실행 파일을 사용자 장치 이미지에 복사할 수 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리

현재 포털에서 Device Provisioning 및 IoT Hub 서비스가 실행 중일 것입니다. 장치 프로비전 설정을 중단하고/하거나 이 자습서 시리즈의 완료 시기를 미루려면 불필요한 비용이 발생하지 않도록 서비스를 종료하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 Device Provisioning Service를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. **모든 리소스** 블레이드 위쪽에서 **삭제**를 클릭합니다.  

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 플랫폼별 Device Provisioning Service 클라이언트 SDK 빌드
> * 보안 아티팩트 추출
> * 장치 등록 소프트웨어 만들기

자동 프로비전을 위한 Azure IoT Hub Device Provisioning Service에 등록하여 IoT Hub에 장치를 프로비전하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [IoT Hub에 장치 프로비전](tutorial-provision-device-to-hub.md)

