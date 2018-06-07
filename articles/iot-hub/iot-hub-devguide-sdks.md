---
title: Azure IoT SDK 이해 | Microsoft Docs
description: 개발자 가이드 - 장치 및 백 엔드 앱을 빌드하는데 사용할 수 있는 다양한 Azure IoT 장치 및 서비스 SDK에 대한 링크 정보입니다.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71d02e3f8bf0757d7ea0622e76b95acda1fefc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDK 이해 및 사용

IoT Hub를 사용하기 위한 SDK(소프트웨어 개발 키트)의 범주는 두 가지입니다.

* **장치 SDK**를 사용하면 IoT 장치에서 실행되는 앱을 빌드할 수 있습니다. 이러한 앱은 IoT Hub로 원격 분석 데이터를 보내고, 필요에 따라 IoT Hub에서 메시지, 작업, 메서드, 쌍 업데이트를 받습니다.

* **서비스 SDK**를 사용하면 IoT Hub를 관리하고 필요에 따라 메시지를 보내거나, 작업을 예약하고, 직접 메서드를 호출하거나, IoT 장치에 원하는 속성 업데이트를 보낼 수 있습니다.

Azure IoT SDK를 사용한 개발의 이점에 대해 알아보려면 [여기를][lnk-benefits-blog]를 참조하세요.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT 장치 SDK

Microsoft Azure IoT 장치 SDK에는 Azure IoT Hub 서비스에 연결되고 Azure IoT Hub 서비스에서 관리하는 장치와 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

.NET용 Azure IoT Hub 장치 SDK: 
* [Nuget][lnk-nuget-csharp-device]에서 설치
* [소스 코드][lnk-dotnet-sdk]
* [API 참조][lnk-dotnet-ref]

C용 Azure IoT Hub 장치 SDK: 이식성과 광범위한 플랫폼 호환성을 위해 ANSI C(C99)로 작성되었습니다.
* [apt-get, MBED, Arduino IDE 또는 Nuget][lnk-c-package]에서 설치
* [소스 코드][lnk-c-sdk]
* [API 참조][lnk-c-ref]

Java용 Azure IoT Hub 장치 SDK: 
* [Maven][lnk-maven-device] 프로젝트에 추가
* [소스 코드][lnk-java-sdk]
* [API 참조][lnk-java-ref]

Node.js용 Azure IoT Hub 장치 SDK: 
* [npm][lnk-npm-device]에서 설치
* [소스 코드][lnk-node-sdk]
* [API 참조][lnk-node-ref]

Python용 Azure IoT Hub 장치 SDK: 
* [pip][lnk-pip-device]에서 설치
* [소스 코드][lnk-python-sdk]

> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS 플랫폼 및 하드웨어 호환성

특정 하드웨어 장치와 SDK 호환성에 대한 자세한 내용은 [IoT용 Azure Certified 장치 카탈로그][lnk-certified] 또는 개별 리포지토리를 참조하세요.

## <a name="azure-iot-service-sdks"></a>Azure IoT 서비스 SDK

Azure IoT 서비스 SDK에는 장치와 보안을 관리하기 위해 IoT Hub 서비스를 직접 조작하는 응용 프로그램의 빌드를 용이하게 하는 코드가 포함되어 있습니다.

.NET용 Azure IoT Hub 서비스 SDK:
* [Nuget][lnk-nuget-csharp-service]에서 다운로드
* [소스 코드][lnk-dotnet-sdk]
* [API 참조][lnk-dotnet-service-ref]

Java용 Azure IoT Hub 서비스 SDK: 
* [Maven][lnk-maven-service] 프로젝트에 추가
* [소스 코드][lnk-java-sdk]
* [API 참조][lnk-java-service-ref]

Node.js용 Azure IoT Hub 서비스 SDK: 
* [npm][lnk-npm-service]에서 다운로드
* [소스 코드][lnk-node-sdk]
* [API 참조][lnk-node-service-ref]

Python용 Azure IoT Hub 서비스 SDK: 
* [pip][lnk-pip-service]에서 다운로드
* [소스 코드][lnk-python-sdk]

C용 Azure IoT Hub 서비스 SDK: 
* [apt-get, MBED, Arduino IDE 또는 Nuget][lnk-c-package]에서 다운로드
* [소스 코드][lnk-c-sdk]

> [!NOTE]
> 언어 및 플랫폼 특정 패키지 관리자를 사용하여 개발 컴퓨터에서 이진 파일 및 종속성을 설치하는 방법에 대한 정보는 GitHub 리포지토리의 추가 정보 파일을 참조하세요.


## <a name="next-steps"></a>다음 단계

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-devguide-endpoints]
* [장치 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어][lnk-devguide-query]
* [할당량 및 제한][lnk-devguide-quotas]
* [IoT Hub MQTT 지원][lnk-devguide-mqtt]
* [IoT Hub REST API 참조][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
