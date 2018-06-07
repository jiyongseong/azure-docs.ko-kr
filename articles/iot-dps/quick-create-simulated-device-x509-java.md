---
title: Java를 사용하여 Azure IoT Hub에 시뮬레이션된 X.509 장치 프로비전 | Microsoft Docs
description: Azure 빠른 시작 - IoT Hub Device Provisioning Service용 Java 장치 SDK를 사용하여 시뮬레이션된 X.509 장치 만들기 및 프로비전
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo;bryanla
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 72460e19d202b79369844db6fea24f2914c8bbbe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service용 Java 장치 SDK를 사용하여 시뮬레이션된 X.509 장치 만들기 및 프로비전
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

다음 단계에서는 Windows OS를 실행 중인 개발 컴퓨터에서 X.509 장치를 시뮬레이션하고, 코드 샘플을 사용하여 이 시뮬레이션된 장치를 Device Provisioning Service 및 IoT Hub와 연결하는 방법을 보여 줍니다. 

자동 프로비전 프로세스에 익숙하지 않은 경우 [자동 프로비전 개념](concepts-auto-provisioning.md)도 검토하세요. 계속하기 전에 [Azure Portal에서 IoT Hub Device Provisioning Service 설정](./quick-setup-auto-provision.md)의 단계를 완료해야 합니다. 

## <a name="prepare-the-environment"></a>환경 준비 

1. 컴퓨터에 [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)이 설치되어 있는지 확인합니다.

