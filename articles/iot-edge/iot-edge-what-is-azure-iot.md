---
title: "사물 인터넷을 위한 Azure 솔루션(IoT Edge) | Microsoft Docs"
description: "샘플 IoT 솔루션 아키텍처의 개요 및 장치에 연결된 방법, Azure IoT Hub 서비스, Azure IoT 장치 SDK, Azure IoT 서비스 SDK 및 기타 Azure 서비스입니다."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: a859e379-dca7-42fa-bdf6-1125c86ad140
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 587b733106d511ec63d71f67a06e520324a3e594
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
[!INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## <a name="next-steps"></a>다음 단계

Azure IoT Edge는 에지에서 분석 및 데이터 처리를 가능하게 해주는 Azure 서비스입니다. IoT Edge를 사용하면 이미 사용 중인 Azure 서비스에서 직접 끌어온 논리를 포함하고 있는 컨테이너 기반 코드 또는 사용자 고유의 솔루션별 코드를 장치에 사용할 수 있습니다. 그러면 장치에서 다음과 같은 작업을 할 수 있습니다.

* 게이트웨이 장치로 작동하여 여러 리프 장치에서 데이터를 집계하여 처리합니다.
* 클라우드의 지침을 기다릴 필요 없이 이상을 감지하고 환경 변화에 대응합니다.
* 데이터를 전처리하고 결과를 전송하여 대역폭 및 저장소 비용을 최소화합니다. 

또한 IoT Edge는 장치를 원격으로 관리할 수 있게 해주는 클라우드 인터페이스를 포함하고 있습니다. 장치에 물리적으로 액세스할 필요 없이 코드를 배포하고, 상태를 모니터링하고, 상태를 업데이트할 수 있습니다. 원격으로 단일 장치를 관리하거나, 정의하는 대규모 장치 집합에 영향을 주는 배포를 만들 수 있습니다. 자세한 내용은 [단일 장치 또는 대규모 IoT Edge 배포에 대한 이해][lnk-deployment]를 참조하세요.

IoT Edge를 사용할 수 있는 구성 요소에 대한 자세한 내용은 [Azure IoT Edge의 작동 원리][lnk-overview]를 참조하세요.

이전에 Azure IoT Hub를 사용해 본 경험이 없으면 [Azure IoT Hub 서비스 개요][lnk-iot-hub]부터 시작하세요.

[lnk-deployment]: module-deployment-monitoring.md
[lnk-overview]: how-iot-edge-works.md
[lnk-iot-hub]: ../iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md
