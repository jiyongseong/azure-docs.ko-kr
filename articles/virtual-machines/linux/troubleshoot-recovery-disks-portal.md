---
title: Azure Portal에서 Linux 문제 해결 VM 사용 | Microsoft Docs
description: Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux 가상 머신 문제를 해결하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.openlocfilehash: 89c4c5c986375177918f14417c6b5a9a24925908
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271745"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결
Linux 가상 머신(VM)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목으로 인해 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에는 가상 하드 디스크를 다른 Linux VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure Portal을 사용하는 방법을 자세히 설명합니다.

## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 문제가 발생하는 VM을 삭제하여, 가상 하드 디스크를 유지합니다.
2. 문제 해결을 위해 가상 하드 디스크를 다른 Linux VM에 연결하고 탑재합니다.
3. 문제 해결 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 원래의 가상 하드 디스크에서 문제를 수정합니다.
4. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
5. 원래 가상 하드 디스크를 사용하여 VM을 만듭니다.

관리 디스크를 사용하는 VM에 대해서는 [새 OS 디스크를 연결하여 Managed Disk VM 문제 해결](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk)을 참조합니다.

## <a name="determine-boot-issues"></a>부팅 문제 확인
VM이 올바르게 부팅할 수 없는 원인을 확인하려면 부팅 진단 및 VM 스크린샷을 검사합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목 또는 삭제하거나 이동 중인 기본 가상 하드 디스크입니다.

포털에서 VM을 선택하고 **지원+문제 해결** 섹션까지 아래로 스크롤합니다. **부팅 진단**을 클릭하여 VM에서 스트리밍된 콘솔 메시지를 볼 수 있습니다. 콘솔 로그를 확인하여 VM에서 문제가 발생하는 이유를 확인할 수 있습니다. 다음 예제에서는 수동 상호 작용을 필요로 하는 유지 관리 모드에 묶여 있는 VM을 보여 줍니다.

