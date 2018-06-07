---
title: Azure IoT Hub IP 연결 필터 | Microsoft Docs
description: 특정 IP 주소에서 Azure IoT hub로 연결을 차단하도록 IP 필터링을 사용하는 방법입니다. 개별 또는 IP 주소 범위에서 연결을 차단할 수 있습니다.
services: iot-hub
documentationcenter: ''
author: BeatriceOltean
manager: timlt
editor: ''
ms.assetid: f833eac3-5b5f-46a7-a47b-f4f6fc927f3f
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: 0229b8b16de1fa04d953c90895b29e4fbad60ec3
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="use-ip-filters"></a>IP 필터 사용

보안은 Azure IoT Hub를 기반으로 하는 모든 IoT 솔루션의 중요한 측면입니다. 경우에 따라 장치가 보안 구성의 일부로 연결할 수 있는 IP 주소를 명시적으로 지정해야 합니다. _IP 필터_ 기능을 사용하면 특정 IPv4 주소에서 들어오는 트래픽을 거부하거나 수락하는 규칙을 구성할 수 있습니다.

## <a name="when-to-use"></a>사용하는 경우

특정 IP 주소에 대해 IoT Hub 끝점을 차단하는 것이 유용한 두 가지 사용 사례가 있습니다.

- IoT Hub가 지정된 범위의 IP 주소에서 오는 트래픽만 수신하고 그 밖의 트래픽은 거부합니다. 예를 들어 IoT Hub를 [Azure ExpressRoute]와 사용하여 IoT Hub와 온-프레미스 인프라 간의 개인 연결을 만드는 경우입니다.
- IoT Hub 관리자에 의해 의심스러운 것으로 식별된 IP 주소에서 오는 트래픽을 거부해야 합니다.

## <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 IoT Hub 서비스 수준에 적용됩니다. 따라서 IP 필터 규칙은 지원되는 모든 프로토콜을 사용하는 장치 및 백 엔드 앱의 모든 연결에 적용됩니다.

IoT Hub의 거부 IP 규칙에 일치하는 IP 주소에서 오는 모든 연결 시도는 권한 없음 401 상태 코드 및 설명을 수신합니다. 응답 메시지는 IP 규칙을 언급하지 않습니다.

## <a name="default-setting"></a>기본 설정

기본적으로 IoT Hub에 대한 포털의 **IP 필터** 그리드는 비어있습니다. 이러한 기본 설정은 허브가 모든 IP 주소의 연결을 수락한다는 것을 의미합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다.

![IoT Hub 기본 IP 필터 설정][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>IP 필터 규칙 추가 또는 편집

IP 필터 규칙을 추가하는 경우 다음 값을 입력하라는 메시지가 표시됩니다.

- **IP 필터 규칙 이름**은 최대 128자의 대/소문자를 구분하지 않는 영숫자 문자열로 고유해야 합니다. ASCII 7 비트 영숫자 및 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`만 허용됩니다.
- IP 필터 규칙에 대한 **작업**으로 **거부** 또는 **수락**을 선택합니다.
- 단일 IPv4 주소 또는 CIDR 표기법으로 IP 주소 블록을 제공합니다. 예를 들어 CIDR 표기법 192.168.100.0/22는 192.168.100.0에서 192.168.103.255까지 IPv4 주소 1024개를 나타냅니다.

![IP 필터 규칙을 IoT Hub에 추가][img-ip-filter-add-rule]

규칙을 저장한 후 업데이트를 진행 중이라고 알려주는 경고가 표시됩니다.

![IP 필터 규칙 저장에 대한 알림][img-ip-filter-save-new-rule]

최대 10개의 IP 필터 규칙에 도달하면 **추가** 옵션이 비활성화됩니다.

규칙을 포함하는 행을 두 번 클릭하면 기존 규칙을 편집할 수 있습니다.

> [!NOTE]
> IP 주소를 거부하면 다른 Azure 서비스(예: Azure Stream Analytics, Azure Virtual Machines 또는 포털의 장치 탐색기)가 IoT Hub와 상호 작용하는 것을 막을 수 있습니다.

> [!WARNING]
> ASA(Azure Stream Analytics)를 사용하여 IP 필터링이 활성화된 IoT Hub에서 메시지를 읽는 경우 ASA 연결 문자열에 이벤트 허브와 호환되는 IoT Hub 이름 및 끝점을 사용합니다.

## <a name="delete-an-ip-filter-rule"></a>IP 필터 규칙 삭제

IP 필터 규칙을 삭제하려면 그리드에서 규칙을 하나 이상 선택하고 **삭제**를 클릭합니다.

![IoT Hub IP 필터 규칙 삭제][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>IP 필터 규칙 평가

IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

예를 들어 192.168.100.0/22 범위의 주소를 수락하고 그 외의 주소는 거부하려는 경우 그리드에 있는 첫 번째 규칙이 주소 범위 192.168.100.0/22를 수락해야 합니다. 다음 규칙은 0.0.0.0/0 범위를 사용하여 모든 주소를 거부해야 합니다.

행의 시작 부분에 있는 세 개의 세로 점을 클릭하고 끌어서 놓기를 사용하여 그리드에서 IP 필터 규칙의 순서를 변경할 수 있습니다.

새 IP 필터 규칙 순서를 저장하려면 **저장**을 클릭합니다.

![IoT Hub IP 필터 규칙의 순서 변경][img-ip-filter-rule-order]

## <a name="next-steps"></a>다음 단계

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

- [작업 모니터링][lnk-monitor]
- [IoT Hub 메트릭][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure ExpressRoute]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
