---
title: Azure IoT Hub(iOS)를 사용한 클라우드-장치 메시지 | Microsoft Docs
description: iOS용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 장치로 클라우드-장치 메시지를 보내는 방법입니다.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 23dbd1f359f947b8e87ab4115887120dfd55907a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>IoT Hub(iOS)를 사용하여 클라우드-장치 메시지 보내기
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


Azure IoT Hub는 수백만 개의 장치와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [장치에서 IoT Hub로 원격 분석 데이터 보내기] 문서에서는 IoT Hub를 만들고 그 안에 장치 ID를 프로비전하고 장치-클라우드 메시지를 보내는 시뮬레이션된 장치 앱을 코딩하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 안내합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-장치 메시지를 단일 장치로 보냅니다.
* 장치에서 클라우드-장치 메시지를 받습니다.
* 솔루션 백 엔드에서, IoT Hub에서 장치로 보낸 메시지에 대한 배달 확인(*피드백*)을 요청합니다.

클라우드-장치 메시지에 자세한 내용은 [IoT Hub 개발자 가이드][IoT Hub developer guide - C2D]에서 찾아볼 수 있습니다.

이 문서의 마지막 부분에서는 다음과 같은 두 Swift iOS 프로젝트를 실행합니다.

* **sample-device** - [장치에서 IoT Hub로 원격 분석 데이터 보내기]에서 만든 앱과 동일한 앱으로, IoT Hub에 연결하고 클라우드-장치 메시지를 수신합니다.
* **sample-service** - IoT Hub를 통해 시뮬레이션된 장치 앱에 클라우드-장치 메시지를 보낸 후 배달 승인을 수신합니다.

> [!NOTE]
> IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 Javascript 포함)를 위해 비록 Azure IoT 장치 SDK이지만 SDK를 지원합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 장치를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

- 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.
- Azure의 활성 IoT Hub. 
- [Azure 샘플](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip)의 코드 샘플.
- 최신 버전의 iOS SDK를 실행 중인 최신 버전의 [XCode](https://developer.apple.com/xcode/). 이 빠른 시작은 XCode 9.3 및 iOS 11.3에서 테스트되었습니다.
- 최신 버전의 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).


## <a name="simulate-an-iot-device"></a>IoT 장치 시뮬레이션
이 섹션에서는 IoT Hub에서 클라우드-장치 메시지를 수신하는 Swift 응용 프로그램을 실행하는 iOS 장치를 시뮬레이션합니다. 

이것은 [장치에서 IoT Hub로 원격 분석 데이터 보내기]에서 만드는 샘플 장치입니다. 이미 샘플 장치를 실행 중인 경우 이 섹션을 건너뛰어도 됩니다.

### <a name="install-cocoapods"></a>CocoaPods 설치

CocoaPods는 타사 라이브러리를 사용하는 iOS 프로젝트의 종속성을 관리합니다.

터미널 창에서 필수 구성 요소에 다운로드한 Azure-IoT-Samples-iOS 폴더로 이동합니다. 그런 다음, 샘플 프로젝트로 이동합니다.

```sh
cd quickstart/sample-device
```

XCode가 닫혀 있는지 확인한 후, 다음 명령을 실행하여 **podfile** 파일에 선언된 CocoaPods를 설치합니다.

```sh
pod install
```

설치 명령은 프로젝트에 필요한 포드를 설치하고, 종속성에 Pod를 사용하도록 이미 구성된 XCode 작업 영역 파일도 만들었습니다. 

### <a name="run-the-sample-device-application"></a>샘플 장치 응용 프로그램 실행 

1. 장치의 연결 문자열을 검색합니다. 장치 세부 정보 블레이드에서 Azure Portal을 통해 이 문자열을 복사하거나 다음 CLI 명령을 사용하여 검색할 수 있습니다. 

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. XCode에서 샘플 작업 영역을 엽니다.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. **MQTT 클라이언트 샘플** 프로젝트를 확장한 다음, 같은 이름의 폴더를 확장합니다.  
3. XCode에서 편집을 위해 **ViewController.swift**를 엽니다. 
4. **connectionString** 변수를 검색하고 첫 번째 단계에서 복사해 둔 장치 연결 문자열로 값을 업데이트합니다.
5. 변경 내용을 저장합니다. 
6. **빌드 및 실행** 단추 또는 **명령 + r** 키 콤보를 사용하여 장치 에뮬레이터에서 프로젝트를 실행합니다. 

   ![프로젝트 실행](media/quickstart-send-telemetry-ios/run-sample.png)


## <a name="simulate-a-service-device"></a>서비스 장치 시뮬레이션

