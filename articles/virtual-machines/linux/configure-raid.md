---
title: Linux를 실행하는 가상 머신에 소프트웨어 RAID 구성 | Microsoft Docs
description: mdadm을 사용하여 Azure에서 Linux에 대해 RAID를 구성하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: jeconnoc
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.openlocfilehash: d6e831692da37645e264c6674f1ba54bb16d25d4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="configure-software-raid-on-linux"></a>Linux에서 소프트웨어 RAID 구성
Azure에서 Linux 가상 머신의 소프트웨어 RAID를 사용하여 연결된 여러 데이터 디스크를 단일 RAID 장치로 나타내는 것이 일반적인 시나리오입니다. 일반적으로 이 시나리오는 단일 디스크만 사용하는 경우와 비교하여 성능을 개선하고 처리량을 향상하기 위해 사용할 수 있습니다.

## <a name="attaching-data-disks"></a>데이터 디스크 연결
RAID 장치를 구성하는 데 두 개 이상의 빈 데이터 디스크가 필요합니다.  RAID 장치를 만드는 주된 이유는 디스크 IO의 성능을 개선하기 위한 것입니다.  IO 요구 사항에 따라 Standard Storage에 저장된 디스크(디스크당 최대 500IO/ps) 또는 Premium Storage에 저장된 디스크(디스크당 최대 5000IO/ps)를 연결할 수 있습니다. Linux 가상 머신에 데이터 디스크를 프로비전 및 연결하는 방법은 이 문서에서 자세히 다루지 않습니다.  Azure에서 빈 데이터 디스크를 Linux 가상 컴퓨터에 연결하는 방법에 대한 자세한 내용은 Microsoft Azure 문서 [디스크 연결](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

## <a name="install-the-mdadm-utility"></a>mdadm 유틸리티 설치
* **Ubuntu**
```bash
sudo apt-get update
sudo apt-get install mdadm
```

* **CentOS 및 Oracle Linux**
```bash
sudo yum install mdadm
```

* **SLES 및 openSUSE**
```bash  
zypper install mdadm
```

## <a name="create-the-disk-partitions"></a>디스크 파티션 만들기
이 예에서는 /dev/sdc에 단일 디스크 파티션을 만듭니다. /dev/sdc1이라는 새 디스크 파티션을 호출합니다.

1. `fdisk`를 시작하여 파티션 만들기를 시작합니다.

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

2. 프롬프트에서 'n'을 눌러 새로운( **n**ew) 파티션을 만듭니다.

    ```bash
    Command (m for help): n
    ```

3. 'p'를 눌러 주( **p**rimary) 파티션을 만듭니다.

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

4. '1'을 눌러 파티션 번호 1을 선택합니다.

    ```bash
    Partition number (1-4): 1
    ```

5. 새 파티션의 시작 지점을 선택하거나 `<enter>` 키를 눌러 드라이브의 가용 공간 시작 부분에 파티션을 배치하는 기본값을 적용할 수 있습니다.

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

6. 파티션 크기를 선택합니다. 예를 들어 10기가바이트 파티션을 만들려면 '+10G'를 입력합니다. 또는 `<enter>` 키를 눌러 범위가 전체 드라이브인 단일 파티션을 만듭니다.

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

7. 그런 다음, 파티션의 ID 및 유형( **t**ype)을 기본 ID '83'(Linux)에서 ID 'fd'(Linux raid auto)로 변경합니다.

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

8. 마지막으로, 드라이브에 파티션 테이블을 쓰고 fdisk를 종료합니다.

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>RAID 배열 만들기
1. 다음 예는 3개의 별도 데이터 디스크(sdc1, sdd1, sde1)에 위치한 3개의 파티션을 "스트라이프"합니다(RAID 수준 0).  이 명령을 실행하면 **/dev/md127** 이라는 새 RAID 장치가 만들어집니다. 이 데이터 디스크가 이전에 작동하지 않는 다른 RAID 배열의 일부였다면 `--force` 매개 변수를 `mdadm` 명령에 추가해야 합니다.

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

2. 새 RAID 장치에서 파일 시스템 만들기
   
    a. **CentOS, Oracle Linux, SLES 12, openSUSE 및 Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    나. **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    다. **SLES 11** - boot.md 사용 및 mdadm.conf 만들기

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > SUSE 시스템에서 이렇게 변경한 후에는 다시 부팅해야 할 수 있습니다. SLES 12에서는 이 단계가 필요하지 *않습니다* .
   > 
   > 

## <a name="add-the-new-file-system-to-etcfstab"></a>/etc/fstab에 새 파일 시스템 추가
> [!IMPORTANT]
> /etc/fstab 파일을 부적절하게 편집하면 부팅할 수 없는 시스템이 발생할 수 있습니다. 확실하지 않은 경우 배포 설명서에서 이 파일을 제대로 편집하는 방법에 대한 자세한 내용을 확인하세요. 또한 편집하기 전에 /etc/fstab 파일의 백업을 만드는 것이 좋습니다.

1. 새 파일 시스템용으로 원하는 탑재 지점을 만듭니다. 예를 들어 다음과 같습니다.

    ```bash
    sudo mkdir /data
    ```
2. /etc/fstab를 편집할 때는 파일 시스템을 참조하는 데 장치 이름 대신 **UUID** 를 사용해야 합니다.  `blkid` 유틸리티를 사용하여 새 파일 시스템의 UUID를 확인합니다.

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

3. 텍스트 편집기에서 /etc/fstab을 열고 예를 들어 다음과 같이 새 파일 시스템에 항목을 추가합니다.

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    또는 **SLES 11**에 대해 다음을 수행합니다.

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    그런 다음, /etc/fstab를 저장하고 닫습니다.

4. /etc/fstab 항목이 올바른지 테스트합니다.

    ```bash  
    sudo mount -a
    ```

    이 명령 결과 오류 메시지가 발생하는 경우 /etc/fstab 파일에서 구문을 확인하세요.
   
    그런 다음, `mount` 명령을 실행하여 파일 시스템이 탑재되었는지 확인합니다.

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

5. (선택 사항) Failsafe 부팅 매개 변수
   
    **fstab 구성**
   
    많은 배포에는 /etc/fstab 파일에 추가할 수 있는 `nobootwait` 또는 `nofail` 탑재 매개 변수가 포함되어 있습니다. 이 매개 변수는 특정 파일 시스템 탑재 시 오류를 허용하며 Linux 시스템이 제대로 RAID 파일 시스템을 탑재할 수 없는 경우에도 계속 부팅되도록 합니다. 이러한 매개 변수에 대한 자세한 내용은 배포 설명서를 참조하십시오.
   
    예제(Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux 부팅 매개 변수**
   
    위의 매개 변수 외에, 커널 매개 변수 "`bootdegraded=true`"는 RAID가 손상 또는 저하된 것으로 인식되는 경우에도(예: 데이터 드라이브가 실수로 가상 머신에서 제거된 경우) 시스템이 부팅되도록 할 수 있습니다. 기본적으로 이 매개 변수는 시스템이 부팅할 수 없게 만들 수도 있습니다.
   
    커널 매개 변수를 올바르게 편집하는 방법에 대해서는 배포 설명서를 참조하십시오. 예를 들어 CentOS, Oracle Linux, SLES 11 등 많은 배포에서 이 매개 변수를 "`/boot/grub/menu.lst`" 파일에 수동으로 추가할 수 있습니다.  Ubuntu에서는 "/etc/default/grub"의 `GRUB_CMDLINE_LINUX_DEFAULT` 변수에 이 매개 변수를 추가할 수 있습니다.


## <a name="trimunmap-support"></a>TRIM/UNMAP 지원
일부 Linux 커널은 디스크에서 사용되지 않은 블록을 버릴 수 있도록 TRIM/UNMAP 작업을 지원합니다. 이러한 작업은 Azure에 삭제된 페이지가 더 이상 유효하지 않으며 폐기될 수 있음을 알리는 데 표준 저장소에서 주로 유용합니다. 큰 파일을 만들고 삭제하는 경우 페이지를 삭제하여 비용을 절감할 수 있습니다.

> [!NOTE]
> 배열에 대한 청크 크기가 기본값(512KB)보다 작은 값으로 설정된 경우, RAID는 취소 명령을 실행하지 않을 수 있습니다. 호스트에서의 unmap 세분성도 512KB이기 때문입니다. mdadm의 `--chunk=` 매개 변수를 통해 배열의 청크 크기를 수정하는 경우, TRIM/매핑 해제 요청이 커널에서 무시될 수 있습니다.

Linux VM에서 TRIM 지원을 사용하는 두 가지 방법이 있습니다. 평소와 같이 권장되는 방법에 대해 배포에 확인하세요.

- `/etc/fstab`에 `discard` 탑재 옵션을 사용합니다. 예:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- 일부 경우 `discard` 옵션에는 성능이 저하 될 수 있습니다. 또는 `fstrim` 명령을 명령줄에서 수동으로 실행하거나, 또는 정기적으로 실행하기 위해 crontab에 추가할 수 있습니다.

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
