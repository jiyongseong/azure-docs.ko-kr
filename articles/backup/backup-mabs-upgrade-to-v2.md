---
title: Azure Backup Server v2 설치 | Microsoft Docs
description: Azure Backup Server v2에서는 VM, 파일 및 폴더, 워크로드 등을 보호하기 위한 향상된 백업 기능을 제공합니다. Azure Backup Server v2를 설치하거나 이 버전으로 업그레이드하는 방법을 알아봅니다.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: dd7b76d9e06bc82ffd75f12131c2c247da05cc91
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="install-azure-backup-server-v2"></a>Azure Backup Server v2 설치

Azure Backup Server를 사용하여 VM(가상 머신), 워크로드, 파일 및 폴더 등을 보호할 수 있습니다. Azure Backup Server v2는 Azure Backup Server v1을 기반으로 빌드되고 v1에서 사용할 수 없는 새로운 기능을 제공합니다. v1 및 v2의 기능 비교에 대해서는 [Azure Backup Server 보호 매트릭스](backup-mabs-protection-matrix.md)를 참조하세요. 

Backup Server v2의 추가 기능은 Backup Server v1의 업그레이드입니다. 하지만 Backup Server v1은 Backup Server v2를 설치하기 위한 필수 구성 요소가 아닙니다. Backup Server v1에서 Backup Server v2로 업그레이드하려면 Backup Server v2를 Backup Server 보호 서버에 설치합니다. 기존 Backup Server 설정은 그대로 유지됩니다.

Backup Server v2를 Windows Server 2012 R2 또는 Windows Server 2016에 설치할 수 있습니다. System Center 2016 Data Protection Manager Modern Backup Storage 같은 새로운 기능을 활용하려면 Backup Server v2를 Windows Server 2016에 설치해야 합니다. Backup Server v2로 업그레이드하거나 이 버전을 설치하기 전에 [installation prerequisites](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)(설치 필수 구성 요소)를 읽어 보세요.

> [!NOTE]
> Azure Backup Server에는 System Center Data Protection Manager와 동일한 코드베이스가 있습니다. Backup Server v1은 Data Protection Manager 2012 R2에 해당하고 Backup Server v2는 Data Protection Manager 2016에 해당합니다. 이 문서에서는 때때로 Data Protection Manager 설명서를 참조합니다.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Backup Server를 v2로 업그레이드
Backup Server v1에서 Backup Server v2로 업그레이드하려면 설치에 필수 업데이트가 있는지 확인합니다.

- 보호된 서버에서 [보호 에이전트를 업데이트](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent)합니다.
- Windows Server 2012 R2를 Windows Server 2016으로 업그레이드합니다.
- 모든 프로덕션 서버에서 Azure Backup Server 원격 관리자를 업그레이드합니다.
- 백업이 프로덕션 서버를 다시 시작하지 않고 계속하도록 설정되어 있는지 확인합니다.


### <a name="upgrade-steps-for-backup-server-v2"></a>Backup Server v2에 대한 업그레이드 단계

