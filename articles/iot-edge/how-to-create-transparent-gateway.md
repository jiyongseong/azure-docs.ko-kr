---
title: Azure IoT Edge를 사용하여 투명한 게이트웨이 장치 만들기 | Microsoft Docs
description: Azure IoT Edge를 사용하여 여러 장치에 대한 정보를 처리할 수 있는 투명한 게이트웨이 장치 만들기
services: iot-edge
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0378cb2964a496a2bfe5a0bc08296cbab462a409
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32170713"
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>투명한 게이트웨이 역할을 하는 IoT Edge 장치 만들기 - 미리 보기

이 문서에서는 IoT Edge 장치를 투명한 게이트웨이로 사용하기 위한 구체적인 지침을 제공합니다. 이 문서의 나머지 부분에서 *IoT Edge 게이트웨이*라는 용어는 투명한 게이트웨이로 사용되는 IoT Edge 장치를 의미합니다. 자세한 내용은 개요를 제공하는 [IoT Edge 장치를 게이트웨이로 사용하는 방법][lnk-edge-as-gateway]을 참조하세요. 

>[!NOTE]
>현재 상황:
> * 게이트웨이가 IoT Hub와의 연결이 끊긴 경우 다운스트림 장치는 게이트웨이를 통해 인증할 수 없습니다.
> * IoT Edge 장치는 IoT Edge 게이트웨이에 연결할 수 없습니다.

## <a name="understand-the-azure-iot-device-sdk"></a>Azure IoT 장치 SDK 이해


모든 IoT Edge 장치에 설치된 Edge Hub는 다운스트림 장치에 다음과 같은 기본 형식을 노출합니다.

* 장치-클라우드 및 클라우드-장치 메시지
* 직접 메서드
* 장치 쌍 작업

현재 다운스트림 장치는 IoT Edge 게이트웨이를 통해 연결될 때 파일 업로드를 사용할 수 없습니다.

Azure IoT 장치 SDK를 사용하여 IoT Edge 게이트웨이에 장치를 연결할 경우 다음을 수행해야 합니다.

* 게이트웨이 장치 호스트 이름을 참조하는 연결 문자열을 사용하여 다운스트림 장치를 설정합니다.
* 다운스트림 장치가 게이트웨이 장치에서 연결을 수락하는 데 사용하는 인증서를 신뢰하는지 확인합니다.

제어 스크립트를 사용하여 Azure IoT Edge 런타임을 설치하면 [Windows][lnk-tutorial1-win] 및 [Linux][lnk-tutorial1-lin]의 시뮬레이트된 장치에 IoT Edge 설치 자습서의 경우처럼 Edge Hub에 대해 인증서가 생성됩니다. 이 인증서는 Edge Hub가 들어오는 TLS 연결을 수락하는 데 사용하며, 게이트웨이 장치에 연결할 때 다운스트림 장치에서 신뢰되어야 합니다.

장치-게이트웨이 토폴로지에 필요한 신뢰를 설정하는 어떤 인증서 인프라도 만들 수 있습니다. 이 문서에서는 IoT Hub에서 [X.509 CA 보안][lnk-iothub-x509]을 사용하도록 설정하는 데 사용하는 것과 동일한 인증서 설정을 가정합니다. 여기에는 특정 IoT Hub에 연결된 X.509 CA 인증서(*IoT Hub 소유자 CA*) 및 이 CA로 서명되고 IoT Edge 장치에 설치된 인증서 시리즈가 포함됩니다.

>[!IMPORTANT]
>현재 IoT Edge 장치 및 다운스트림 장치는 [SAS 토큰][lnk-iothub-tokens]만 사용하여 IoT Hub에서 인증을 받을 수 있습니다. 인증서는 리프 및 게이트웨이 장치 간의 TLS 연결이 유효한지 검사하는 데만 사용됩니다.

이 구성에서는 **IoT Hub 소유자 CA**를 다음으로 모두 사용합니다.
* 모든 IoT Edge 장치에 IoT Edge 런타임을 설치하기 위한 서명 인증서
* 다운스트림 장치에 설치되는 공개 키 인증

이렇게 하면 동일한 IoT Hub에 연결되기만 하면 모든 장치에서 모든 IoT Edge 장치를 게이트웨이로 사용할 수 있도록 하는 솔루션이 구현됩니다.

## <a name="create-the-certificates-for-test-scenarios"></a>테스트 시나리오를 위한 인증서 만들기

[CA 인증서 샘플 관리][lnk-ca-scripts]에 설명된 샘플 PowerShell 및 Bash 스크립트를 사용하여 자체 서명된 **IoT Hub 소유자 CA**와 이 CA로 서명한 장치 인증서를 생성할 수 있습니다.

>[!IMPORTANT]
>이 샘플은 테스트 목적으로만 사용됩니다. 프로덕션 시나리오를 보려면 [IoT 배포 보안 유지][lnk-iothub-secure-deployment]에서 IoT 솔루션 보안 유지 및 인증서 프로비전 방법에 대한 Azure IoT 지침을 참조하세요.


