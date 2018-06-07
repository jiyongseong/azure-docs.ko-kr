---
title: "Azure IoT Edge 런타임 이해 | Microsoft Docs"
description: "Azure IoT Edge 런타임 및 Edge 장치에 권한을 부여하는 방법을 알아봅니다."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8bd725e2201cb08853f4fb63d156b6359427663b
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2018
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture---preview"></a>Azure IoT Edge 런타임 및 아키텍처에 대한 이해 - 미리 보기

IoT Edge 런타임은 장치에 설치해야 하는 프로그램 모음으로 IoT Edge 장치로 간주됩니다. 전체적으로 IoT Edge 런타임의 구성 요소는 IoT Edge 장치를 사용하여 에지에서 실행될 코드를 받고 결과를 전달합니다. 

IoT Edge 런타임은 IoT Edge 장치에서 다음 기능을 수행합니다.

* 장치에 워크로드를 설치하고 업데이트합니다.
* 장치에서 Azure IoT Edge 보안 표준을 유지 관리합니다.
* [IoT Edge 모듈][lnk-modules]이 항상 실행되도록 합니다.
* 원격 모니터링을 위해 모듈 상태를 클라우드에 보고합니다.
* 다운스트림 리프 장치와 IoT Edge 장치 간의 통신을 용이하게 합니다.
* IoT Edge 장치의 모듈 간 통신을 용이하게 합니다.
* IoT Edge 장치와 클라우드 간의 통신을 용이하게 합니다.

![IoT Hub에 통찰력 및 모듈 상태를 전달하는 IoT Edge 런타임][1]

IoT Edge 런타임의 책임은 모듈 관리와 통신이라는 두 가지 범주로 나뉩니다. 이 두 역할은 IoT Edge 런타임을 구성하는 두 가지 구성 요소를 통해 수행됩니다. IoT Edge 허브는 통신을 담당하고, IoT Edge 에이전트는 모듈 배포 및 모니터링을 관리합니다. 

Edge 에이전트와 Edge 허브는 모두 IoT Edge 장치에서 실행되는 다른 모듈과 마찬가지로 모듈입니다. 모듈 작동 방식에 대한 자세한 내용은 [lnk-modules]를 참조하세요. 

## <a name="iot-edge-hub"></a>IoT Edge 허브

Edge 허브는 Azure IoT Edge 런타임을 구성하는 두 가지 모듈 중 하나입니다. IoT Hub와 동일한 프로토콜 끝점을 노출하여 IoT Hub에 대한 로컬 프록시 역할을 합니다. 이 일관성은 클라이언트(장치 또는 모듈)에서 IoT Hub와 마찬가지로 IoT Edge 런타임에 연결할 수 있음을 의미합니다. 

>[!NOTE]
> 공개 미리 보기 동안 Edge 허브는 MQTT를 사용하여 연결하는 클라이언트만 지원합니다.

Edge 허브는 로컬로 실행되는 IoT Hub의 전체 버전이 아닙니다. Edge 허브에서 자동으로 IoT Hub에 위임하는 몇 가지 사항이 있습니다. 예를 들어 장치에서 처음으로 연결하려고 할 때 Edge 허브는 IoT Hub에 인증 요청을 전달합니다. 첫 번째 연결이 설정되면 Edge 허브에서 보안 정보를 로컬로 캐시합니다. 클라우드에서 인증하지 않아도 해당 장치로부터의 후속 연결이 허용됩니다. 

>[!NOTE]
> 공개 미리 보기 동안 런타임은 장치를 인증하려고 할 때마다 연결되어야 합니다.

Edge 허브는 IoT Edge 솔루션에서 사용하는 대역폭을 줄이기 위해 클라우드에 실제로 연결되는 수를 최적화합니다. Edge 허브는 모듈 또는 리프 장치와 같은 클라이언트에서 논리적 연결을 가져와서 클라우드에 대한 하나의 실제 연결에 결합합니다. 이 프로세스의 세부 정보는 솔루션의 나머지 부분에 투명합니다. 클라이언트는 모두 동일한 연결을 통해 전송되는 경우에도 클라우드에 대한 자체 연결이 있다고 생각하면 됩니다. 

![여러 물리적 장치와 클라우드 간의 게이트웨이 역할을 하는 Edge 허브][2]

Edge 허브는 IoT Hub에 연결되어 있는지 여부를 결정할 수 있습니다. 연결이 끊어진 경우 Edge 허브는 메시지 또는 쌍 업데이트를 로컬로 저장합니다. 연결이 다시 설정되면 모든 데이터가 동기화됩니다. 이 임시 캐시에 사용되는 위치는 Edge 허브 모듈 쌍의 속성으로 결정됩니다. 캐시의 크기는 제한되지 않으며, 장치에 저장 용량이 있는 동안에는 증가합니다. 

>[!NOTE]
>추가 캐싱 매개 변수에 대한 컨트롤 추가는 일반 공급으로 전환되기 전에 제품에 추가됩니다.

### <a name="module-communication"></a>모듈 통신

Edge 허브는 모듈 간 통신을 용이하게 합니다. Edge broker를 메시지 broker로 사용하면 모듈이 서로 독립적으로 유지됩니다. 모듈은 메시지를 수락하는 입력과 메시지를 쓰는 출력을 지정하기만 하면 됩니다. 솔루션 개발자는 이러한 입력과 출력을 함께 연결하여 모듈에서 해당 솔루션과 관련된 순서로 데이터를 처리하도록 합니다. 

