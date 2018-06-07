---
title: "Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트 | Microsoft Docs"
description: "Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: hermannd
ms.openlocfilehash: 072a70c1da74b3b50ad8c0a93ee3c079a724d81f
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 실행
이 문서에서는 Microsoft Azure SUSE Linux VM(가상 머신)에서 SAP NetWeaver를 실행할 때 고려해야 할 다양한 항목을 설명합니다. 2016년 5월 19일을 기준으로 SAP NetWeaver는 Azure의 SUSE Linux VM에서 공식적으로 지원됩니다. Linux 버전, SAP 커널 버전 및 기타 필수 조건과 관련된 모든 세부 정보는 SAP 정보 1928533, “Azure의 SAP 응용 프로그램: 지원 제품 및 Azure VM 유형”에서 찾을 수 있습니다.
Linux VM의 SAP에 대한 자세한 내용은 [Linux VM(가상 머신)에서 SAP 사용](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.

다음 정보는 일부 잠재적인 문제를 예방하는 데 도움이 됩니다.

## <a name="suse-images-on-azure-for-running-sap"></a>SAP 실행을 위한 Azure의 SUSE 이미지
Azure에서 SAP NetWeaver를 실행하기 위해서는 SUSE Linux Enterprise Server SLES 12(SPx) 또는 SAP를 사용해야 합니다. 자세한 내용은 SAP 정보 1928533을 참조하세요. 특수한 SUSE 이미지는 Azure Marketplace(“SLES 11 SP3 for SAP CAL”)에 있지만, 일반 용도는 아닙니다. 이 이미지는 [SAP 클라우드 어플라이언스 라이브러리](https://cal.sap.com/) 솔루션을 위한 것이기 때문에 사용하지 않습니다.  

Azure에 모두 설치를 위한 Azure Resource Manager 배포 프레임워크를 사용해야 합니다. Azure PowerShell 또는 Azure CLI(명령줄 인터페이스)를 사용하여 SUSE SLES 이미지 및 버전을 찾아보려면 다음 명령을 사용합니다. 그런 다음 새 SUSE Linux VM 배포용 JSON 템플릿에서 OS 이미지를 정의하는 등 출력 내용을 사용할 수 있습니다.
PowerShell 명령은 Azure PowerShell 1.0.1 이상 버전에 유효합니다.

SAP 설치를 위한 표준 SLES 이미지를 여전히 사용할 수 있지만 이제 새 SLES를 SAP 이미지에 사용하는 것이 좋습니다. 이러한 이미지는 Azure 이미지 갤러리에서 현재 사용할 수 있습니다. 이러한 이미지에 대한 자세한 내용은 [Azure Marketplace 페이지]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) 또는 [SAP용 SLES에 관한 SUSE FAQ 웹 페이지]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ )를 참조하십시오.


* SUSE를 포함하는 기존 게시자를 찾습니다.
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* SUSE에서 기존 제품을 찾습니다.
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* SUSE SLES 제품을 찾습니다.
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* 특정 버전의 SLES SKU를 찾습니다.
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>SUSE VM에 WALinuxAgent 설치
WALinuxAgent라고 하는 이 에이전트는 Azure Marketplace 내 SLES 이미지의 일부분입니다. 수동으로(예: 온-프레미스에서 SLES OS VHD(가상 하드 디스크)를 업로드하는 경우) 설치와 관련된 내용은 다음을 참조하세요.

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "향상된 모니터링"
SAP "향상된 모니터링"은 Azure에서 SAP를 실행하기 위한 필수 구성 요소입니다. SAP 정보 2191498 “Azure와 Linux의 SAP: 향상된 모니터링”에서 자세한 내용을 확인하세요.

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Azure Linux VM에 Azure 데이터 디스크 연결
장치 ID를 사용하여 Azure 데이터 디스크를 Azure Linux VM에 탑재해서는 안됩니다. 대신 UUID(Universally Unique Identifier)를 사용합니다. 예를 들어 Azure 데이터 디스크를 탑재하기 위해 그래픽 도구를 사용하는 경우 주의해야 합니다. /etc/fstab의 항목을 재차 확인하세요.

장치 ID의 문제점은 변경될 가능성이 있고 그렇게 되면 Azure VM이 부팅 프로세스에 머무를 수 있다는 점입니다. 문제를 완화시키려면 /etc/fstab에 nofail 매개 변수를 추가할 수 있습니다. 하지만 응용 프로그램이 전과 같이 탑재 지점을 사용할 수 있고, 외부 Azure 데이터 디스크가 부팅 중에 탑재되지 않은 경우에 루트 파일 시스템에 작성할 수 있기 때문에 nofail에 유의합니다.

