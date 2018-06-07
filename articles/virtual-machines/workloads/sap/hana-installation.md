---
title: Azure(큰 인스턴스)에서 SAP HANA 설치 | Microsoft Docs
description: Azure(큰 인스턴스)에서 SAP HANA를 설치하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 768d9c31cdf019bf73a9d3b3a239c537c72725f6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778599"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>Azure(큰 인스턴스)에서 SAP HANA를 설치하고 구성하는 방법

다음은 이 지침을 읽기 전에 알아야 할 중요한 정의입니다. [Azure(큰 인스턴스)에서 SAP HANA 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)에서 HANA 큰 인스턴스 단위의 두 가지 다른 클래스를 도입했습니다.

- SKU의 'Type I 클래스'인 S72, S72m, S144, S144m, S192 및 S192m.
- SKU의 'Type II 클래스'인 S384, S384m, S384xm, S576m, S768m 및 S960m.

클래스 지정자는 HANA 큰 인스턴스 설명서 전반에서 HANA 큰 인스턴스 SKU를 기반으로 하는 다양한 기능과 요구 사항을 언급하는 데 사용됩니다.

자주 사용하는 다른 정의는 다음과 같습니다.
- **큰 인스턴스 스탬프:** SAP HANA TDI 인증을 받았고 Azure 내에서 SAP HANA 인스턴스를 전용으로 실행하는 하드웨어 인프라 스택입니다.
- **Azure(큰 인스턴스)에서 SAP HANA:** 다양한 Azure 지역에서 큰 인스턴스 스탬프로 배포된 SAP HANA TDI 인증 하드웨어에서 HANA 인스턴스를 실행하는 Azure 제품에 대한 공식적인 이름입니다. 관련 용어인 **HANA 큰 인스턴스**는 Azure(큰 인스턴스)에서 SAP HANA의 줄임말이며 기술 배포 가이드에 널리 사용됩니다.


SAP HANA 설치는 사용자가 진행하며 Azure(큰 인스턴스) 서버에 새 SAP HANA가 전달된 후에 작업을 시작할 수 있습니다. 그리고 Azure VNet과 HANA 큰 인스턴스 유닛 사이의 연결이 설정된 후에 가능합니다. 

> [!Note]
> SAP 정책에 따라 SAP HANA 설치는 SAP HANA 설치를 수행하도록 인증된 사람이 수행해야 합니다. Certified SAP Technology Associate 시험, SAP HANA 설치 인증 시험에 합격한 사람 또는 SAP 인증 SI(시스템 통합자).

