---
title: Azure IoT Hub를 진단 설정으로 마이그레이션 | Microsoft Docs
description: 작업 모니터링 대신 Azure 진단 설정을 사용하여 IoT Hub의 작업 상태를 실시간으로 모니터링하도록 Azure IoT Hub를 업데이트하는 방법을 설명합니다.
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
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: a46f6798a71c93ed769ae68877e72801d45b74a4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>Azure IoT Hub를 작업 모니터링에서 진단 설정으로 마이그레이션

[작업 모니터링][lnk-opsmon]을 사용하여 IoT Hub의 작업 상태를 추적하는 고객은 해당 워크플로를 Azure Monitor의 기능인 [Azure 진단 설정][lnk-diagnostics-settings]으로 마이그레이션할 수 있습니다. 진단 설정은 여러 Azure 서비스에 대한 리소스 수준의 진단 정보를 제공합니다.

Azure IoT Hub의 작업 모니터링 기능은 사용되지 않으며 나중에 제거될 예정입니다. 이 문서에서는 워크로드를 작업 모니터링에서 진단 설정으로 이동하는 단계를 설명합니다. 사용 중단 타임라인에 대한 자세한 내용은 [Azure Monitor 및 Azure Resource Health로 Azure IoT 솔루션 모니터링][lnk-blog-announcement]을 참조하세요.

## <a name="update-iot-hub"></a>IoT Hub 업데이트

Azure Portal에서 IoT Hub를 업데이트하려면 먼저 진단 설정을 켠 다음 작업 모니터링을 꺼야 합니다.  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>작업 모니터링 끄기

워크플로에서 새 진단 설정을 테스트한 후에는 작업 모니터링 기능을 끕니다. 

1. IoT Hub 메뉴에서 **작업 모니터링**을 선택합니다.
1. 각 모니터링 범주에서 **없음**을 선택합니다.
1. 작업 모니터링 변경 내용을 저장합니다.

## <a name="update-applications-that-use-operations-monitoring"></a>작업 모니터링을 사용하는 응용 프로그램 업데이트

작업 모니터링과 진단 설정의 스키마는 약간 다릅니다. 오늘은 작업 모니터링에서 사용하는 응용 프로그램을 업데이트하여 진단 설정에서 사용하는 스키마로 매핑하게 만드는 것이 중요합니다. 

또한 진단 설정은 5개의 새 범주를 추적할 수 있습니다. 기존 스키마에 대한 응용 프로그램을 업데이트한 후에는 새 범주를 추가합니다.

- 클라우드-장치 쌍 작업
- 장치-클라우드 쌍 작업
- 쌍 쿼리
- 작업 연산
- 직접 메서드

특정 스키마 구조에 대한 자세한 내용은 [진단 설정에 대한 스키마의 이해][lnk-diagnostics-schema]를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure IoT Hub 상태 모니터링 및 신속한 문제 진단][lnk-monitor]

[lnk-opsmon]: iot-hub-operations-monitoring.md
[lnk-diagnostics-settings]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[lnk-diagnostics-schema]: iot-hub-monitor-resource-health.md#understand-the-logs
[lnk-blog-announcement]: https://azure.microsoft.com/blog/monitor-your-azure-iot-accelerators-with-azure-monitor-and-azure-resource-health
[lnk-monitor]: iot-hub-monitor-resource-health.md