2. [Maven](https://maven.apache.org/install.html)을 다운로드하고 설치합니다.

3. Git이 컴퓨터에 설치되어 있고 명령 창에서 액세스할 수 있는 환경 변수에 추가되었는지 확인합니다. 설치할 `git` 도구의 최신 버전은 [Software Freedom Conservancy의 Git 클라이언트 도구](https://git-scm.com/download/)를 참조하세요. 여기에는 로컬 Git 리포지토리와 상호 작용하는 데 사용할 수 있는 명령줄 앱인 **Git Bash**가 포함됩니다. 

4. 명령 프롬프트를 엽니다. 장치 시뮬레이션 샘플 코드에 대한 GitHub 리포지토리를 복제합니다.
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. 필요한 모든 패키지를 다운로드하려면 루트 `azure-iot-sdk-`java 디렉터리로 이동하고 프로젝트를 빌드합니다.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. 인증서 생성기 프로젝트로 이동하여 프로젝트를 빌드합니다. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>자체 서명된 X.509 장치 인증서 및 개별 등록 항목 만들기

1. 이전 단계의 명령 프롬프트를 사용하여 `target` 폴더로 이동한 다음, 이전 단계에서 만든 jar 파일을 실행합니다.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. _Do you want to input common name_(일반 이름을 입력하시겠습니까?)에서 **N**을 입력합니다. *-----BEGIN CERTIFICATE-----* 에서 시작하여 *-----END CERTIFICATE-----* 를 통하는 `Client Cert`의 출력을 클립보드에 복사합니다.

   ![개별 인증서 생성기](./media/java-quick-create-simulated-device-x509/individual.png)

3. Windows 컴퓨터에 **_X509individual.pem_** 이라는 파일을 만들어 원하는 편집기에서 연 다음, 클립보드의 내용을 이 파일에 복사합니다. 파일을 저장하고 편집기를 닫습니다.

4. 명령 프롬프트의 _Do you want to input Verification Code_(확인 코드를 입력하시겠습니까?)에서 **N**을 입력하고, 나중에 빠른 시작에서 참조할 수 있도록 프로그램 출력을 열어 둡니다. 나중에 다음 섹션에서 사용하기 위해 `Client Cert` 및 `Client Cert Private Key` 값을 복사합니다.

5. [Azure Portal](https://portal.azure.com)에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning 서비스 인스턴스를 엽니다.

6. Device Provisioning Service 요약 블레이드에서 **등록 관리**를 선택합니다. **개별 등록** 탭을 선택하고 맨 위에서 **추가** 단추를 클릭합니다. 

7. **등록 추가** 패널 아래에 다음 정보를 입력합니다.
    - ID 증명 *메커니즘*으로 **X.509**를 선택합니다.
    - *기본 인증서 .pem 또는 .cer 파일* 아래에서 *파일 선택*을 클릭하여 이전 단계에서 만든 **X509individual.pem** 인증서 파일을 선택합니다.  
    - 필요에 따라 다음 정보를 입력합니다.
      - 프로비전 서비스와 연결된 IoT Hub를 선택합니다.
      - 고유한 장치 ID를 입력합니다. 장치 이름을 지정할 때 중요한 데이터가 포함되지 않도록 합니다. 
      - 장치에 대해 원하는 초기 구성으로 **초기 장치 쌍 상태**를 업데이트합니다.
   - 완료되면 **저장** 단추를 클릭합니다. 

    [![포털에 X.509 증명에 대한 개별 등록 추가](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     성공적으로 등록되면 *개별 등록* 탭의 *등록 ID* 열 아래에 X.509 장치가 **microsoftriotcore**로 표시됩니다. 



## <a name="simulate-the-device"></a>장치 시뮬레이션

1. Device Provisioning Service 요약 블레이드에서 **개요**를 선택하고, _ID 범위_ 및 _Provisioning Service Global Endpoint_(프로비전 서비스 전역 엔드포인트)를 참고합니다.

    ![서비스 정보](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. 명령 프롬프트를 엽니다. Java SDK 리포지토리의 샘플 프로젝트 폴더로 이동합니다.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. 코드에 프로비전 서비스 및 X.509 ID 정보를 입력합니다. 장치를 등록하기 전에 시뮬레이션된 장치의 증명을 위해 자동 프로비전하는 동안 사용됩니다.

   - 앞에서 설명한 대로 _ID 범위_ 및 _프로비전 서비스 전역 엔드포인트_를 포함하도록 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` 파일을 편집합니다. 또한 이전 섹션에서 설명한 대로 _클라이언트 인증서_ 및 _클라이언트 인증서 개인 키_도 포함합니다.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - 인증서 및 개인 키를 복사/붙여넣을 때 다음 형식을 사용합니다.
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. 샘플을 빌드합니다. `target` 폴더로 이동하고 만든 jar 파일을 실행합니다.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Azure Portal에서 프로비전 서비스에 연결된 IoT 허브로 이동하고 **Device Explorer** 블레이드를 엽니다. 시뮬레이션된 X.509 장치가 허브에 성공적으로 프로비전되면 장치 ID가 **Device Explorer** 블레이드에 표시되고 *상태*가 **사용**으로 표시됩니다.  샘플 장치 응용 프로그램을 실행하기 전에 블레이드가 이미 열려 있으면 위쪽의 **새로 고침** 단추를 클릭해야 할 수도 있습니다. 

    ![장치가 IoT Hub에 등록됨](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> 장치에 대한 등록 항목의 기본값으로부터 *초기 장치 쌍 상태*를 변경한 경우, 허브에서 원하는 쌍 상태를 가져와서 그에 맞게 작동할 수 있습니다. 자세한 내용은 [IoT Hub의 장치 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.
>


## <a name="clean-up-resources"></a>리소스 정리

장치 클라이언트 샘플을 계속해서 작업하고 탐색할 계획인 경우 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계를 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. 컴퓨터에서 장치 클라이언트 샘플 출력 창을 닫습니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 Device Provisioning Service를 선택합니다. 서비스에 대한 **등록 관리** 블레이드를 연 다음, **개별 등록** 탭을 클릭합니다. 이 빠른 시작에서 등록한 장치의 *등록 ID*를 선택하고, 위쪽의 **삭제** 단추를 클릭합니다. 
3. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음 사용자의 IoT Hub를 선택합니다. 허브에 대한 **IoT 장치** 블레이드를 열고, 이 빠른 시작에서 등록한 장치의 *장치 ID*를 선택한 다음, 위쪽의 **삭제** 단추를 클릭합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Windows 컴퓨터에서 시뮬레이션된 X.509 장치를 만들었습니다. Azure IoT Hub Device Provisioning 서비스에 해당 등록을 구성한 다음, IoT 허브에 장치를 자동 프로비전했습니다. 프로그래밍 방식으로 X.509 장치를 등록하는 방법을 알아보려면 프로그래밍 방식으로 X.509 장치를 등록하는 빠른 시작으로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure 빠른 시작 - Azure IoT Hub Device Provisioning Service에 X.509 장치 등록](quick-enroll-device-x509-java.md)