![VM 부팅 진단 콘솔 로그 보기](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

VM 스크린샷의 캡처를 다운로드하려면 부팅 진단 로그의 위쪽에 있는 **스크린 샷**을 클릭할 수도 있습니다.


## <a name="view-existing-virtual-hard-disk-details"></a>기존 가상 하드 디스크 세부 정보 보기
가상 하드 디스크를 다른 VM에 연결하기 전에 가상 하드 디스크(VHD)의 이름을 식별해야 합니다. 

포털에서 리소스 그룹을 선택하고 저장소 계정을 선택합니다. **Blob**를 다음 예제와 같이 클릭합니다.

![저장소 Blob 선택](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

일반적으로 가상 하드 디스크를 저장하는 **vhd**로 명명된 컨테이너가 있습니다. 가상 하드 디스크의 목록을 보려면 컨테이너를 선택합니다. VHD의 이름을 적어둡니다(접두사는 일반적으로 VM의 이름임).

![저장소 컨테이너에서 VHD 식별](./media/troubleshoot-recovery-disks-portal/storage-container.png)

목록에서 기존 가상 하드 디스크를 선택하고 다음 단계에 사용하기 위해 URL을 복사합니다.

![기존 가상 하드 디스크 URL 복사](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>기존 VM 삭제
가상 하드 디스크와 VM은 Azure의 두 가지 별개의 리소스입니다. 가상 하드 디스크에는 운영 체제 자체, 응용 프로그램 및 구성이 저장됩니다. VM 자체는 크기 또는 위치를 정의하고 가상 하드 디스크 또는 가상 네트워크 인터페이스 카드(NIC)와 같은 리소스를 참조하는 메타데이터일 뿐입니다. 각 가상 하드 디스크에는 VM에 연결할 때 할당된 임대가 있습니다. VM을 실행하는 동안에도 데이터 디스크를 연결하고 분리할 수 있지만, VM 리소스를 삭제하지 않는 한 OS 디스크를 분리할 수 없습니다. 해당 VM이 중지 및 할당 취소된 상태에 있을 때에도 임대는 OS 디스크와 VM을 계속 연결합니다.

VM을 복구하는 첫 번째 단계는 자체 VM 리소스를 삭제하는 것입니다. VM을 삭제하면 가상 하드 디스크는 저장소 계정에 남게 됩니다. VM을 삭제한 후 가상 하드 디스크를 다른 VM에 연결하여 문제와 오류를 해결합니다.

포털에서 VM을 선택한 다음 **삭제**를 클릭합니다.

![부팅 오류를 보여 주는 VM 부팅 진단 스크린샷](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

가상 하드 디스크를 다른 VM에 연결 하기 전에 VM이 삭제 작업을 끝낼 때까지 기다립니다. VM과 연결하는 가상 하드 디스크의 임대는 가상 하드 디스크를 다른 VM에 연결하기 전에 해제해야 합니다.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>기존 가상 하드 디스크를 다른 VM에 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 기존 가상 하드 디스크를 이 문제 해결 VM에 연결하여 디스크의 콘텐츠를 찾아 편집할 수 있습니다. 예를 들어 이 프로세스를 사용하면 구성 오류를 수정하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다. 다른 VM을 선택하거나 만들어 문제 해결에 사용합니다.

1. 포털에서 리소스 그룹을 선택하고 문제를 해결하는 VM을 선택합니다. **디스크**를 선택한 다음 **기존 연결**을 클릭합니다.

    ![포털에서 기존 디스크 연결](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. 기존 가상 하드 디스크를 선택하려면 **VHD 파일**을 클릭합니다.

    ![기존 VHD 찾아보기](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. 저장소 계정 및 컨테이너를 선택한 다음 기존 VHD를 클릭합니다. **선택** 단추를 클릭하여 선택 내용을 확인합니다.

    ![기존 VHD 선택](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. 이제 VHD를 선택하였으므로 **확인**을 클릭하여 기존 가상 하드 디스크를 연결합니다.

    ![기존 가상 하드 디스크 연결 확인](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. 몇 초 후에 VM의 **디스크** 창에서는 데이터 디스크로 연결된 기존 가상 하드 디스크를 나열합니다.

    ![데이터 디스크로 연결된 기존 가상 하드 디스크](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>연결된 데이터 디스크 탑재

> [!NOTE]
> 다음 예제에서는 Ubuntu VM에 필요한 단계를 자세히 설명합니다. Red Hat Enterprise Linux 또는 SUSE와 같은 다른 Linux 배포판을 사용하는 경우 로그 파일 위치와 `mount` 명령을 약간 다를 수 있습니다. 명령의 적절한 변경에 대한 특정 배포에 대한 설명서를 참조하세요.

1. 적절한 자격 증명을 사용하여 문제 해결 VM에 대한 SSH입니다. 이 디스크가 문제 해결 VM에 연결된 첫 번째 데이터 디스크인 경우 `/dev/sdc`에 연결할 수 있습니다. `dmseg`를 사용하여 연결된 디스크를 나열할 수 있습니다.

    ```bash
    dmesg | grep SCSI
    ```
    다음 예제와 유사하게 출력됩니다.

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    앞의 예제에서 OS 디스크는 `/dev/sda`에 있으며 각 VM에 제공된 임시 디스크는 `/dev/sdb`에 있습니다. 여러 데이터 디스크가 있는 경우 `/dev/sdd`, `/dev/sde` 등에 있어야 합니다.

2. 디렉터리를 만들어 기존 가상 하드 디스크를 탑재합니다. 다음 예제는 디렉터리 `troubleshootingdisk`를 만듭니다.

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 기존 가상 하드 디스크에 여러 개의 파티션이 있는 경우 필수 파티션을 탑재합니다. 다음 예제에서는 `/dev/sdc1`에 첫 번째 주 파티션을 탑재합니다.

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 가상 하드 디스크의 UUID(Universally Unique Identifier)를 사용하여 Azure의 VM에 데이터 디스크를 마운트하는 것이 가장 좋습니다. 이 짧은 문제 해결 시나리오에서는 UUID를 사용하여 가상 하드 디스크를 탑재할 필요는 없습니다. 그러나 일반적인 사용 환경에서 UUID 대신 장치 이름을 사용하여 가상 하드 디스크를 탑재하기 위해 `/etc/fstab`을 편집하면 VM이 부팅되지 않을 수 있습니다.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 문제 해결
기존 가상 하드 디스크가 탑재되면 이제 필요에 따라 모든 유지 관리 및 문제 해결 단계를 수행할 수 있습니다. 문제가 해결되면 다음 단계를 계속합니다.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>원래 가상 하드 디스크의 탑재 해제 및 분리
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 사용할 수 없습니다.

1. SSH 세션에서 문제 해결 VM까지 기존 가상 하드 디스크를 탑재 해제합니다. 먼저 탑재 지점에 대한 상위 디렉터리에서 변경합니다.

    ```bash
    cd /
    ```

    이제 기존 가상 하드 디스크를 탑재 해제합니다. 다음 예제는 `/dev/sdc1`에 있는 장치를 탑재 해제합니다.

    ```bash
    sudo umount /dev/sdc1
    ```

2. 이제 VM에서 가상 하드 디스크를 분리합니다. 포털에서 VM을 선택하고 **디스크**를 클릭합니다. 기존 가상 하드 디스크를 선택한 다음 **분리**를 클릭합니다.

    ![기존 가상 하드 디스크 분리](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    계속하기 전에 VM이 데이터 디스크를 성공적으로 분리할 때까지 기다립니다.

## <a name="create-vm-from-original-hard-disk"></a>원래 하드 디스크에서 VM 만들기
원래 가상 하드 디스크에서 VM을 만들려면 [이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet)을 사용합니다. 템플릿은 이전 명령의 VHD URL을 사용하여 VM을 기존 가상 네트워크에 배포합니다. 다음과 같이 **Azure에 배포** 단추를 클릭합니다.

![GitHub의 템플릿에서 VM 배포](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

템플릿은 배포를 위해 Azure Portal에 로드됩니다. 새 VM 및 기존 Azure 리소스의 이름을 입력하고 URL을 기존 가상 하드 디스크에 붙여 넣습니다. 배포를 시작하려면 **구매**를 클릭합니다.

![템플릿에서 VM 배포](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>부트 진단 다시 사용
기존 가상 하드 디스크에서 VM을 만든 경우 부팅 진단을 자동으로 사용할 수 없습니다. 부팅 진단의 상태를 확인하고 필요한 경우 사용하려면 포털에서 VM을 선택합니다. **모니터링**에서 **진단 설정**을 클릭합니다. 상태가 **켜기**이고 **진단 부팅** 옆에 있는 확인 표시가 선택되었는지 확인합니다. 항목을 변경하려면 **저장**을 클릭합니다.

![부팅 진단 설정 업데이트](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>새 OS 디스크를 연결하여 Managed Disk VM 문제 해결
1. 영향을 받는 Managed Disk Windows VM을 중지합니다.
2. Managed Disk VM의 OS 디스크의 [관리 디스크 스냅숏을 만듭니다](../windows/snapshot-copy-managed-disk.md).
3. [스냅숏에서 새 관리 디스크를 만듭니다](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [VM의 데이터 디스크로서 관리 디스크를 연결합니다](../windows/attach-disk-ps.md).
5. [4단계의 데이터 디스크를 OS 디스크로 변경합니다](../windows/os-disk-swap.md).

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. VM에서 실행 중인 응용 프로그램에 액세스하는 데 문제가 있는 경우 [Linux VM에서 응용 프로그램 연결 문제 해결](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

Resource Manager를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
