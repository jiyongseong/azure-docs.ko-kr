---
title: "미리 구성된 솔루션 시작 | Microsoft Docs"
description: "미리 구성된 Azure IoT Suite 솔루션을 배포하는 방법을 알아보려면 이 자습서를 따릅니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2017
---
# <a name="get-started-with-the-preconfigured-solutions"></a>미리 구성된 솔루션 시작

Azure IoT Suite의 [미리 구성된 솔루션][lnk-preconfigured-solutions]은 여러 Azure IoT 서비스를 결합하여 일반적인 IoT 비즈니스 시나리오를 구현하는 종합적인 솔루션을 제공합니다. 미리 구성된 *원격 모니터링* 솔루션은 장치에 연결되고 장치를 모니터링합니다. 데이터의 해당 스트림에 자동으로 응답하는 프로세스를 만들어 장치에서 데이터 스트림을 분석하고 비즈니스 성과를 향상시키는 솔루션을 사용할 수 있습니다.

이 자습서는 미리 구성된 원격 모니터링 솔루션을 프로비전하는 방법을 보여 줍니다. 또한 미리 구성된 솔루션의 기본 기능을 안내합니다. 미리 구성된 솔루션의 일부로 배포되는 솔루션 *대시보드*에서 다수의 기능에 액세스할 수 있습니다.

![미리 구성된 원격 모니터링 솔루션 대시보드][img-dashboard]

이 자습서를 완료하려면 활성 Azure 구독이 필요합니다.

> [!NOTE]
> 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk_free_trial]을 참조하세요.

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>시나리오 개요

미리 구성된 원격 모니터링 솔루션을 배포하는 경우 일반적인 원격 모니터링 시나리오를 단계별로 실행할 수 있는 리소스가 미리 채워집니다. 이 시나리오에서는 솔루션에 연결된 여러 장치에서 예기치 않은 온도 값을 보고하고 있습니다. 다음 섹션에서는 아래 방법을 보여줍니다.

* 예기치 않은 온도 값을 보내는 장치를 식별합니다.
* 더 자세한 원격 분석을 보내도록 이러한 장치를 구성합니다.
* 이러한 장치의 펌웨어를 업데이트하여 문제를 해결합니다.
* 작업으로 인해 문제가 해결되었는지 확인합니다.

이 시나리오의 주요 특징은 솔루션 대시보드에서 이러한 모든 작업을 원격으로 수행할 수 있다는 것입니다 장치에 대한 물리적인 액세스가 필요하지 않습니다.

## <a name="view-the-solution-dashboard"></a>솔루션 대시보드 보기

솔루션 대시보드를 사용하면 배포된 솔루션을 관리할 수 있습니다. 예를 들어 원격 분석 보기, 장치 추가 및 규칙 구성 작업을 수행할 수 있습니다.

1. 프로비전이 완료되고 미리 구성된 솔루션에 대한 타일이 **준비**를 가리키면 **시작**을 선택하여 새 탭에서 원격 모니터링 솔루션 포털을 엽니다.

    ![미리 구성된 솔루션 시작][img-launch-solution]

1. 기본적으로 솔루션 포털에 *대시보드*가 표시됩니다. 페이지 왼쪽의 메뉴를 사용하여 솔루션 포털의 다른 영역으로 이동할 수 있습니다.

    ![미리 구성된 원격 모니터링 솔루션 대시보드][img-menu]

대시보드에서는 다음 정보를 표시합니다.

* 지도는 솔루션에 연결된 각 장치의 위치를 보여줍니다. 솔루션을 처음으로 실행하는 경우 25개의 시뮬레이션된 장치가 있습니다. 시뮬레이션된 장치는 Azure WebJob으로 구현되며, 솔루션은 Bing 지도 API를 사용하여 지도에 대한 정보를 구상합니다. 지도를 동적으로 만드는 방법을 알아보려면 [FAQ][lnk-faq]를 참조하세요.
* **원격 분석 기록** 패널은 선택한 장치의 습도 및 온도 원격 분석을 거의 실시간으로 나타내고 최고, 최저 및 평균 습도와 같은 집계 데이터를 표시합니다.
* 원격 분석 값이 임계값을 초과하면 **알람 기록** 패널에 최근 경보 이벤트가 표시됩니다. 미리 구성된 솔루션으로 생성된 예제 외에 자신만의 경보를 정의할 수 있습니다.
* **작업** 패널은 예약된 작업에 대한 정보를 표시합니다. **관리 작업** 페이지에서 사용자 고유의 작업을 예약할 수 있습니다.

