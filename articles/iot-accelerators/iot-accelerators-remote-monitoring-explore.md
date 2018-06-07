---
title: 원격 모니터링 솔루션 시작 - Azure | Microsoft Docs
description: 이 자습서에서는 원격 모니터링 솔루션 가속기를 소개하기 위해 시뮬레이트된 시나리오를 사용합니다. 이러한 시나리오는 원격 모니터링 솔루션 가속기를 처음으로 배포할 때 생성됩니다.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c8a055dd0dc8e2d9a9a64b492fb134caaacda489
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="explore-the-capabilities-of-the-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기의 기능 탐색

이 자습서에서는 원격 모니터링 솔루션의 주요 기능을 보여 줍니다. 이러한 기능을 소개하기 위해 자습서에서는 Contoso라는 회사에 대한 시뮬레이트된 IoT 응용 프로그램을 사용하여 일반적인 고객 시나리오를 보여 줍니다.

자습서를 통해 원격 모니터링 솔루션에서 기본으로 제공하는 일반적인 IoT 시나리오를 이해할 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 대시보드의 장치 시각화 및 필터링
> * 경고에 응답
> * 장치에서 펌웨어 업데이트
> * 자산 구성
> * 시뮬레이션된 장치 중지 및 시작

다음 비디오에서는 원격 모니터링 솔루션 연습을 보여 줍니다.

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Part-28-An-introduction-to-Azure-IoT-through-the-new-Remote-Monitoring-Preconfigured-Solution/Player]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure 구독에서 원격 모니터링 솔루션의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션을 아직 배포하지 않은 경우 [원격 모니터링 솔루션 가속기 배포](iot-accelerators-remote-monitoring-deploy.md) 자습서를 완료해야 합니다.

## <a name="the-contoso-sample-iot-deployment"></a>Contoso 샘플 IoT 배포

Contoso 샘플 IoT 배포를 사용하여 원격 모니터링 솔루션에서 기본으로 제공하는 기본 시나리오를 이해할 수 있습니다. 이러한 시나리오는 실제 IoT 배포를 기반으로 합니다. 대부분의 경우 특정 요구 사항을 충족하기 위해 원격 모니터링 솔루션을 사용자 지정하도록 선택하지만 Contoso 샘플을 통해 기본 사항을 알아볼 수 있습니다.

> [!NOTE]
> CLI를 사용하여 솔루션 가속기를 배포하는 경우 `deployment-{your deployment name}-output.json` 파일은 배포된 샘플에 액세스하기 위한 URL과 같은 배포에 대한 정보를 포함합니다.

Contoso 샘플은 시뮬레이트된 장치 집합 및 작동하는 규칙을 프로비전합니다. 기본 시나리오를 이해하면 [원격 모니터링 솔루션을 사용하여 고급 장치 모니터링 수행](iot-accelerators-remote-monitoring-monitor.md)에서 더 많은 솔루션 기능을 계속해서 탐색할 수 있습니다.

Contoso는 서로 다른 환경에서 다양한 자산을 관리하는 회사입니다. Contoso는 클라우드 기반 IoT 응용 프로그램을 사용하여 중앙 집중화된 응용 프로그램의 여러 자산을 원격으로 모니터링하고 관리하려고 합니다. 다음 섹션에서는 Contoso 샘플의 초기 구성의 요약을 제공합니다.

> [!NOTE]
> Contoso 데모는 시뮬레이트된 장치를 프로비전하고 규칙을 만드는 유일한 방법입니다. 다른 프로비전 옵션은 고유한 사용자 지정 장치의 생성을 포함합니다. 사용자 고유의 장치 및 규칙을 만드는 방법에 대해 자세히 알아보려면 [장치 관리 및 구성](iot-accelerators-remote-monitoring-manage.md) 및 [임계값 기반 규칙을 사용하여 문제 감지](iot-accelerators-remote-monitoring-automate.md)를 참조하세요.

