---
title: Azure CLI 1.0을 사용하여 Linux 문제 해결 VM 사용 | Microsoft Docs
description: Azure CLI 1.0을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제를 해결하는 방법 알아보기
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
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 047d0041fa89fa480de0744e594b8ac4f974973a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-cli-10"></a>Azure CLI 1.0을 사용하여 OS 디스크를 복구 VM에 연결함으로써 Linux VM 문제 해결
Linux 가상 머신(VM)에 부팅 또는 디스크 오류가 발생하는 경우 가상 하드 디스크에서 바로 문제 해결 단계를 수행해야 합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목으로 인해 VM이 성공적으로 부팅되지 않는 경우입니다. 이 문서에는 가상 하드 디스크를 다른 Linux VM에 연결하여 모든 오류를 수정한 후 원래 VM을 다시 만들기 위해 Azure CLI 1.0을 사용하는 방법을 자세히 설명합니다.


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](#recovery-process-overview) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)
- [Azure CLI 2.0](../windows/troubleshoot-recovery-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 리소스 관리 배포 모델용 차세대 CLI


## <a name="recovery-process-overview"></a>복구 프로세스 개요
문제 해결 프로세스는 다음과 같습니다.

1. 문제가 발생하는 VM을 삭제하여, 가상 하드 디스크를 유지합니다.
2. 문제 해결을 위해 가상 하드 디스크를 다른 Linux VM에 연결하고 탑재합니다.
3. 문제 해결 VM에 연결합니다. 파일을 편집하거나 도구를 실행하여 원래의 가상 하드 디스크에서 문제를 수정합니다.
4. 문제 해결 VM에서 가상 하드 디스크를 탑재 해제하고 분리합니다.
5. 원래 가상 하드 디스크를 사용하여 VM을 만듭니다.

[최신 Azure CLI 1.0](../../cli-install-nodejs.md)이 설치되어 있고 Resource Manager 모드를 사용하여 로그인했는지 확인합니다.

```azurecli
azure config mode arm
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`이 포함됩니다.


## <a name="determine-boot-issues"></a>부팅 문제 확인
VM이 올바르게 부팅할 수 없는 원인을 확인하려면 직렬 출력을 검사합니다. 일반적인 예로는 `/etc/fstab`의 잘못된 항목 또는 삭제하거나 이동 중인 기본 가상 하드 디스크입니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`에서 직렬 출력을 수신합니다.

```azurecli
azure vm get-serial-output --resource-group myResourceGroup --name myVM
```

직렬 출력을 검토하여 VM가 부팅되지 않는 원인을 확인합니다. 직렬 출력에 아무런 표시가 없는 경우, 가상 하드 디스크가 문제 해결 VM에 연결하고 `/var/log`에 로그 파일을 검토해야 할 수 있습니다.


## <a name="view-existing-virtual-hard-disk-details"></a>기존 가상 하드 디스크 세부 정보 보기
가상 하드 디스크를 다른 VM에 연결하기 전에 가상 하드 디스크(VHD)의 이름을 식별해야 합니다. 

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`에 대한 정보를 수신합니다.

```azurecli
azure vm show --resource-group myResourceGroup --name myVM
```

이전 명령의 출력에서 `Vhd URI`를 찾습니다. 다음의 잘린 예제 출력은 마지막 줄에 `Vhd URI`가 표시됩니다.

```azurecli
info:    Executing command vm show
+ Looking up the VM "myVM"
+ Looking up the NIC "myNic"
+ Looking up the public ip "myPublicIP"
...
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :myVM
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
```


## <a name="delete-existing-vm"></a>기존 VM 삭제
가상 하드 디스크와 VM은 Azure의 두 가지 별개의 리소스입니다. 가상 하드 디스크에는 운영 체제 자체, 응용 프로그램 및 구성이 저장됩니다. VM 자체는 크기 또는 위치를 정의하고 가상 하드 디스크 또는 가상 네트워크 인터페이스 카드(NIC)와 같은 리소스를 참조하는 메타데이터일 뿐입니다. 각 가상 하드 디스크에는 VM에 연결할 때 할당된 임대가 있습니다. VM을 실행하는 동안에도 데이터 디스크를 연결하고 분리할 수 있지만, VM 리소스를 삭제하지 않는 한 OS 디스크를 분리할 수 없습니다. 해당 VM이 중지 및 할당 취소된 상태에 있을 때에도 임대는 OS 디스크와 VM을 계속 연결합니다.

VM을 복구하는 첫 번째 단계는 자체 VM 리소스를 삭제하는 것입니다. VM을 삭제하면 가상 하드 디스크는 저장소 계정에 남게 됩니다. VM을 삭제한 후 가상 하드 디스크를 다른 VM에 연결하여 문제와 오류를 해결합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`에서 VM `myVM`을 삭제합니다.

```azurecli
azure vm delete --resource-group myResourceGroup --name myVM 
```

가상 하드 디스크를 다른 VM에 연결 하기 전에 VM이 삭제 작업을 끝낼 때까지 기다립니다. VM과 연결하는 가상 하드 디스크의 임대는 가상 하드 디스크를 다른 VM에 연결하기 전에 해제해야 합니다.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>기존 가상 하드 디스크를 다른 VM에 연결
다음 몇 단계에서는 문제 해결을 위해 다른 VM을 사용합니다. 기존 가상 하드 디스크를 이 문제 해결 VM에 연결하여 디스크의 콘텐츠를 찾아 편집합니다. 예를 들어 이 프로세스를 사용하면 구성 오류를 수정하거나 추가 응용 프로그램 또는 시스템 로그 파일을 검토할 수 있습니다. 다른 VM을 선택하거나 만들어 문제 해결에 사용합니다.

기존 가상 하드 디스크를 연결하는 경우 이전 `azure vm show` 명령에서 획득한 디스크에 URL을 지정합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 문제 해결 VM `myVMRecovery`에 기존 가상 하드 디스크를 연결합니다.

```azurecli
azure vm disk attach --resource-group myResourceGroup --name myVMRecovery \
    --vhd-url https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>연결된 데이터 디스크 탑재

> [!NOTE]
> 다음 예제에서는 Ubuntu VM에 필요한 단계를 자세히 설명합니다. Red Hat Enterprise Linux 또는 SUSE와 같은 다른 Linux 배포판을 사용하는 경우 로그 파일 위치와 `mount` 명령을 약간 다를 수 있습니다. 명령의 적절한 변경에 대한 특정 배포에 대한 설명서를 참조하세요.

1. 적절한 자격 증명을 사용하여 문제 해결 VM에 대한 SSH입니다. 이 디스크가 문제 해결 VM에 연결된 첫 번째 데이터 디스크인 경우 디스크는 `/dev/sdc`에 연결할 수 있습니다. `dmseg`를 사용하여 연결된 디스크를 볼 수 있습니다.

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
오류가 해결되면 문제 해결 VM에서 기존 가상 하드 디스크를 탑재 해제하고 분리합니다. 가상 하드 디스크를 문제 해결 VM에 연결하는 임대가 해제될 때까지 가상 하드 디스크를 다른 VM과 사용할 수 없습니다.

1. SSH 세션에서 문제 해결 VM까지 기존 가상 하드 디스크를 탑재 해제합니다. 먼저 탑재 지점에 대한 상위 디렉터리에서 변경합니다.

    ```bash
    cd /
    ```

    이제 기존 가상 하드 디스크를 탑재 해제합니다. 다음 예제는 `/dev/sdc1`에 있는 장치를 탑재 해제합니다.

    ```bash
    sudo umount /dev/sdc1
    ```

2. 이제 VM에서 가상 하드 디스크를 분리합니다. 문제 해결 VM에 대한 SSH 세션을 종료합니다. Azure CLI에서 먼저 문제 해결 VM에 연결된 데이터 디스크를 나열합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVMRecovery`에 연결된 데이터 디스크를 나열합니다.

    ```azurecli
    azure vm disk list --resource-group myResourceGroup --vm-name myVMRecovery
    ```

    기존 가상 하드 디스크에 대한 `Lun` 값을 적어둡니다. 다음 예제 명령 출력은 LUN 0에서 연결된 기존 가상 디스크를 보여줍니다.

    ```azurecli
    info:    Executing command vm disk list
    + Looking up the VM "myVMRecovery"
    data:    Name              Lun  DiskSizeGB  Caching  URI
    data:    ------            ---  ----------  -------  ------------------------------------------------------------------------
    data:    myVM              0                None     https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    info:    vm disk list command OK
    ```

    적용 가능한 `Lun` 값을 사용하여 VM에서 데이터 디스크를 분리합니다.

    ```azurecli
    azure vm disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --lun 0
    ```


## <a name="create-vm-from-original-hard-disk"></a>원래 하드 디스크에서 VM 만들기
원래 가상 하드 디스크에서 VM을 만들려면 [이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)을 사용합니다. 실제 JSON 템플릿은 다음 링크에 있습니다.

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

템플릿은 이전 명령의 VHD URL을 사용하여 VM을 기존 가상 네트워크에 배포합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`에 템플릿을 배포합니다.

```azurecli
azure group deployment create --resource-group myResourceGroup --name myDeployment \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

VM 이름(다음 예제의 `myDeployedVM`), OS 유형(`Linux`) 및 VM 크기(`Standard_DS1_v2`) 등 템플릿에 대한 프롬프트에 응답합니다. `osDiskVhdUri`는 기존 가상 하드 디스크를 문제 해결 VM에 연결할 때 이전에 사용된 것과 동일합니다. 명령 출력과 프롬프트의 예는 다음과 같습니다.

```azurecli
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName:  myDeployedVM
osType:  Linux
osDiskVhdUri:  https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
vmSize:  Standard_DS1_v2
existingVirtualNetworkName:  myVnet
existingVirtualNetworkResourceGroup:  myResourceGroup
subnetName:  mySubnet
dnsNameForPublicIP:  mypublicipdeployed
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "mydeployment"
+ Waiting for deployment to complete
+
```


## <a name="re-enable-boot-diagnostics"></a>부트 진단 다시 사용

기존 가상 하드 디스크에서 VM을 만든 경우 부팅 진단을 자동으로 사용할 수 없습니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myDeployedVM`에서 진단 확장을 사용할 수 있습니다.

```azurecli
azure vm enable-diag --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [Azure VM에 SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. VM에서 실행 중인 응용 프로그램에 액세스하는 데 문제가 있는 경우 [Linux VM에서 응용 프로그램 연결 문제 해결](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.