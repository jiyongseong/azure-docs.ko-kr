---
title: Azure의 Linux VM에 디스크 연결 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 Linux VM에 데이터 디스크를 연결하고 디스크를 사용 가능하도록 초기화하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 4901384d-2a6f-4f46-bba0-337a348b7f87
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 07b7f16cc019f993df90b217abd0c7ad16db75e1
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-attach-a-data-disk-to-a-linux-virtual-machine"></a>Linux Virtual Machine에 데이터 디스크를 연결하는 방법
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [리소스 관리자 배포 모델을 사용하여 데이터 디스크를 연결](../add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하는 방법을 참조하세요.

빈 디스크와 데이터가 포함된 디스크를 모두 Azure VM에 연결할 수 있습니다. 두 유형의 디스크 모두 Azure Storage 계정에 상주하는 .vhd 파일입니다. Linux 컴퓨터에 디스크를 추가하는 것처럼 디스크를 연결한 후에는 사용할 준비를 하기 위해 디스크를 초기화하고 포맷해야 합니다. 이 문서에서는 새 디스크를 초기화하고 포맷하는 방법뿐만 아니라 빈 디스크와 이미 데이터가 있는 디스크를 모두 VM에 연결하는 방법에 대해 자세히 설명합니다.

> [!NOTE]
> 모범 사례는 별도 디스크를 하나 이상 사용하여 가상 머신의 데이터를 저장하는 것입니다. Azure 가상 머신을 만들면 운영 체제 디스크와 임시 디스크가 있습니다. **임시 디스크를 사용하여 영구 데이터를 저장하지 마세요.** 이름이 의미하는 것과 같이 D 드라이브는 임시 저장소만 제공합니다. Azure 저장소에 상주하지 않으므로 중복성이나 백업을 제공하지 않습니다.
> 임시 디스크는 일반적으로 Azure Linux 에이전트에 의해 관리되며 **/mnt/resource**(또는 Ubuntu 이미지의 **/mnt**)에 자동으로 탑재됩니다. 반면, 데이터 디스크 이름은 Linux 커널에서 `/dev/sdc`와 같이 지정될 수 있으며 사용자가 이 리소스를 분할, 포맷 및 탑재해야 합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드][Agent]를 참조하세요.
> 
> 

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-linux.md)]