### <a name="contoso-devices"></a>Contoso 장치

Contoso는 다양한 유형의 스마트 장치를 사용합니다. 이러한 장치는 다양한 원격 분석 스트림을 보내는 회사에 대한 서로 다른 역할을 충족합니다. 또한 각 장치 유형에는 서로 다른 장치 속성 및 지원되는 메서드가 있습니다.

다음 표는 프로비전된 장치 유형의 요약을 보여 줍니다.

| 장치 유형        | 원격 분석                                  | properties                                  | 태그들                    | 메서드                                                                                      |
| ------------------ | ------------------------------------------ | ------------------------------------------- | ----------------------- | -------------------------------------------------------------------------------------------- |
| 냉각기            | 온도, 습도, 압력            | 유형, 펌웨어 버전, 모델               | Location, Floor, Campus | Reboot, Firmware Update, Emergency Valve Release, Increase Pressure                          |
| 프로토타입 장치 | 온도, 압력, 지리적 위치        | 유형, 펌웨어 버전, 모델               | Location, Mode          | Reboot, Firmware Update, Move device, Stop device, Temperature release, Temperature increase |
| 엔진             | 탱크 연료 수준, 냉각수 센서, 진동 | 유형, 펌웨어 버전, 모델               | Location, Floor, Campus | Firmware Update, Empty tank, Fill tank                                              |
| 트럭              | 지리적 위치, 속도, 화물 온도     | 유형, 펌웨어 버전, 모델               | Location, Load          | Lower cargo temperature, Increase cargo temperature, Firmware update                         |
| 엘리베이터           | 층, 진동, 온도              | 유형, 펌웨어 버전, 모델, 지리적 위치 | Location, Campus        | Stop elevator, Start elevator, Firmware update                                               |

> [!NOTE]
> Contoso 데모 샘플은 유형당 두 개의 장치를 프로비전합니다. 각 유형의 경우 하나는 Contoso에서 정상으로 정의된 경계 내에서 올바르게 작동하며 하나는 일종의 오작동이 있습니다. 다음 섹션에서는 Contoso에서 장치에 대해 정의하는 규칙에 대해 알아봅니다. 이러한 규칙은 올바른 동작의 경계를 정의합니다.

### <a name="contoso-rules"></a>Contoso 규칙

Contoso에서 운영자는 장치가 올바르게 작동 하는지 여부를 결정하는 임계값을 알고 있습니다. 예를 들어, 보고하는 압력이 250 PSI보다 큰 경우 냉각기는 제대로 작동하지 않습니다. 다음 표에서는 각 장치 유형에 대해 Contoso에서 정의하는 임계값 기반 규칙을 보여 줍니다.

| 규칙 이름 | 설명 | 임계값 | 심각도 | 영향을 받는 장치 |
| --------- | ----------- | --------- | -------- | ---------------- |
| 냉각기 압력 너무 높음 | 냉각기가 정상 압력 수준보다 높은 압력에 도달하는 경우 경보   |P>250psi       | 중요 | 냉각기            |
| 프로토타입 장치 온도 너무 높음  | 프로토타입 장치가 정상 온도 수준보다 높은 온도에 도달하는 경우 경보  |T>80&deg; F |중요 | 프로토타입 장치 |
| 빈 엔진 탱크  | 엔진 연료 탱크가 비어가는 경우 경보                     | F<5갤런 | 정보     | 엔진             |
| 정상 화물 온도보다 높음 | 트럭의 화물 온도가 정상보다 높은 경우 경보                 | T<45&deg; F |Warning  | 트럭              |
| 엘리베이터 진동 중지됨      | 엘리베이터가 완전히 중지하는 경우 경보(진동 수준에 따라)                     | V<0.1mm |Warning  | 엘리베이터           |

### <a name="operate-the-contoso-sample-deployment"></a>Contoso 샘플 배포 동작

