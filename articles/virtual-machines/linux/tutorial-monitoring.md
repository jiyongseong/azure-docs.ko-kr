---
title: 자습서 - Azure에서 Linux 가상 머신 모니터링 및 업데이트 | Microsoft Docs
description: 이 자습서에서는 Linux 가상 머신에 대한 부팅 진단과 성능 메트릭을 모니터링하고 패키지 업데이트를 관리하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0dc403d92855902daef09c91a5dd022beb23fd71
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>자습서: Azure에서 Linux 가상 머신 모니터링 및 업데이트

Azure에서 VM(가상 머신)이 올바르게 실행되도록 부팅 진단 및 성능 메트릭을 검토하고 패키지 업데이트를 관리할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * VM에서 진단 확장을 사용하도록 설정
> * VM 메트릭 보기
> * 진단 메트릭을 기반으로 하는 알림 만들기
> * 패키지 업데이트 관리
> * 변경 내용 및 인벤토리 모니터링
> * 고급 모니터링 설정

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-vm"></a>VM 만들기

작동 중인 진단과 메트릭을 확인하려면 VM이 필요합니다. 먼저 [az group create](/cli/azure/group#az_group_create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupMonitor*라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

이제 [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create)로 VM을 만듭니다. 다음 예제에서는 *myVM*이라는 VM을 만듭니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>부팅 진단을 사용하도록 설정

Linux VM이 부팅할 때 부팅 진단 확장에서는 부팅 출력을 캡처하여 Azure 저장소에 저장합니다. 이 데이터는 VM 부팅 문제를 해결하는 데 사용할 수 있습니다. Azure CLI를 사용하여 Linux VM을 만들 때 부팅 진단을 사용하도록 자동으로 설정되지 않습니다.

부팅 진단을 사용하도록 설정하기 전에 먼저 부팅 로그를 저장할 저장소 계정을 만들어야 합니다. Storage 계정은 전역적으로 고유한 이름을 가져야 하며, 3-24자 사이의 숫자와 소문자만 포함해야 합니다. [az storage account create](/cli/azure/storage/account#az_storage_account_create)를 사용하여 저장소 계정을 만듭니다. 이 예제에서는 임의의 문자열을 사용하여 고유한 저장소 계정 이름을 만듭니다.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

부팅 진단을 사용하도록 설정하는 경우 Blob 저장소 컨테이너에 대한 URI가 필요합니다. 다음 명령은 저장소 계정을 쿼리하여 이 URI를 반환합니다. URI 값은 *bloburi* 변수 이름에 저장되며 다음 단계에서 사용됩니다.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

이제 [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable)을 사용하여 부팅 진단을 사용하도록 설정합니다. `--storage` 값은 이전 단계에서 수집된 Blob URI입니다.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>부트 진단 보기

부팅 진단을 사용하도록 설정되면, VM을 중지하고 시작할 때마다 부팅 프로세스에 대한 정보가 로그 파일에 기록됩니다. 이 예제에서는 먼저 다음과 같이 [az vm deallocate](/cli/azure/vm#az_vm_deallocate) 명령을 사용하여 VM의 할당을 취소합니다.

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

이제 다음과 같이 [az vm start]( /cli/azure/vm#az_vm_stop) 명령을 사용하여 VM을 시작합니다.

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

다음과 같이 [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) 명령을 사용하여 *myVM*에 대한 부팅 진단 데이터를 얻을 수 있습니다.

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>호스트 메트릭 보기

Linux VM에는 Azure에서 상호 작용하는 전용 호스트가 있습니다. 이 호스트에 대한 메트릭이 자동으로 수집되며, 다음과 같이 Azure Portal에서 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroupMonitor**를 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
1. 호스트 VM의 성능을 보려면 VM 블레이드에서 **메트릭**을 클릭한 다음 **사용 가능한 메트릭**에서 *[호스트]* 메트릭 중 하나를 선택합니다.

    ![호스트 메트릭 보기](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>진단 확장 설치

> [!IMPORTANT]
> 이 문서에서는 사용되지 않는 Linux 진단 확장 2.3 버전에 대해 설명합니다. 버전 2.3은 2018년 6월 30일까지 지원됩니다.
>
> 대신 Linux 진단 확장 3.0 버전을 사용할 수 있습니다. 자세한 내용은 [설명서](./diagnostic-extension.md)를 참조하세요.

기본 호스트 메트릭을 사용할 수 있지만, 더 세분화된 VM 관련 메트릭을 보려면 VM에 Azure 진단 확장을 설치해야 합니다. Azure 진단 확장을 사용하면 VM에서 추가 모니터링 및 진단 데이터를 검색할 수 있습니다. 이러한 성능 메트릭을 보고 VM 성능에 따라 경고를 만들 수 있습니다. 진단 확장은 다음과 같이 Azure Portal을 통해 설치됩니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
1. **진단 설정**을 클릭합니다. 목록에서는 *부트 진단*이 이전 섹션에서 이미 사용하도록 설정되었음을 보여 줍니다. *기본 메트릭*에 대한 확인란을 클릭합니다.
1. *Storage 계정* 섹션에서 이전 섹션에서 만든 *mydiagdata[1234]* 계정을 찾아 선택합니다.
1. **저장** 단추를 클릭합니다.

    ![진단 메트릭 보기](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>VM 메트릭 보기

호스트 VM 메트릭을 본 것과 동일한 방법으로 VM 메트릭을 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
1. VM의 성능을 보려면 VM 블레이드에서 **메트릭**을 클릭한 다음 **사용 가능한 메트릭**에서 진단 메트릭 중 하나를 선택합니다.

    ![VM 메트릭 보기](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>경고 만들기

특정 성능 메트릭을 기반으로 하는 경고를 만들 수 있습니다. 예를 들어 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지면 경고를 사용하여 사용자에게 알릴 수 있습니다. 경고는 Azure Portal에 표시되거나 전자 메일을 통해 보낼 수 있습니다. 또한 생성되는 경고에 대한 응답으로 Azure Automation Runbook 또는 Azure Logic Apps를 트리거할 수도 있습니다.

다음 예제에서는 평균 CPU 사용량에 대한 경고를 만듭니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroup**을 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.
2. VM 블레이드에서 **경고 규칙**을 클릭한 다음, 경고 블레이드 위쪽에 있는 **메트릭 경고 추가**를 클릭합니다.
3. *myAlertRule*과 같이 경고에 대한 **이름**을 입력합니다.
4. CPU 사용률이 5분 동안 1.0을 초과할 때 경고를 트리거하려면 다른 모든 기본값을 선택한 상태로 둡니다.
5. 필요한 경우 전자 메일 알림을 보내도록 *전자 메일 소유자, 참가자 및 읽기 권한자*의 확인란을 선택합니다. 기본 작업은 포털에서 알림을 제공하는 것입니다.
6. **확인** 단추를 클릭합니다.

## <a name="manage-package-updates"></a>패키지 업데이트 관리

업데이트 관리를 사용하면 Azure Linux VM에 대한 업데이트 및 패치를 관리할 수 있습니다.
VM에서 직접, 사용 가능한 업데이트의 상태를 빠르게 평가하고, 필수 업데이트의 설치를 예약하고, 배포 결과를 검토하여 업데이트가 VM에 성공적으로 적용되었는지 확인할 수 있습니다.

가격 책정 정보에 대해서는 [업데이트 관리를 위한 Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

### <a name="enable-update-management"></a>업데이트 관리 사용

VM에 대한 업데이트 관리를 사용합니다.

1. 화면 왼쪽에서 **가상 머신**를 선택합니다.
2. 목록에서 VM을 선택합니다.
3. VM 화면의 **작업** 섹션에서 **업데이트 관리**를 클릭합니다. **업데이트 관리 사용** 화면이 열립니다.

이 VM에 대해 업데이트 관리가 사용되도록 설정되어 있는지를 확인하기 위해 유효성 검사가 수행됩니다.
유효성 검사 중에는 Log Analytics 작업 영역 및 연결된 Automation 계정이 확인되고 솔루션이 작업 영역에 있는지 여부가 확인됩니다.

[Log Analytics](../../log-analytics/log-analytics-overview.md) 작업 영역은 기능 및 서비스(예: 업데이트 관리)에서 생성된 데이터를 수집하는 데 사용됩니다.
이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.
업데이트가 필요한 VM에서 추가 작업을 수행하려면 Azure Automation에서 VM에 대해 Runbook(예: 업데이트 다운로드 및 적용)을 실행할 수 있습니다.

또한 유효성 검사 프로세스는 VM이 MMA 및 Automation 하이브리드 Runbook 작업자를 통해 프로비전되는지 확인합니다.
이 에이전트는 VM과 통신하고 업데이트 상태에 대한 정보를 얻습니다.

Log Analytics 작업 영역 및 Automation 계정을 선택하고, **사용**을 클릭하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

등록 중에 다음 필수 구성 요소 중 하나가 누락된 것으로 확인되면 자동으로 추가됩니다.

* [Log Analytics](../../log-analytics/log-analytics-overview.md) 작업 영역
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md)가 VM에서 사용되도록 설정됩니다.

**업데이트 관리** 화면이 열립니다. 사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용**을 클릭합니다. 필드가 회색으로 표시되면 해당 VM에 대해 다른 자동화 솔루션을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 함을 의미합니다.

![업데이트 관리 솔루션 사용](./media/tutorial-monitoring/manage-updates-update-enable.png)

솔루션을 사용하도록 설정하는 데 최대 15분이 걸릴 수 있습니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다. 솔루션이 사용되도록 설정되면 VM의 누락된 업데이트에 대한 정보가 Log Analytics로 이동됩니다. 이 데이터를 분석에 사용할 수 있게 되는 데 30분에서 6시간까지 걸릴 수 있습니다.

### <a name="view-update-assessment"></a>업데이트 평가 보기

**업데이트 관리**를 사용하도록 설정하면 **업데이트 관리** 화면이 나타납니다. 업데이트 평가가 완료되면 **누락 업데이트** 탭에 누락된 업데이트 목록이 표시됩니다.

 ![업데이트 상태 보기](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

**업데이트 관리** 화면 맨 위에서 **업데이트 배포 예약**을 클릭하여 VM에 대한 새 업데이트 배포를 예약합니다. **새 배포 업데이트** 화면에서 다음 정보를 지정합니다.

* **이름** - 업데이트 배포를 식별하는 고유 이름을 제공합니다.
* **업데이트 분류** - 배포에 포함되는 업데이트 배포 소프트웨어의 종류를 선택합니다. 분류 형식은 다음과 같습니다.
  * 중요 업데이트 및 보안 업데이트
  * 다른 업데이트
* **제외할 업데이트** - 업데이트 배포 시 건너뛰어야 하는 패키지 이름 목록을 제공할 수 있습니다. 패키지 이름은 와일드 카드를 지원합니다(예: \*커널\*).

  ![업데이트 일정 설정 화면](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* **일정 설정** - 현재 시간부터 30분 이후에 해당하는 기본 날짜 및 시간을 그대로 적용하거나 다른 시간을 지정할 수 있습니다.
  배포가 한 번만 수행될지 여부를 지정하거나 되풀이 일정을 설정할 수도 있습니다. 되풀이 일정을 설정하려면 되풀이에서 되풀이 옵션을 클릭합니다.

  ![업데이트 일정 설정 화면](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **유지 관리 기간(분)** - 업데이트 배포가 수행될 기간을 지정합니다. 이 기간을 지정하면 정해진 서비스 기간 내에 변경이 수행됩니다.

일정 구성을 완료한 후 **만들기** 단추를 클릭하여 상태 대시보드로 돌아갑니다.
**예약됨** 표에는 만든 배포 일정이 표시됩니다.

> [!WARNING]
> 재부팅이 필요한 업데이트의 경우 VM은 자동으로 다시 시작됩니다.

### <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작되면 **업데이트 관리** 화면의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 볼 수 있습니다.
현재 실행 중인 경우 상태가 **진행 중**으로 표시됩니다. 성공적으로 완료되면 **성공**으로 바뀝니다.
배포에서 하나 이상의 업데이트가 실패하면 상태는 **부분적으로 실패**로 나타납니다.
완료된 배포 업데이트를 클릭하여 해당 업데이트 배포에 대한 대시보드를 확인합니다.

![특정 배포에 대한 업데이트 배포 상태 대시보드](./media/tutorial-monitoring/manage-updates-view-results.png)

**업데이트 결과** 타일에는 VM의 총 업데이트 수 및 배포 결과 요약이 표시됩니다.
오른쪽의 표에는 각 업데이트에 대한 자세한 분석과 다음 값 중 하나로 표시되는 설치 결과가 제공됩니다.

* **시도 안 함** - 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* **성공** - 업데이트했습니다.
* **실패** -업데이트하지 못했습니다.

배포를 통해 생성된 항목에 대한 모든 로그를 보려면 **모든 로그**를 클릭합니다.

**출력** 타일을 클릭하여 대상 VM의 업데이트 배포를 관리하는 runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 클릭합니다.

## <a name="monitor-changes-and-inventory"></a>변경 내용 및 인벤토리 모니터링

컴퓨터의 소프트웨어, 파일, Linux 디먼, Windows 서비스 및 Windows 레지스트리 키에 대한 인벤토리를 수집하고 볼 수 있습니다. 컴퓨터 구성을 추적하면 환경 전체에서 작동 문제를 정확히 식별하고 컴퓨터의 상태를 더 잘 파악할 수 있습니다.

### <a name="enable-change-and-inventory-management"></a>변경 내용 및 재고 관리 사용

VM에 대해 변경 내용 및 인벤토리 관리 사용:

1. 화면 왼쪽에서 **가상 머신**를 선택합니다.
2. 목록에서 VM을 선택합니다.
3. VM 화면의 **작업** 섹션에서 **인벤토리** 또는 **변경 내용 추적**을 클릭합니다. **변경 내용 추적 및 인벤토리 사용** 화면이 열립니다.

사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 구성하고 **사용**을 클릭합니다. 필드가 회색으로 표시되면 해당 VM에 대해 다른 자동화 솔루션을 사용하도록 설정하고 동일한 작업 영역과 Automation 계정을 사용해야 함을 의미합니다. 메뉴에서 솔루션이 분리되어 있어도 동일한 솔루션입니다. 한 솔루션을 사용하도록 설정하면 VM에서 둘 다 사용할 수 있습니다.

![변경 내용 추적 및 인벤토리 사용](./media/tutorial-monitoring/manage-inventory-enable.png)

솔루션을 사용하도록 설정한 후 데이터가 표시되기까지 VM에서 인벤토리가 수집되는 동안 시간이 걸릴 수 있습니다.

### <a name="track-changes"></a>변경 내용 추적

VM에서 **작업** 아래에 있는 **변경 내용 추적**을 선택합니다. **설정 편집**을 클릭하면 **변경 내용 추적** 페이지가 표시됩니다. 추적할 설정 유형을 선택한 다음, **+ 추가**를 클릭하여 설정을 구성합니다. 사용 가능한 Linux 옵션은 **Linux 파일**입니다.

변경 내용 추적에 대한 자세한 내용은 [VM에서 변경 내용 문제 해결](../../automation/automation-tutorial-troubleshoot-changes.md)을 참조하세요.

### <a name="view-inventory"></a>인벤토리 보기

VM에서 **작업** 아래에 있는 **인벤토리**를 선택합니다. **소프트웨어** 탭에는 검색된 소프트웨어가 나열되는 테이블 목록이 있습니다. 각 소프트웨어 레코드에 대한 높은 수준의 자세한 정보는 이 테이블에서 볼 수 있습니다. 이러한 세부 정보에는 소프트웨어 이름, 버전, 게시자, 마지막으로 새로 고친 시간이 포함됩니다.

![인벤토리 보기](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>활동 로그 및 변경 내용 모니터링

VM에 대한 **변경 내용 추적** 페이지에서 **활동 로그 연결 관리**를 선택합니다. 이 작업은 **Azure 활동 로그** 페이지를 엽니다. **연결**을 선택하여 변경 내용 추적을 VM에 대한 Azure 활동 로그에 연결합니다.

이 설정을 사용하도록 설정하면 VM에 대한 **개요** 페이지로 이동하여 VM을 중지하기 위해 **중지**를 선택합니다. 메시지가 표시되면 **예**를 선택하여 VM을 중지합니다. 할당이 취소되면 **시작**을 선택하여 VM을 다시 시작합니다.

VM을 중지하고 시작하면 활동 로그에 이벤트가 기록됩니다. **변경 내용 추적** 페이지로 다시 이동합니다. 페이지 아래쪽의 **이벤트** 탭을 선택합니다. 잠시 후에 이벤트가 차트와 테이블에 표시됩니다. 각 이벤트를 선택하여 해당 이벤트에 대한 자세한 정보를 볼 수 있습니다.

![활동 로그의 변경 내용 보기](./media/tutorial-monitoring/manage-activitylog-view-results.png)

차트에는 시간이 지남에 따라 발생한 변경 내용이 표시됩니다. 활동 로그 연결을 추가하면 위쪽의 선 그래프에 Azure 활동 로그 이벤트가 표시됩니다. 막대 그래프의 각 행은 서로 다른 추적 가능한 변경 유형을 나타냅니다. 이러한 유형은 Linux 디먼, 파일 및 소프트웨어입니다. 변경 탭에는 시각화에 표시된 변경 내용에 대한 세부 정보가 변경이 발생한 시간의 내림차순(가장 최근이 첫 번째)으로 표시됩니다.

## <a name="advanced-monitoring"></a>고급 모니터링

[Azure Automation](../../automation/automation-intro.md)에서 제공하는 업데이트 관리, 변경 내용 및 인벤토리 등의 솔루션을 사용하여 VM의 고급 모니터링을 수행할 수 있습니다.

Log Analytics 작업 영역에 액세스할 수 있는 경우, **설정**에서 **고급 설정**을 선택하여 작업 영역 키와 작업 영역 식별자를 찾을 수 있습니다. \<workspace-key\> 및 **workspace-id**를 Log Analytics 작업 영역의 값으로 바꾼 다음, \<az vm extension set\>을 사용하여 VM에 확장을 추가할 수 있습니다.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

몇 분 후 새 VM이 Log Analytics 작업 영역에 표시됩니다.

![OMS 블레이드](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM에 대한 업데이트를 구성, 검토 및 관리했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * VM에서 진단 확장을 사용하도록 설정
> * VM 메트릭 보기
> * 진단 메트릭을 기반으로 하는 알림 만들기
> * 패키지 업데이트 관리
> * 변경 내용 및 인벤토리 모니터링
> * 고급 모니터링 설정

Azure Security Center에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VM 보안 관리](./tutorial-azure-security.md)