1. GitHub에서 Microsoft Azure IoT SDK 및 C 라이브러리를 복제합니다.

   ```cmd/sh
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. 인증서 스크립트를 설치하려면 [CA 인증서 샘플 관리][lnk-ca-scripts]의 **1단계 - 초기 설치** 지침을 따릅니다. 
3. **IoT 허브 소유자 CA**를 생성하려면 **2단계 - 인증서 체인 만들기**의 지침에 따릅니다. 이 파일은 다운스트림 장치에서 연결 유효성 검사에 사용됩니다.
4. 게이트웨이 장치에 대한 인증서를 생성하려면 Bash 또는 PowerShell 지침을 사용합니다.

### <a name="bash"></a>Bash

새 장치 인증서를 만듭니다.  `myGatewayCAName`의 이름을 게이트웨이 호스트의 이름과 동일하게 지정하지 **마십시오**.  이렇게 하면 이러한 인증서에 대한 클라이언트 인증이 실패합니다.

   ```bash
   ./certGen.sh create_edge_device_certificate myGatewayCAName
   ```

새 파일이 만들어집니다.\certs\new-edge-device.* 는 공개 키와 PFX를, .\private\new-edge-device.key.pem은 장치의 개인 키를 포함합니다.
 
`certs` 디렉터리에서 다음 명령을 실행하여 장치 공개 키의 전체 체인을 가져옵니다.

   ```bash
   cd ./certs
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>PowerShell

새 장치 인증서를 만듭니다. 
   ```powershell
   New-CACertsEdgeDevice myGateway
   ```

이렇게 하면 공개 키, 개인 키 및 이 인증서의 PFX가 포함된 새 myEdgeDevice* 파일이 생성됩니다. 

서명 프로세스 동안 암호를 입력하라는 메시지가 표시되면 "1234"를 입력합니다.

## <a name="configure-a-gateway-device"></a>게이트웨이 장치 만들기

IoT Edge 장치를 게이트웨이로 구성하려면 이전 섹션에서 만든 장치 인증서를 사용하도록 구성하기만 하면 됩니다.

위의 샘플 스크립트에 나오는 다음 파일 이름을 사용합니다.

| 출력 | 파일 이름 |
| ------ | --------- |
| 장치 인증서 | `certs/new-edge-device.cert.pem` |
| 장치 개인 키 | `private/new-edge-device.cert.pem` |
| 장치 인증서 체인 | `certs/new-edge-device-full-chain.cert.pem` |
| IoT Hub 소유자 CA | `certs/azure-iot-test-only.root.ca.cert.pem`  |

IoT Edge 런타임에 장치 및 인증서 정보를 제공합니다. 
 
Linux에서 Bash 출력 사용:

   ```bash
   sudo iotedgectl setup --connection-string {device connection string} \
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com} \
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem \
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem \
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem \
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

Windows에서 PowerShell 출력 사용:

   ```powershell
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

기본적으로 샘플 스크립트는 장치 개인 키에 대해 암호를 설정하지 않습니다. 암호를 설정하는 경우 다음 매개 변수를 추가합니다. `--device-ca-passphrase {passphrase}`

이 스크립트는 Edge 에이전트 인증서에 대한 암호를 설정하도록 요구합니다. 이 명령 후에 IoT Edge 런타임을 다시 시작합니다.

   ```cmd/sh
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>다운스트림 장치 구성

다운스트림 장치는 [.NET을 사용하여 IoT Hub에 장치 연결][lnk-iothub-getstarted]에 설명된 간단한 응용 프로그램과 같이 [Azure IoT 장치 SDK][lnk-devicesdk]를 사용하는 어떤 응용 프로그램도 될 수 있습니다.

먼저 다운스트림 장치 응용 프로그램은 게이트웨이 장치에 대한 TLS 연결의 유효성을 검사하기 위해 **IoT Hub 소유자 CA** 인증서를 신뢰해야 합니다. 이 단계는 일반적으로 두 가지 방법, 즉 OS 수준 또는 (특정 언어의 경우) 응용 프로그램 수준에서 수행됩니다.

예를 들어 .NET 응용 프로그램의 경우 다음 코드 조각을 추가하여 경로 `certPath`에 저장된 PEM 형식의 인증서를 신뢰할 수 있습니다. 사용한 스크립트 버전에 따라 경로는 `certs/azure-iot-test-only.root.ca.cert.pem`(Bash) 또는 `RootCA.pem`(Powershell)을 참조합니다.

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

OS 수준에서 이 단계를 수행하는 방식이 Windows와 Linux 배포판 간에 다릅니다.

두 번째 단계는 게이트웨이 장치의 호스트 이름을 참조하는 연결 문자열을 사용하여 IoT Hub 장치 SDK를 초기화하는 것입니다.
이 작업은 장치 연결 문자열에 `GatewayHostName`을 추가하여 수행합니다. 예를 들어, 다음은 `GatewayHostName` 속성을 추가하는 장치에 대한 샘플 장치 연결 문자열입니다.

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

이러한 두 단계를 진행하면 장치 응용 프로그램이 게이트웨이 장치에 연결할 수 있게 됩니다.

## <a name="next-steps"></a>다음 단계
[IoT Edge 모듈을 개발하기 위한 요구 사항 및 도구 이해][lnk-module-dev]

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
