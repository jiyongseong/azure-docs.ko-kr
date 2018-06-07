---
title: Azure에서 Linux VHD 만들기 및 업로드
description: Linux 운영 체제가 포함된 Azure VHD(가상 하드 디스크)를 만들고 업로드하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33944589"
---
# <a name="information-for-non-endorsed-distributions"></a>보증되지 않는 배포에 대한 정보
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

[보증 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 중 하나를 사용하는 경우에만 Linux OS를 실행하는 가상 머신에 Azure 플랫폼 SLA가 적용됩니다. 이러한 보증 배포한의 경우, 필수 구성을 포함하는 Linux 이미지가 Azure Marketplace에 제공됩니다.

* [Azure의 Linux - 보증 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Microsoft Azure의 Linux 이미지 지원](https://support.microsoft.com/kb/2941892)

Azure에서 실행되는 모든 배포판은 플랫폼에서 제대로 실행될 수 있도록 하는 여러 가지 필수 구성 요소를 충족해야 합니다.  모든 배포판이 서로 다르므로 이 문서는 결코 포괄적이지 않으며, 아래의 모든 조건을 충족하더라도 플랫폼에서 제대로 실행될 수 있도록 Linux 시스템을 크게 조정해야 할 수도 있습니다.

이러한 이유로 가능한 경우 [Azure 보증 배포판의 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)로 시작하는 것이 좋습니다. 다음 문서에서는 Azure에서 지원되는 다양한 Linux 보증 배포판을 준비하는 방법을 안내합니다.

* **[CentOS 기반 배포](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 및 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

이 문서의 나머지 부분에서는 Azure에서 Linux 배포판을 실행하기 위한 일반 지침에 대해 중점적으로 설명합니다.

## <a name="general-linux-installation-notes"></a>일반 Linux 설치 참고 사항
* VHDX 형식은 Azure에서 지원되지 않습니다. **고정된 VHD**만 지원됩니다.  Hyper-V 관리자 또는 convert-vhd cmdlet을 사용하여 디스크를 VHD 형식으로 변환할 수 있습니다. VirtualBox를 사용하면 디스크를 만들 때 기본적으로 동적 할당되지 않고 **고정 크기**가 선택됩니다.
* Azure에서는 1세대 가상 머신만 지원합니다. VHDX에서 VHD 파일 형식으로, 동적 확장에서 고정된 크기의 디스크로 1세대 가상 컴퓨터를 변환할 수 있습니다. 하지만 가상 머신의 세대를 변경할 수 없습니다. 자세한 내용은 [Hyper-V에 1 또는 2세대 가상 머신을 만들어야 합니까?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조하세요.
* VHD에 허용되는 최대 크기는 1,023GB입니다.
* Linux 시스템 설치 시에는 LVM(설치 기본값인 경우가 많음)이 아닌 표준 파티션을 사용하는 것이 *좋습니다*. 이렇게 하면 특히 문제 해결을 위해 OS 디스크를 다른 VM에 연결해야 하는 경우 복제된 VM과 LVM 이름이 충돌하지 않도록 방지합니다. 데이터 디스크에서 [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용할 수 있습니다.
* UDF 파일 시스템 탑재에 대한 커널 지원이 필요합니다. Azure에서 처음 부팅 시 프로비저닝 구성이 게스트에 연결된 UDF 형식의 미디어를 통해 Linux VM에 전달됩니다. Azure Linux 에이전트는 해당 구성을 읽고 VM을 프로비전하기 위해 UDF 파일 시스템을 탑재할 수 있어야 합니다.
* 2.6.37 버전 미만의 Linux 커널은 VM 크기가 더 클 때 Hyper-V에서 NUMA를 지원하지 않습니다. 이 문제는 주로 업스트림 Red Hat 2.6.32 커널을 사용하는 이전 배포에 영향을 주며 RHEL 6.6(kernel-2.6.32-504)에서는 해결되었습니다. 2.6.37보다 오래된 사용자 지정 커널 또는 2.6.32-504보다 오래된 RHEL 기반 커널을 실행하는 시스템의 경우 grub.conf의 커널 명령줄에 부트 매개 변수 `numa=off` 자세한 내용은 Red Hat [KB 436883](https://access.redhat.com/solutions/436883)을 참조하세요.
* OS 디스크에 스왑 파티션을 구성하지 마세요. 임시 리소스 디스크에서 스왑 파일을 만들도록 Linux 에이전트를 구성할 수 있습니다.  여기에 대한 자세한 내용은 아래 단계에서 확인할 수 있습니다.
* Azure의 모든 VHD는 가상 크기가 1MB 단위로 조정되어야 합니다. 원시 디스크에서 VHD로 변환할 때 변환하기 전에 원시 디스크 크기가 1MB의 배수인지 확인해야 합니다. 자세한 내용은 아래 단계에서 찾을 수 있습니다.

### <a name="installing-kernel-modules-without-hyper-v"></a>Hyper-V 없이 커널 모듈 설치
Azure는 Hyper-V 하이퍼바이저에서 실행되므로 Azure에서 Linux를 실행하려면 특정 커널 모듈이 설치되어야 합니다. Hyper-V 외부에서 만든 VM이 있는 경우 Linux 설치 관리자에서 Hyper-V 환경에서 실행되고 있음을 감지하지 못하면 초기 ramdisk(initrd 또는 initramfs)에 Hyper-V용 드라이버를 포함하지 않을 수 있습니다. 다른 가상화 시스템(예: Virtualbox, KVM 등)을 사용하여 Linux 이미지를 준비하는 경우 초기 ramdisk에서 최소한 `hv_vmbus` 및 `hv_storvsc` 커널 모듈을 사용할 수 있도록 initrd를 다시 작성해야 할 수 있습니다.  이는 적어도 업스트림 Red Hat 배포를 기반으로 하는 시스템의 알려진 문제입니다.

initrd 또는 initramfs 이미지를 다시 작성하는 메커니즘은 배포에 따라 다를 수 있습니다. 적절한 절차에 대해서는 배포판의 설명서를 참조하거나 고객 지원팀에 문의하세요.  다음은 `mkinitrd` 유틸리티를 사용하여 initrd를 다시 작성하는 방법에 대한 한 가지 예입니다.

먼저 기존 initrd 이미지를 백업합니다.

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

다음으로 `hv_vmbus` 및 `hv_storvsc` 커널 모듈을 사용하여 initrd를 다시 작성합니다.

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>VHD 크기 조정
Azure의 VHD 이미지에는 1MB로 조정된 가상 크기가 있어야 합니다.  일반적으로 Hyper-V를 사용하여 만든 VHD는 이미 올바르게 조정되어 있습니다.  VHD가 올바르게 조정되지 않으면 VHD에서 *이미지*를 만들려고 할 때 다음과 비슷한 오류 메시지가 나타날 수 있습니다.

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

이 문제를 해결하려면 Hyper-V 관리자 콘솔 또는 [Resize-VHD](http://technet.microsoft.com/library/hh848535.aspx) Powershell cmdlet을 사용하여 VM 크기를 조정합니다.  Windows 환경에서 실행되지 않는 경우 qemu-img를 사용하여 VHD를 변환(필요한 경우)하고 크기를 조정하는 것이 좋습니다.

> [!NOTE]
> VHD 형식이 잘못 지정되는 qemu-img 버전 >=2.2.1에 알려진 버그가 있습니다. 이 문제는 QEMU 2.6에서 해결되었습니다. qemu-img 2.2.0 이하 버전을 사용하거나 2.6 이상으로 업데이트하는 것이 좋습니다. 참조: https://bugs.launchpad.net/qemu/+bug/1490611
> 
> 

1. `qemu-img` 또는 `vbox-manage`와 같은 도구를 사용하여 직접 VHD 크기를 조정하면 VHD가 부팅되지 않을 수도 있습니다.  따라서 먼저 VHD를 원시 디스크 이미지로 변환하는 것이 좋습니다.  VM 이미지가 이미 원시 디스크 이미지로 만들어진 경우(KVM과 같은 일부 하이퍼바이저의 경우 기본값)에는 다음 단계를 건너뛸 수 있습니다.
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. 가상 크기가 1MB 단위로 조정되도록 디스크 이미지에 필요한 크기를 계산합니다.  다음 bash 셸 스크립트를 사용하여 이 작업을 간편하게 수행할 수 있습니다.  스크립트는 "`qemu-img info`"를 사용하여 디스크 이미지의 가상 크기를 결정한 후 크기를 다음 1MB로 계산합니다.
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. 위 스크립트에 설정된 대로 $rounded_size를 사용하여 원시 디스크의 크기를 조정합니다.
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. 이제 원시 디스크를 고정 크기 VHD로 다시 변환합니다.
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   또는, qemu 버전 **2.6 +** 는 `force_size` 옵션을 포함합니다.

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Linux 커널 요구 사항
Hyper-V 및 Azure용 LIS(Linux 통합 서비스) 드라이버는 업스트림 Linux 커널로 직접 제공됩니다. 최신 Linux 커널 버전(예: 3.x)이 포함된 많은 배포판에서 이러한 드라이버를 이미 사용할 수 있으며, 그렇지 않으면 이러한 드라이버의 백 포트 버전이 커널에 제공됩니다.  이러한 드라이버는 업스트림 커널에서 새로운 수정과 기능으로 지속적으로 업데이트되므로, 가능한 경우 이러한 수정과 업데이트가 포함된 [보증 배포판](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 실행하는 것이 좋습니다.

Red Hat Enterprise Linux 버전 **6.0-6.3**의 변형을 실행하는 경우 Hyper-V용 최신 LIS 드라이버를 설치해야 합니다. 드라이버는 [이 위치](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)에 있습니다. RHEL **6.4 이상** 및 파생 버전에서는 LIS 드라이버가 커널에 이미 포함되어 있으므로 Azure에서 해당 시스템을 실행하기 위해 추가 설치 패키지가 필요하지 않습니다.

사용자 지정 커널이 필요한 경우, 최신 커널 버전(예: **3.8 이상**)을 사용하는 것이 좋습니다. 자체 커널을 유지 관리하는 배포업체 또는 공급업체의 경우, 업스트림 커널에서 사용자 지정 커널로 LIS 드라이버를 정기적으로 백포팅하기 위해 약간의 노력이 필요합니다.  비교적 최신 커널 버전을 이미 실행 중이더라도 LIS 드라이버의 업스트림 수정 사항을 계속 추적하여 필요한 경우 백포팅하는 것이 좋습니다. LIS 드라이버 원본 파일의 위치는 Linux 커널 원본 트리의 [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) 파일에서 확인할 수 있습니다.

    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/

최소한 다음과 같은 패치를 적용하지 않으면 Azure에서 문제가 발생하는 것으로 알려져 있으므로 이러한 패치는 커널에 포함되어야 합니다. 이 목록에는 모든 배포에 대해 완전한 전체 정보가 포함되어 있지는 않습니다.

* [ata_piix: 기본적으로 Hyper-V 드라이버로 디스크 연기](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: RESET 경로의 전송 중인 패킷용 계정](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME을 사용하지 마세요.](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: RAID 및 가상 호스트 어댑터 드라이버에 대한 WRITE SAME 해제](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: NULL 포인터 역참조 수정](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: 링 버퍼 오류로 I/O가 중지됨](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: __scsi_remove_device 이중 실행 방지](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux 에이전트
Azure에서 Linux 가상 머신을 올바르게 프로비전하려면 [Azure Linux 에이전트](../extensions/agent-linux.md) (waagent)가 필요합니다. [Linux Agent GitHub 리포지토리](https://github.com/Azure/WALinuxAgent)에서 최신 버전을 가져오거나 문제를 보고하거나 끌어오기 요청을 제출할 수 있습니다.

* Linux 에이전트는 Apache 2.0 라이선스 하에서 릴리스되었습니다. 많은 배포판에서 이미 에이전트에 RPM 또는 deb 패키지를 제공하므로 경우에 따라 약간의 노력으로 이러한 패키지를 설치 및 업데이트할 수 있습니다.
* Azure Linux 에이전트를 사용하려면 Python v2.6 이상이 필요합니다.
* 또한 에이전트에는 python-pyasn1 모듈도 필요합니다. 대부분의 배포에서는 이 모듈이 설치 가능한 별도의 패키지로 제공됩니다.
* Azure Linux 에이전트가 NetworkManager와 호환되지 않는 경우도 있습니다. 배포를 통해 제공되는 RPM/Deb 패키지는 대부분 NetworkManager를 waagent 패키지에 대한 충돌 항목으로 구성하므로 Linux 에이전트 패키지 설치 시 NetworkManager가 제거됩니다.
* Azure Linux 에이전트는 해야 지원되는 최소 버전보다 높아야 합니다. [세부 정보](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)에 대해서는 이 문서를 참조하세요.

## <a name="general-linux-system-requirements"></a>일반 Linux 시스템 요구 사항

* 다음 매개 변수를 포함하려면 GRUB 또는 GRUB2의 커널 부팅 줄을 수정합니다. 이렇게 하면 모든 콘솔 메시지가 Azure에서 디버깅 문제를 지원할 수 있는 첫 번째 직렬 포트로 전송됩니다.
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    이렇게 하면 모든 콘솔 메시지가 Azure에서 디버깅 문제를 지원할 수 있는 첫 번째 직렬 포트로 전송됩니다.
  
    위의 작업을 수행하는 동시에 다음 매개 변수도 있는 경우 *제거* 하는 것이 좋습니다.
  
        rhgb quiet crashkernel=auto
  
    모든 로그가 직렬 포트로 전송되도록 하려는 클라우드 환경에서는 그래픽 및 자동 부팅 기능이 유용하지 않습니다. `crashkernel` 옵션은 원하는 경우 구성한 상태로 유지할 수 있지만, 이 매개 변수는 VM에서 사용 가능한 메모리 양을 128MB 이상 줄이므로 더 작은 VM 크기에서는 문제가 될 수 있습니다.

* Azure Linux 에이전트 설치
  
    Azure에서 Linux 이미지를 프로비전하려면 Azure Linux 에이전트가 필요합니다.  대부분의 배포에서는 에이전트를 RPM 또는 Deb 패키지로 제공합니다. 패키지의 이름은 보통 'WALinuxAgent' 또는 'walinuxagent'입니다.  [Linux 에이전트 가이드](../extensions/agent-linux.md)의 단계를 수행하여 에이전트를 수동으로 설치할 수도 있습니다.

* SSH 서버가 설치되어 부팅 시 시작되도록 구성되어 있는지 확인합니다.  보통 SSH 서버는 기본적으로 이와 같이 구성되어 있습니다.

* OS 디스크에 스왑 공간을 만들지 마십시오.
  
    Azure Linux 에이전트는 Azure에서 프로비전한 후 VM에 연결된 로컬 리소스 디스크를 사용하여 자동으로 스왑 공간을 구성할 수 있습니다. 로컬 리소스 디스크는 *임시* 디스크이며, VM의 프로비전을 해제할 때 비울 수 있습니다. Azure Linux 에이전트를 설치한 후(이전 단계 참조) /etc/waagent.conf에서 다음 매개 변수를 적절하게 수정합니다.
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* 마지막 단계로 다음 명령을 실행하여 가상 머신의 프로비전을 해제합니다.
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Virtualbox에서 'waagent-force-deprovision'을 실행한 후 다음 오류가 표시될 수 있습니다. `[Errno 5] Input/output error` 이 오류 메시지는 중요하지 않으므로 무시할 수 있습니다.
  > 
  > 

* 가상 머신을 종료하고 Azure에 VHD를 업로드합니다.