이제 Contoso 샘플의 초기 설치를 살펴 보았습니다. 다음 섹션에서는 운영자가 솔루션 가속기를 사용할 수 있는 방법을 설명하는 Contoso 샘플의 세 가지 시나리오를 설명합니다.

## <a name="respond-to-a-pressure-alert"></a>압력 경고에 응답

이 시나리오는 냉각기 장치에 의해 트리거되는 경고를 식별하고 이에 응답하는 방법을 보여줍니다. 냉각기는 빌딩 43, 2층, Redmond에 있습니다.

운영자로서 냉각기의 압력과 관련이 있는 경고가 있다는 것을 대시보드에서 볼 수 있습니다. 지도에서 이동 및 확대/축소하여 자세히 볼 수 있습니다.

1. **대시보드** 페이지의 **경고** 표에서 **냉각기 압력 너무 높음** 경고를 볼 수 있습니다. 냉각기는 지도에서 강조 표시됩니다.

    ![대시보드는 지도에서 압력 경고 및 장치를 보여줍니다.](./media/iot-accelerators-remote-monitoring-explore/dashboardalarm.png)

1. **유지 관리** 페이지로 이동하려면 탐색 메뉴에서 **유지 관리**를 선택합니다. **유지 관리** 페이지에서 냉각기 압력 경고를 트리거한 규칙의 세부 정보를 볼 수 있습니다.

1. 경고 목록에는 경고가 트리거된 횟수, 수신 확인 및 열린 경고와 닫힌 경고가 표시됩니다.

    ![유지 관리 페이지는 트리거된 경고의 목록을 보여줍니다.](./media/iot-accelerators-remote-monitoring-explore/maintenancealarmlist.png)

1. 목록에서 마지막 경고는 가장 최근의 것입니다. **냉각기 압력 너무 높음** 경고를 클릭하여 관련된 장치 및 원격 분석을 봅니다. 원격 분석은 냉각기에 대한 압력 스파이크를 보여 줍니다.

    ![유지 관리 페이지는 선택한 경고에 대한 원격 분석을 보여줍니다.](./media/iot-accelerators-remote-monitoring-explore/maintenancetelemetry.png)

이제 경고를 트리거한 문제 및 관련된 장치를 식별했습니다. 운영자로서 다음 단계는 경고를 승인하고 문제를 완화하는 것입니다.

1. 현재 경고에 대해 작업 중임을 나타내려면 **경고 상태**를 **승인됨**으로 변경합니다.

    ![경고 선택 및 승인](./media/iot-accelerators-remote-monitoring-explore/maintenanceacknowledge.png)

1. 냉각기를 작동하려면 선택한 다음, **작업**을 선택합니다. **Run method**, **EmergencyValveRelease**를 차례로 선택하고, 작업 이름 **ChillerPressureRelease**를 추가하고, **적용**을 선택합니다. 이러한 설정은 즉시 실행하는 작업을 만듭니다.

    ![장치 선택 및 작업 예약](./media/iot-accelerators-remote-monitoring-explore/maintenanceschedule.png)

1. 작업 상태를 확인하려면 **유지 관리** 페이지로 돌아가서 **작업** 보기에서 작업 목록을 봅니다. 냉각기에 대한 밸브 압력을 해제하기 위해 작업이 실행된 것을 확인할 수 있습니다.

    ![작업 보기의 작업 상태](./media/iot-accelerators-remote-monitoring-explore/maintenancerunningjob.png)

마지막으로 냉각기의 원격 분석 값이 정상으로 돌아갔는지 확인합니다.

1. 경고 표를 보려면 **대시보드** 페이지로 이동합니다.

1. 장치 원격 분석을 보려면 지도에서 원래 경고에 대한 장치를 선택하고, 정상으로 돌아갔는지 확인합니다.