1. 다운로드 센터에서 [업그레이드 설치 관리자를 다운로드](https://go.microsoft.com/fwlink/?LinkId=626082)합니다.

2. 설치 마법사를 추출한 후 **setup.exe 실행**이 선택되어 있는지 확인하고 나서 **마침**을 선택합니다.

  ![설치 관리자 - 설치 프로그램 실행](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. Microsoft Azure Backup Server 마법사의 **설치**에서 **Microsoft Azure Backup Server**를 선택합니다.

  ![설치 관리자 - 설치 선택](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. **시작** 페이지에서 경고를 검토하고 **다음**을 선택합니다.

  ![설치 관리자 - 시작 페이지](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. 설치 마법사에서는 필수 구성 요소 확인을 수행하여 사용자 환경을 업그레이드할 수 있는지 확인합니다. **필수 구성 요소 확인** 페이지에서 **확인**을 선택합니다.

  ![설치 관리자 - 필수 구성 요소 확인 페이지](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. 사용자 환경은 필수 구성 요소 확인을 통과해야 합니다. 사용자 환경이 확인을 통과하지 못하면 문제를 확인하고 해결하세요. 그다음에 **다시 확인**을 선택합니다. 필수 구성 요소 확인을 통과한 후 **다음**을 선택합니다.

  ![설치 관리자 - 다시 확인 단추](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. **SQL 설정** 페이지에서 SQL 설치에 대한 관련 옵션을 선택하고 **확인 후 설치**를 선택합니다.

  ![설치 관리자 - SQL 설정 페이지](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  확인은 몇 분 정도 걸립니다. 확인이 완료되면 **다음**을 선택합니다.

  ![설치 관리자 - SQL 설정 확인 및 설치 단추](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. **설치 설정** 페이지에서 Backup Server가 설치된 위치 및 스크래치 위치를 변경합니다. **다음**을 선택합니다.

  ![설치 관리자 - 설치 설정 페이지](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. 설치 마법사를 완료하려면 **마침**을 선택합니다.

  ![설치 관리자 - 마침](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Modern Backup Storage에 대한 저장소 추가

백업 저장소 효율성을 개선하기 위해 Backup Server v2에서는 볼륨에 대한 지원을 추가합니다. Backup Server v1처럼 Backup Server v2는 디스크를 지원합니다.

### <a name="add-volumes-and-disks"></a>볼륨 및 디스크 추가
Windows Server 2016에서 Backup Server v2를 실행할 경우 볼륨을 사용하여 백업 데이터를 저장할 수 있습니다. 볼륨을 사용하면 저장소가 절약되고 더 빠르게 백업할 수 있습니다. 볼륨은 Backup Server의 새로운 기능이므로 볼륨을 추가해야 합니다. 

볼륨을 Backup Server에 추가할 때 볼륨에 이름을 지정할 수 있습니다. 이름을 지정할 볼륨의 **이름** 열을 클릭합니다. 필요한 경우 나중에 이름을 변경할 수 있습니다. PowerShell을 사용하여 볼륨의 이름을 추가하거나 변경할 수도 있습니다.

관리자 콘솔에서 볼륨을 추가하려면:

1. Azure Backup Server 관리자 콘솔에서 **관리** > **디스크 저장소** > **추가**를 선택합니다.

    ![[디스크 저장소 추가] 마법사를 엽니다.](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    [디스크 저장소 추가] 마법사가 열립니다.

2. **디스크 저장소 추가** 페이지의 **사용 가능한 볼륨** 상자에서 볼륨을 선택하고 나서 **추가**를 선택합니다.
3. **선택한 볼륨** 상자에 볼륨의 이름을 입력하고 **확인**을 선택합니다.

      ![디스크 저장소 추가 마법사 - 볼륨 추가](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  디스크를 추가하려면 레거시 저장소가 있는 보호 그룹에 디스크가 속해야 합니다. 이러한 디스크는 해당 보호 그룹에만 사용할 수 있습니다. Backup Server에 레거시 보호가 있는 원본이 없다면 디스크가 나열되지 않습니다.

  디스크 추가에 대한 자세한 내용은 [Adding disks to increase legacy storage](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage)(디스크를 추가하여 레거시 저장소 늘리기)를 참조하세요. 디스크에는 이름을 지정할 수 없습니다.


### <a name="assign-workloads-to-volumes"></a>볼륨에 워크로드 할당

Backup Server에서 어떤 볼륨에 어떤 워크로드를 할당할지 지정합니다. 예를 들어 빈번한 대규모 백업이 필요한 워크로드만 저장하도록 많은 수의 IOPS(초당 입/출력 작업)를 지원하는 확장 볼륨을 설정할 수 있습니다. 예를 들어 트랜잭션 로그가 포함된 SQL Server가 있습니다.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Backup Server의 저장소 풀에서 볼륨 속성을 업데이트하려면 PowerShell cmdlet Update-DPMDiskStorage를 사용합니다.

구문

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

PowerShell을 사용하여 변경한 모든 내용은 UI에 반영됩니다.


## <a name="protect-data-sources"></a>데이터 원본 보호
데이터 원본 보호를 시작하려면 보호 그룹을 만듭니다. 다음 단계에서는 [새 보호 그룹] 마법사에 대한 변경 내용이나 추가 내용을 강조 표시합니다.

보호 그룹을 만들려면:

1. Backup Server 관리자 콘솔에서 **보호**를 선택합니다.

2. 도구 리본에서 **새로 만들기**를 선택합니다.

    [새 보호 그룹 만들기] 마법사가 열립니다.

  ![새 보호 그룹 만들기 마법사](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. **Welcome** 페이지에서 **다음**을 선택합니다.
4. **보호 그룹 형식 선택** 페이지에서 만들려는 보호 그룹의 형식을 선택하고 나서 **다음**을 선택합니다.

  ![보호 그룹 형식 선택 페이지](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. **그룹 구성원 선택** 페이지의 **사용 가능한 구성원** 창에서 보호 에이전트가 있는 구성원이 나열됩니다. 이 예제에서는 볼륨 D:\ 및 E:\를 선택하고 **선택한 구성원** 창에 추가합니다. **다음**을 선택합니다.

  ![그룹 구성원 선택 페이지](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. **데이터 보호 방법 선택** 페이지에서 **보호 그룹 이름**을 입력하고, 보호 방법을 선택하고 나서, **다음**을 선택합니다. 단기 보호가 필요하면 **디스크** 백업 방법을 선택해야 합니다.

  ![데이터 보호 방법 선택 페이지](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. **단기 목표 지정** 페이지에서 **보존 범위** 및 **동기화 빈도**에 대한 세부 정보를 선택합니다. 그다음에 **다음**을 선택합니다. 필요한 경우 복구 지점이 발생하는 일정을 변경하려면 **수정**을 선택합니다.

  ![단기 목표 지정 페이지](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. **디스크 저장소 할당 검토** 페이지에서 선택한 데이터 원본에 대한 세부 정보, 데이터 원본 크기 및 프로비전할 공간과 대상 저장소 볼륨에 대한 값을 검토합니다.

  ![디스크 저장소 할당 검토 페이지](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  저장소 볼륨은 워크로드 볼륨 할당(PowerShell을 사용하여 설정됨) 및 사용 가능한 저장소에 따라 달라집니다. 드롭다운 메뉴에서 다른 볼륨을 선택하여 저장소 볼륨을 변경할 수 있습니다. **대상 저장소** 값을 변경하면 **사용 가능한 공간** 및 **Underprovisioned Space**(미달 프로비전된 공간) 아래 값을 반영하도록 **사용 가능한 디스크 저장소**가 크게 변경됩니다.

  데이터 원본이 계획대로 증가하면 **사용 가능한 디스크 저장소**의 **Underprovisioned Space**(미달 프로비전된 공간) 열의 값은 필요한 추가 저장소의 크기를 반영합니다. 이 값을 사용하면 원활한 백업에 필요한 저장소를 계획하는 데 도움이 됩니다. 값이 0이면 예측 가능한 미래에 저장소 관련 문제가 발생할 가능성이 없습니다. 값이 0 이외의 숫자이면 보호 정책 및 보호된 구성원의 데이터 크기에 따라 할당된 저장소가 충분하지 않은 것입니다.

  ![미달 할당된 디스크 저장소](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   보호 그룹 만들기를 마치려면 마법사를 완료합니다.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Modern Backup Storage로 레거시 저장소 마이그레이션
Backup Server v2로 업그레이드하거나 이 버전을 설치하고 운영 체제를 Windows Server 2016으로 업그레이드한 후 Modern Backup Storage를 사용하도록 보호 그룹을 업데이트합니다. 기본적으로 보호 그룹은 변경되지 않습니다. 보호 그룹은 처음에 설정된 대로 계속 작동합니다. 

Modern Backup Storage를 사용하도록 보호 그룹을 업데이트하는 것은 선택 사항입니다. 보호 그룹을 업데이트하려면 데이터 보존 옵션을 사용하여 모든 데이터 원본의 보호를 중지합니다. 그다음에 데이터 원본을 새 보호 그룹에 추가합니다.

1. 관리자 콘솔에서 **보호** 기능을 선택합니다. **보호 그룹 구성원** 목록에서 구성원을 마우스 오른쪽 단추로 클릭하고 **구성원 보호 중지**를 선택합니다.

  ![구성원 보호 중지](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. **그룹에서 제거** 대화 상자에서 저장소 풀의 사용된 디스크 공간 및 사용 가능한 공간을 검토합니다. 기본값은 디스크에서 복구 지점을 유지하고 관련 보존 정책에 따라 만료되도록 허용하는 것입니다. **확인**을 클릭합니다.

  사용된 디스크 공간을 사용 가능한 저장소 풀로 즉시 반환하려면 **디스크의 복제본 삭제** 확인란을 선택하여 해당 구성원과 연결된 백업 데이터(및 복구 지점)를 삭제합니다.

  ![그룹에서 제거 대화 상자](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Modern Backup Storage를 사용하는 보호 그룹을 만듭니다. 보호되지 않는 데이터 원본을 포함합니다.


## <a name="add-disks-to-increase-legacy-storage"></a>디스크를 추가하여 레거시 저장소 늘리기

Backup Server에서 레거시 저장소를 사용하려면 디스크를 추가하여 레거시 저장소를 늘려야 합니다. 

디스크 저장소를 추가하려면:

1. 관리자 콘솔에서 **관리** > **디스크 저장소** > **추가**를 선택합니다.

    ![디스크 저장소 추가 대화 상자](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. **디스크 저장소 추가** 대화 상자에서 **디스크 추가**를 선택합니다.

5. 사용 가능한 디스크 목록에서 추가할 디스크를 선택하고, **추가**를 선택하고 나서, **확인**을 선택합니다.

## <a name="update-the-data-protection-manager-protection-agent"></a>Data Protection Manager 보호 에이전트 업데이트

Backup Server에서는 업데이트에 System Center Data Protection Manager 보호 에이전트를 사용합니다. 네트워크에 연결되지 않은 보호 에이전트를 업그레이드할 경우 연결된 에이전트 업그레이드를 완료하는 데는 Data Protection Manager 관리자 콘솔을 사용할 수 없습니다. 비활성 도메인 환경에서 보호 에이전트를 업그레이드해야 합니다. 클라이언트 컴퓨터가 네트워크에 연결될 때까지 Data Protection Manager 관리자 콘솔에는 보호 에이전트 업데이트가 보류 중인 것으로 표시됩니다.

다음 섹션에서는 연결되거나 연결되지 않은 클라이언트 컴퓨터에 대한 보호 에이전트를 업데이트하는 방법을 설명합니다.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>연결된 클라이언트 컴퓨터에 대한 보호 에이전트 업데이트

1. Backup Server 관리자 콘솔에서 **관리** > **에이전트**를 선택합니다.

2. 표시 창에서 보호 에이전트를 업데이트할 클라이언트 컴퓨터를 선택합니다.

  > [!NOTE]
  > **에이전트 업데이트** 열에는 각 보호된 컴퓨터에 보호 에이전트 업데이트를 사용할 수 있는 시기가 표시됩니다. **작업** 창에서 **업데이트** 작업은 보호된 컴퓨터가 선택되고 업데이트가 사용 가능한 경우에만 사용할 수 있습니다.
  >
  >

3. 선택된 컴퓨터에 업데이트된 보호 에이전트를 설치하려면 **작업** 창에서 **업데이트**를 선택합니다.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>연결되지 않은 클라이언트 컴퓨터에 대한 보호 에이전트 업데이트

1. Backup Server 관리자 콘솔에서 **관리** > **에이전트**를 선택합니다.

2. 표시 창에서 보호 에이전트를 업데이트할 클라이언트 컴퓨터를 선택합니다.

  > [!NOTE]
   > **에이전트 업데이트** 열에는 각 보호된 컴퓨터에 보호 에이전트 업데이트를 사용할 수 있는 시기가 표시됩니다. 업데이트가 사용 가능하지 않으면 보호된 컴퓨터가 선택된 경우 **작업** 창에서 **업데이트** 작업을 사용할 수 없습니다.
  >
  >

3. 선택된 컴퓨터에 업데이트된 보호 에이전트를 설치하려면 **업데이트**를 선택합니다.

4. 네트워크에 연결되지 않은 클라이언트 컴퓨터의 경우 컴퓨터가 네트워크에 연결될 때까지 **에이전트 상태** 열에는 **업데이트 보류 중** 상태가 표시됩니다.

  클라이언트 컴퓨터가 네트워크에 연결된 후 클라이언트 컴퓨터에 대한 **에이전트 업데이트** 열에는 **업데이트 중** 상태가 표시됩니다.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>이전 버전에서 레거시 보호 그룹 이동 및 새 버전과 Azure 동기화

Azure Backup Server와 OS가 모두 업데이트되면 Modern Backup Storage를 사용하여 새 데이터 원본을 보호할 준비가 된 것입니다. 이미지 보호된 데이터 원본은 Azure Backup Server에 있는 것처럼 레거시 방법으로 계속 보호되지만, 완전 새로운 보호 기능은 Modern Backup Storage를 사용합니다.

아래 단계는 보호의 레거시 모드에서 Modern Backup Storage로 데이터 원본을 마이그레이션하는 것입니다.

• 새 볼륨을 DPM 저장소 풀에 추가하고 원하는 경우 친숙한 이름과 데이터 원본 태그를 할당합니다.
• 레거시 모드의 각 데이터 원본의 경우 데이터 원본의 보호를 중지하고 “보호된 데이터를 보존”합니다.  이렇게 하면 마이그레이션 후 이전 복구 지점을 복구할 수 있게 됩니다.

• 새 PG를 만들고 새 형식을 사용하여 저장할 수 있는 데이터 원본을 선택합니다.
• DPM은 레거시 백업 저장소에서 Modern Backup Storage 볼륨으로 로컬에서 복제본 복사를 수행합니다.
참고: 이 작업은 사후 복구 작업으로 간주됩니다. • 그런 다음 모든 새로운 동기화 및 복구 지점은 Modern Backup Storage에 저장됩니다.
• 이전 복구 지점은 만료되면 사용할 수 없게 되며 결과적으로 디스크 공간은 늘어납니다.
• 모든 기존 볼륨이 이전 저장소에서 삭제되면 디스크를 Azure Backup 및 시스템에서 제거할 수 있습니다.
• Azure DPMDB를 백업합니다.

파트 2:-중요 항목 > 새 서버는 원래 Azure Backup Server와 같은 이름으로 지정되어야 합니다. 이전 저장소 풀 및 DPMDB을 사용하여 복구 지점을 유지하려는 경우 새 Azure Backup Server의 이름을 변경할 수 없습니다. 복원에 필요하므로 DPMDB의 백업본을 가지고 있어야 합니다.

1) 원래 Azure Backup Server를 종료하거나 끕니다.
2) 활성 디렉터리에서 컴퓨터 계정을 다시 설정합니다.
3) 새 컴퓨터에 Server 2016을 설치하고 원래 Azure Backup Server와 동일한 컴퓨터 이름으로 지정합니다.
4) 도메인 조인
5) Azure Backup Server V2 설치(DPM 저장소 풀 디스크를 이전 서버에서 이동 및 가져오기)
6) 파트 2의 끝에서 가져온 DPMDB 복원
7) 저장소를 원래 백업 서버에서 새 서버로 연결합니다.
8) SQL에서 DPMDB 복원
9) 새 서버의 관리 명령줄에서 Microsoft Azure Backup 설치 위치 및 bin 폴더로 cd

경로 예: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
Azure Backup, DPMSYNC -SYNC 실행

10) DPMSYNC -SYNC 실행 이전 것을 옮기는 대신 DPM Storage 풀에 새 디스크를 추가했다면 DPMSYNC -Reallocatereplica 실행

## <a name="new-powershell-cmdlets-in-v2"></a>v2의 새로운 PowerShell cmdlet

Azure Backup Server v2를 설치하면 두 개의 새로운 cmdlet을 사용할 수 있습니다. 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>다음 단계

서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.
- [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
- [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
- [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)
- [Backup Server에서 Modern Backup Storage 사용](backup-mabs-add-storage.md)

