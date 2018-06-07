---
title: 여러 Azure 가상 머신에 대한 업데이트 관리
description: 이 항목에서는 Azure 가상 머신에 대한 업데이트를 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.component: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1f34255bdbcc8761f1c68adbb2f1828521f789e4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194022"
---
# <a name="manage-updates-for-multiple-machines"></a>여러 컴퓨터의 업데이트 관리

업데이트 관리를 사용하여 Windows 및 Linux 가상 머신의 업데이트 및 패치를 관리할 수 있습니다. [Azure Automation](automation-offering-get-started.md) 계정에서 다음을 수행할 수 있습니다.

- 가상 머신을 등록합니다.
- 사용 가능한 업데이트의 상태를 평가합니다.
- 필수 업데이트의 설치를 예약합니다.
- 배포 결과를 검토하여 업데이트 관리를 사용하는 모든 가상 머신에 업데이트가 성공적으로 적용되었는지 확인합니다.

## <a name="prerequisites"></a>필수 조건

업데이트 관리를 사용하려면 다음이 필요합니다.

- Azure Automation 실행 계정 계정을 만드는 방법에 대한 지침은 [Azure Automation 시작](automation-offering-get-started.md)을 참조하세요.

- 지원되는 운영 체제 중 하나가 설치된 가상 머신 또는 컴퓨터

## <a name="supported-operating-systems"></a>지원되는 운영 체제

업데이트 관리를 지원하는 운영 체제는 다음과 같습니다.

### <a name="windows"></a>Windows

- Windows Server 2008 이상 및 Windows Server 2008 R2 SP1 이상에 대한 업데이트 배포 Nano 서버는 지원되지 않습니다.

  Windows Server 2008 R2 SP1에 대한 업데이트 배포를 지원하려면 .NET Framework 4.5 및 Windows Management Framework 5.0 이상이 필요합니다.

- Windows 클라이언트 운영 체제는 지원되지 않습니다.

Windows 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성되거나 Microsoft Update에 대한 액세스 권한이 있어야 합니다.

> [!NOTE]
> System Center Configuration Manager는 동시에 Windows 에이전트를 관리할 수 없습니다.
>

### <a name="linux"></a>Linux

- CentOS 6(x86/x64) 및 7(x64)

- Red Hat Enterprise 6(x86/x64) 및 7(x64)

- SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)

- Ubuntu 12.04 LTS 이상(x86/x64)

> [!NOTE]
> Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 방지하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 자세한 내용은 [Ubuntu Server의 자동 업데이트 항목 가이드](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)를 참조하세요.

Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.

이 솔루션은 여러 Log Analytics 작업 영역에 보고하도록 구성된 Linux용 OMS 에이전트는 지원하지 않습니다.

## <a name="enable-update-management-for-azure-virtual-machines"></a>Azure 가상 머신에 대한 업데이트 관리 사용

Azure Portal에서 Automation 계정을 열고 **업데이트 관리**를 선택합니다.

창 맨 위에서 **Azure VM 추가**를 선택합니다.

![Azure VM 추가 탭](./media/manage-update-multi/update-onboard-vm.png)

등록할 가상 머신을 선택합니다. **업데이트 관리 사용** 대화 상자가 나타납니다. **사용**을 선택하여 가상 머을신 등록합니다. 온보딩이 완료되면 가상 머신에 대한 업데이트 관리를 사용합니다.

![업데이트 관리 사용 대화 상자](./media/manage-update-multi/update-enable.png)

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>비Azure 가상 머신 및 컴퓨터에 대한 업데이트 관리 사용

비Azure Windows 가상 머신 및 컴퓨터에 대한 업데이트 관리를 사용하도록 설정하는 방법에 대한 지침은 [Azure에서 Log Analytics 서비스에 Windows 컴퓨터 연결](../log-analytics/log-analytics-windows-agent.md)을 참조하세요.

비Azure Linux 가상 머신 및 컴퓨터에 대한 업데이트 관리를 사용하도록 설정하는 방법에 대한 지침은 [Log Analytics에 Linux 머신 연결](../log-analytics/log-analytics-agent-linux.md)을 참조하세요.

## <a name="view-computers-attached-to-your-automation-account"></a>자동화 계정에 연결된 컴퓨터 보기

컴퓨터에 대한 업데이트 관리를 설정하면 **컴퓨터**를 클릭하여 해당 정보를 볼 수 있습니다. *이름*, *준수*, *환경*, *OS 형식*, *중요 및 보안 업데이트* , *다른 업데이트* 및 *업데이트 에이전트 준비*와 같은 컴퓨터 정보를 사용할 수 있습니다.

  ![컴퓨터 보기 탭](./media/manage-update-multi/update-computers-tab.png)

최근에 업데이트 관리가 설정된 컴퓨터의 경우 아직 평가를 받지 않았을 수도 있습니다. 이러한 컴퓨터의 규정 준수 상태는 *평가되지 않음*입니다.  다음은 규정 준수 상태 값의 목록입니다.

- 준수 - 중요 또는 보안 업데이트가 누락되지 않은 컴퓨터.

- 비호환 - 중요 또는 보안 업데이트가 하나 이상 누락된 컴퓨터.

- 평가되지 않음 - 예상 기간 내에 컴퓨터의 업데이트 평가 데이터를 받지 못했습니다.  Linux 컴퓨터의 경우 최근 3시간, Windows 컴퓨터의 경우 최근 12시간입니다.