1. 인시던트를 종결하려면 **유지 관리** 페이지로 이동하고, 경고를 선택하고, 상태를 **닫힘**으로 설정합니다.

    ![경고 선택 및 닫기](./media/iot-accelerators-remote-monitoring-explore/maintenanceclose.png)

## <a name="update-device-firmware"></a>장치 펌웨어 업데이트

Contoso는 필드에서 새로운 유형의 장치를 테스트하고 있습니다. 테스트 주기의 일부로 장치 펌웨어 업데이트가 올바르게 작동하는지 확인해야 합니다. 다음 단계에서는 원격 모니터링 솔루션을 사용하여 여러 장치에서 펌웨어를 업데이트하는 방법을 설명합니다.

필요한 장치 관리 작업을 수행하려면 **장치** 페이지를 사용합니다. 모든 프로토타입 장치에 대해 필터링하여 시작합니다.

1. **장치** 페이지로 이동합니다. **필터** 드롭다운에서 **프로토타입 장치** 필터를 선택합니다.

    ![장치 페이지에서 필터 사용](./media/iot-accelerators-remote-monitoring-explore/devicesprototypingfilter.png)

    > [!TIP]
    > **관리**를 클릭하여 사용 가능한 필터를 관리합니다.

1. 프로토타입 장치 중 하나를 선택합니다.

    ![장치 페이지에서 장치를 선택합니다.](./media/iot-accelerators-remote-monitoring-explore/devicesselect.png)

1. **작업** 단추를 클릭하고, **메서드 실행**을 선택한 다음, **펌웨어 업데이트**를 선택합니다. **작업 이름**, **펌웨어 버전** 및 **펌웨어 URI**에 대한 값을 입력합니다. **적용**을 선택하여 지금 실행하도록 작업을 예약합니다.

    ![장치에서 펌웨어 업데이트 예약](./media/iot-accelerators-remote-monitoring-explore/devicesschedulefirmware.png)

    > [!NOTE]
    > 시뮬레이트된 장치를 통해 **펌웨어 URI**와 같은 원하는 모든 URL 및 **펌웨어 버전**에 대해 원하는 모든 값을 사용할 수 있습니다. 시뮬레이트된 장치는 URL에 액세스하지 않습니다.

1. 작업이 영향을 주는 장치 수를 확인하고 **적용**을 선택합니다.

    ![펌웨어 업데이트 작업 제출](./media/iot-accelerators-remote-monitoring-explore/devicessubmitupdate.png)

**유지 관리** 페이지를 사용하여 실행되는 작업을 추적할 수 있습니다.

1. 작업 목록을 보려면 **유지 관리** 페이지로 이동하고 **작업**을 클릭합니다.

1. 만든 작업에 관련된 이벤트를 찾습니다. 펌웨어 업데이트 프로세스가 올바르게 시작되었는지 확인합니다.

<!-- 05/01 broken 
You can create a filter to verify the firmware version updated correctly.

