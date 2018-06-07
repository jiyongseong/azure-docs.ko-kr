---
title: Azure 모니터링 데이터 보관 | Microsoft Docs
description: Azure 내의 로그 및 메트릭 데이터를 저장소 계정에 보관합니다.
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.openlocfilehash: b44bbd9cb2f54107d2593b1ab7f07f07fcc41e57
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="archive-azure-monitoring-data"></a>Azure 모니터링 데이터 보관

Azure 환경의 여러 계층에서 Azure Storage 계정에 보관할 수 있는 로그 및 메트릭 데이터를 생성합니다. Log Analytics 또는 Azure Monitor에서 데이터가 보존 기간을 경과한 후에 검색이 불가능한 저렴한 상점에서 시간에 따라 데이터를 모니터링하는 기록을 유지하기 위해 이 작업을 수행하려고 할 수 있습니다. 이 자습서에서는 저장소 계정에 데이터를 보관하도록 Azure 환경을 구성하는 과정을 안내합니다.

> [!div class="checklist"]
> * 모니터링 데이터를 저장할 저장소 계정을 만듭니다.
> * 구독 로그를 해당 계정으로 라우팅합니다.
> * 리소스 데이터를 해당 계정으로 라우팅합니다.
> * 가상 머신(게스트 OS) 데이터를 해당 계정으로 라우팅합니다.
> * 모니터링 데이터를 확인합니다.
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

먼저 모니터링 데이터를 보관할 저장소 계정을 설정해야 합니다. 이렇게 하려면 [여기 있는 단계를 수행하세요.](../storage/common/storage-create-storage-account.md)

## <a name="route-subscription-logs-to-the-storage-account"></a>저장소 계정에 구독 로그 라우팅

이제 저장소 계정에 모니터링 데이터를 라우팅하도록 Azure 환경 설정을 시작할 준비가 되었습니다. 먼저 저장소 계정에 라우팅되도록 구독 수준 데이터(Azure 활동 로그에 포함됨)를 구성합니다. [**Azure 활동 로그**](monitoring-overview-activity-logs.md)는 Azure에서 구독 수준 이벤트 기록을 제공합니다. Azure Portal에서 찾아보기를 통해 만들고 업데이트한 *사람*이 누구이고 *어떤* 리소스를 *언제* 삭제했는지 확인할 수 있습니다.