이 섹션에서는 IoT Hub를 통해 클라우드-장치 메시지를 보내는 Swift 앱을 사용하여 두 번째 iOS 장치를 시뮬레이션합니다. 이 구성은 iPhone 또는 iPad 한 대가 IoT Hub에 연결된 다른 iOS 장치의 컨트롤러 역할을 하는 IoT 시나리오에 유용합니다. 

### <a name="install-cocoapods"></a>CocoaPods 설치

CocoaPods는 타사 라이브러리를 사용하는 iOS 프로젝트의 종속성을 관리합니다.

필수 구성 요소에서 다운로드한 Azure IoT iOS 샘플 폴더로 이동합니다. 그런 다음, 샘플 서비스 프로젝트로 이동합니다.

```sh
cd quickstart/sample-service
```

XCode가 닫혀 있는지 확인한 후, 다음 명령을 실행하여 **podfile** 파일에 선언된 CocoaPods를 설치합니다.

```sh
pod install
```

설치 명령은 프로젝트에 필요한 포드를 설치하고, 종속성에 Pod를 사용하도록 이미 구성된 XCode 작업 영역 파일도 만들었습니다.

### <a name="run-the-sample-service-application"></a>샘플 서비스 응용 프로그램 실행

1. IoT Hub의 서비스 연결 문자열을 검색합니다. **공유 액세스 정책** 블레이드의 **iothubowner** 정책에서 Azure Portal을 통해 이 문자열을 복사하거나 다음 CLI 명령을 사용하여 검색할 수 있습니다.  

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

2. XCode에서 샘플 작업 영역을 엽니다.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

3. **AzureIoTServiceSample** 프로젝트를 확장한 다음, 같은 이름의 폴더를 확장합니다.  
4. XCode에서 편집을 위해 **ViewController.swift**를 엽니다. 
5. **connectionString** 변수를 검색하고 이전에 복사해 둔 서비스 연결 문자열로 값을 업데이트합니다.
6. 변경 내용을 저장합니다. 
7. Xcode에서 에뮬레이터 설정을 IoT 장치 실행에 사용한 다른 iOS 장치로 변경합니다. XCode는 같은 유형의 에뮬레이터를 여러 개 실행할 수 없습니다. 

   ![에뮬레이터 장치 변경](media/iot-hub-ios-swift-c2d/change-device.png)

8. **빌드 및 실행** 단추 또는 **명령 + r** 키 콤보를 사용하여 장치 에뮬레이터에서 프로젝트를 실행합니다. 

   ![프로젝트 실행](media/iot-hub-ios-swift-c2d/run-app.png)


## <a name="send-a-cloud-to-device-message"></a>클라우드-장치 메시지 보내기
이제 클라우드-장치 메시지를 주고 받는 두 응용 프로그램을 사용할 준비가 완료되었습니다.

1. 시뮬레이션된 IoT 장치에서 실행되는 **iOS 앱 샘플**에서 **시작**을 클릭합니다. 응용 프로그램이 장치-클라우드 메시지 송신을 시작하고, 그와 동시에 클라우드-장치 메시지 수신 대기도 시작합니다. 

   ![샘플 IoT 장치 앱 보기](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. 시뮬레이션된 서비스 장치에서 실행되는 **IoTHub 서비스 클라이언트 샘플** 앱에서 메시지를 보내고 싶은 IoT 장치의 ID를 입력합니다. 
3. 일반 텍스트 메시지를 작성한 다음, **보내기**를 클릭합니다. 

보내기를 클릭하는 즉시 여러 작업이 발생합니다. 서비스 샘플은 IoT Hub로 메시지를 보내고, 앱은 사용자가 입력한 서비스 연결 문자열 때문에 허브에 액세스할 수 있습니다. IoT Hub는 장치 ID를 검사하고, 대상 장치에 메시지를 보내고, 원본 장치에 수신 확인을 보냅니다. 시뮬레이션된 IoT 장치에서 실행 중인 앱은 IoT Hub에서 보낸 메시지를 검사하여 가장 최근에 온 메시지의 텍스트를 화면에 인쇄합니다.

출력은 다음 예제와 비슷합니다.

   ![클라우드-장치 메시지 보기](media/iot-hub-ios-swift-c2d/view-c2d.png)


## <a name="next-steps"></a>다음 단계
이 자습서에서 클라우드-장치 메시지를 보내고 받는 방법을 알아보았습니다. 

IoT Hub를 사용하는 완전한 종단 간 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[장치에서 IoT Hub로 원격 분석 데이터 보내기]: quickstart-send-telemetry-ios.md

[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[Azure IoT 개발자 센터]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure portal]: https://portal.azure.com
[Azure IoT 원격 모니터링 솔루션 가속기]: https://azure.microsoft.com/documentation/suites/iot-suite/