UUID를 통한 탑재에서 유일한 예외는 다음 섹션에 설명된 대로 문제 해결을 위해 OS 디스크를 연결하는 것입니다.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>더 이상 액세스할 수 없는 SUSE VM 문제 해결
Azure에서 SUSE VM 부팅 프로세스가 중지되는 상황이 발생할 수 있습니다(예: 디스크 탑재에 관련된 오류로 인해). Azure 포털에서 Azure Virtual Machines v2에 대한 부팅 진단 기능을 사용하여 이 문제를 확인할 수 있습니다. 자세한 내용은 [부팅 진단](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)을 참조하세요.

문제를 해결하는 한 가지 방법은 손상된 VM에서 Azure의 다른 SUSE VM에 OS 디스크를 연결하는 것입니다. 다음 섹션에 설명된 대로 /etc/fstab을 편집하거나 네트워크 udev 규칙을 제거하는 것과 같이 적절하게 변경합니다.

하지만 고려해야 할 한 가지 중요한 사항이 있습니다. 동일한 Azure Marketplace 이미지(예: SLES 11 SP4)에서 여러 SUSE VM을 배포하면 OS 디스크가 항상 동일한 UUID로 탑재되게 됩니다. 따라서 동일한 Azure Marketplace 이미지를 사용하여 배포된 다른 VM에서 OS 디스크를 연결하도록 UUID를 사용하면 두 개의 동일한 UUID가 발생합니다. 두 개의 동일한 UUID는 VM이 문제 해결에 사용되도록 하며, 원래 OS 디스크 대신 연결되고 손상된 OS 디스크에서 부팅됩니다.

문제를 방지하는 방법에는 다음 두 가지가 있습니다.

* VM 문제 해결을 위해 서로 다른 Azure Marketplace 이미지를 사용합니다(예: SLES 12 대신 SLES 11 SPx).
* UUID를 사용하여 다른 VM에서 손상된 OS 디스크를 연결하지 말고 다른 방법을 사용합니다.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>온-프레미스에서 Azure로 SUSE VM 업로드
온-프레미스에서 Azure에 SUSE VM을 업로드하는 단계에 대한 설명은 [Azure에 SLES 또는 openSUSE 가상 머신 준비](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

예를 들면, 기존 SAP 설치는 물론 호스트 이름을 유지하기 위해서 마지막에 프로비전 해제 단계 없이 VM을 업로드하려면 다음 항목을 확인합니다.

* 장치 ID가 아닌 UUID를 사용하여 OS 디스크가 탑재되어야 합니다. /etc/fstab에서만 UUID로 변경하는 것은 OS 디스크에 충분하지 않습니다. 또한, YaST를 통해 또는 /boot/grub/menu.lst를 편집하여 부팅 로더를 적용하는 것도 잊지 말아야 합니다.
* SUSE OS 디스크에 VHDX 형식을 사용하고 Azure에 업로드하기 위해서 이를 VHD로 변환하면 네트워크 장치가 eth0에서 eth1로 변경될 가능성이 높습니다. 나중에 Azure에서 부팅할 때 문제를 방지하려면 [복제된 SLES 11 VMware에서 eth0 수정](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)에 설명된 것처럼 eth0으로 다시 변경해야 합니다.

문서에 설명된 내용 외에 다음 파일을 삭제하는 것이 좋습니다.

   /lib/udev/rules.d/75-persistent-net-generator.rules

Azure Linux 에이전트(waagent)를 설치하여 NIC이 여러 개만 아니라면 잠재적인 문제를 방지할 수 있습니다.

## <a name="deploying-a-suse-vm-on-azure"></a>Azure에 SUSE VM 배포
새 Azure Resource Manager 모델의 JSON 템플릿 파일을 사용하여 새 SUSE VM을 만들어야 합니다. JSON 템플릿 파일을 만든 후에 PowerShell을 대신하여 다음 CLI 명령을 사용해 VM을 배포할 수 있습니다.

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
JSON 템플릿 파일에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../../resource-group-authoring-templates.md) 및 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)을 참조하세요.

CLI 및 Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager에서 Mac, Linux 및 Windows용 Azure CLI 사용](../../../xplat-cli-azure-resource-manager.md)을 참조하세요.