특히 HANA 2.0을 설치할 때 설치하기로 결정한 SAP HANA 릴리스에서 지원되는 OS를 확인하려면 [SAP Support Note # 2235581 - SAP HANA: 지원되는 운영 체제](https://launchpad.support.sap.com/#/notes/2235581/E)를 다시 확인하세요. HANA 2.0에서 지원되는 OS는 HANA 1.0에서 지원되는 OS보다 더 제한적이라는 것을 알고 있습니다. 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>HANA 큰 인스턴스 단위를 받은 후 첫 번째 단계

HANA 큰 인스턴스를 받고 인스턴스에 대한 액세스 및 연결을 설정한 후 **첫 번째 단계**는 OS 공급자에게 인스턴스의 OS를 등록하는 것입니다. 이 단계에는 Azure에서 VM에 배포해야 하는 SUSE SMT 인스턴스에 SUSE Linux OS를 등록하는 것이 포함됩니다. HANA 큰 인스턴스 유닛은 SMT 인스턴스(이 설명서의 뒷부분 참조)에 연결할 수 있습니다. 또는 연결해야 하는 Red Hat Subscription Manager에 RedHat OS를 등록해야 합니다. 이 [문서](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)의 설명도 참조하세요. 이 단계는 OS를 패치할 수도 있어야 합니다. 고객에게 책임이 있는 작업입니다. SUSE의 경우 [여기](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)서 SMT 설치 및 구성을 위한 설명서를 찾아보세요.

**두 번째 단계**는 특정 OS 릴리스/버전의 새 패치 및 수정을 확인하는 것입니다. HANA 큰 인스턴스의 패치 수준이 최신 상태인지 확인하세요. OS 패치/릴리스의 타이밍과 Microsoft가 배포할 수 있는 이미지의 변경 사항에 따라 최신 패치가 포함되지 않을 수 있습니다. 따라서 HANA 큰 인스턴스 유닛을 인수한 후 보안, 기능, 가용성 및 성능이 적절한 패치가 특정 Linux 공급 업체에 의해 릴리스되었는지 여부를 확인하고 적용해야 하는 필수 단계입니다.

**세 번째 단계**는 특정 OS 릴리스/버전에 SAP HANA를 설치 및 구성하기 위해 관련 SAP Note를 확인하는 것입니다. 개별 설치 시나리오에 따라 SAP Note 또는 구성에 대한 권장 사항이나 변경 사항이 달라지므로 Microsoft에서 항상 HANA 큰 인스턴스 단위를 완벽하게 구성할 수 있는 것은 아닙니다. 따라서 정확한 Linux 릴리스에서 SAP HANA와 관련된 SAP Notes를 반드시 읽어야 합니다. 또한 필요한 OS 릴리스/버전의 구성을 확인하고 이미 설정되지 않은 경우 구성 설정을 적용하세요.

구체적으로 다음 매개 변수를 확인하고 다음과 같이 조정하세요.

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 및 RHEL 7.2부터 이러한 매개 변수를 /etc/sysctl.d 디렉터리의 구성 파일에 설정해야 합니다. 예를 들어 이름이 91-NetApp-HANA.conf인 구성 파일을 만들어야 합니다. 이전 SLES 및 RHEL 릴리스의 경우 /etc/sysctl.conf에서 이러한 매개 변수를 설정해야 합니다.

모든 RHEL 릴리스와 SLES12부터는 
- sunrpc.tcp_slot_table_entries = 128

매개 변수를 /etc/modprobe.d/sunrpc-local.conf에서 설정해야 합니다. 파일이 없으면 다음 항목을 추가하여 먼저 생성해야 합니다. 
- options sunrpc tcp_max_slot_table_entries=128

**네 번째 단계**는 HANA 큰 인스턴스 단위의 시스템 시간을 확인하는 것입니다. 인스턴스는 HANA 큰 인스턴스 스탬프가 있는 Azure 지역의 위치를 나타내는 시스템 표준 시간대로 배포됩니다. 소유한 인스턴스의 시스템 시간이나 표준 시간대를 자유롭게 변경할 수 있습니다. 이렇게 하고 더 많은 인스턴스를 테넌트에 주문하면 새로 전달된 인스턴스의 표준 시간대를 적용해야 합니다. Microsoft 작업에서는 인계 후 인스턴스에서 사용자가 설정한 시스템 표준 시간대에 대해 모릅니다. 따라서 새로 배포된 인스턴스는 변경한 동일한 표준 시간대로 설정되지 않을 수 있습니다. 따라서 인계된 인스턴스의 표준 시간대를 확인하고 필요할 경우 적용하는 것은 고객의 책임입니다. 

**다섯 번째 단계**는 etc/hosts를 확인하는 것입니다. 블레이드를 인계하면 다양한 용도로 서로 다른 IP 주소가 할당됩니다(다음 섹션 참조). etc/hosts 파일을 확인합니다. 기존 테넌트에 단위가 추가된 경우 새로 배포된 시스템의 etc/hosts가 이전에 제공된 시스템의 IP 주소로 올바르게 유지되지 않을 수 있습니다. 따라서 새로 배포된 인스턴스에서 상호 작용하고 테넌트에 이전에 배포된 단위의 이름을 확인할 수 있도록 올바른 설정을 확인하는 것은 고객의 책임입니다. 

## <a name="networking"></a>네트워킹
Azure VNet을 디자인할 때 권장 사항을 따르고 다음 문서에 설명된 대로 해당 VNet을 HANA 큰 인스턴스에 연결한다고 가정합니다.

- [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure의 SAP HANA(큰 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

단일 단위의 네트워킹에 대해 알아야 할 몇 가지 세부 사항이 있습니다. 모든 HANA 큰 인스턴스 단위에는 2개 또는 3개의 단위 NIC 포트에 할당된 2개 또는 3개의 IP 주소가 포함되어 있습니다. HANA 확장 구성 및 HANA 시스템 복제 시나리오에는 3개의 IP 주소가 사용됩니다. 단위의 NIC에 할당된 IP 주소 중 하나가 [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)에 설명된 서버 IP 풀을 벗어납니다.

할당된 두 IP 주소가 있는 단위에 대한 배포는 다음과 같습니다.

- eth0.xx에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 IP 주소가 할당되어 있어야 합니다. 이 IP 주소는 OS의 /etc/hosts에서 유지 관리에 사용됩니다.
- eth1.xx에는 NFS와의 통신에 사용되는 IP 주소가 할당되어 있어야 합니다. 따라서 이러한 주소는 테넌트 내에서 인스턴스 트래픽에 대한 인스턴스를 허용하기 위해 etc/hosts에서 유지 관리할 필요가 **없습니다**.

HANA 시스템 복제 또는 HANA 확장 배포의 경우 두 개의 IP 주소가 할당된 블레이드 구성은 적합하지 않습니다. IP 주소가 두 개만 할당되어 있는 경우 이러한 구성을 배포하려면 할당된 세 번째 VLAN의 세 번째 IP 주소를 Azure의 SAP HANA Service Management에 문의하세요. 세 개의 NIC 포트에 새 개의 IP 주소가 할당되어 있는 HANA 큰 인스턴스 유닛에는 다음 사용 규칙이 적용됩니다.

- eth0.xx에는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 IP 주소가 할당되어 있어야 합니다. 따라서 이 IP 주소는 OS의 /etc/hosts에서 유지 관리에 사용됩니다.
- eth1.xx에는 NFS 저장소와의 통신에 사용되는 IP 주소가 할당되어 있어야 합니다. 따라서 이 주소 유형은 etc/hosts에서 유지 관리되지 않아야 합니다.
- eth2.xx는 다른 인스턴스 간의 통신을 위해 etc/hosts에서 유지 관리되도록 독점적으로 사용되어야 합니다. 이 주소는 HANA가 노드 간 구성에 사용하는 IP 주소로 규모 확장 HANA 구성에서 유지해야 하는 IP 주소이기도 합니다.



## <a name="storage"></a>Storage

Azure(큰 인스턴스)에서 SAP HANA에 대한 저장소 레이아웃은 [SAP HANA 저장소 요구 사항](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서에 문서화된 SAP 권장 지침을 통해 Azure Service Management의 SAP HANA에 의해 구성됩니다. 다른 HANA 큰 인스턴스 SKU가 있는 서로 다른 볼륨의 대략적인 크기는 [Azure에서 SAP HANA(큰 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 설명되어 있습니다.

저장소 볼륨에 대한 명명 규칙은 아래 테이블에 나열되어 있습니다.

| 저장소 사용 | 탑재 이름 | 볼륨 이름 | 
| --- | --- | ---|
| HANA data | /hana/data/SID/mnt0000<m> | 저장소 IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA log | /hana/log/SID/mnt0000<m> | 저장소 IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA log backup | /hana/log/backups | 저장소 IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA shared | /hana/shared/SID | 저장소 IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 저장소 IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

SID = HANA 인스턴스 시스템 ID 

tenant = 테넌트 배치 시 연산에 대한 내부 열거

위에서 볼 수 있듯이 HANA shared와 usr/sap는 같은 볼륨을 공유합니다. 탑재 지점의 명명법에는 HANA 인스턴스의 시스템 ID는 물론 탑재 번호가 포함됩니다. 강화(scale-up) 배포의 경우 탑재가 하나(예: mnt00001)만 있습니다. 반면에 확장(scale-out) 배포의 경우 worker 및 master 노드를 포함하여 많은 탑재를 볼 수 있습니다. 확장 환경의 경우, 데이터, 로그, 로그 백업 볼륨이 공유되고 확장 구성의 각 노드에 연결됩니다. 여러 SAP 인스턴스를 실행하는 구성의 경우 다른 볼륨 세트가 만들어져 HAN 큰 인스턴스 유닛에 연결됩니다.

이 문서를 읽고 HANA 큰 인스턴스 유닛을 살펴보면 유닛의 HANA/data에 상당히 많은 디스크 볼륨이 있고 HANA/log/backup 볼륨이 있다는 것을 볼 수 있습니다. HANA/data 크기가 너무 큰 이유는 고객에게 제공되는 저장소 스냅숏이 동일한 디스크 볼륨을 사용하기 때문입니다. 저장소 스냅숏을 더 많이 수행할수록 할당된 저장소 볼륨에서 더 많은 공간이 스냅숏에 소비된다는 의미입니다. HANA/log/backup 볼륨은 데이터베이스 백업을 넣는 볼륨으로 간주되지 않습니다. HANA 트랜잭션 로그 백업을 위한 백업 볼륨에 사용되도록 크기가 설정되었습니다. 향후 버전의 저장소 스냅숏 셀프 서비스는 이 특정 볼륨에 보다 빈번하게 스냅숏을 저장하도록 만드는 것이 목표입니다. HANA Large Instance 인프라에서 제공하는 재해 복구 기능에 옵트인하려는 경우 재해 복구 사이트를 보다 빈번하게 복제할 수 있습니다. 자세한 내용은 [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

제공된 저장소 외에도 1TB 단위로 추가 저장소 용량을 구입할 수 있습니다. 이 추가 저장소는 HANA 큰 인스턴스에 새 볼륨으로 추가될 수 있습니다.

Azure의 SAP HANA Service Management로 온보딩하는 동안 고객은 sidadm 사용자 및 sapsys 그룹(예: 1000,500)에 대한 사용자 ID(UID) 및 그룹 ID(GID)를 지정합니다. SAP HANA 시스템을 설치하는 동안 동일한 값이 사용되어야 합니다. 유닛에 다수의 HANA 인스턴스를 배포하려는 경우 다수의 볼륨 세트(각 인스턴스당 하나씩)를 갖게 됩니다. 결과적으로 배포 시 다음을 정의해야 합니다.

- 다수의 HANA 인스턴스에 대한 SID(sidadm은 여기서 파생되었음).
- 다수의 HANA 인스턴스에 대한 메모리 크기. 인스턴스당 메모리 크기는 각각의 개별적인 볼륨 세트에서 볼륨의 크기를 정의합니다.

저장소 공급자 권장 사항에 따라 탑재된 모든 볼륨(부팅 LUN 제외)에 대해 다음 탑재 옵션이 구성됩니다 .

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

탑재 지점은 다음 그래픽과 같이 /etc/fstab에 구성됩니다.

![HANA 큰 인스턴스 유닛의 탑재된 볼륨의 fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA 큰 인스턴스 유닛에 대한 df -h 명령의 출력은 다음과 같습니다.

![HANA 큰 인스턴스 유닛의 탑재된 볼륨의 fstab](./media/hana-installation/image2_df_output.PNG)


큰 인스턴스 스탬프의 저장소 컨트롤러와 노드는 NTP 서버와 동기화됩니다. Azure(큰 인스턴스) 장치의 SAP HANA와 Azure VM을 NTP 서버에 대해 동기화할 경우 Azure 또는 큰 인스턴스 스탬프에서 인프라 및 계산 장치 간에 중대한 시간 변동이 발생하지 않게 됩니다.

아래 사용된 저장소에 대해 SAP HANA를 최적화하려면 다음 SAP HANA 구성 매개 변수도 설정해야 합니다.

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 버전 SPS12까지는 [SAP Note #2267798 - SAP HANA 데이터베이스의 구성](https://launchpad.support.sap.com/#/notes/2267798)에 설명된 대로 SAP HANA 데이터베이스 설치 중에 이러한 매개 변수를 설정할 수 있습니다.

hdbparam 프레임워크를 사용하여 SAP HANA 데이터베이스 설치 후 매개 변수를 구성할 수도 있습니다. 

SAP HANA 2.0에서는 hdbparam 프레임워크가 사용되지 않습니다. 따라서 SQL 명령을 사용하여 매개 변수를 설정해야 합니다. 자세한 내용은 [SAP Note #2399079: HANA 2에서 hdbparam 제거](https://launchpad.support.sap.com/#/notes/2399079)를 참조하세요.


## <a name="operating-system"></a>운영 체제

제공되는 OS 이미지의 스왑 공간은 [SAP Support Note #1999997 - FAQ: SAP HANA 메모리](https://launchpad.support.sap.com/#/notes/1999997/E)(영문)에 따라 2GB로 설정됩니다. 원하는 다른 모든 설정은 고객이 설정해야 합니다.

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana)는 Azure(큰 인스턴스)에서 SAP HANA에 대해 설치되는 Linux 배포판입니다. 이 특정 배포판은 SAP 관련 기능을 &quot;바로 사용할 수 있게&quot; 제공합니다(SLES에서 SAP을 효과적으로 실행하기 위해 미리 설정된 매개 변수 포함).

SLES의 SAP HANA 배포와 관련된 몇 가지 유용한 리소스(고가용성 설정, SAP 작업과 관련된 보안 강화 등)에 대해서는 SUSE 웹 사이트의 [리소스 라이브러리/백서](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) 및 SCN(SAP Community Network)의 [SUSE의 SAP](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)을 참조하세요.

SUSE 관련 링크에 있는 유용한 추가 SAP 정보:

- [SUSE Linux의 SAP HANA 사이트](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 모범 사례: 복제 큐에 넣기 – SUSE Linux Enterprise 12의 SAP NetWeaver](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)(영문)
- [ClamSAP - SAP용 SLES 바이러스 방지](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)(SLES 12 for SAP Applications 포함)(영문)

SLES 12에서 SAP HANA 구현에 적용할 수 있는 SAP Support Note:

- [SAP Support Note #1944799 – SLES 운영 체제 설치를 위한 SAP HANA 지침](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)(영문)
- [SAP Support Note #2205917 – SAP HANA DB: SLES 12 for SAP Applications를 위한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2205917/E)(영문)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: 설치 참고](https://launchpad.support.sap.com/#/notes/1984787)(영문)
- [SAP Support Note #171356 – SAP Software on Linux: 일반 정보](https://launchpad.support.sap.com/#/notes/1984787)(영문)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)

[SAP HANA용 Red Hat Enterprise Linux](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana)(영문)는 HANA 큰 인스턴스에서 SAP HANA를 실행하기 위한 또 다른 제품입니다. RHEL 6.7 및 7.2 릴리스를 사용할 수 있습니다. RHEL 7.2 및 최신 릴리스만 지원되는 기본 Azure VM에 비해 HANA Large Instances는 RHEL 6.7도 지원합니다. 그러나 RHEL 7.x 버전을 사용하는 것이 좋습니다.

Red Hat 관련 링크에 있는 유용한 추가 SAP 정보:
- [Red Hat Linux 사이트의 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)

Red Hat에서 SAP HANA 구현에 적용할 수 있는 SAP Support Note:

- [SAP Support Note #2009879 – RHEL(Red Hat Enterprise Linux) 운영 체제를 위한 SAP HANA 지침](https://launchpad.support.sap.com/#/notes/2009879/E)(영문)
- [SAP Support Note #2292690 - SAP HANA DB: RHEL 7을 위한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)(영문)
- [SAP Support Note #2247020 - SAP HANA DB: RHEL 6.7을 위한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2247020)(영문)
- [SAP Support Note #1391070 – Linux UUID 솔루션](https://launchpad.support.sap.com/#/notes/1391070)(영문)
- [SAP Support Note #2228351 - Linux: RHEL 6 또는 SLES 11의 SAP HANA Database SPS 11 수정 번호 110 이상](https://launchpad.support.sap.com/#/notes/2228351)(영문)
- [SAP Support Note #2397039 - FAQ: RHEL의 SAP](https://launchpad.support.sap.com/#/notes/2397039)(영문)
- [SAP Support Note #1496410 - Red Hat Enterprise Linux 6.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/1496410)(영문)
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/2002167)(영문)

## <a name="time-synchronization"></a>시간 동기화

SAP NetWeaver 아키텍처를 기반으로 구축된 SAP 응용 프로그램은 SAP 시스템을 구성하는 다양한 구성 요소의 시간 차이에 민감합니다. 오류 제목이 ZDATE\_LARGE\_TIME\_DIFF인 SAP ABAP 짧은 덤프에 익숙할 수 있습니다. 서로 다른 서버 또는 VM의 시스템 시간이 너무 멀리 떨어지면 이러한 짧은 덤프가 표시되기 때문입니다.

Azure(큰 인스턴스)에 있는 SAP HANA의 경우 Azure에서 수행되는 시간 동기화가 큰 인스턴스 스탬프의 계산 장치에 적용되지 않습니다. 이 동기화는 원시 Azure VM에서 SAP 응용 프로그램을 실행하는 데는 적용할 수 없습니다. Azure가 시스템 시간이 적절하게 동기화되도록 보장하기 때문입니다. 결과적으로 Azure VM에서 실행되는 SAP 응용 프로그램 서버와 HANA 큰 인스턴스에서 실행되는 SAP HANA 데이터베이스 인스턴스에서 사용할 수 있는 별도의 시간 서버를 설정해야 합니다. 큰 인스턴스 스탬프의 저장소 인프라는 NTP 서버와 시간 동기화됩니다.

## <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux용 SMT 서버 설정
SAP HANA 큰 인스턴스는 인터넷에 직접 연결되지 않습니다. 따라서 이러한 유닛을 OS 제공자에 등록하고 패치를 다운로드하여 적용하는 것은 간단한 프로세스가 아닙니다. SUSE Linux의 경우 Azure VM에 SMT 서버를 설정하는 것이 하나의 솔루션이 될 수 있습니다. 반면에 Azure VM은 HANA 큰 인스턴스에 연결된 Azure VNet에서 호스트되어야 합니다. 이러한 SMT 서버를 사용하면 HANA Large Instance 유닛이 패치를 등록하고 다운로드할 수 있습니다. 

SUSE의 [Subscription Management Tool for SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)(SLES 12 SP2용 구독 관리 도구) 사이트에 자세한 지침이 제공되어 있습니다. 

HANA Large Instance에 대한 작업을 수행하는 SMT 서버를 설치하기 위한 전제 조건은 다음과 같습니다.

- HANA Large Instance ER 회로에 연결된 Azure VNet.
- 조직과 연결된 SUSE 계정. 조직에는 유효한 SUSE 구독이 있어야 합니다.

### <a name="installation-of-smt-server-on-azure-vm"></a>Azure VM에 SMT 서버 설치

이 단계에서는 Azure VM에 SMT 서버를 설치합니다. 첫 번째 조치는 [SUSE Customer Center](https://scc.suse.com/)(SUSE 고객 센터)에 로그인하는 것입니다.

로그인한 상태에서 Organization(조직)--> Organization Credentials(조직 자격 증명)로 이동합니다. 이 섹션에서 SMT 서버를 설정하는 데 필요한 자격 증명을 찾아야 합니다.

세 번째 단계는 Azure VNet에 SUSE Linux VM을 설치하는 것입니다. VM을 배포하려면 Azure의 SLES 12 SP2 갤러리 이미지를 사용합니다. 배포 과정에서 DNS 이름을 정의하지 않고 고정 IP 주소를 사용하지 마십시오(아래 스크린샷 참조).

![SMT 서버용 VM 배포](./media/hana-installation/image3_vm_deployment.png)

배포된 VM은 더 작은 VM이고 Azure VNet에서 내부 IP 주소인 10.34.1.4를 가져왔습니다. VM의 이름은 smtserver였습니다. 설치 후 HANA 큰 인스턴스 유닛에 대한 연결이 확인되었습니다. 이름 확인을 구성하는 방식에 따라 Azure VM의 etc/hosts에 HANA 큰 인스턴스 유닛의 확인을 구성해야 할 수 있습니다. 패치를 보관하는 데 사용할 VM에 디스크를 더 추가합니다. 부팅 디스크 자체가 너무 작을 수 있습니다. 데모의 경우 다음 스크린샷처럼 디스크가 /srv/www/htdocs에 탑재되었습니다. 100GB 디스크로 충분합니다.

![SMT 서버용 VM 배포](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

HANA 큰 인스턴스 유닛에 로그인하고 /etc/hosts를 유지 관리하고 네트워크를 통해 SMT 서버를 실행해야 하는 Azure VM에 연결할 수 있는지 확인합니다.

이 확인이 성공적으로 완료되면 SMT 서버를 실행할 Azure VM에 로그인해야 합니다. putty를 사용하여 VM에 로그인하는 경우 bash 창에서 다음 명령을 순서대로 실행해야 합니다.

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

이 명령을 실행한 후 bash를 다시 시작하여 설정을 활성화합니다. 그런 다음 YAST를 시작합니다.

YAST에서 Software Maintenance(소프트웨어 유지 관리)로 이동하여 smt를 검색합니다. smt를 선택하면 yast2-smt로 자동으로 전환됩니다(아래 그림 참조).

![yast의 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


smtserver에 설치를 위한 선택 사항을 수락합니다. 설치가 완료되면 SMT 서버 구성으로 이동하여 앞에서 검색한 SUSE Customer Center(SUSE 고객 센터)의 조직 자격 증명을 입력합니다. Azure VM 호스트 이름을 SMT 서버 URL로 입력합니다. 이 데모에서는 https://smtserver입니다(다음 그래픽 참조).

![SMT 서버 구성](./media/hana-installation/image6_configuration_of_smtserver1.png)

다음 단계로, SUSE Customer Center(SUSE 고객 센터)에 대한 연결이 작동하는지 테스트해야 합니다. 다음 그래픽에서 볼 수 있듯이 데모에서는 작동이 되었습니다.

![SUSE Customer Center(SUSE 고객 센터)에 대한 연결 테스트](./media/hana-installation/image7_test_connect.png)

SMT 설정이 시작되면 데이터베이스 암호를 제공해야 합니다. 새로운 설치이므로 다음 그래픽과 같이 암호를 정의해야 합니다.

![데이터베이스에 대한 암호 정의](./media/hana-installation/image8_define_db_passwd.PNG)

다음 인터랙션은 인증서가 만들어 질 때 수행됩니다. 다음과 같이 대화 상자를 통해 단계를 진행합니다.

![SMT 서버의 인증서 만들기](./media/hana-installation/image9_certificate_creation.PNG)

구성 끝 부분의 'Run synchronization check'(동기화 확인 실행) 단계에서 몇 분 정도가 소요될 수 있습니다. SMT 서버를 설치하고 구성한 후에는 탑재 지점인 /srv/www/htdocs/ 아래에서 repo 디렉터리와 repo의 하위 디렉터리를 찾을 수 있습니다. 

다음 명령으로 SMT 서버 및 관련 서비스를 다시 시작합니다.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>SMT 서버에 패키지 다운로드

모든 서비스가 다시 시작된 후 Yast를 사용하여 SMT Management에서 적절한 패키지를 선택합니다. 패키지 선택은 SMT 서버를 실행하는 SLES 릴리스 또는 VM 버전이 아니라 HANA 큰 인스턴스 서버의 OS 이미지에 따라 달라집니다. 선택 화면의 예는 다음과 같습니다.

![패키지 선택](./media/hana-installation/image10_select_packages.PNG)

패키지 선택이 끝나면 설정한 SMT 서버에 선택한 패키지의 초기 복사를 시작해야 합니다. 이 복사는 smt-mirror 명령을 사용하여 셸에서 트리거됩니다(아래 그림 참조).


![SMT 서버에 패키지 다운로드](./media/hana-installation/image11_download_packages.PNG)

위에서 볼 수 있듯이 패키지는 탑재 지점 /srv/www/htdocs 아래 생성된 디렉터리에 복사되어야 합니다. 이 프로세스는 시간이 어느 정도 걸릴 수 있습니다. 선택한 패키지 수에 따라 최대 1시간 이상이 걸릴 수 있습니다.
이 프로세스를 마치면 SMT 클라이언트 설정으로 이동해야 합니다. 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>HANA 큰 인스턴스 유닛에 SMT 클라이언트 설정

이 경우 클라이언트는 HANA 큰 인스턴스 유닛입니다. SMT 서버 설정이 clientSetup4SMT.sh 스크립트를 Azure VM에 복사했습니다. 이 스크립트를 SMT 서버에 연결할 HANA 큰 인스턴스 유닛으로 복사합니다. -h 옵션을 사용하여 스크립트를 시작하고 매개 변수로 SMT 서버의 이름을 입력합니다. 이 예제에서는 smtserver입니다.

![SMT 클라이언트 구성](./media/hana-installation/image12_configure_client.PNG)

클라이언트에 의해 서버에서 인증서 로드가 성공했지만 등록이 실패하는 시나리오가 있을 수 있습니다(아래 참조).

![클라이언트 등록 실패](./media/hana-installation/image13_registration_failed.PNG)

등록이 실패하면 [ SUSE 지원 문서](https://www.suse.com/de-de/support/kb/doc/?id=7006024)를 읽고 문서에 설명된 단계를 실행합니다.

> [!IMPORTANT] 
> 서버 이름으로 VM 이름을 제공해야 합니다. 이 경우 smtserver이며 정규화된 도메인 이름은 사용하지 않습니다. VM 이름만 작동합니다. 

이 단계를 실행한 후에는 HANA 큰 인스턴스 유닛에서 다음 명령을 실행해야 합니다

```
SUSEConnect –cleanup
```

> [!Note] 
> 지난 테스트에서는 해당 단계 후 항상 몇 분을 기다려야 했습니다. SUSE 문서에 설명된 시정 조치 후에 즉시 실행되는 clientSetup4SMT.sh는 인증서가 아직 유효하지 않다는 메시지로 종료되었습니다. 대개 5~10분을 기다려서 clientSetup4SMT.sh를 실행하면 클라이언트 구성이 성공적으로 끝납니다.

SUSE 문서의 단계를 기반으로 수정해야 하는 문제가 발생하면 HANA 큰 인스턴스 유닛에서 clientSetup4SMT.sh를 다시 시작해야 합니다. 이제 아래와 같이 성공적으로 완료됩니다.

![클라이언트 등록 성공](./media/hana-installation/image14_finish_client_config.PNG)

이 단계에서 Azure VM에 설치된 SMT 서버에 연결하기 위해 HANA 큰 인스턴스 유닛의 SMT 클라이언트를 구성했습니다. 이제 'zypper up' 또는 'zypper in'을 사용하여 HANA 큰 인스턴스에 OS 패치를 설치하거나 추가 패키지를 설치할 수 있습니다. 이전에 다운로드한 패치만 SMT 서버에서 가져올 수 있는 것으로 간주됩니다.


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>HANA 큰 인스턴스에 SAP HANA 설치의 예
이 섹션에서는 HANA 큰 인스턴스 유닛에 SAP HANA를 설치하는 방법을 설명합니다. 시작 상태는 다음과 같습니다.

- SAP HANA 큰 인스턴스에 배포하기 위해 Microsoft에 모든 데이터를 제공했습니다.
- Microsoft로부터 SAP HANA 큰 인스턴스를 받았습니다.
- 온-프레미스 네트워크에 연결된 Azure VNet을 만들었습니다.
- HANA 큰 인스턴스용 ExpressRoute 회로를 동일한 Azure VNet에 연결했습니다.
- HANA 큰 인스턴스의 점프 박스로 사용하는 Azure VM을 설치했습니다.
- 점프 박스에서 HANA 큰 인스턴스 유닛으로 연결할 수 있고 그 반대로도 연결할 수 있는 것을 확인했습니다.
- 필요한 패키지와 패치가 모두 설치되어 있는지 확인했습니다.
- 사용 중인 OS에 HANA 설치에 관한 SAP 메모 및 문서를 읽었고 선택한 HANA 릴리스가 OS 릴리스에서 지원되는지 확인했습니다.

다음 순서로 표시되는 내용은 점프 박스 VM(이 경우 Windows OS에서 실행 중인)에 HANA 설치 패키지를 다운로드하고, HANA 큰 인스턴스 유닛에 패키지를 복사하고 설치하는 시퀀스입니다.

### <a name="download-of-the-sap-hana-installation-bits"></a>SAP HANA 설치 비트 다운로드
HANA 큰 인스턴스 장치는 인터넷에 직접 연결되어 있지 않기 때문에 설치 패키지를 SAP에서 HANA 큰 인스턴스 VM으로 직접 다운로드할 수 없습니다. 인터넷에 직접 연결되지 않는 상황을 해결하려면 점프 박스가 필요합니다. 패키지를 점프 박스 VM에 다운로드합니다.

HANA 설치 패키지를 다운로드하려면 SAP Marketplace에 액세스할 수 있는 SAP S-user 또는 다른 사용자가 필요합니다. 로그인한 후 화면의 순서에 따라 이동합니다.

[SAP Service Marketplace](https://support.sap.com/en/index.html)로 이동하여 > Download Software(소프트웨어 다운로드) > Installations and Upgrade(설치 및 업그레이드) >By Alphabetical Index(알파벳 순서에 따라)를 클릭하고 H – SAP HANA Platform Edition > SAP HANA Platform Edition 2.0 > Installation(설치) 아래에서 다음 파일을 다운로드합니다.

![HANA 설치 다운로드](./media/hana-installation/image16_download_hana.PNG)

데모에서는 SAP HANA 2.0 설치 패키지를 다운로드했습니다. Azure 점프 박스 VM에서 자동 압축 해제 아카이브를 디렉터리에 추출합니다(아래 그림 참조).

![HANA 설치 추출](./media/hana-installation/image17_extract_hana.PNG)

아카이브가 추출되면 추출을 통해 생성된 디렉터리(위의 경우 51052030)를 HANA 큰 인스턴스 유닛에서 /hana/shared 볼륨으로 작성한 디렉터리로 복사합니다.

> [!Important]
> 공간에 제한되어 있고 다른 프로세스에서도 사용해야 하므로 설치 패키지를 루트 또는 부팅 LUN에 복사하지 마세요.


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>HANA 큰 인스턴스 유닛에 SAP HANA 설치
SAP HANA를 설치하려면 root 사용자로 로그인해야 합니다. root만 SAP HANA를 설치할 수 있는 권한이 있습니다.
먼저 해야 할 일은 /hana/shared에 복사한 디렉터리에 대한 권한을 설정하는 것입니다. 권한은 다음과 같이 설정해야 합니다.

```
chmod –R 744 <Installation bits folder>
```

그래픽 설치를 사용하여 SAP HANA를 설치하려면 gtk2 패키지가 HANA 큰 인스턴스에 설치되어 있어야 합니다. 명령을 사용하여 설치되었는지 확인합니다.

```
rpm –qa | grep gtk2
```

이후 단계에서는 그래픽 사용자 인터페이스를 사용하여 SAP HANA 설치를 보여줍니다. 다음 단계로 설치 디렉터리로 이동하여 하위 디렉터리인 HDB_LCM_LINUX_X86_64로 이동합니다. 시작

```
./hdblcmgui 
```
해당 디렉터리에서. 이제 설치를 위해 데이터를 제공해야 하는 일련의 화면이 설명되어 있습니다. 데모에서는 SAP HANA 데이터베이스 서버 및 SAP HANA 클라이언트 구성 요소를 설치합니다. 따라서 아래와 같이 'SAP HANA Database'(SAP HANA 데이터베이스)가 선택됩니다.

![설치 시 HANA 선택](./media/hana-installation/image18_hana_selection.PNG)

다음 화면에서는 'Install New System'(새 시스템 설치) 옵션을 선택합니다.

![HANA 신규 설치 선택](./media/hana-installation/image19_select_new.PNG)

이 단계 후에는 SAP HANA 데이터베이스 서버에 추가로 설치할 수 있는 몇 가지 추가 구성 중 하나를 선택해야 합니다.

![추가 HANA 구성 요소 선택](./media/hana-installation/image20_select_components.PNG)

이 문서에서는 SAP HANA Client와 SAP HANA Studio를 선택했습니다. 강화 인스턴스도 설치했습니다. 따라서 다음 화면에서 'Single-Host System'(단일 호스트 시스템)을 선택해야 합니다. 

![강화 설치 선택](./media/hana-installation/image21_single_host.PNG)

다음 화면에서 몇 가지 데이터를 제공해야 합니다.

![SAP HANA SID 제공](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> HANA SID(System ID)로 HANA 큰 인스턴스 배포를 주문할 때 Microsoft에 제공한 것과 동일한 SID를 제공해야 합니다. 다른 SID를 선택하면 다른 볼륨의 액세스 권한 문제로 인해 설치가 실패합니다.

설치 디렉터리로 /hana/shared 디렉터리를 사용합니다. 다음 단계에서는 HANA 데이터 파일과 HANA 로그 파일의 위치를 제공해야 합니다.


![HANA 로그 위치 제공](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 데이터 및 로그 파일로 이 화면의 이전 화면에서 선택한 SID가 포함된 탑재 지점과 함께 제공된 볼륨을 정의해야 합니다. SID가 앞의 화면에서 입력한 내용과 일치하지 않으면 뒤로 돌아가서 탑재 지점에 있는 값으로 SID를 조정합니다.

다음 단계에서 호스트 이름을 검토하고 수정합니다. 

![호스트 이름 검토](./media/hana-installation/image24_review_host_name.PNG)

다음 단계에서는 HANA 큰 인스턴스 배포를 주문할 때 Microsoft에 제공한 데이터를 검색해야 합니다. 

![시스템 사용자 UID 및 GID 제공](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 유닛 배포를 주문할 때 Microsoft에 제공한 것과 동일한 사용자 ID 및 사용자 그룹 ID를 제공해야 합니다. 동일한 ID를 제공하지 못하면 HANA 큰 인스턴스 유닛에 SAP HANA를 설치할 수 없습니다.

다음 두 화면에서는 이 문서에는 없지만 SAP HANA 데이터베이스 인스턴스의 일부로 설치되는 SAP Host Agent에 사용되는 SAP HANA 데이터베이스의 SYSTEM 사용자에 대한 암호와 sapadm 사용자에 대한 암호를 제공해야 합니다.

암호를 정의한 후 확인 화면이 표시됩니다. 나열된 모든 데이터를 확인하고 설치를 계속합니다. 아래와 같이 설치 진행률을 보여주는 진행률 화면이 표시됩니다.

![설치 진행률 확인](./media/hana-installation/image27_show_progress.PNG)

설치를 마치면 다음과 같은 그림이 표시됩니다.

![설치를 마쳤습니다.](./media/hana-installation/image28_install_finished.PNG)

이 시점에서 SAP HANA 인스턴스가 실행되어 사용할 준비가 되어야 합니다. SAP HANA Studio에서 연결할 수 있어야 합니다. 또한 SAP HANA의 최신 패치를 확인하고 해당 패치를 적용해야 합니다.
























































 







 




