---
title: "Azure IoT Edge 모듈 이해 | Microsoft Docs"
description: "Azure IoT Edge 모듈 및 구성 방법에 대해 알아보기"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0f3ce7496427b6975eb4ac476e7d1737321ed2e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Azure IoT Edge 모듈 이해 - 미리 보기

Azure IoT Edge를 사용하면 에지에서 비즈니스 논리를 *모듈* 형태로 배포하고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 계산의 최소 단위이며 Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션별 코드를 포함할 수 있습니다. 모듈을 개발, 배포 및 유지 관리하는 방법을 이해하려면 모듈을 구성하는 4가지 개념적 요소를 고려하는 것이 좋습니다.

* **모듈 이미지**는 모듈을 정의하는 소프트웨어가 포함된 패키지입니다.
* **모듈 인스턴스**는 IoT Edge 장치에서 모듈 이미지를 실행하는 특정 계산 단위입니다. 모듈 인스턴스는 IoT Edge 런타임에 의해 시작됩니다.
* **모듈 ID**는 각 모듈 인스턴스에 연결된 IoT Hub에 저장된 정보(보안 자격 증명 포함)입니다.
* **모듈 쌍**은 IoT Hub에 저장된 JSON 문서로, 메타데이터, 구성 및 조건 등 모듈 인스턴스의 상태 정보를 포함합니다. 

## <a name="module-images-and-instances"></a>모듈 이미지 및 인스턴스

IoT Edge 모듈 이미지에는 IoT Edge 런타임의 관리, 보안 및 통신 기능을 활용하는 응용 프로그램이 포함되어 있습니다. 자신만의 모듈 이미지를 개발하거나 Azure Stream Analytics와 같이 지원되는 Azure 서비스에서 모듈 이미지를 내보낼 수 있습니다.
이미지는 클라우드에 존재하며 다른 솔루션으로 업데이트, 변경 및 배포할 수 있습니다. 예를 들어 기계 학습을 사용하여 생산 라인의 결과를 예측하는 모듈은 컴퓨터 시각을 사용하여 드론을 제어하는 모듈과는 별도의 이미지로 존재합니다. 

모듈 이미지가 장치에 배포되고 IoT Edge 런타임에서 시작될 때마다 해당 모듈의 새 인스턴스가 만들어집니다. 전 세계의 다른 지역에 있는 두 장치가 동일한 모듈 이미지를 사용할 수 있습니다. 그러나 각 모듈이 장치에서 시작될 때 고유한 모듈 인스턴스를 사용하게 됩니다. 

![클라우드의 모듈 이미지 - 장치의 모듈 인스턴스][1]

구현 시 모듈 이미지는 컨테이너 이미지로 리포지토리에 존재하며 모듈 인스턴스는 장치의 컨테이너입니다. Azure IoT Edge의 사용 사례가 증가함에 따라 새로운 유형의 모듈 이미지와 인스턴스가 생성됩니다. 예를 들어 리소스가 제한된 장치는 컨테이너를 실행할 수 없으므로 동적 연결 라이브러리 및 인스턴스(실행 파일)로 존재하는 모듈 이미지가 필요할 수 있습니다. 

## <a name="module-identities"></a>모듈 ID

새로운 모듈 인스턴스가 IoT Edge 런타임에 의해 생성되면 인스턴스가 해당 모듈 ID와 연관됩니다. 모듈 ID는 IoT Hub에 저장되며 특정 모듈 인스턴스의 모든 로컬 및 클라우드 통신에 대한 주소 지정 및 보안 범위로 사용됩니다.
모듈 인스턴스와 연관된 ID는 인스턴스가 실행 중인 장치의 ID와 솔루션의 해당 모듈에 제공한 이름에 따라 다릅니다. 예를 들어 Azure Stream Analytics를 사용하는 `insight` 모듈을 호출하여 `Hannover01`이라는 장치에 배포하면 IoT Edge 런타임에서 `/devices/Hannover01/modules/insight`이라는 해당 모듈 ID를 만듭니다.

동일한 장치에 하나의 모듈 이미지를 여러 번 배포해야 하는 경우 동일한 이미지를 다른 이름으로 여러 번 배포할 수 있습니다.

![모듈 ID가 고유한 경우][2]

## <a name="module-twins"></a>모듈 쌍

각 모듈 인스턴스에는 모듈 인스턴스를 구성하는 데 사용할 수 있는 해당 모듈 쌍이 있습니다. 인스턴스와 쌍은 모듈 ID를 통해 서로 연결됩니다. 

모듈 쌍은 모듈 정보와 구성 속성을 저장하는 JSON 문서입니다. 이 개념은 IoT Hub의 [장치 쌍][lnk-device-twin] 개념과 유사합니다. 모듈 쌍의 구조는 장치 쌍과 정확하게 같습니다. 두 유형의 쌍과 상호 작용하는 데 사용되는 API도 같습니다. 이 둘의 유일한 차이점은 클라이언트 SDK를 인스턴스화하는 데 사용되는 ID입니다. 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>다음 단계
 - [Azure IoT Edge 런타임 및 해당 아키텍처 이해][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md