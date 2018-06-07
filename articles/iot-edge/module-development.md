---
title: "Azure IoT Edge용 모듈 개발 | Microsoft Docs"
description: "Azure IoT Edge용 사용자 지정 모듈을 만드는 방법을 알아봅니다."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>IoT Edge 모듈 개발을 위한 요구 사항 및 도구 이해 - 미리 보기

이 문서에서는 IoT Edge 모듈로 실행되는 응용 프로그램을 작성할 때 사용할 수 있는 기능과 이를 활용하는 방법에 대해 설명합니다.

## <a name="iot-edge-runtime-environment"></a>IoT Edge 런타임 환경
IoT Edge 런타임은 여러 IoT Edge 모듈의 기능을 통합하고 이를 IoT Edge 장치에 배포하기 위한 인프라를 제공합니다. 상위 수준에서 모든 프로그램을 IoT Edge 모듈로 패키지할 수 있습니다. 그러나 IoT Edge 통신 및 관리 기능을 최대한 활용하기 위해 모듈에서 실행되는 프로그램이 IoT Edge 런타임에 통합된 로컬 IoT Edge 허브에 연결할 수 있습니다.

## <a name="using-the-iot-edge-hub"></a>IoT Edge 허브 사용
IoT Edge 허브는 IoT Hub에 프록시 및 로컬 통신이라는 두 가지 주요 기능을 제공합니다.

### <a name="iot-hub-primitives"></a>IoT Hub 기본 형식
IoT Hub는 다음과 같은 점에서 장치와 비슷하게 모듈 인스턴스를 표시합니다.

* [장치 쌍][lnk-devicetwin] 및 해당 장치의 다른 모듈 쌍과 별개이며 격리된 모듈 쌍이 있습니다.
* [장치-클라우드 메시지][lnk-iothub-messaging]을 보낼 수 있습니다.
* 특별히 해당 ID에서 대상으로 지정된 [직접 메서드][lnk-methods]를 받을 수 있습니다.

현재 모듈은 클라우드-장치 메시지를 받거나 파일 업로드 기능을 사용할 수 없습니다.

모듈을 작성할 때 [Azure IoT 장치 SDK][lnk-devicesdk]를 사용하여 IoT Edge 허브에 연결하고, 장치 응용 프로그램과 함께 IoT Hub를 사용할 때와 동일한 방식으로 위의 기능을 사용하기만 하면 됩니다. 유일한 차이점은 응용 프로그램 백 엔드에서 장치 ID 대신 모듈 ID를 참조해야 한다는 것입니다.

장치-클라우드 메시지를 보내고 모듈 쌍을 사용하는 모듈 응용 프로그램의 예제는 [시뮬레이션된 장치에 IoT Edge 모듈 개발 및 배포][lnk-tutorial2]를 참조하세요.

### <a name="device-to-cloud-messages"></a>장치-클라우드 메시지
IoT Edge 허브는 장치-클라우드 메시지의 복잡한 처리를 사용할 수 있게 하기 위해 모듈 간 메시지 및 모듈과 IoT Hub 간 메시지의 선언적 라우팅을 제공합니다.
이렇게 하면 모듈은 다른 모듈에서 보낸 메시지를 가로채고, 처리하고, 복잡한 파이프라인에 전파할 수 있습니다.
[모듈 구성][lnk-module-comp] 문서에서는 경로를 사용하여 복잡한 파이프라인으로 모듈을 구성하는 방법을 설명합니다.

IoT Edge 모듈은 일반적인 IoT Hub 장치 응용 프로그램과 달리 처리를 위해 로컬 IoT Edge 허브에서 프록시하는 장치-클라우드 메시지를 받을 수 있습니다.

IoT Edge 허브는 [모듈 구성][lnk-module-comp] 문서에서 설명하는 선언적 경로에 따라 메시지를 모듈에 전파합니다. IoT Edge 모듈을 개발할 때 [시뮬레이션된 장치에 IoT Edge 모듈 개발 및 배포][lnk-tutorial2] 자습서에 나와 있는 것처럼 메시지 핸들러를 설정하여 이러한 메시지를 받을 수 있습니다.

IoT Edge는 경로 만들기를 간소화하기 위해 모듈 '입력' 및 '출력' 끝점의 개념을 추가합니다. 모듈은 입력을 지정하지 않고 라우팅되는 모든 장치-클라우드 메시지를 받을 수 있으며, 출력을 지정하지 않고 장치-클라우드 메시지를 보낼 수 있습니다.
그러나 명시적 입력 및 출력을 사용하면 라우팅 규칙을 더 쉽게 이해할 수 있습니다. 모듈에 대한 라우팅 규칙 및 입력/출력 끝점에 대한 자세한 내용은 [모듈 구성][lnk-module-comp]을 참조하세요.

마지막으로 Edge 허브에서 처리하는 장치-클라우드 메시지는 다음 시스템 속성으로 스탬프 처리됩니다.

| 속성 | 설명 |
| -------- | ----------- |
| $connectionDeviceId | 메시지를 보낸 클라이언트의 장치 ID입니다. |
| $connectionModuleId | 메시지를 보낸 모듈의 모듈 ID입니다. |
| $inputName | 이 메시지를 받은 입력입니다. 비어 있을 수 있습니다. |
| $outputName | 메시지를 보내는 데 사용된 출력입니다. 비어 있을 수 있습니다. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>모듈에서 IoT Edge 허브에 연결
모듈에서 로컬 IoT Edge 허브에 연결하는 과정은 모듈 시작 시 IoT Edge 런타임에서 제공하는 연결 문자열을 사용하고, 응용 프로그램이 해당 장치의 IoT Edge 허브에서 제공하는 인증서를 수락하는지 확인하는 두 단계로 구성됩니다.

사용할 연결 문자열은 IoT Edge 런타임에서 `EdgeHubConnectionString` 환경 변수에 삽입됩니다. 이렇게 하면 이를 사용하려는 모든 프로그램에서 사용할 수 있습니다.

마찬가지로 IoT Edge 허브 연결의 유효성을 검사하는 데 사용할 인증서는 IoT Edge 런타임에서 `EdgeModuleCACertificateFile` 환경 변수에 사용할 수 있는 경로의 파일에 삽입됩니다.

[시뮬레이션된 장치에 IoT Edge 모듈 개발 및 배포][lnk-tutorial2] 자습서에서는 인증서가 모듈 응용 프로그램의 컴퓨터 저장소에 있는지 확인하는 방법을 보여 줍니다. 확실히 해당 인증서를 사용하는 연결을 신뢰하는 다른 메서드가 효과적입니다.

## <a name="packaging-as-an-image"></a>이미지로 패키징
IoT Edge 모듈은 Docker 이미지로 패키지됩니다.
[시뮬레이션된 장치에 IoT Edge 모듈 개발 및 배포][lnk-tutorial2] 자습서에서 설명한 대로 Docker 도구 체인을 직접적으로 또는 Visual Studio Code로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

모듈을 개발한 후에 [대규모 IoT Edge 모듈 배포 및 모니터링][lnk-howto-deploy]을 수행하는 방법을 알아봅니다.

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