## <a name="initialize-a-new-data-disk-in-linux"></a>Linux에서 새 데이터 디스크 초기화
1. VM에 SSH를 사용합니다. 자세한 내용은 [Linux를 실행하는 가상 머신에 로그온하는 방법][Logon]을 참조하세요.
2. 이제 초기화할 데이터 디스크의 장치 식별자를 찾아야 합니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다.
   
    a) 다음 명령과 같이 로그에서 SCSI 장치를 grep합니다.
   
    ```bash
    sudo grep SCSI /var/log/messages
    ```
   
    최근 Ubuntu 배포의 경우 `/var/log/messages`에 대한 로깅이 기본적으로 사용하지 않도록 설정될 수 있으므로 `sudo grep SCSI /var/log/syslog`를 사용해야 할 수 있습니다.
   
    표시되는 메시지에서 추가된 마지막 데이터 디스크의 식별자를 찾을 수 있습니다.
   
    ![디스크 메시지 가져오기](./media/attach-disk/scsidisklog.png)
   
    또는
   
    b) `lsscsi` 명령을 사용하여 장치 ID를 확인합니다. `lsscsi`는 `yum install lsscsi`(Red Hat 기반 배포) 또는 `apt-get install lsscsi`(Debian 기반 배포)를 통해 설치할 수 있습니다. *lun* , 즉 **논리 단위 번호**를 사용하여 원하는 디스크를 찾을 수 있습니다. 예를 들어 연결한 디스크의 *lun*은 `azure vm disk list <virtual-machine>`에서 다음과 같이 쉽게 확인할 수 있습니다.

    ```azurecli
    azure vm disk list myVM
    ```

    다음과 유사하게 출력됩니다.

    ```azurecli
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                         OS
    data:    ---  --------  --------------------------------  -----
    data:         30        myVM-2645b8030676c8f8.vhd  Linux
    data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
    info:    vm disk list command OK
    ```
   
    이 데이터를 동일한 샘플 가상 머신에 대한 `lsscsi` 출력과 비교합니다.
   
    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```
   
    각 행의 튜플에 있는 마지막 숫자는 *lun*입니다. 자세한 내용은 `man lsscsi`를 참조하세요.
3. 프롬프트에서 다음 명령을 입력하여 장치를 만듭니다.
   
    ```bash
    sudo fdisk /dev/sdc
    ```

4. 프롬프트가 표시되면 **n**을 입력하여 파티션을 만듭니다.

    ![장치 만들기](./media/attach-disk/fdisknewpartition.png)

5. 프롬프트가 표시되면 **p** 를 입력하여 파티션을 주 파티션으로 지정합니다. **1** 을 입력하여 첫 번째 파티션으로 설정한 다음 Enter 키를 입력하여 실린더에 대한 기본값을 적용합니다. 일부 시스템에서 실린더 대신 첫 번째 및 마지막 섹터의 기본값이 표시될 수 있습니다. 이러한 기본값을 수락하도록 선택할 수 있습니다.

    ![파티션 만들기](./media/attach-disk/fdisknewpartdetails.png)


6. **p** 를 입력하여 분할되는 디스크에 대한 세부 정보를 확인합니다.

    ![디스크 정보 나열](./media/attach-disk/fdiskpartitiondetails.png)


7. **w** 를 입력하여 디스크에 대한 설정을 씁니다.

    ![디스크 변경 내용 쓰기](./media/attach-disk/fdiskwritedisk.png)

8. 이제 새 파티션에 파일 시스템을 만들 수 있습니다. 장치 ID에 파티션 번호를 추가합니다(다음 예제에서는 `/dev/sdc1`). 다음 예제에서는 /dev/sdc1에 ext4 파티션을 만듭니다.
   
    ```bash
    sudo mkfs -t ext4 /dev/sdc1
    ```
   
    ![파일 시스템 만들기](./media/attach-disk/mkfsext4.png)
   
   > [!NOTE]
   > SuSE Linux Enterprise 11 시스템에서는 ext4 파일 시스템에 대해 읽기 전용 권한만 지원합니다. 이 시스템에서 새 파일 시스템 형식을 ext4 대신 ext3으로 지정하는 것이 좋습니다.

9. 다음과 같이 새 파일 시스템을 탑재할 디렉터리를 만듭니다.
   
    ```bash
    sudo mkdir /datadrive
    ```

10. 마지막으로 다음과 같이 드라이브를 탑재할 수 있습니다.
   
    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```
   
    이제 데이터 디스크를 **/datadrive**로 사용할 준비가 되었습니다.
   
    ![디렉터리를 만들고 디스크를 탑재](./media/attach-disk/mkdirandmount.png)

