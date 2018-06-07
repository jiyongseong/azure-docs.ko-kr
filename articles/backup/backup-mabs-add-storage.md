---
title: "Azure Backup Server v2에서 Modern Backup Storage 사용 | Microsoft Docs"
description: "Azure Backup Server v2의 새로운 기능에 대해 알아봅니다. 이 문서에서는 Backup Server 설치를 업그레이드하는 방법을 설명합니다."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: 751b9b495fd368dff1f72429707f5f33a0ccb569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="add-storage-to-azure-backup-server-v2"></a>Azure Backup Server v2에 저장소 추가

Azure Backup Server v2에는 System Center 2016 Data Protection Manager Modern Backup Storage가 함께 제공됩니다. Modern Backup Storage를 사용하면 저장소를 50% 절약할 수 있고, 백업이 3배 더 빨라지고, 저장소를 더 효율적으로 사용할 수 있습니다. 저장소에서 워크로드를 인식할 수도 있습니다. 

> [!NOTE]
> Modern Backup Storage를 사용하려면 Windows Server 2016에서 Backup Server v2를 실행해야 합니다. Backup Server v2를 이전 버전의 Windows Server에서 실행하면 Azure Backup Server는 Modern Backup Storage를 사용할 수 없습니다. 대신에 Backup Server v1에서 보호하는 것처럼 워크로드를 보호합니다. 자세한 내용은 Backup Server 버전 [보호 매트릭스](backup-mabs-protection-matrix.md)를 참조하세요.

## <a name="volumes-in-backup-server-v2"></a>Backup Server v2의 볼륨

Backup Server v2는 저장소 볼륨을 허용합니다. 볼륨을 추가하면 Backup Server에서는 볼륨 형식을 Modern Backup Storage에 필요한 ReFS(Resilient File System)로 지정합니다. 볼륨을 추가하고 필요한 경우 나중에 확장하려면 이 워크플로를 사용하는 것이 좋습니다.

1.  VM에 Backup Server v2를 설치합니다.
2.  저장소 풀의 가상 디스크에 볼륨을 만듭니다.
    1.  저장소 풀에 디스크를 추가하고 간단한 레이아웃으로 가상 디스크를 만듭니다.
    2.  다른 디스크를 추가하고 가상 디스크를 확장합니다.
    3.  가상 디스크에 볼륨을 만듭니다.
3.  Backup Server에 볼륨을 추가합니다.
4.  워크로드 인식 저장소를 구성합니다.

## <a name="create-a-volume-for-modern-backup-storage"></a>Modern Backup Storage에 대한 볼륨 만들기

볼륨이 포함된 Backup Server v2를 디스크 저장소로 사용하면 저장소를 지속적으로 제어할 수 있습니다. 볼륨은 단일 디스크일 수 있습니다. 하지만 나중에 저장소를 확장하려는 경우에는 저장소 공간을 사용하여 만들어진 디스크에서 볼륨을 만듭니다. 이렇게 하면 백업 저장소용 볼륨을 확장하려는 경우 도움이 될 수 있습니다. 이 섹션에서는 이 설정을 통해 볼륨을 만드는 모범 사례를 제공합니다.

1. 서버 관리자에서 **파일 및 저장소 서비스** > **볼륨** > **저장소 풀**을 선택합니다. **실제 디스크**에서 **새 저장소 풀**을 선택합니다. 

    ![새 저장소 풀 만들기](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. **작업** 드롭다운 상자에서 **새 가상 디스크**를 선택합니다.

    ![가상 디스크 추가](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. 저장소 풀을 선택하고 나서 **실제 디스크 추가**를 선택합니다.

    ![실제 디스크 추가](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. 실제 디스크를 선택하고 나서 **가상 디스크 확장**을 선택합니다.

    ![가상 디스크 확장](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. 가상 디스크를 선택하고 나서 **새 볼륨**을 선택합니다.

    ![새 볼륨 만들기](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. **서버 및 디스크 선택** 대화 상자에서 서버와 새 디스크를 선택합니다. 그다음에 **다음**을 선택합니다.

    ![서버 및 디스크 선택](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Backup Server 디스크 저장소에 볼륨 추가

Backup Server에 볼륨을 추가하려면 **관리** 창에서 저장소를 다시 검사하고 나서 **추가**를 선택합니다. Backup Server 저장소에 추가할 수 있는 모든 볼륨 목록이 표시됩니다. 사용 가능한 볼륨이 선택한 볼륨 목록에 추가된 후 해당 볼륨에 이름을 지정하면 관리하는 데 도움이 될 수 있습니다. 이러한 볼륨의 형식을 ReFS로 지정하여 Backup Server에서 Modern Backup Storage의 이점을 활용할 수 있게 하려면 **확인**을 선택합니다.

![사용 가능한 볼륨 추가](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>워크로드 인식 저장소 설정

워크로드 인식 저장소를 사용하면 특정 종류의 워크로드를 선별적으로 저장하는 볼륨을 선택할 수 있습니다. 예를 들어 빈번한 대규모 백업이 필요한 워크로드만 저장하도록 많은 수의 IOPS(초당 입/출력 작업)를 지원하는 확장 볼륨을 설정할 수 있습니다. 예를 들어 트랜잭션 로그가 포함된 SQL Server가 있습니다. VM과 같이 덜 빈번하게 백업되는 다른 워크로드는 저비용 볼륨으로 백업할 수 있습니다.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Data Protection Manager 서버의 저장소 풀에서 볼륨 속성을 업데이트하는 PowerShell cmdlet Update-DPMDiskStorage를 사용하여 워크로드 인식 저장소를 설정할 수 있습니다.

구문

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
다음 스크린샷은 PowerShell 창의 Update-DPMDiskStorage cmdlet을 보여 줍니다.

![PowerShell 창의 Update-DPMDiskStorage 명령](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

PowerShell을 사용하여 변경하는 내용은 Backup Server 관리자 콘솔에 반영됩니다.

![관리자 콘솔의 디스크 및 볼륨](./media/backup-mabs-add-storage/mabs-add-storage-9.png)

## <a name="next-steps"></a>다음 단계
Backup Server를 설치한 후 서버를 준비하는 방법을 알아보거나 워크로드 보호를 시작합니다.

- [Backup Server 워크로드 준비](backup-azure-microsoft-azure-backup.md)
- [Backup Server를 사용하여 VMware 서버 백업](backup-azure-backup-server-vmware.md)
- [Backup Server를 사용하여 SQL Server 백업](backup-azure-sql-mabs.md)