1. 왼쪽 탐색 목록에 있는 **모니터** 단추를 클릭한 후 **활동 로그**에서 다음을 수행합니다.

   ![활동 로그 섹션](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. 표시된 활동 로그 섹션에서 **내보내기** 단추를 클릭합니다.

3. 표시되는 **활동 로그 내보내기** 섹션에서 **저장소 계정으로 내보내기** 상자를 선택하고 **저장소 계정 선택**을 클릭합니다.

   ![활동 로그 내보내기](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. 표시되는 섹션에서 **저장소 계정** 드롭다운을 사용하여 이전 **저장소 계정 만들기** 단계에서 만든 저장소 계정 이름을 선택한 후 **확인**을 클릭합니다.

   ![저장소 계정 선택](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. **보존 기간(일)** 슬라이더를 30으로 설정합니다. 이 슬라이더는 저장소 계정에 모니터링 데이터를 보존할 일 수를 설정합니다. Azure Monitor는 지정된 일 수보다 오래된 데이터를 자동으로 삭제합니다. 보존 기간을 0일로 설정하면 데이터를 무기한 저장합니다.

6. **저장**을 클릭하여 이 섹션을 닫습니다.

이제 구독의 모니터링 데이터가 저장소 계정으로 이동합니다.

## <a name="route-resource-data-to-the-storage-account"></a>저장소 계정에 리소스 데이터 라우팅

이제 **리소스 진단 설정**을 설정하여 저장소 계정에 라우팅되도록 리소스 수준 데이터(리소스 메트릭 및 진단 로그)를 구성합니다.

1. 왼쪽 탐색 목록에 있는 **모니터** 단추를 클릭한 후 **진단 설정**에서 다음을 수행합니다. 여기에 Azure Monitor를 통해 모니터링 데이터를 생성하는 구독에 있는 모든 리소스 목록이 표시됩니다. 이 목록에 리소스가 없는 경우 계속 진행하기 전에 [논리 앱을 만들어](../logic-apps/quickstart-create-first-logic-app-workflow.md) 진단 설정을 구성할 수 있는 리소스를 포함할 수 있습니다.

2. 목록에서 리소스를 클릭한 다음 **진단 켜기**를 클릭합니다.

   ![진단 켜기](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   이미 구성된 설정이 있는 경우 대신 기존 설정과 **진단 설정 추가** 단추를 확인합니다. 이 단추를 클릭합니다.

   리소스 진단 설정은 특정 리소스에서 *어떤* 모니터링 데이터를 라우팅할지, 모니터링 데이터를 *어디로* 이동할지를 정의한 것입니다.

3. 표시된 섹션에서 설정에 **이름**을 지정하고 **저장소 계정에 보관** 상자를 선택합니다.

   ![진단 설정 섹션](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. **저장소 계정에 보관** 아래 **구성** 단추를 클릭하고 이전 섹션에서 만든 저장소 계정을 선택합니다. **확인**을 클릭합니다.

   ![저장소 계정 진단 설정](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. **로그** 및 **메트릭** 아래의 상자를 모두 선택합니다. 리소스 종류에 따라 이러한 옵션 중 하나를 포함할 수 있습니다. 이 확인란에 따라 선택한 대상(이 경우, 저장소 계정)으로 보내지는 해당 리소스 종류에서 사용할 수 있는 로그 및 메트릭 데이터의 범주가 달라집니다.

   ![진단 설정 범주](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)

6. **보존 기간(일)** 슬라이더를 30으로 설정합니다. 이 슬라이더는 저장소 계정에 모니터링 데이터를 보존할 일 수를 설정합니다. Azure Monitor는 지정된 일 수보다 오래된 데이터를 자동으로 삭제합니다. 보존 기간을 0일로 설정하면 데이터를 무기한 저장합니다.

7. **저장**을 클릭합니다.

이제 리소스의 모니터링 데이터가 저장소 계정으로 이동합니다.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: 이벤트 허브의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 이벤트 허브의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>저장소 계정에 가상 머신(게스트 OS) 데이터 라우팅

1. 구독에 가상 머신이 아직 없는 경우 [가상 머신을 만듭니다](../virtual-machines/windows/quick-create-portal.md).

2. 포털의 왼쪽 탐색 목록에서 **Virtual Machines**를 클릭합니다.

3. 표시된 가상 머신 목록에서 만든 가상 머신을 클릭합니다.

4. 표시되는 섹션에서 왼쪽 탐색에서 **진단 설정**을 클릭합니다. 이 섹션에서는 가상 머신에서 Azure Monitor의 기본 모니터링 확장을 설정하고 Windows 또는 Linux에서 생성되는 데이터를 저장소 계정으로 라우팅할 수 있습니다.

   ![진단 설정으로 이동](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. 표시되는 섹션에서 **게스트 수준 모니터링을 사용하도록 설정**을 클릭합니다.

   ![진단 설정을 사용하도록 설정](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. 진단 설정이 올바르게 저장되었으면 **개요** 탭에 수집되는 데이터 목록과 데이터가 저장된 위치가 표시됩니다. **성능 카운터** 섹션을 클릭하여 수집되는 Windows 성능 카운터 집합을 검토합니다.

   ![성능 카운터 설정](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)

7. **로그** 탭을 클릭하고 응용 프로그램 및 시스템 로그에서 **정보** 수준 로그에 대한 확인란을 선택합니다.

   ![로그 설정](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. **에이전트** 탭을 클릭하고 **저장소 계정** 아래에서 표시된 저장소 계정 이름을 클릭합니다.

   ![저장소 계정 업데이트](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. 표시되는 섹션에서 이전 **저장소 계정 만들기** 단계에서 만든 저장소 계정을 선택합니다.

10. **저장**을 클릭합니다.

이제 가상 머신의 모니터링 데이터가 저장소 계정으로 이동합니다.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>저장소 계정에서 모니터링 데이터 보기

이전 단계를 수행했다면 데이터가 저장소 계정으로 이동하기 시작했습니다.

1. 활동 로그와 같은 일부 데이터 형식의 경우, 저장소 계정에 이벤트를 생성하는 활동이 있어야 합니다. 활동 로그에 활동을 생성하려면 [다음 지침](./monitor-quick-audit-notify-action-in-subscription.md)을 따르세요. 저장소 계정에 이벤트가 표시되려면 최대 5분 동안 기다려야 할 수 있습니다.

2. 포털의 왼쪽 탐색 모음에서 찾아 **저장소 계정** 섹션으로 이동합니다.

3. 이전 섹션에서 만든 저장소 계정을 식별하고 클릭합니다.

4. **Blob**을 클릭한 후 **insights-operational-logs**라는 레이블의 컨테이너와 **name=default**라는 레이블의 컨테이너를 차례로 클릭합니다. 안에 활동 로그가 있는 컨테이너입니다. 모니터링 데이터는 리소스 ID(활동 로그의 구독 ID만)별로 컨테이너로 분류된 후 날짜 및 시간별로 분류됩니다. 이러한 Blob에 대한 전체 형식은 다음과 같습니다.

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

5. 리소스 ID, 날짜 및 시간에 해당하는 컨테이너를 클릭하여 PT1H.json 파일로 이동합니다. PT1H.json 파일을 클릭하고 **다운로드**를 클릭합니다. 각 PT1H.json Blob은 Blob URL에 지정된 시간 내에서 발생한 이벤트의 JSON Blob을 포함합니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 로그 이벤트는 시간당 개별 Blob으로 나뉘므로 분 값(m=00)은 항상 00입니다.

   이제 저장소 계정에 저장된 JSON 이벤트를 볼 수 있습니다. 리소스 진단 로그에서 Blob에 대한 형식은 다음과 같습니다.

   insights-logs-{log category name}/resourceId=/{resource ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json

6. 게스트 OS 모니터링 데이터는 테이블에 저장됩니다. 저장소 계정 홈으로 돌아가 **테이블**을 클릭합니다. 메트릭, 성능 카운터 및 이벤트 로그에 대한 테이블이 있습니다.

이제 저장소 계정에 보관할 모니터링 데이터를 성공적으로 설정했습니다.

## <a name="clean-up-resources"></a>리소스 정리

1. 이전 **저장소 계정에 구독 로그 라우팅** 단계에서 **활동 로그 내보내기** 섹션으로 돌아가 **다시 설정**을 클릭합니다.

2. **진단 설정** 섹션으로 이동하여 이전 **저장소 계정에 리소스 데이터 라우팅** 단계에서 진단 설정을 만든 리소스를 클릭한 후 만든 설정을 찾고 **설정 편집** 단추를 클릭하고 **삭제**를 클릭합니다.

3. 이전 **저장소 계정에 가상 머신(게스트 OS) 데이터 라우팅** 단계에서 구성한 가상 머신에서 **진단 설정** 섹션으로 이동하고 **에이전트** 탭 아래에서 **제거**(**Azure 진단 에이전트 제거** 섹션 아래)를 클릭합니다.

4. 이전 **저장소 계정 만들기** 단계에서 만든 저장소 계정으로 이동하여 **저장소 계정 삭제**를 클릭합니다. 저장소 계정의 이름을 입력한 다음 **삭제**를 클릭합니다.

5. 이전 단계에서 가상 머신 또는 논리 앱을 만든 경우 해당 항목도 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 저장소 계정에 보관할 Azure 환경(구독, 리소스 및 게스트 OS)에서 모니터링 데이터를 설정하는 방법을 배웠습니다.


> [!div class="checklist"]
> * 모니터링 데이터를 저장할 저장소 계정을 만듭니다.
> * 구독 로그를 해당 계정으로 라우팅합니다.
> * 리소스 데이터를 해당 계정으로 라우팅합니다.
> * 가상 머신(게스트 OS) 데이터를 해당 계정으로 라우팅합니다.
> * 모니터링 데이터를 확인합니다.
> * 리소스 정리

데이터를 좀 더 잘 활용하고 추가 정보를 얻기 위해 Log Analytics로 데이터를 보낼 수도 있습니다.

> [!div class="nextstepaction"]
> [Log Analytics 시작](../log-analytics/log-analytics-get-started.md)