![모듈 간 통신을 용이하게 하는 Edge 허브][3]

Edge 허브에 데이터를 보내려면 모듈에서 SendEventAsync 메서드를 호출합니다. 첫 번째 인수는 메시지를 보낼 출력을 지정합니다. 다음 의사 코드는 메시지를 output1에 보냅니다.

   ```csharp
   DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

메시지를 수신하려면 특정 입력에 들어오는 메시지를 처리하는 콜백을 등록합니다. 다음 의사 코드는 input1에서 받은 모든 메시지를 처리하는 데 사용할 messageProcessor 함수를 등록합니다.

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

솔루션 개발자는 Edge 허브에서 모듈 간에 메시지를 전달하는 방식을 결정하는 규칙을 지정합니다. 라우팅 규칙은 클라우드에서 정의되며 장치 쌍의 Edge 허브로 푸시 다운됩니다. IoT Hub 경로에 대한 동일한 구문이 Azure IoT Edge의 모듈 간 경로를 정의하는 데 사용됩니다. 

<!--- For more info on how to declare routes between modules, see []. --->   

![모듈 간 경로][4]

## <a name="iot-edge-agent"></a>IoT Edge 에이전트

IoT Edge 에이전트는 Azure IoT Edge 런타임을 구성하는 다른 모듈입니다. 모듈을 인스턴스화하고, 모듈을 계속 실행하며, 모듈의 상태를 IoT Hub에 다시 보고합니다. Edge 에이전트는 다른 모듈과 마찬가지로 모듈 쌍을 사용하여 이 구성 데이터를 저장합니다. 

Edge 에이전트의 실행을 시작하려면 azure-iot-runtime-ctl.py 시작 명령을 실행합니다. 에이전트는 IoT Hub에서 모듈 쌍을 검색하고 모듈 사전을 검사합니다. 모듈 사전은 시작해야 하는 모듈의 컬렉션입니다. 

모듈 사전의 각 항목에는 모듈에 대한 특정 정보가 포함되며, 모듈의 수명 주기를 제어하기 위해 Edge 에이전트에서 사용합니다. 더 유용한 속성 중 일부는 다음과 같습니다. 

* **settings.image** – Edge 에이전트에서 모듈을 시작하는 데 사용하는 컨테이너 이미지입니다. 이미지가 암호로 보호되는 경우 Edge 에이전트는 컨테이너 레지스트리에 대한 자격 증명으로 구성되어야 합니다. Edge 에이전트를 구성하려면 `azure-iot-edge-runtime-ctl.py –configure` 명령을 사용합니다.
* **settings.createOptions** – 모듈의 컨테이너를 시작할 때 Docker 디먼에 직접 전달되는 문자열입니다. 이 속성에 Docker 옵션을 추가하면 포트 전달 또는 모듈의 컨테이너에 볼륨 탑재와 같은 고급 옵션을 사용할 수 있습니다.  
* **status** – Edge 에이전트에서 모듈을 배치하는 상태입니다. 대부분의 사람들이 Edge 에이전트에서 장치의 모든 모듈을 즉시 시작하려고 하기 때문에 이 값은 일반적으로 'running'으로 설정됩니다. 그러나 중지할 모듈의 초기 상태를 지정하고, 나중에 Edge 에이전트에 모듈 시작을 알릴 때까지 기다릴 수 있습니다. Edge 에이전트는 각 모듈의 상태를 reported 속성의 클라우드로 다시 보고합니다. desired 속성과 reported 속성의 차이는 표시기 또는 오작동 장치입니다. 지원되는 상태는 다음과 같습니다.
   * 다운로드 중
   * 실행 중
   * 비정상
   * 실패
   * 중지됨
* **restartPolicy** – Edge 에이전트에서 모듈을 다시 시작하는 방법입니다. 가능한 값은 다음과 같습니다.
   * Never – Edge 에이전트에서 모듈을 다시 시작하지 않습니다.
   * onFailure - 모듈이 충돌하면 Edge 에이전트에서 해당 모듈을 다시 시작합니다. 모듈이 완전히 종료되면 Edge 에이전트에서 해당 모듈을 다시 시작하지 않습니다.
   * Unhealthy - 모듈이 충돌하거나 비정상 상태로 간주되면 Edge 에이전트에서 해당 모듈을 다시 시작합니다.
   * Always - 모듈이 충돌하거나 비정상 상태로 간주되거나 어떤 방법으로든 종료되면 Edge 에이전트에서 해당 모듈을 다시 시작합니다. 

IoT Edge 에이전트는 IoT Hub에 런타임 응답을 보냅니다. 가능한 응답 목록은 다음과 같습니다.
  * 200 - 확인
  * 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
  * 417 - 장치에 배포 구성 집합이 없습니다.
  * 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
  * 406 - 에지 장치가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
  * 500 - 에지 런타임에서 오류가 발생했습니다.

### <a name="security"></a>보안

IoT Edge 에이전트는 IoT Edge 장치의 보안에서 중요한 역할을 합니다. 예를 들어 모듈의 이미지를 시작하기 전에 해당 이미지의 확인과 같은 작업을 수행합니다. 이러한 기능은 V2 기능의 일반 공급 시에 추가됩니다. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>다음 단계

- [Azure IoT Edge 모듈 이해][lnk-modules]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-modules]: iot-edge-modules.md