1. To create a filter, navigate to the **Devices** page and select **Manage device groups**:

    ![Manage device groups](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. Create a filter that includes only devices with the new firmware version:

    ![Create device filter](./media/iot-accelerators-remote-monitoring-explore/devicescreatefilter.png)

1. Return to the **Devices** page and verify that the device has the new firmware version. -->

## <a name="organize-your-assets"></a>자산 구성

Contoso에는 필드 서비스 활동에 대한 두 개의 다른 팀이 있습니다.

* Smart Building 팀은 냉각기, 엘리베이터 및 엔진을 관리합니다.
* Smart Vehicle 팀은 트럭 및 프로토타입 장치를 관리합니다.

운영자로서 장치를 쉽게 구성하고 관리하기 위해 적절한 팀 이름으로 태그를 지정하려고 합니다.

장치와 사용하도록 태그 이름을 만들 수 있습니다.

1. 모든 장치를 표시하려면 **장치** 페이지로 이동하고 **모든 장치** 필터를 선택합니다.

    ![모든 장치 표시](./media/iot-accelerators-remote-monitoring-explore/devicesalldevices.png)

1. **트럭** 및 **프로토타입** 장치를 선택합니다. 그런 다음, **작업**을 선택합니다.

    ![프로토타입 및 트럭 장치 선택](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect.png)

1. **태그**를 선택한 후 **ConnectedVehicle** 값으로 **FieldService**라는 새 텍스트 태그를 만듭니다. 작업에 대한 이름을 선택합니다. 그런 다음 **적용**을 클릭합니다.

    ![프로토타입 및 트럭 장치에 태그 추가](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag.png)

1. **냉각기**, **엘리베이터** 및 **엔진** 장치를 선택합니다. 그런 다음, **작업**을 선택합니다.

    ![냉각기, 엔진 및 엘리베이터 장치 선택](./media/iot-accelerators-remote-monitoring-explore/devicesmultiselect2.png)

1. **태그**를 선택한 후 **SmartBuilding** 값으로 **FieldService**라는 새 텍스트 태그를 만듭니다. 작업에 대한 이름을 선택합니다. 그런 다음 **적용**을 클릭합니다.

    ![냉각기, 엔진 및 엘리베이터 장치에 태그 추가](./media/iot-accelerators-remote-monitoring-explore/devicesaddtag2.png)

태그 값을 사용하여 필터를 만들 수 있습니다.

1. **장치** 페이지에서 **장치 그룹 관리**를 선택합니다.

    ![장치 그룹 관리](./media/iot-accelerators-remote-monitoring-explore/devicesmanagefilters.png)

1. 태그 이름 **FieldService** 및 값 **SmartBuilding**을 사용하는 새 필터를 만듭니다. **Smart Building**으로 필터를 저장합니다.

1. 태그 이름 **FieldService** 및 값 **ConnectedVehicle**을 사용하는 새 필터를 만듭니다. **Connected Vehicle**로 필터를 저장합니다.

이제 Contoso 운영자는 장치에서 아무 것도 변경하지 않고도 운영 팀에 기반하여 장치를 쿼리할 수 있습니다.

## <a name="stop-simulated-devices"></a>시뮬레이션된 장치 중지

설정 메뉴를 사용하여 시뮬레이션된 장치를 중지할 수 있습니다. 이렇게 하면 솔루션 테스트 및 탐색 비용을 줄일 수 있습니다. 시뮬레이션된 장치를 시작 또는 중지하려면

1. **설정** 아이콘을 선택합니다.

1. 그런 다음, **흐름**을 켜거나 끕니다.

    ![설정 메뉴](./media/iot-accelerators-remote-monitoring-explore/settings.png)

## <a name="customize-the-ui"></a>UI 사용자 지정

설정 메뉴에서 원격 모니터링 솔루션 가속기에 기본 사용자 지정을 적용할 수 있습니다. 다음을 수행할 수 있습니다.

- 밝은 테마와 어두운 테마 간에 전환합니다.
- 솔루션의 이름을 변경합니다.
- 사용자 지정 로고를 업로드합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아보았습니다.

>[!div class="checklist"]
> * 대시보드의 장치 시각화 및 필터링
> * 경고에 응답
> * 장치에서 펌웨어 업데이트
> * 자산 구성
> * 시뮬레이션된 장치 중지 및 시작

이제 원격 모니터링 솔루션을 탐색했으며 제안된 다음 단계는 원격 모니터링 솔루션의 고급 기능에 대해 자세히 알아보는 것입니다.

* [장치 모니터링](./iot-accelerators-remote-monitoring-monitor.md)
* [장치 관리](./iot-accelerators-remote-monitoring-manage.md)
* [규칙을 사용하여 솔루션 자동화](./iot-accelerators-remote-monitoring-automate.md)
* [솔루션 유지 관리](iot-accelerators-remote-monitoring-maintain.md)