에이전트의 상태를 보려면 **업데이트 에이전트 준비** 열에서 링크를 클릭합니다. 그러면 Hybrid Worker의 상태를 보여주는 Hybrid Worker 페이지가 열립니다. 다음 그림에서는 확대된 시간 동안 업데이트 관리에 연결되지 않은 에이전트의 예제를 보여줍니다.

![컴퓨터 보기 탭](./media/manage-update-multi/update-agent-broken.png)

## <a name="view-an-update-assessment"></a>업데이트 평가 보기

업데이트 관리를 사용하도록 설정하면 **업데이트 관리** 대화 상자가 나타납니다. **누락된 업데이트** 탭에서 누락된 업데이트 목록을 볼 수 있습니다.

## <a name="collect-data"></a>데이터 수집

가상 머신 및 컴퓨터에 설치된 에이전트에서 업데이트에 대한 데이터를 수집하여 Azure 업데이트 관리로 보냅니다.

### <a name="supported-agents"></a>지원되는 에이전트

다음 표에서는 솔루션이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |업데이트 관리에서 Windows 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 필요한 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |업데이트 관리에서 Linux 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필요한 업데이트를 설치하기 시작합니다. |
| Operations Manager 관리 그룹 |예 |업데이트 관리에서 연결된 관리 그룹의 에이전트로부터 시스템 업데이트에 대한 정보를 수집합니다. |
| Azure Storage 계정 |아니오 |Azure Storage는 시스템 업데이트에 대한 정보를 포함하지 않습니다. |

### <a name="collection-frequency"></a>수집 빈도

관리되는 Windows 머신 각각의 경우 검사는 하루에 두 번 실행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하고 상태가 변경되었는지 확인합니다. 그렇다면 규정 준수 검사를 시작합니다. 관리되는 Linux 머신 각각의 경우 검사는 세 시간마다 실행됩니다.

관리되는 컴퓨터의 업데이트 데이터를 대시보드에 표시하는 데 30분에서 6시간이 걸릴 수 있습니다.

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트를 설치하려면 릴리스 일정 및 서비스 기간 이후로 배포를 예약합니다.
배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

**업데이트 관리** 대화 상자의 맨 위에서 **업데이트 배포 예약**을 선택하여 하나 이상의 가상 머신에 대한 새 업데이트 배포를 예약합니다.
**새 배포 업데이트** 창에서 다음을 지정합니다.

- **이름**: 업데이트 배포를 식별하는 고유 이름을 제공합니다.
- **OS 형식**: Windows 또는 Linux를 선택합니다.
- **업데이트할 컴퓨터**: 업데이트하려는 가상 머신을 선택합니다. 컴퓨터의 준비는 **업데이트 에이전트 준비** 열에 표시됩니다. 이를 통해 업데이트 배포를 예약하기 전에 컴퓨터의 상태를 확인할 수 있습니다.

  !["새 업데이트 배포" 창](./media/manage-update-multi/update-select-computers.png)

- **업데이트 분류**: 업데이트 배포가 포함되는 소프트웨어의 종류를 선택합니다. 분류 형식에 대한 설명은 [업데이트 분류](automation-update-management.md#update-classifications)를 참조하세요. 분류 형식은 다음과 같습니다.
  - 중요 업데이트
  - 보안 업데이트
  - 업데이트 롤업
  - 기능 팩
  - 서비스 팩
  - 정의 업데이트
  - 도구
  - 업데이트
- **일정 설정**: 현재 시간부터 30분 이후인 기본 날짜 및 시간을 그대로 적용할 수 있습니다. 또는 다른 시간을 지정할 수 있습니다.
   배포가 한 번만 수행될지 아니면 되풀이 일정으로 수행될지를 지정할 수도 있습니다. 되풀이 일정을 설정하려면 **되풀이**에서 **되풀이** 옵션을 선택합니다.

   ![일정 설정 대화 상자](./media/manage-update-multi/update-set-schedule.png)

- **유지 관리 기간(분)**: 업데이트 배포를 수행할 기간을 지정합니다. 이 설정을 통해 정해진 서비스 기간 내에 변경 내용을 수행할 수 있습니다.

일정 구성을 마친 후에 **만들기** 단추를 선택하여 상태 대시보드로 돌아갑니다. **예약됨** 표에는 만든 배포 일정이 표시됩니다.

> [!WARNING]
> 다시 시작이 필요한 업데이트의 경우 가상 머신은 자동으로 다시 시작됩니다.

## <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작된 후에 **업데이트 관리** 대화 상자의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 확인할 수 있습니다.
현재 실행 중인 배포 상태를 **진행 중**에서 확인할 수 있습니다. 배포가 성공적으로 완료된 후에는 **성공**으로 변경됩니다.
배포에서 하나 이상의 업데이트가 실패하면 상태는 **부분적으로 실패**로 나타납니다.

![업데이트 배포 상태](./media/manage-update-multi/update-view-results.png)

업데이트 배포의 대시보드를 확인하려면 완료된 배포 업데이트를 선택합니다.

**업데이트 결과** 창은 가상 머신의 총 업데이트 수 및 배포 결과를 보여줍니다.
오른쪽의 표에서는 각 업데이트에 대한 자세한 분석 및 설치 결과를 보여줍니다. 설치 결과는 다음 값 중 하나일 수 있습니다.

- 시도 안 함: 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
- 성공: 업데이트했습니다.
- 실패: 업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그**를 선택합니다.

대상 가상 머신에 대한 업데이트 배포를 관리하는 Runbook의 작업 스트림을 보려면 **출력** 타일을 선택합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 로그, 출력 및 오류를 비롯해 업데이트 관리에 대한 자세한 정보를 알아보려면 [Azure에서 업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md)을 참조하세요.