11. /etc/fstab에 새 드라이브를 추가합니다.
   
    다시 부팅 후 드라이브가 자동으로 다시 탑재되도록 하려면 /etc/fstab 파일에 추가해야 합니다. 또한 /etc/fstab에 UUID(Universally Unique IDentifier)를 사용하여 장치 이름(즉, /dev/sdc1) 대신 드라이브를 가리키는 것이 좋습니다. UUID를 사용하면 운영 체제에서 부팅하는 동안 디스크 오류를 감지하므로 올바르지 않은 디스크가 지정된 위치에 탑재되지 않으며 나머지 데이터 디스크는 해당 장치 ID에 할당됩니다. 새 드라이브의 UUID를 찾으려면 **blkid** 유틸리티를 사용할 수 있습니다.
   
    ```bash
    sudo -i blkid
    ```
   
    출력은 다음 예제와 유사합니다.
   
    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

    > [!NOTE]
    > **/etc/fstab** 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

    다음으로, 텍스트 편집기에서 **/etc/fstab** 파일을 엽니다.

    ```bash
    sudo vi /etc/fstab
    ```

    이 예제에서는 이전 단계에서 만든 새 **/dev/sdc1** 장치의 UUID 값과 탑재 지점 **/datadrive**를 사용합니다. **/etc/fstab** 파일의 끝에 다음 줄을 추가합니다.

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
    ```

    또는 SuSE Linux 기반 시스템에서는 약간 다른 형식을 사용해야 할 수 있습니다.

    ```sh
    /dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults,nofail   1   2
    ```

    > [!NOTE]
    > `nofail` 옵션은 파일 시스템이 손상되었거나 디스크가 부팅 시 존재하지 않더라도 VM이 시작되도록 합니다. 이 옵션이 없으면 [FSTAB 오류로 인해 Linux에 SSH를 사용할 수 없음](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)(영문)에 설명되어 있는 동작이 발생할 수 있습니다.

    파일 시스템을 탑재 해제했다가 다시 탑재하여 파일 시스템이 제대로 탑재되었는지 테스트할 수 있습니다. 즉 앞의 단계에서 만든 예제 마운트 지점 `/datadrive`를 사용합니다.

    ```bash
    sudo umount /datadrive
    sudo mount /datadrive
    ```

    `mount` 명령에서 오류가 발생하는 경우 /etc/fstab 파일에서 구문이 올바른지 확인합니다. 추가 데이터 드라이브 또는 파티션을 만든 경우에는 /etc/fstab에 해당 드라이브나 파티션도 별도로 입력합니다.

    다음 명령을 사용하여 드라이브를 쓰기 가능하게 지정합니다.

    ```bash
    sudo chmod go+w /datadrive
    ```

    > [!NOTE]
    > 이후에 fstab을 편집하지 않고 데이터 디스크를 제거하면 VM이 부팅되지 않을 수 있습니다. 이런 경우가 자주 발생하면 대부분의 배포에서 디스크가 부팅 시 탑재되지 않더라도 시스템이 부팅되도록 하는 `nofail` 및/또는 `nobootwait` fstab 옵션을 제공합니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하세요.

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure에서 Linux에 대한 TRIM/UNMAP 지원
일부 Linux 커널은 디스크에서 사용되지 않은 블록을 버릴 수 있도록 TRIM/UNMAP 작업을 지원합니다. 이러한 작업은 Azure에 삭제된 페이지가 더 이상 유효하지 않으며 폐기될 수 있음을 알리는 데 표준 저장소에서 주로 유용합니다. 큰 파일을 만들고 삭제하는 경우 페이지를 삭제하여 비용을 절감할 수 있습니다.

Linux VM에서 TRIM 지원을 사용하는 두 가지 방법이 있습니다. 평소와 같이 권장되는 방법에 대해 배포에 확인하세요.

* `/etc/fstab`에 `discard` 탑재 옵션을 사용합니다. 예:

    ```sh
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```

* 일부 경우 `discard` 옵션에는 성능이 저하 될 수 있습니다. 또는 `fstrim` 명령을 명령줄에서 수동으로 실행하거나, 또는 정기적으로 실행하기 위해 crontab에 추가할 수 있습니다.
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**
  
    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>문제 해결
[!INCLUDE [virtual-machines-linux-lunzero](../../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>다음 단계
다음 문서에서 Linux VM을 사용하는 방법을 읽어볼 수 있습니다.

* [Linux를 실행하는 가상 머신에 로그온하는 방법][Logon]
* [Linux 가상 머신에서 디스크를 분리하는 방법](detach-disk-classic.md)
* [클래식 배포 모델에서 Azure CLI 사용](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure에서 Linux VM에 RAID 구성](../configure-raid.md)
* [Azure에서 Linux VM에 LVM 구성](../configure-lvm.md)

<!--Link references-->
[Agent]:../../extensions/agent-linux.md
[Logon]:../mac-create-ssh-keys.md