## <a name="view-alarms"></a>알람 보기

알람 기록 패널은 5대의 장치에서 예상한 원격 분석 값보다 높은 값을 보고하는 것을 보여줍니다.

![솔루션 대시보드의 TODO 알람 기록][img-alarms]

> [!NOTE]
> 이러한 알람은 미리 구성된 솔루션에 포함된 규칙에 의해 생성됩니다. 이 규칙은 장치에서 보낸 온도 값이 60을 초과하면 알람을 생성합니다. 왼쪽 메뉴에서 [규칙](#add-a-rule) 및 [작업](#add-an-action)을 선택하여 자신만의 규칙과 작업을 정의할 수 있습니다.

## <a name="view-devices"></a>장치 보기

*장치* 목록에 솔루션에 등록된 모든 장치가 표시됩니다. 장치 목록에서 장치 메타데이터를 보고 편집하고 장치를 추가 또는 제거하고 장치의 메서드를 호출할 수 있습니다. 장치 목록에서 장치의 목록을 필터링하고 정렬할 수 있습니다. 장치 목록에 표시된 열을 사용자 지정할 수도 있습니다.

1. 이 솔루션에 대한 장치 목록을 표시하려면 **장치**를 클릭합니다.

   ![솔루션 포털에서 장치 목록 보기][img-devicelist]

1. 장치 목록에는 프로비전 프로세스에서 생성된 25개의 시뮬레이션된 장치가 처음에 표시됩니다. 솔루션에 추가 시뮬레이션된 장치 및 물리적 장치를 추가할 수 있습니다.

1. 장치의 세부 정보를 보려면 장치 목록에서 장치를 선택합니다.

   ![솔루션 포털에서 장치 세부 정보 보기][img-devicedetails]

**장치 세부 정보** 패널은 다음과 같은 여섯 가지 섹션을 포함합니다.

* 장치 아이콘을 사용자 지정, 장치를 비활성화, 규칙 추가, 메서드 호출 또는 명령을 보낼 수 있도록 하는 링크의 컬렉션입니다. 명령(장치-클라우드 메시지) 및 메서드(직접 메서드)의 비교는 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.
* **장치 쌍 - 태그** 섹션을 통해 장치에 대한 태그 값을 편집할 수 있습니다. 장치 목록에 태그 값을 표시할 수 있으며 태그 값을 사용하여 장치 목록을 필터링할 수 있습니다.
* **장치 쌍 - Desired 속성** 섹션을 통해 장치에 전송될 속성 값을 설정할 수 있습니다.
* **장치 쌍 - Reported 속성** 섹션은 장치에서 전송된 속성 값을 보여 줍니다.
* **장치 속성** 섹션은 장치 ID 및 인증 키와 같은 ID 레지스트리에서 정보를 표시합니다.
* **최근 작업** 섹션은 이 장치에 가장 최근에 지정된 모든 작업에 대한 정보를 표시합니다.

## <a name="filter-the-device-list"></a>장치 목록 필터링

필터를 사용하면 예기치 않은 온도 값을 보내는 장치만 표시할 수 있습니다. 미리 구성된 원격 모니터링 솔루션에는 평균 온도 값이 60을 초과하는 장치를 표시하는**비정상 상태의 장치** 필터가 포함되어 있습니다. [직접 필터를 만들 수](#add-a-filter)도 있습니다.

1. 사용 가능한 필터 목록을 표시하려면 **저장한 필터 열기**를 선택합니다. 그런 다음 **비정상 상태의 장치**를 선택하여 필터를 적용합니다.

    ![필터 목록 표시][img-unhealthy-filter]

1. 이제 장치 목록에 평균 온도가 60보다 높은 장치만 표시됩니다.

    ![비정상 상태의 장치를 보여주는 필터링된 장치 목록 보기][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>desired 속성 업데이트

이제 수정이 필요할 수 있는 장치 집합을 식별했습니다. 하지만 15초라는 데이터 빈도가 문제를 명확하게 진단하기에 충분하지 않다고 판단했습니다. 문제를 더 잘 진단할 수 있도록 더 많은 데이터 요소를 제공하기 위해 원격 분석 빈도를 5초로 변경합니다. 이러한 구성 변경은 솔루션 포털에서 원격 장치에 푸시할 수 있습니다. 한 번 변경하고, 영향을 평가한 다음 결과에 따라 조치를 취할 수 있습니다.

영향을 받는 장치에 대해 **TelemetryInterval** desired 속성을 변경하는 작업을 실행하려면 다음 단계를 수행하세요. 장치에서 새로운 **TelemetryInterval** 속성 값을 받으면 15초가 아닌 5초마다 원격 분석을 보내도록 구성이 변경됩니다.

1. 장치 목록에 비정상 상태의 장치 목록이 표시되는 동안 **작업 Scheduler**, **장치 편집**을 차례로 선택합니다.

1. 작업 이름을 **원격 분석 간격 변경**으로 지정합니다.

1. **원하는 속성 이름** **desired.Config.TelemetryInterval**의 값을 5초로 변경합니다.

1. **예약**을 선택합니다.

    ![TelemetryInterval 속성을 5초로 변경][img-change-interval]

1. 작업 진행률은 포털의 **관리 작업** 페이지에서 모니터링 할 수 있습니다.

> [!NOTE]
> 개별 장치에 대한 원하는 속성 값을 변경하려면 작업을 실행하는 대신 **장치 세부 정보** 패널의 **원하는 속성** 섹션을 사용하세요.

이 작업은 필터로 선택된 모든 장치에 대한 장치 쌍에서 **TelemetryInterval** desired 속성의 값을 설정합니다. 장치는 장치 쌍에서 이 값을 가져와서 장치의 동작을 업데이트합니다. 장치가 장치 쌍에서 desired 속성을 가져와서 처리할 때 해당하는 reported 값 속성이 설정됩니다.

## <a name="invoke-methods"></a>메서드 호출

작업이 실행되는 동안 비정상 상태의 장치 목록에서 모든 장치의 펌웨어 버전이 오래된 것을(버전 1.6 미만) 볼 수 있습니다.

![비정상 상태의 장치에 대한 보고된 펌웨어 버전 보기][img-old-firmware]

이 펌웨어 버전이 예기치 못한 온도 값의 근본 원인일 수 있습니다. 다른 정상 상태의 장치는 최근에 버전 2.0으로 업데이트되었기 때문입니다. 기본 제공되는 **Old firmware devices**(오래된 펌웨어 장치) 필터를 사용하여 펌웨어 버전이 오래된 장치를 식별할 수 있습니다. 그런 다음 포털에서 아직 오래된 펌웨어 버전을 실행 중인 모든 장치를 원격으로 업데이트할 수 있습니다.

1. 사용 가능한 필터 목록을 표시하려면 **저장한 필터 열기**를 선택합니다. 그 후 **Old firmware devices**(오래된 펌웨어 장치)를 선택하여 필터를 적용합니다.

    ![필터 목록 표시][img-old-filter]

1. 이제 장치 목록에 펌웨어 버전이 오래된 장치만 표시됩니다. 이 목록에는 **비정상 상태의 장치** 필터로 식별된 5대의 장치와 3대의 추가 장치가 포함됩니다.

    ![오래된 장치를 보여주는 필터링된 장치 목록 보기][img-filtered-old-list]

1. **작업 Scheduler**, **메서드 호출**을 차례로 선택합니다.

1. **작업 이름**을 **버전 2.0으로 펌웨어 업데이트**로 설정합니다.

1. **InitiateFirmwareUpdate**를 **메서드**로 선택합니다.

1. **FwPackageUri** 매개 변수를 **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**으로 설정합니다.

1. **예약**을 선택합니다. 작업에 대한 기본값은 지금 실행하는 것입니다.

    ![선택한 장치의 펌웨어를 업데이트하도록 작업 만들기][img-method-update]

> [!NOTE]
> 개별 장치에서 메서드를 호출하려면 작업을 실행하는 대신 **장치 세부 정보** 패널에서 **메서드**를 선택합니다.

이 작업은 필터로 선택된 모든 장치에서 **InitiateFirmwareUpdate** 직접 메서드를 호출합니다. 장치는 IoT Hub에 즉시 응답한 후 비동기적으로 펌웨어 업데이트 프로세스를 시작합니다. 다음 스크린샷에서 볼 수 있듯이 장치는 보고된 속성 값을 통해 펌웨어 업데이트 프로세스에 대한 상태 정보를 제공합니다. 장치 및 작업 목록의 정보를 업데이트하려면 **새로 고침** 아이콘을 선택합니다.

![실행 중인 펌웨어 업데이트 목록을 보여주는 작업 목록][img-update-1]
![펌웨어 업데이트 상태를 보여주는 장치 목록][img-update-2]
![완료된 펌웨어 업데이트 목록을 보여주는 작업 목록][img-update-3]

> [!NOTE]
> 프로덕션 환경에서 지정된 유지 관리 기간 동안 작업이 실행되도록 예약할 수 있습니다.

## <a name="scenario-review"></a>시나리오 검토

이 시나리오에서는 대시보드의 알람 기록 및 필터를 사용하여 일부 원격 장치의 잠재적인 문제를 식별했습니다. 그 후 문제 진단에 도움을 주기 위해 필터 및 작업을 사용하여 더 많은 정보를 제공하도록 원격으로 장치를 구성했습니다. 마지막으로 필터 및 작업을 사용하여 영향을 받는 장치에 대해 유지 관리를 예약했습니다. 대시보드로 돌아가면 솔루션의 장치에서 발생하는 알람이 더 이상 없는 것을 확인할 수 있습니다. 필터를 사용하면 솔루션에 포함된 모든 장치의 펌웨어가 최신인지, 비정상 상태의 장치가 더 이상 없는지 확인할 수 있습니다.

![모든 장치의 펌웨어가 최신이라는 것을 보여주는 필터][img-updated]

![모든 장치가 정상이라는 것을 보여주는 필터][img-healthy]

## <a name="other-features"></a>기타 기능

다음 섹션은 이전 시나리오의 일부로 설명하지 않은 미리 구성된 원격 모니터링 솔루션의 일부 추가 기능을 설명합니다.

### <a name="customize-columns"></a>열 사용자 지정

**열 편집기**를 선택하여 장치 목록에 표시되는 정보를 사용자 지정할 수 있습니다. reported 속성 및 태그 값을 표시하는 열을 추가 및 제거할 수 있습니다. 또한 열의 순서를 변경하고 이름을 바꿀 수도 있습니다.

   ![장치 목록의 열 편집기 아이콘][img-columneditor]

### <a name="customize-the-device-icon"></a>장치 아이콘 사용자 지정

다음과 같이 **장치 세부 정보** 패널에서 장치 목록에 표시되는 장치 아이콘을 사용자 지정할 수 있습니다.

1. 연필 아이콘을 선택하여 장치에 대한 **이미지 편집** 패널을 엽니다.

   ![장치 이미지 편집기 열기][img-startimageedit]

1. 새 이미지를 업로드하거나 기존 이미지 중 하나를 사용한 다음 **저장**을 선택합니다.

   ![장치 이미지 편집기 편집][img-imageedit]

1. 이제 선택한 이미지가 장치에 대한 **아이콘** 열에 표시됩니다.

> [!NOTE]
> 이미지는 Blob 저장소에 저장됩니다. 장치 쌍의 태그는 Blob 저장소의 이미지에 대한 링크를 포함합니다.

### <a name="add-a-device"></a>장치 추가

미리 구성된 솔루션을 배포할 때 장치 목록에서 볼 수 있는 25개의 샘플 장치를 자동으로 프로비전합니다. 이러한 장치는 Azure WebJob에서 실행되는 *시뮬레이션된 장치* 입니다. 시뮬레이션된 장치를 사용하면 실제 물리적 장치를 배포하지 않고도 미리 구성된 솔루션을 쉽게 실험할 수 있습니다. 솔루션에 실제 장치를 연결하려면 [미리 구성된 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm] 자습서를 참조하세요.

다음 단계에서는 솔루션에 시뮬레이션된 장치를 추가하는 방법을 보여 줍니다.

1. 다시 장치 목록으로 이동합니다.

1. 장치를 추가하려면 왼쪽 아래 모서리에서 **+ 장치 추가**를 선택합니다.

   ![미리 구성된 솔루션에 장치 추가][img-adddevice]

1. **시뮬레이션된 장치** 타일에서 **새로 추가**를 선택합니다.

   ![대시보드에서 새 장치 세부 정보 설정][img-addnew]

   시뮬레이션된 새 장치를 만드는 것 외에, **사용자 지정 장치**를 만들도록 선택하면 물리적 장치를 추가할 수도 있습니다. 솔루션에 물리적 장치를 연결하는 방법을 자세히 알아보려면 [미리 구성된 IoT Suite 원격 모니터링 솔루션에 장치 연결][lnk-connect-rm]을 참조하세요.

1. **나만의 장치 ID 정의**를 선택하고 **mydevice_01**처럼 고유한 장치 ID 이름을 입력합니다.

1. **만들기**를 선택합니다.

   ![새 장치 저장][img-definedevice]

1. 3단계 **시뮬레이션된 장치 추가**에서 **완료**를 선택하여 장치 목록으로 돌아갑니다.

1. 장치 목록에서 장치가 **실행 중** 인 것을 볼 수 있습니다.

    ![장치 목록에서 새 장치 보기][img-runningnew]

1. 대시보드에서 새 장치의 시뮬레이션된 원격 분석을 볼 수도 있습니다.

    ![새 장치에서 원격 분석 보기][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>장치 비활성화 및 삭제

장치를 비활성화할 수 있으며, 비활성화된 후에는 파일을 제거할 수 있습니다.

![장치 비활성화 및 제거][img-disable]

### <a name="add-a-rule"></a>규칙 추가

방금 추가한 새 장치에 대해서는 규칙이 없습니다. 이 섹션에서는 새 장치에서 보고하는 온도가 47도를 넘으면 경보를 트리거하는 규칙을 추가하게 됩니다. 시작하기 전에, 대시보드의 새 장치에 대한 원격 분석 기록은 장치 온도가 45도를 절대 넘지 않도록 표시됩니다.

1. 다시 장치 목록으로 이동합니다.

1. 장치에 대한 규칙을 추가하려면 **장치 목록**에서 새 장치를 선택한 후 **규칙 추가**를 선택합니다.

1. 온도가 47도를 넘으면 **온도**를 데이터 필드로 사용하고 **AlarmTemp**를 출력으로 사용하는 규칙을 만듭니다.

    ![장치 규칙 추가][img-adddevicerule]

1. 변경 내용을 저장하려면 **규칙 저장 및 보기**를 선택합니다.

1. 새 장치에 대한 장치 세부 정보 창에서 **명령**을 선택합니다.

    ![장치 규칙 추가][img-adddevicerule2]

1. 명령 목록에서 **ChangeSetPointTemp**를 선택한 후 **SetPointTemp**를 45로 설정합니다. 그런 다음 **명령 보내기**를 선택합니다.

    ![장치 규칙 추가][img-adddevicerule3]

1. 대시보드로 다시 돌아갑니다. 잠시 후, 새 장치에 의해 보고된 온도가 47도 임계값을 초과하면 **알람 기록** 창에서 새 항목을 확인할 수 있습니다.

    ![장치 규칙 추가][img-adddevicerule4]

1. 대시보드의 **규칙** 페이지에서 모든 규칙을 검토하고 편집할 수 있습니다.

    ![장치 규칙 나열][img-rules]

1. 대시보드 **작업** 페이지의 규칙에 대응하여 취할 수 있는 모든 작업을 검토하고 편집할 수 있습니다.

    ![장치 작업 나열][img-actions]

> [!NOTE]
> 규칙에 대한 전자 메일 메시지 또는 SMS를 보낼 수 있는 작업을 정의하거나 [Logic App][lnk-logic-apps]을 통해 LOB(기간 업무) 시스템과 통합할 수 있습니다. 자세한 내용은 [미리 구성된 Azure IoT Suite 원격 모니터링 솔루션에 Logic App 연결][lnk-logicapptutorial]을 참조하세요.

### <a name="manage-filters"></a>필터 관리

장치 목록에서 허브에 연결된 장치의 사용자 지정된 목록을 표시하는 필터를 생성, 저장 및 다시 로드할 수 있습니다. 필터를 만들려면:

1. 장치 목록 위의 필터 편집 아이콘을 선택합니다.

    ![필터 편집기 열기][img-editfiltericon]

1. **필터 편집기**에서 필드, 연산자 및 값을 추가하여 장치 목록을 필터링합니다. 여러 절을 추가하여 필터를 조정할 수 있습니다. **필터**를 선택하여 필터를 적용합니다.

    ![필터 만들기][img-filtereditor]

1. 이 예제에서는 제조업체 및 모델 번호로 목록이 필터링됩니다.

    ![필터링된 목록][img-filterelist]

1. 사용자 지정 이름으로 필터를 저장하려면 **다른 이름으로 저장** 아이콘을 선택합니다.

    ![필터 저장][img-savefilter]

1. 이전에 저장한 필터를 다시 적용하려면 **저장된 필터 열기** 아이콘을 선택합니다.

    ![필터 열기][img-openfilter]

장치 ID, 장치 상태, desired 속성, reported 속성 및 태그를 기준으로 필터를 만들 수 있습니다. **장치 세부 정보** 패널의 **태그** 섹션에서 자신만의 사용자 지정 태그를 장치에 추가하거나 작업을 실행하여 여러 장치에서 태그를 업데이트합니다.

> [!NOTE]
> **필터 편집기**에서 **고급 보기**를 사용하여 쿼리 텍스트를 직접 편집할 수 있습니다.

### <a name="commands"></a>명령

**장치 세부 정보** 패널에서 장치에 명령을 보낼 수 있습니다. 장치가 처음으로 시작되면, 지원하는 명령에 대한 정보를 솔루션에 보냅니다. 명령과 메서드의 차이점에 대한 논의는 [Azure IoT Hub 클라우드-장치 옵션][lnk-c2d-guidance]을 참조하세요.

1. 선택한 장치에 대한 **장치 세부 정보** 창에서 **명령**을 선택합니다.

   ![대시보드의 장치 명령][img-devicecommands]

1. 명령 목록에서 **PingDevice**를 선택합니다.

1. **명령 보내기**를 선택합니다.

1. 명령 기록에서 명령 상태를 볼 수 있습니다.

   ![대시보드의 명령 상태][img-pingcommand]

솔루션은 보내는 각 명령의 상태를 추적합니다. 초기에 결과는 **보류 중**입니다. 장치가 명령을 실행했다고 보고하면, 결과는 **성공**으로 설정됩니다.

## <a name="behind-the-scenes"></a>배후 상황

미리 구성된 솔루션을 배포할 때, 배포 프로세스는 사용자가 선택한 Azure 구독에 여러 리소스를 만듭니다. Azure [Portal][lnk-portal]에서 이러한 리소스를 볼 수 있습니다. 배포 프로세스는 미리 구성된 솔루션에 대해 선택한 이름을 기반으로 하는 이름으로 **리소스 그룹** 을 만듭니다.

![Azure 포털에서 미리 구성된 솔루션][img-portal]

리소스 그룹의 리소스 목록에서 각 리소스를 선택하여 설정을 볼 수 있습니다.

미리 구성된 솔루션의 소스 코드를 볼 수도 있습니다. 원격 모니터링 사전 구성 솔루션 소스 코드는 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 리포지토리에 있습니다.

* **DeviceAdministration** 폴더는 대시보드에 대한 소스 코드를 포함합니다.
* **Simulator** 폴더는 시뮬레이션된 장치에 대한 소스 코드를 포함합니다.
* **EventProcessor** 폴더는 들어오는 원격 분석을 처리하는 백 엔드 프로세스에 대한 소스 코드를 포함합니다.

완료되면 [azureiotsuite.com][lnk-azureiotsuite] 사이트의 Azure 구독에서 미리 구성된 솔루션을 삭제할 수 있습니다. 이 사이트를 사용하면 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스를 쉽게 삭제할 수 있습니다.

> [!NOTE]
> 미리 구성된 솔루션과 관련된 모든 항목을 삭제하려면 포털에서 리소스 그룹을 삭제하지 않고 [azureiotsuite.com][lnk-azureiotsuite]에서 삭제합니다.

## <a name="next-steps"></a>다음 단계

미리 구성한 작업 솔루션을 배포했으므로 다음 문서를 참고하여 IoT Suite 시작을 계속할 수 있습니다.

* [미리 구성된 원격 모니터링 솔루션 연습][lnk-rm-walkthrough]
* [미리 구성된 원격 모니터링 솔루션 연습][lnk-connect-rm]
* [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md