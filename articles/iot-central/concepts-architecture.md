---
title: Azure IoT Central의 아키텍처 개념 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 아키텍처와 관련된 주요 개념을 소개합니다.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: c97db3eb2c0fe1a5ec3c743ca75c595ec127823e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200091"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central 아키텍처

이 문서에서는 Microsoft Azure IoT Central 아키텍처의 개요를 제공합니다.

![최상위 아키텍처](media/concepts-architecture/architecture.png)

## <a name="devices"></a>장치

장치는 Azure IoT Central 응용 프로그램과 데이터를 교환합니다. 장치는 다음을 수행할 수 있습니다.

- 원격 분석 데이터 같은 측정값을 보냅니다.
- 응용 프로그램과 설정을 동기화합니다.

Azure IoT Central에서, 장치가 응용 프로그램과 교환할 수 있는 데이터는 장치 템플릿에 지정됩니다. 장치 템플릿에 대한 자세한 내용은 [메타데이터 관리](#metadata-management)를 참조하세요.

장치가 Azure IoT Central 응용 프로그램에 연결하는 방법에 대한 자세한 내용은 [장치 연결](concepts-connectivity.md)을 참조하세요.

## <a name="cloud-gateway"></a>클라우드 게이트웨이

Azure IoT Central은 장치 연결을 가능하게 하는 클라우드 게이트웨이로 Azure IoT Hub를 사용합니다. IoT Hub의 기능은 다음과 같습니다.

- 클라우드에서 대규모 데이터 수집.
- 장치 관리.
- 장치 연결 보안.

IoT Hub에 대한 자세한 내용은 [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/)를 참조하세요.

Azure IoT Central의 장치 연결에 대한 자세한 내용은 [장치 연결](concepts-connectivity.md)을 참조하세요.

## <a name="data-stores"></a>데이터 저장소

Azure IoT Central은 응용 프로그램 데이터를 클라우드에 저장합니다. 다음과 같은 응용 프로그램 데이터가 저장됩니다.

- 장치 템플릿.
- 장치 ID.
- 장치 메타데이터.
- 사용자 및 역할 데이터.

Azure IoT Central은 장치에서 보낸 측정값 데이터에 시계열 저장소를 사용합니다. 분석 서비스에서 사용하는 장치의 시계열 데이터.

## <a name="analytics"></a>분석

분석 서비스는 응용 프로그램에서 표시하는 사용자 지정 보고 데이터를 생성합니다. 운영자는 응용 프로그램에 표시되는 [분석을 사용자 지정](howto-create-analytics.md)할 수 있습니다. 분석 서비스는 [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) 위에 빌드되며, 장치에서 보낸 측정값 데이터를 처리합니다.

## <a name="rules-and-actions"></a>규칙 및 동작

[규칙 및 동작](howto-create-telemetry-rules.md)은 서로 긴밀하게 작동하여 응용 프로그램 내의 작업을 자동화합니다. 작성자는 정의된 임계값을 초과하는 온도 같은 장치 원격 분석 데이터를 기반으로 규칙을 정의할 수 있습니다. Azure IoT Central은 스트림 프로세서를 사용하여 규칙 조건 충족 여부를 확인합니다. 규칙 조건이 충족되면 작성자가 정의한 작업을 트리거합니다. 예를 들어 장치 온도가 너무 높다고 엔지니어에게 알리는 이메일을 작업에서 보낼 수 있습니다.

## <a name="metadata-management"></a>메타데이터 관리

Azure IoT Central 응용 프로그램에서 장치 템플릿은 장치 유형의 동작 및 기능을 정의합니다. 예를 들어 냉장고 장치 템플릿은 냉장고가 응용 프로그램에 보내는 원격 분석 데이터를 지정합니다.

![템플릿 아키텍처](media/concepts-architecture/template_architecture.png)

장치 템플릿에서:

- **측정값**은 장치가 원격 분석 응용 프로그램으로 보내는 원격 분석 데이터를 지정합니다.
- **설정**은 운영자가 설정할 수 있는 구성을 지정합니다.
- **속성**은 운영자가 설정할 수 있는 메타데이터를 지정합니다.
- **규칙**은 장치에서 보낸 데이터를 기반으로 응용 프로그램의 동작을 자동화합니다.
- **대시보드**는 응용 프로그램에서 장치를 볼 수 있는 사용자 지정 가능한 보기입니다.

각 장치 템플릿에 따라 한 응용 프로그램에서 시뮬레이션된 장치 및 실제 장치를 하나 이상 사용할 수 있습니다.

## <a name="rbac"></a>RBAC

관리자는 미리 정의된 역할을 사용하여 Azure IoT Central 응용 프로그램에 대한 [액세스 규칙을 정의](howto-administer.md)할 수 있습니다. 관리자는 사용자가 액세스할 수 있는 응용 프로그램 영역을 결정하는 역할을 사용자에게 할당할 수 있습니다.

## <a name="security"></a>보안

Azure IoT Central에는 다음과 같은 보안 기능이 있습니다.

- 전송 및 저장 시 데이터가 암호화됩니다.
- Azure Active Directory 또는 Microsoft 계정을 통해 인증이 제공됩니다. 2단계 인증이 지원됩니다.
- 테넌트가 완전히 격리됩니다.
- 장치 수준 보안을 제공합니다.

## <a name="ui-shell"></a>UI 셸

UI 셸은 응답성이 우수한 최신 HTML5 브라우저 기반 응용 프로그램입니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central의 아키텍처에 대해 배웠으니, 그 다음 단계로 Azure IoT Central의 [장치 연결](concepts-connectivity.md)에 대해 알아보세요.