## <a name="sap-license-and-hardware-key"></a>SAP 라이선스 및 하드웨어 키
공식적인 SAP-Azure 인증을 위해, SAP 라이선스에 사용되는 SAP 하드웨어 키 계산을 위한 새로운 메커니즘이 도입되었습니다. 새 알고리즘을 사용하려면 SAP 커널이 적용되어야 합니다. 이전 Linux용 SAP 커널 버전에는 이 코드 변경이 포함되어 있지 않습니다. 따라서 특정한 상황(예: Azure VM 크기 조정)에서 SAP 하드웨어 키가 변경되고 SAP 라이선스가 더 이상 유효하지 않을 수 있습니다. 해결책은 최신 SAP Linux 커널과 함께 제공됩니다.  자세한 SAP 커널 패치는 SAP 정보 1928533에 설명되어 있습니다.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf 패키지 / tuned-adm
SUSE는 일련의 SAP 관련 설정을 관리하는 "sapconf"라는 패키지를 제공합니다. 이 패키지의 용도 및 설치하고 사용하는 방법에 대한 자세한 내용은 [sapconf를 사용하여 SAP 시스템을 실행하는 SUSE Linux Enterprise Server 준비](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) 및 [sapconf 정의 또는 SAP 시스템을 실행하기 위한 SUSE Linux Enterprise Server를 준비하는 방법](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)을 참조하세요.

그동안 ‘sapconf - tuned-adm’을 대체하는 새로운 도구가 있습니다. 이 도구에 대한 자세한 내용은 아래의 두 링크를 참조하세요.

- ‘tuned-adm’ 프로필 sap-hana에 대한 SLES 설명서는 [여기](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- ‘tuned-adm’을 사용하여 SAP 워크로드를 위한 시스템 튜닝은 6.2장의 [여기](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)에서 확인할 수 있습니다.

## <a name="nfs-share-in-distributed-sap-installations"></a>분산된 SAP 설치에서 NFS 공유
예를 들어 데이터베이스 및 SAP 응용 프로그램 서버를 별도의 VM에 설치하려는 분산 설치의 경우 NFS(네트워크 파일 시스템)을 통해 /sapmnt 디렉터리를 공유할 수 있습니다. /sapmnt에 NFS 공유를 생성한 후에 설치 단계에서 문제가 발생하면 공유에 "no_root_squash"가 설정되어 있는지 확인합니다.

## <a name="logical-volumes"></a>논리 볼륨
과거에는 여러 Azure 데이터 디스크에 큰 논리 볼륨이 필요한 경우(예: SAP 데이터베이스에 대해) Linux LVM(논리 볼륨 관리자)은 아직 Azure에서 유효성이 완전하게 확인되지 않았으므로 Raid 관리 도구 MDADM을 사용하는 것이 권장되었습니다. mdadm을 사용하여 Azure에서 Linux RAID를 설정하는 방법을 알아보려면 [Linux에서 소프트웨어 RAID 구성](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 그동안 2016년 5월부터 Linux 논리 볼륨 관리자가 Azure에서 완전히 지원되어 MDADM 대신 사용할 수 있게 되었습니다. Azure에서 LVM에 관한 자세한 내용을 보려면 다음을 읽어 보세요.  
[Azure에서 Linux VM에 LVM을 구성합니다](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Azure SUSE 리포지토리
표준 Azure SUSE 리포지토리에 액세스하는 데 문제가 있는 경우 명령을 사용하여 리포지토리를 다시 설정할 수 있습니다. Azure 지역에 개인 OS 이미지를 만든 다음 이 개인 OS 이미지를 기반으로 하는 새 VM을 배포하려는 다른 Azure 하위 지역으로 해당 이미지를 복사하는 경우에 이런 문제가 발생할 수 있습니다. VM 내부에서 다음 명령을 실행합니다.

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome 데스크톱
Gnome 데스크톱을 사용하여 SAP GUI, 브라우저 및 SAP 관리 콘솔을 비롯한 단일 VM 내에 전체 SAP 데모 시스템을 설치하려는 경우 이 힌트를 사용하여 Azure SLES 이미지에 설치합니다.

   SLES 11의 경우:

   ```
   zypper in -t pattern gnome
   ```

   SLES 12의 경우:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>클라우드에서 Linux의 Oracle에 대한 SAP 지원
가상화된 환경에서 Linux의 Oracle을 지원하는 것에 관한 제한 사항이 있습니다. 이 지원 제한 사항이 Azure 관련 토픽은 아니지만 이해하는 것이 중요합니다. SAP은 Azure와 같은 공용 클라우드에 있는 SUSE 또는 Red Hat에서 Oracle을 지원하지 않습니다. 실행되는 동안, Azure의 Oracle DB는 Oracle Linux의 SAP에서 완전하게 지원됩니다(SAP 참고 1928533 참조). 다른 조합이 필요한 경우 Oracle에 직접 문의합니다.

